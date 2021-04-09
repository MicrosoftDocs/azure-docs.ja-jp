---
title: Azure Cosmos DB Async Java SDK v2 のパフォーマンスに関するヒント
description: Async Java SDK v2 で Azure Cosmos データベースのパフォーマンスを向上させるためのクライアント構成オプションについて説明します
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: bd009ae4909c8cb016a31323294df3a359eb7c51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033665"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Azure Cosmos DB Async Java SDK v2 のパフォーマンスに関するヒント
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)


> [!IMPORTANT]  
> これは Azure Cosmos DB 用の最新の Java SDK では "*ありません*"。 プロジェクトを [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) にアップグレードしてから、Azure Cosmos DB Java SDK v4 の[パフォーマンス ヒント ガイド](performance-tips-java-sdk-v4-sql.md)をお読みください。 [Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)ガイド、および [Reactor と RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) に関するガイドの手順に従ってアップグレードします。 
> 
> この記事のパフォーマンスに関するヒントは、Azure Cosmos DB Async Java SDK v2 のみを対象としています。 詳細については、Azure Cosmos DB Async Java SDK v2 の[リリース ノート](sql-api-sdk-async-java.md)、[Maven リポジトリ](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)、Azure Cosmos DB Async Java SDK v2 の[トラブルシューティング ガイド](troubleshoot-java-async-sdk.md)を参照してください。
>

Azure Cosmos DB は、高速で柔軟性に優れた分散データベースです。待機時間とスループットが保証されており、シームレスにスケーリングできます。 Azure Cosmos DB でデータベースをスケーリングするために、アーキテクチャを大きく変更したり、複雑なコードを記述したりする必要はありません。 スケールアップとスケールダウンは、API 呼び出しか SDK メソッド呼び出しを 1 回行うだけで簡単に実行できます。 ただし、Azure Cosmos DB にはネットワーク呼び出しによってアクセスするため、[Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) を使用するときに最高のパフォーマンスを実現するために、クライアント側の最適化を行うことができます。

データベースのパフォーマンスを向上させる場合は、 以下のオプションを検討してください。

## <a name="networking"></a>ネットワーク

* **接続モード: 直接モードを使用する**
    
  クライアントが Azure Cosmos DB に接続する方法は、特にクライアント側の待機時間の観点から、パフォーマンスに重要な影響を及ぼします。 *ConnectionMode* は、クライアントの *ConnectionPolicy* を構成するときに利用できる重要な構成設定です。 Azure Cosmos DB Async Java SDK v2 では、次の 2 つの ConnectionMode を使用できます。  
      
  * [Gateway (既定値)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [直接](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  ゲートウェイ モードは、すべての SDK プラットフォームでサポートされており、既定で構成されているオプションです。 ゲートウェイ モードでは標準の HTTPS ポートと単一のエンドポイントを使用するため、ファイアウォールの厳しい制限がある企業ネットワーク内でアプリケーションを実行する場合は、ゲートウェイ モードが最適な選択肢です。   ただし、パフォーマンスのトレードオフとして、ゲートウェイ モードでは、Azure Cosmos DB に対してデータの読み取りまたは書き込みを行うたびに、追加のネットワーク ホップが必要になります。 そのため、ネットワーク ホップ数が少ない直接モードの方がパフォーマンスが向上します。
  
  *ConnectionMode* は、*DocumentClient* インスタンスの作成時に *ConnectionPolicy* パラメーターを使って構成されます。

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **パフォーマンスを確保するために同じ Azure リージョン内にクライアントを併置する**

  可能な場合は、Azure Cosmos DB を呼び出すすべてのアプリケーションを Azure Cosmos データベースと同じリージョンに配置します。 大ざっぱな比較ですが、Azure Cosmos DB の呼び出しは、同じリージョン内であれば 1 ～ 2 ミリ秒以内で完了するのに対し、米国西部と米国東部との間では待ち時間が 50 ミリ秒を超えます。 要求がクライアントから Azure データセンターの境界まで流れるときに使用されるルートに応じて、この待機時間が要求ごとに異なる可能性があります。 最短の待機時間は、プロビジョニングされた Azure Cosmos DB エンドポイントと同じ Azure リージョン内に呼び出し元アプリケーションを配置することによって実現されます。 使用可能なリージョンの一覧については、「 [Azure のリージョン](https://azure.microsoft.com/regions/#services)」を参照してください。

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 接続ポリシーの図" border="false":::

## <a name="sdk-usage"></a>SDK の使用例

* **最新の SDK をインストールする**

  Azure Cosmos DB SDK は、最適なパフォーマンスを提供するために頻繁に改善されています。 Azure Cosmos DB Async Java SDK v2 の[リリース ノート](sql-api-sdk-async-java.md)のページを参照して最新の SDK を確認し、改善点を確認してください。

* **アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する**

  各 AsyncDocumentClient インスタンスはスレッドセーフであり、効率的な接続管理とアドレスのキャッシュが実行されます。 AsyncDocumentClient による効率的な接続管理とパフォーマンスの向上を実現するために、アプリケーションの有効期間中は、AppDomain ごとに AsyncDocumentClient の単一のインスタンスを使用することをお勧めします。

* **ConnectionPolicy のチューニング**

  既定では、Azure Cosmos DB Async Java SDK v2 を使用するときに、直接モードの Cosmos DB 要求は TCP 経由で行われます。 内部的には、SDK で特別な直接モード アーキテクチャを使用してネットワーク リソースを動的に管理し、最良のパフォーマンスを実現します。

  Azure Cosmos DB Async Java SDK v2 では、ほとんどのワークロードでデータベースのパフォーマンスを向上させるための最適な選択肢は直接モードです。 

  * ***直接モードの概要***

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="直接モード アーキテクチャの図" border="false":::
  
  直接モードで使用されるクライアント側のアーキテクチャにより、予測可能なネットワーク使用率と Azure Cosmos DB レプリカへの多重アクセスが可能になります。 上の図は、直接モードで Cosmos DB バックエンドのレプリカにクライアント要求をルーティングする方法を示しています。 直接モード アーキテクチャでは、クライアント側で DB レプリカあたり最大 10 個の **チャネル** が割り当てられます。 チャネルは、要求バッファーが先行する TCP 接続であり、要求の深さは 30 個です。 レプリカに属するチャネルは、レプリカの **サービス エンドポイント** によって、必要に応じて動的に割り当てられます。 ユーザーが直接モードで要求を発行すると、**TransportClient** により、パーティション キーに基づいて要求が適切なサービス エンドポイントにルーティングされます。 **要求キュー** では、サービス エンドポイントの前に要求がバッファリングされます。

  * ***直接モード用の ConnectionPolicy 構成オプション***

    最初の手順として、次の推奨される構成設定を使用します。 この特定のトピックで問題が発生した場合は、[Azure Cosmos DB チーム](mailto:CosmosDBPerformanceSupport@service.microsoft.com)にお問い合わせください。

    Azure Cosmos DB を参照データベースとして使用している (つまり、データベースが多数のポイント読み取り操作に使用され、書き込み操作にはほとんど使用されていない) 場合には、*idleEndpointTimeout* を 0 (タイムアウトなし) に設定してもかまいません。


    | 構成オプション       | Default    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | connectionTimeout          | "PT1M"     |
    | idleChannelTimeout         | "PT0S"     |
    | idleEndpointTimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | "PT1M5S"   |
    | requestExpiryInterval      | "PT5S"     |
    | requestTimeout             | "PT1M"     |
    | requestTimerResolution     | "PT0.5S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***直接モードのプログラミングのヒント***

  SDK の問題を解決するためのベースラインとして、Azure Cosmos DB Async Java SDK v2 の[トラブルシューティング](troubleshoot-java-async-sdk.md)に関する記事を確認してください。
  
  直接モードを使用する場合の重要なプログラミングのヒントを次に示します。
  
  * **効率的な TCP データ転送のためには、アプリケーションでマルチスレッドを使用する** - アプリケーションでは、要求を行った後に、別のスレッドでデータを受信するようにサブスクライブする必要があります。 そうしないと、意図しない "半二重" 操作が強制され、以降の要求は、前の要求の応答を待機してブロックされます。
  
  * **コンピューティング集中型のワークロードは専用スレッドで実行する** - 上述のヒントと同様の理由から、複雑なデータ処理などの操作は、個別のスレッドに配置するのが最適です。 別のデータ ストアからデータを取り込む要求の場合 (たとえば、スレッドで Azure Cosmos DB と Spark のデータ ストアを同時に使用する場合)、待機時間が長くなる可能性があるため、他のデータ ストアからの応答を待機する追加のスレッドを生成することをお勧めします。
  
    * Azure Cosmos DB Async Java SDK v2 の基礎となるネットワーク IO は、Netty によって管理されています。[Netty IO スレッドをブロックするコーディング パターンを回避するためのヒント](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread)を参照してください。
  
  * **データ モデリング** - Azure Cosmos DB SLA では、ドキュメント サイズを 1 KB 未満と想定しています。 ドキュメント サイズが小さくなるようにデータ モデルとプログラミングを最適化することで、通常は待機時間が短縮されます。 1 KB よりも大きいドキュメントを格納することや取得することが必要な場合、推奨される方法は、ドキュメントを Azure Blob Storage のデータにリンクすることです。

* **パーティション分割コレクションに対する並列クエリを調整する**

  Azure Cosmos DB Async Java SDK v2 では、パーティション分割コレクションに対してクエリを並列で実行できるようにするために、並列クエリがサポートされています。 詳細については、SDK の操作に関連した[コード サンプル](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples)を参照してください。 並列クエリは、シリアル クエリよりもクエリの待機時間とスループットを向上させるように設計されています。

  * ***setMaxDegreeOfParallelism を調整する\:***
    
    並列クエリは、複数のパーティションに並列にクエリを実行することによって機能します。 ただし、個々のパーティション分割されたコレクションからのデータは、クエリごとに順番に取得されます。 そのため、setMaxDegreeOfParallelism を使ってパーティションの数を設定すると、その他のすべてのシステムの条件が変わらなければ、クエリのパフォーマンスを最大にできる可能性が最大になります。 パーティションの数が不明な場合は、setMaxDegreeOfParallelism を使って大きな数を設定すると、システムが並列処理の最大限度として最小値 (パーティションの数、ユーザー指定の入力) を選びます。

    並列クエリが最も有効に機能するのは、クエリに対するデータがすべてのパーティションに均等に分散している場合であることに注意する必要があります。 パーティション分割されたコレクションが、クエリによって返されるすべてまたは大部分のデータがわずかな数のパーティション (最悪の場合は 1 つのパーティション) に集中するように分割されている場合、クエリのパフォーマンスに関してこれらのパーティションがボトルネックになるでしょう。

  * ***setMaxBufferedItemCount を調整する\:***
    
    並列クエリは、結果の現在のバッチがクライアントによって処理されている間に結果をプリフェッチするように設計されています。 プリフェッチは、クエリの全体的な遅延の削減に役立ちます。 setMaxBufferedItemCount は、プリフェッチされる結果の数を制限します。 setMaxBufferedItemCount を、返される結果の予期される数 (またはそれ以上の数) に設定すると、クエリに対するプリフェッチの効果が最大になります。

    プリフェッチは MaxDegreeOfParallelism とは無関係に同じように動作し、すべてのパーティションからのデータに対して単一のバッファーが存在します。

* **GetRetryAfterInMilliseconds の間隔でバックオフを実装する**

  パフォーマンス テストでは、調整される要求の割合がわずかになるまで負荷を上げる必要があります。 スロットル状態になった場合は、クライアント アプリケーションでバックオフ値を適用し、サーバー側によって指定された再試行間隔を後退させる必要があります。 バックオフにより、再試行までの待ち時間を最小限に抑えることができます。

* **クライアント ワークロードをスケールアウトする**

  高スループット レベル (1 秒あたりの要求ユニット数が 50,000 超) でテストを行っている場合、コンピューターが CPU 使用率またはネットワーク使用率の上限に達したことでクライアント アプリケーションがボトルネックになることがあります。 この状態に達しても、クライアント アプリケーションを複数のサーバーにスケールアウトすることで引き続き同じ Azure Cosmos DB アカウントで対応できます。

* **名前ベースのアドレス指定を使う**

  `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` という形式の SelfLinks (\_self) ではなく、`dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` というリンク形式の名前ベースのアドレス指定を使って、リンクの作成に使われるすべてのリソースの ResourceId を取得しなくて済むようにします。 また、これらのリソースは (場合によっては同じ名前で) 再作成されるので、これらをキャッシュしても役に立たない場合があります。

* **パフォーマンスを向上させるために、クエリ/読み取りフィードのページ サイズを調整する**

  読み取りフィード機能 (readDocuments など) を使ってドキュメントの一括読み取りを実行するときや、SQL クエリを発行するときに、結果セットが大きすぎる場合、セグメント化された形式で結果が返されます。 既定では、100 項目または 1 MB (先に達した方) のチャンク単位で結果が返されます。

  該当するすべての結果を取得するために必要なネットワーク ラウンド トリップの回数を減らすために、[x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 要求ヘッダーを使って、ページ サイズを最大 1,000 まで増やすことができます。 ごく少数の結果のみを表示する必要がある場合は (ユーザー インターフェイスやアプリケーション API が一度に 10 件しか結果を返さない場合など)、読み取りとクエリに使用されるスループットを減らすために、ページ サイズを 10 に減らすこともできます。

  また、setMaxItemCount メソッドを使ってページ サイズを設定することもできます。

* **適切なスケジューラを使用する (イベント ループの IO Netty スレッドを盗まない)**

  Azure Cosmos DB Async Java SDK v2 では、非ブロッキング IO に [netty](https://netty.io/) を使用します。 SDK は、固定数の IO netty イベント ループ スレッド (コンピューターの CPU コアと同じ数) を使って IO 操作を実行します。 API によって返される Observable は、共有 IO イベント ループ netty スレッドの 1 つに結果を出力します。 したがって、共有 IO イベント ループ netty スレッドをブロックしないことが重要です。 IO イベント ループ netty スレッドで CPU を大量に使う処理を行ったり、操作をブロックしたりすると、デッドロックが発生したり、SDK のスループットが大幅に低下したりする可能性があります。

  たとえば、次のコードは、イベント ループ IO netty スレッドで CPU を大量に使う処理を実行します。

  **Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)**

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

  **Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)**

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

  詳細については、Azure Cosmos DB Async Java SDK v2 に関する [GitHub のページ](https://github.com/Azure/azure-cosmosdb-java)をご覧ください。

* **netty のログを無効にする**

    netty ライブラリのログは量が多いので、CPU コストが増えないようにオフにする必要があります (構成のサインインを抑制するだけでは不十分な場合があります)。 デバッグ モードではない場合は、netty のログを完全に無効にします。 したがって、log4j を使って netty からの ``org.apache.log4j.Category.callAppenders()`` によって発生する追加の CPU コストを削除するには、コードベースに次の行を追加します。

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **OS の開かれるファイルのリソース制限**
 
    Red Hat などの一部の Linux システムには、開かれるファイルの数、したがって合計接続数に上限があります。 現在の制限を確認するには、次のコマンドを実行します。

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

## <a name="indexing-policy"></a>インデックス作成ポリシー
 
* **インデックス作成から未使用のパスを除外して書き込みを高速化する**

    Azure Cosmos DB のインデックス作成ポリシーでは、パスのインデックス作成 (setIncludedPaths および setExcludedPaths) を使って、インデックス作成に含める/除外するドキュメント パスを指定できます。 インデックス作成コストはインデックス付きの一意のパスの数に直接関係するため、パスのインデックス作成を使用すると、クエリ パターンが事前にわかっているシナリオで書き込みパフォーマンスが向上し、インデックス ストレージを削減できます。 たとえば、次のコードは、ワイルドカード "*" を使用して、ドキュメントのセクション全体 (サブツリーとも呼ばれる) をインデックス作成から除外する方法を示しています。

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    詳細については、[Azure Cosmos DB インデックス作成ポリシー](./index-policy.md)に関するページをご覧ください。

## <a name="throughput"></a><a id="measure-rus"></a>スループット

* **測定と調整によって 1 秒あたりの要求ユニットの使用量を削減する**

    Azure Cosmos DB には、UDF、ストアド プロシージャ、トリガーを使ったリレーショナル クエリや階層クエリなど、さまざまなデータベース操作が用意されています。これらの操作はすべて、データベース コレクション内のドキュメントに対して実行できます。 これらの操作のそれぞれに関連付けられたコストは、操作を完了するために必要な CPU、IO、およびメモリに応じて異なります。 ハードウェア リソースの管理について考える代わりに、各種のデータベース操作を実行しアプリケーション要求を処理するのに必要なリソースに関する単一の測定単位として要求単位 (RU) を考えることができます。

    コンテナーごとに設定された[要求ユニット](request-units.md)の数に基づいて、スループットをプロビジョニングします。 要求単位の消費は、1 秒あたりのレートとして評価されます。 コンテナーのプロビジョニング済み要求ユニット レートを超過したアプリケーションは、レートがそのコンテナーにプロビジョニングされているレベルを下回るまで制限されます。 アプリケーションでより高いスループットが必要になった場合は、追加の要求ユニットをプロビジョニングしてスループットを増やすことができます。

    クエリの複雑さは、操作で消費される要求ユニット数に影響します。 述語の数、述語の特性、UDF 数、ソース データ セットのサイズのすべてがクエリ操作のコストに影響します。

    操作 (作成、更新、または削除) のオーバーヘッドを測定するには、[x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ヘッダーを調べて、これらの操作で使われる要求ユニット数を測定します。 ResourceResponse\<T> または FeedResponse\<T> で同等の RequestCharge プロパティを確認することもできます。

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    このヘッダーで返される要求の使用量は、プロビジョニングしたスループットの一部です。 たとえば、2000 RU/秒がプロビジョニングされていて、上記のクエリが 1 KB のドキュメントを 1000 個返した場合、この操作のコストは 1000 になります。 そのため、後続の要求をレート制限する前に、サーバーは 1 秒以内にこのような要求を 2 つだけ受け付けます。 詳細については、[要求ユニット](request-units.md)に関する記事および[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)のページを参照してください。

* **レート制限と大きすぎる要求レートに対処する**

    クライアントがアカウントの予約済みスループットを超えようとしても、サーバーでパフォーマンスの低下が発生することはなく、予約済みのレベルを超えてスループット容量が使用されることもありません。 サーバーはいち早く RequestRateTooLarge (HTTP 状態コード 429) で要求を終了させ、要求を再試行するまでにユーザーが待機しなければならない時間 (ミリ秒) を示す [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ヘッダーを返します。

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    SDK はすべてこの応答を暗黙的にキャッチし、サーバーが指定した retry-after ヘッダーを優先して要求を再試行します。 アカウントに複数のクライアントが同時アクセスしている状況でなければ、次回の再試行は成功します。

    複数のクライアントが要求レートを常に上回った状態で累積的に動作している場合、現在クライアントによって内部的に 9 に設定されている既定の再試行回数では不十分な可能性があります。この場合、クライアントは状態コード 429 を含む DocumentClientException をアプリケーションにスローします。 既定の再試行回数は、ConnectionPolicy インスタンスで setRetryOptions を使って変更できます。 既定では、要求レートを超えて要求が続行されている場合に、30 秒の累積待機時間を過ぎると、状態コード 429 を含む DocumentClientException が返されます。 これは、現在の再試行回数が最大再試行回数 (既定値の 9 またはユーザー定義の値) より少ない場合でも発生します。

    自動再試行動作により、ほとんどのアプリケーションの回復性とユーザービリティが向上しますが、パフォーマンス ベンチマークの実行時 (特に待機時間の測定時) に問題が生じることがあります。 実験でサーバー スロットルが発生し、クライアント SDK によって警告なしに再試行が行われると、クライアントが監視する待機時間が急増します。 パフォーマンスの実験中に待機時間が急増するのを回避するには、各操作で返される使用量を測定し、予約済みの要求レートを下回った状態で要求が行われていることを確認します。 詳細については、 [要求ユニット](request-units.md)に関する記事を参照してください。

* **スループットを向上させるためにサイズの小さいドキュメントに合わせて設計する**

    特定の操作の要求の使用量 (要求処理コスト) は、ドキュメントのサイズに直接関係します。 サイズの大きいドキュメントの操作は、サイズの小さいドキュメントの操作よりもコストがかかります。

## <a name="next-steps"></a>次のステップ

スケーリングと高パフォーマンスのためのアプリケーションの設計について詳しくは、「[Azure Cosmos DB でのパーティション分割とスケーリング](partitioning-overview.md)」をご覧ください。