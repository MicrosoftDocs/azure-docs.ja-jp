---
title: Async Java の Azure Cosmos DB パフォーマンスに関するヒント
description: Azure Cosmos DB データベースのパフォーマンスを向上させるクライアント構成オプションについて説明します
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.openlocfilehash: 07da7f8905d7b8952db852d3da1dab12884de509
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041800"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Azure Cosmos DB と Async Java のパフォーマンスに関するヒント

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB は、高速で柔軟性に優れた分散データベースです。待機時間とスループットが保証されており、シームレスにスケーリングできます。 Azure Cosmos DB でデータベースをスケーリングするために、アーキテクチャを大きく変更したり、複雑なコードを記述したりする必要はありません。 スケールアップとスケールダウンは、API 呼び出しか SDK メソッド呼び出しを 1 回行うだけで簡単に実行できます。 ただし、Azure Cosmos DB にはネットワーク呼び出しによってアクセスするため、[SQL Async Java SDK](sql-api-sdk-async-java.md) を使うと、最高のパフォーマンスを実現するためにクライアント側の最適化を行うことができます。

データベースのパフォーマンスを向上させる場合は、 以下のオプションを検討してください。

## <a name="networking"></a>ネットワーク
   <a id="same-region"></a>
1. **パフォーマンスを確保するために同じ Azure リージョン内にクライアントを併置する**

    可能であれば、Azure Cosmos DB を呼び出すアプリケーションを Azure Cosmos DB データベースと同じリージョンに配置します。 大ざっぱな比較ですが、Azure Cosmos DB の呼び出しは、同じリージョン内であれば 1 ～ 2 ミリ秒以内で完了するのに対し、米国西部と米国東部との間では待ち時間が 50 ミリ秒を超えます。 要求がクライアントから Azure データセンターの境界まで流れるときに使用されるルートに応じて、この待機時間が要求ごとに異なる可能性があります。 最短の待機時間は、プロビジョニングされた Azure Cosmos DB エンドポイントと同じ Azure リージョン内に呼び出し元アプリケーションを配置することによって実現されます。 使用可能なリージョンの一覧については、「 [Azure のリージョン](https://azure.microsoft.com/regions/#services)」を参照してください。

    ![Azure Cosmos DB 接続ポリシーの図](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK の使用例
1. **最新の SDK をインストールする**

    Azure Cosmos DB SDK は、最適なパフォーマンスを提供するために頻繁に改善されています。 [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) のページを参照して、最新の SDK を確認し、改善点を確認してください。
2. **アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する**

    各 AsyncDocumentClient インスタンスはスレッドセーフであり、効率的な接続管理とアドレスのキャッシュが実行されます。 AsyncDocumentClient による効率的な接続管理とパフォーマンスの向上を実現するために、アプリケーションの有効期間中は、AppDomain ごとに AsyncDocumentClient の単一のインスタンスを使用することをお勧めします。

   <a id="max-connection"></a>

3. **ConnectionPolicy のチューニング**

    Async Java SDK を使っているときは Azure Cosmos DB の要求は HTTPS/REST を介して行われ、既定の最大接続プール サイズ (1000) の対象となります。 ほとんどのユース ケースではこの既定値で最適なはずです。 ただし、多数のパーティションを含む大きいコレクションがある場合は、setMaxPoolSize を使って、最大接続プール サイズをもっと大きい値 (たとえば 1500) に設定できます。

4. **パーティション分割コレクションに対する並列クエリを調整する**

    Azure Cosmos DB SQL Async Java SDK は、パーティション分割コレクションに対して並列にクエリを実行するために使用できる並列クエリをサポートします。 詳細については、SDK の操作に関連した[コード サンプル](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples)を参照してください。 並列クエリは、シリアル クエリよりもクエリの待機時間とスループットを向上させるように設計されています。

    (a) ***setMaxDegreeOfParallelism の調整\:*** 並列クエリは、複数のパーティションに並列にクエリを実行することによって機能します。 ただし、個々のパーティション分割されたコレクションからのデータは、クエリごとに順番に取得されます。 そのため、setMaxDegreeOfParallelism を使ってパーティションの数を設定すると、その他のすべてのシステムの条件が変わらなければ、クエリのパフォーマンスを最大にできる可能性が最大になります。 パーティションの数が不明な場合は、setMaxDegreeOfParallelism を使って大きな数を設定すると、システムが並列処理の最大限度として最小値 (パーティションの数、ユーザー指定の入力) を選びます。

    並列クエリが最も有効に機能するのは、クエリに対するデータがすべてのパーティションに均等に分散している場合であることに注意する必要があります。 パーティション分割されたコレクションが、クエリによって返されるすべてまたは大部分のデータがわずかな数のパーティション (最悪の場合は 1 つのパーティション) に集中するように分割されている場合、クエリのパフォーマンスに関してこれらのパーティションがボトルネックになるでしょう。

    (b) ***setMaxBufferedItemCount の調整\:*** 並列クエリは、結果の現在のバッチがクライアントによって処理されている間に結果をプリフェッチするように設計されています。 プリフェッチは、クエリの全体的な遅延の削減に役立ちます。 setMaxBufferedItemCount は、プリフェッチされる結果の数を制限します。 setMaxBufferedItemCount を、返される結果の予期される数 (またはそれ以上の数) に設定すると、クエリに対するプリフェッチの効果が最大になります。

    プリフェッチは MaxDegreeOfParallelism とは無関係に同じように動作し、すべてのパーティションからのデータに対して単一のバッファーが存在します。

5. **GetRetryAfterInMilliseconds の間隔でバックオフを実装する**

    パフォーマンス テストでは、調整される要求の割合がわずかになるまで負荷を上げる必要があります。 スロットル状態になった場合は、クライアント アプリケーションでバックオフ値を適用し、サーバー側によって指定された再試行間隔を後退させる必要があります。 バックオフにより、再試行までの待ち時間を最小限に抑えることができます。
6. **クライアント ワークロードをスケールアウトする**

    高スループット レベル (1 秒あたりの要求ユニット数が 50,000 超) でテストを行っている場合、コンピューターが CPU 使用率またはネットワーク使用率の上限に達したことでクライアント アプリケーションがボトルネックになることがあります。 この状態に達しても、クライアント アプリケーションを複数のサーバーにスケールアウトすることで引き続き同じ Azure Cosmos DB アカウントで対応できます。

7. **名前ベースのアドレス指定を使う**

    `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` という形式の SelfLinks (\_self) ではなく、`dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` というリンク形式の名前ベースのアドレス指定を使って、リンクの作成に使われるすべてのリソースの ResourceId を取得しなくて済むようにします。 また、これらのリソースは (場合によっては同じ名前で) 再作成されるので、これらをキャッシュしても役に立たない場合があります。

   <a id="tune-page-size"></a>
8. **パフォーマンスを向上させるために、クエリ/読み取りフィードのページ サイズを調整する**

    読み取りフィード機能 (readDocuments など) を使ってドキュメントの一括読み取りを実行するときや、SQL クエリを発行するときに、結果セットが大きすぎる場合、セグメント化された形式で結果が返されます。 既定では、100 項目または 1 MB (先に達した方) のチャンク単位で結果が返されます。

    該当するすべての結果を取得するために必要なネットワーク ラウンド トリップの回数を減らすために、[x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 要求ヘッダーを使って、ページ サイズを最大 1,000 まで増やすことができます。 ごく少数の結果のみを表示する必要がある場合は (ユーザー インターフェイスやアプリケーション API が一度に 10 件しか結果を返さない場合など)、読み取りとクエリに使用されるスループットを減らすために、ページ サイズを 10 に減らすこともできます。

    また、setMaxItemCount メソッドを使ってページ サイズを設定することもできます。

9. **適切なスケジューラを使用する (イベント ループの IO Netty スレッドを盗まない)**

    Async Java SDK は非ブロッキング IO に [netty](https://netty.io/) を使います。 SDK は、固定数の IO netty イベント ループ スレッド (コンピューターの CPU コアと同じ数) を使って IO 操作を実行します。 API によって返される Observable は、共有 IO イベント ループ netty スレッドの 1 つに結果を出力します。 したがって、共有 IO イベント ループ netty スレッドをブロックしないことが重要です。 IO イベント ループ netty スレッドで CPU を大量に使う処理を行ったり、操作をブロックしたりすると、デッドロックが発生したり、SDK のスループットが大幅に低下したりする可能性があります。

    たとえば、次のコードは、イベント ループ IO netty スレッドで CPU を大量に使う処理を実行します。

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    結果を受け取った後、結果に対して CPU 負荷の高い操作を実行する場合は、イベント ループ IO netty スレッドでは行わないようにする必要があります。 代わりに、独自のスケジューラを用意し、処理の実行に専用のスレッドを提供できます。

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    処理の種類に基づいて、処理に適した既存の RxJava Scheduler を使う必要があります。 「[``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)」をご覧ください。

    詳しくは、Async Java SDK に関する [GitHub のページ](https://github.com/Azure/azure-cosmosdb-java)をご覧ください。

10. **netty のログを無効にする** netty ライブラリのログは量が多いので、CPU コストが増えないようにオフにする必要があります (構成へのサインインを抑制するだけでは不十分な場合があります)。 デバッグ モードではない場合は、netty のログを完全に無効にします。 したがって、log4j を使って netty からの ``org.apache.log4j.Category.callAppenders()`` によって発生する追加の CPU コストを削除するには、コードベースに次の行を追加します。

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **OS の開かれるファイルのリソース制限** Red Hat などの一部の Linux システムには、開かれるファイルの数、したがって合計接続数に上限があります。 現在の制限を確認するには、次のコマンドを実行します。

    ```bash
    ulimit -a
    ```

    構成されている接続プール サイズおよび OS によって開かれる他のファイルのために十分なスペースがあるよう、開かれるファイル (nofile) の値は十分な大きさである必要があります。 大きい接続プール サイズに対応できるように変更できます。

    limits.conf ファイルを開きます。

    ```bash
    vim /etc/security/limits.conf
    ```
    
    次の行を追加または変更します。

    ```
    * - nofile 100000
    ```

12. **netty にネイティブ SSL の実装を使用する** netty は SSL 実装スタックに対して直接 OpenSSL を使い、パフォーマンスを向上させることができます。 この構成がない場合、netty は Java の既定の SSL の実装にフォールバックします。

    Ubuntu の場合:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    そして、次の依存関係をプロジェクトの maven の依存関係に追加します。
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

他のプラットフォーム (Red Hat、Windows、Mac など) の場合は、 https://netty.io/wiki/forked-tomcat-native.html の説明をご覧ください。

## <a name="indexing-policy"></a>インデックス作成ポリシー
 
1. **インデックス作成から未使用のパスを除外して書き込みを高速化する**

    Azure Cosmos DB のインデックス作成ポリシーでは、パスのインデックス作成 (setIncludedPaths および setExcludedPaths) を使って、インデックス作成に含める/除外するドキュメント パスを指定できます。 インデックス作成コストはインデックス付きの一意のパスの数に直接関係するため、パスのインデックス作成を使用すると、クエリ パターンが事前にわかっているシナリオで書き込みパフォーマンスが向上し、インデックス ストレージを削減できます。 たとえば、次のコードは、ワイルドカード "*" を使用してドキュメントのセクション全体 (別名 サブツリーとも呼ばれます) をインデックス作成から除外する方法を示しています。

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    詳細については、[Azure Cosmos DB インデックス作成ポリシー](indexing-policies.md)に関するページをご覧ください。

## <a name="throughput"></a>スループット
<a id="measure-rus"></a>

1. **測定と調整によって 1 秒あたりの要求ユニットの使用量を削減する**

    Azure Cosmos DB には、UDF、ストアド プロシージャ、トリガーを使ったリレーショナル クエリや階層クエリなど、さまざまなデータベース操作が用意されています。これらの操作はすべて、データベース コレクション内のドキュメントに対して実行できます。 これらの操作のそれぞれに関連付けられたコストは、操作を完了するために必要な CPU、IO、およびメモリに応じて異なります。 ハードウェア リソースの管理について考える代わりに、各種のデータベース操作を実行しアプリケーション要求を処理するのに必要なリソースに関する単一の測定単位として要求単位 (RU) を考えることができます。

    コンテナーごとに設定された[要求ユニット](request-units.md)の数に基づいて、スループットをプロビジョニングします。 要求単位の消費は、1 秒あたりのレートとして評価されます。 コンテナーのプロビジョニング済み要求ユニット レートを超過したアプリケーションは、レートがそのコンテナーにプロビジョニングされているレベルを下回るまで制限されます。 アプリケーションでより高いスループットが必要になった場合は、追加の要求ユニットをプロビジョニングしてスループットを増やすことができます。

    クエリの複雑さは、操作で消費される要求ユニット数に影響します。 述語の数、述語の特性、UDF 数、ソース データ セットのサイズのすべてがクエリ操作のコストに影響します。

    操作 (作成、更新、または削除) のオーバーヘッドを測定するには、[x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ヘッダーを調べて、これらの操作で使われる要求ユニット数を測定します。 ResourceResponse<T> または FeedResponse<T> で同等の RequestCharge プロパティを確認することもできます。

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    このヘッダーで返される要求の使用量は、プロビジョニングしたスループットの一部です。 たとえば、2000 RU/秒がプロビジョニングされていて、上記のクエリが 1 KB のドキュメントを 1000 個返した場合、この操作のコストは 1000 になります。 そのため、後続の要求をレート制限する前に、サーバーは 1 秒以内にこのような要求を 2 つだけ受け付けます。 詳細については、[要求ユニット](request-units.md)に関する記事および[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)のページを参照してください。
<a id="429"></a>
2. **レート制限と大きすぎる要求レートに対処する**

    クライアントがアカウントの予約済みスループットを超えようとしても、サーバーでパフォーマンスの低下が発生することはなく、予約済みのレベルを超えてスループット容量が使用されることもありません。 サーバーはいち早く RequestRateTooLarge (HTTP 状態コード 429) で要求を終了させ、要求を再試行するまでにユーザーが待機しなければならない時間 (ミリ秒) を示す [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ヘッダーを返します。

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK はすべてこの応答を暗黙的にキャッチし、サーバーが指定した retry-after ヘッダーを優先して要求を再試行します。 アカウントに複数のクライアントが同時アクセスしている状況でなければ、次回の再試行は成功します。

    複数のクライアントが要求レートを常に上回った状態で累積的に動作している場合、現在クライアントによって内部的に 9 に設定されている既定の再試行回数では不十分な可能性があります。この場合、クライアントは状態コード 429 を含む DocumentClientException をアプリケーションにスローします。 既定の再試行回数は、ConnectionPolicy インスタンスで setRetryOptions を使って変更できます。 既定では、要求レートを超えて要求が続行されている場合に、30 秒の累積待機時間を過ぎると、状態コード 429 を含む DocumentClientException が返されます。 これは、現在の再試行回数が最大再試行回数 (既定値の 9 またはユーザー定義の値) より少ない場合でも発生します。

    自動再試行動作により、ほとんどのアプリケーションの回復性とユーザービリティが向上しますが、パフォーマンス ベンチマークの実行時 (特に待機時間の測定時) に問題が生じることがあります。 実験でサーバー スロットルが発生し、クライアント SDK によって警告なしに再試行が行われると、クライアントが監視する待機時間が急増します。 パフォーマンスの実験中に待機時間が急増するのを回避するには、各操作で返される使用量を測定し、予約済みの要求レートを下回った状態で要求が行われていることを確認します。 詳細については、 [要求ユニット](request-units.md)に関する記事を参照してください。
3. **スループットを向上させるためにサイズの小さいドキュメントに合わせて設計する**

    特定の操作の要求の使用量 (要求処理コスト) は、ドキュメントのサイズに直接関係します。 サイズの大きいドキュメントの操作は、サイズの小さいドキュメントの操作よりもコストがかかります。

## <a name="next-steps"></a>次の手順
スケーリングと高パフォーマンスのためのアプリケーションの設計について詳しくは、「[Azure Cosmos DB でのパーティション分割とスケーリング](partition-data.md)」をご覧ください。
