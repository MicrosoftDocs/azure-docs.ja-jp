---
title: Azure Cosmos DB Sync Java SDK v2 のパフォーマンスに関するヒント
description: Sync Java SDK v2 で Azure Cosmos データベースのパフォーマンスを向上させるためのクライアント構成オプションについて説明します
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6e7b01ae88645d8b16c3a43e21e40b53d242fdde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549244"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Azure Cosmos DB Sync Java SDK v2 のパフォーマンスに関するヒント
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> これは Azure Cosmos DB 用の最新の Java SDK では "*ありません*"。 プロジェクトを [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) にアップグレードしてから、Azure Cosmos DB Java SDK v4 の[パフォーマンス ヒント ガイド](performance-tips-java-sdk-v4-sql.md)をお読みください。 [Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)に関するガイド、および[リアクターと RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) に関するガイドの手順に従ってアップグレードします。 
> 
> これらのパフォーマンスに関するヒントは、Azure Cosmos DB Sync Java SDK v2 専用です。 詳細については、Azure Cosmos DB Sync Java SDK v2 の[リリース ノート](sql-api-sdk-java.md)および [Maven リポジトリ](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)を参照してください。
>

Azure Cosmos DB は、高速で柔軟性に優れた分散データベースです。待機時間とスループットが保証されており、シームレスにスケーリングできます。 Azure Cosmos DB でデータベースをスケーリングするために、アーキテクチャを大きく変更したり、複雑なコードを記述したりする必要はありません。 スケールアップとスケールダウンは、API 呼び出しを 1 回行うだけの簡単なものです。 詳細については、[コンテナーのスループットをプロビジョニングする方法](how-to-provision-container-throughput.md)または[データベースのスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)に関するページを参照してください。 ただし、Azure Cosmos DB にはネットワーク呼び出しによってアクセスするため、[Azure Cosmos DB Sync Java SDK v2](./sql-api-sdk-java.md) を使うと、最高のパフォーマンスを実現するためにクライアント側の最適化を行うことができます。

データベースのパフォーマンスを向上させる場合は、 以下のオプションを検討してください。

## <a name="networking"></a>ネットワーク
<a id="direct-connection"></a>

1. **接続モード: DirectHttps を使う**

    クライアントが Azure Cosmos DB に接続する方法は、特に監視対象となるクライアント側の待機時間の観点から、パフォーマンスに重要な影響を及ぼします。 クライアントの [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) を構成するときに利用できる重要な構成設定の 1 つが [ConnectionMode](/java/api/com.microsoft.azure.documentdb.connectionmode) です。  次の 2 つの ConnectionMode を使うことができます。

   1. [Gateway (既定値)](/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](/java/api/com.microsoft.azure.documentdb.connectionmode)

      Gateway モードは構成済みの既定のモードであり、すべての SDK プラットフォームでサポートされています。  Gateway モードでは標準の HTTPS ポートと単一のエンドポイントを使用するため、ファイアウォールの厳しい制限がある企業ネットワーク内でアプリケーションを実行する場合は、Gateway が最適な選択肢です。 ただし、パフォーマンスのトレードオフとして、Gateway モードでは、Azure Cosmos DB に対してデータの読み取りまたは書き込みを行うたびに、追加のネットワーク ホップが必要になります。 そのため、ネットワーク ホップ数が少ない DirectHttps モードの方がパフォーマンスが向上します。 

      Azure Cosmos DB Sync Java SDK v2 では、トランスポート プロトコルとして HTTPS を使用します。 HTTPS では、初期の認証とトラフィックの暗号化に TLS を使用します。 Azure Cosmos DB Sync Java SDK v2 を使うときに開く必要があるのは、HTTPS ポート 443 だけです。 

      ConnectionMode は、ConnectionPolicy パラメーターを使って DocumentClient インスタンスの作成時に構成されます。 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Sync Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="Azure Cosmos DB の接続ポリシーを示す図。" border="false":::

   <a id="same-region"></a>
2. **パフォーマンスを確保するために同じ Azure リージョン内にクライアントを併置する**

    可能な場合は、Azure Cosmos DB を呼び出すすべてのアプリケーションを Azure Cosmos データベースと同じリージョンに配置します。 大ざっぱな比較ですが、Azure Cosmos DB の呼び出しは、同じリージョン内であれば 1 ～ 2 ミリ秒以内で完了するのに対し、米国西部と米国東部との間では待ち時間が 50 ミリ秒を超えます。 要求がクライアントから Azure データセンターの境界まで流れるときに使用されるルートに応じて、この待機時間が要求ごとに異なる可能性があります。 最短の待機時間は、プロビジョニングされた Azure Cosmos DB エンドポイントと同じ Azure リージョン内に呼び出し元アプリケーションを配置することによって実現されます。 使用可能なリージョンの一覧については、「 [Azure のリージョン](https://azure.microsoft.com/regions/#services)」を参照してください。

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="2 つのリージョンでの要求と応答を示す図。コンピューターは、中間層サービスを介して Cosmos DB アカウントに接続します。" border="false":::
   
## <a name="sdk-usage"></a>SDK の使用例
1. **最新の SDK をインストールする**

    Azure Cosmos DB SDK は、最適なパフォーマンスを提供するために頻繁に改善されています。 [Azure Cosmos DB SDK](./sql-api-sdk-java.md) のページを参照して、最新の SDK を確認し、改善点を確認してください。
2. **アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する**

    各 [DocumentClient](/java/api/com.microsoft.azure.documentdb.documentclient) インスタンスはスレッド セーフであり、直接モードで動作しているときには効率的な接続管理とアドレスのキャッシュが実行されます。 DocumentClient による効率的な接続管理とパフォーマンスの向上を実現するために、アプリケーションの有効期間中は、AppDomain ごとに DocumentClient の単一のインスタンスを使用することをお勧めします。

   <a id="max-connection"></a>
3. **Gateway モードを使うときはホストごとに MaxPoolSize を増やす**

    Gateway モードを使用するときは Azure Cosmos DB の要求は HTTPS/REST を介して行われ、ホスト名または IP アドレスごとの既定の接続数の制限の対象となります。 場合によっては、Azure Cosmos DB に対する複数の同時接続をクライアント ライブラリが活かすためには、MaxPoolSize を 200 ～ 1000 に増やす必要があります。 Azure Cosmos DB Sync Java SDK v2 での [ConnectionPolicy.getMaxPoolSize](/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) の既定値は 100 です。 値を変更するには [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) を使います。

4. **パーティション分割コレクションに対する並列クエリを調整する**

    Azure Cosmos DB Sync Java SDK バージョン 1.9.0 以降は、パーティション分割コレクションに対して並列にクエリを実行するために使用できる並列クエリをサポートします。 詳細については、SDK の操作に関連した[コード サンプル](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples)を参照してください。 並列クエリは、シリアル クエリよりもクエリの待機時間とスループットを向上させるように設計されています。

    (a) ***setMaxDegreeOfParallelism の調整\:*** 並列クエリは、複数のパーティションに並列にクエリを実行することによって機能します。 ただし、個々のパーティション分割されたコレクションからのデータは、クエリごとに順番に取得されます。 そのため、[setMaxDegreeOfParallelism](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) を使ってパーティションの数を設定すると、その他のすべてのシステムの条件が変わらなければ、クエリのパフォーマンスを最大にできる可能性が最大になります。 パーティションの数が不明な場合は、setMaxDegreeOfParallelism を使って大きな数を設定すると、システムが並列処理の最大限度として最小値 (パーティションの数、ユーザー指定の入力) を選びます。 

    並列クエリが最も有効に機能するのは、クエリに対するデータがすべてのパーティションに均等に分散している場合であることに注意する必要があります。 パーティション分割されたコレクションが、クエリによって返されるすべてまたは大部分のデータがわずかな数のパーティション (最悪の場合は 1 つのパーティション) に集中するように分割されている場合、クエリのパフォーマンスに関してこれらのパーティションがボトルネックになるでしょう。

    (b) ***setMaxBufferedItemCount の調整\:*** 並列クエリは、結果の現在のバッチがクライアントによって処理されている間に結果をプリフェッチするように設計されています。 プリフェッチは、クエリの全体的な遅延の削減に役立ちます。 setMaxBufferedItemCount は、プリフェッチされる結果の数を制限します。 [setMaxBufferedItemCount](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) を、返される結果の予期される数 (またはそれ以上の数) に設定すると、クエリに対するプリフェッチの効果が最大になります。

    プリフェッチは MaxDegreeOfParallelism とは無関係に同じように動作し、すべてのパーティションからのデータに対して単一のバッファーが存在します。  

5. **GetRetryAfterInMilliseconds の間隔でバックオフを実装する**

    パフォーマンス テストでは、調整される要求の割合がわずかになるまで負荷を上げる必要があります。 スロットル状態になった場合、クライアント アプリケーション側でバックオフ値を適用し、サーバー側によって指定された再試行間隔のスロットル時間を後退させるようにしてください。 バックオフにより、再試行までの待ち時間を最小限に抑えることができます。 再試行ポリシーのサポートは、[Azure Cosmos DB Sync Java SDK](./sql-api-sdk-java.md) のバージョン 1.8.0 以降に含まれます。 詳細については、[getRetryAfterInMilliseconds](/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds) を参照してください。

6. **クライアント ワークロードをスケールアウトする**

    高スループット レベル (1 秒あたりの要求ユニット数が 50,000 超) でテストを行っている場合、コンピューターが CPU 使用率またはネットワーク使用率の上限に達したことでクライアント アプリケーションがボトルネックになることがあります。 この状態に達しても、クライアント アプリケーションを複数のサーバーにスケールアウトすることで引き続き同じ Azure Cosmos DB アカウントで対応できます。

7. **名前ベースのアドレス指定を使う**

    `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` という形式の SelfLinks (\_self) ではなく、`dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` というリンク形式の名前ベースのアドレス指定を使って、リンクの作成に使われるすべてのリソースの ResourceId を取得しなくて済むようにします。 また、これらのリソースは (場合によっては同じ名前で) 再作成されるので、これらをキャッシュしても役に立たない場合があります。

   <a id="tune-page-size"></a>
8. **パフォーマンスを向上させるために、クエリ/読み取りフィードのページ サイズを調整する**

    読み取りフィード機能 ([readDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments) など) を使ってドキュメントの一括読み取りを実行するときや、SQL クエリを発行するときに、結果セットが大きすぎる場合、セグメント化された形式で結果が返されます。 既定では、100 項目または 1 MB (先に達した方) のチャンク単位で結果が返されます。

    該当するすべての結果を取得するために必要なネットワーク ラウンド トリップの回数を減らすために、[x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 要求ヘッダーを使って、ページ サイズを最大 1,000 まで増やすことができます。 ごく少数の結果のみを表示する必要がある場合は (ユーザー インターフェイスやアプリケーション API が一度に 10 件しか結果を返さない場合など)、読み取りとクエリに使用されるスループットを減らすために、ページ サイズを 10 に減らすこともできます。

    また、[setPageSize メソッド](/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize)を使ってページ サイズを設定することもできます。

## <a name="indexing-policy"></a>インデックス作成ポリシー
 
1. **インデックス作成から未使用のパスを除外して書き込みを高速化する**

    Azure Cosmos DB のインデックス作成ポリシーでは、パスのインデックス作成 ([setIncludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) および [setExcludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)) を使って、インデックス作成に含める/除外するドキュメント パスを指定できます。 インデックス作成コストはインデックス付きの一意のパスの数に直接関係するため、パスのインデックス作成を使用すると、クエリ パターンが事前にわかっているシナリオで書き込みパフォーマンスが向上し、インデックス ストレージを削減できます。  たとえば、次のコードは、ワイルドカード "*" を使用して、ドキュメントのセクション全体 (サブツリー) をインデックス作成から除外する方法を示しています。


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Sync Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

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

## <a name="throughput"></a>スループット
<a id="measure-rus"></a>

1. **測定と調整によって 1 秒あたりの要求ユニットの使用量を削減する**

    Azure Cosmos DB には、UDF、ストアド プロシージャ、トリガーを使ったリレーショナル クエリや階層クエリなど、さまざまなデータベース操作が用意されています。これらの操作はすべて、データベース コレクション内のドキュメントに対して実行できます。 これらの操作のそれぞれに関連付けられたコストは、操作を完了するために必要な CPU、IO、およびメモリに応じて異なります。 ハードウェア リソースの管理について考える代わりに、各種のデータベース操作を実行しアプリケーション要求を処理するのに必要なリソースに関する単一の測定単位として要求単位 (RU) を考えることができます。

    コンテナーごとに設定された[要求ユニット](request-units.md)の数に基づいて、スループットをプロビジョニングします。 要求単位の消費は、1 秒あたりのレートとして評価されます。 コンテナーのプロビジョニング済み要求ユニット レートを超過したアプリケーションは、レートがそのコンテナーにプロビジョニングされているレベルを下回るまで制限されます。 アプリケーションでより高いスループットが必要になった場合は、追加の要求ユニットをプロビジョニングしてスループットを増やすことができます。 

    クエリの複雑さは、操作で消費される要求ユニット数に影響します。 述語の数、述語の特性、UDF 数、ソース データ セットのサイズのすべてがクエリ操作のコストに影響します。

    操作 (作成、更新、または削除) のオーバーヘッドを測定するには、[x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) ヘッダー (あるいは、[ResourceResponse\<T>](/java/api/com.microsoft.azure.documentdb.resourceresponse) または [FeedResponse\<T>](/java/api/com.microsoft.azure.documentdb.feedresponse) の同等の RequestCharge プロパティ) を調べて、これらの操作で使われる要求ユニット数を測定します。


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Sync Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    このヘッダーで返される要求の使用量は、プロビジョニングしたスループットの一部です。 たとえば、2000 RU/秒がプロビジョニングされていて、上記のクエリが 1 KB のドキュメントを 1000 個返した場合、この操作のコストは 1000 になります。 そのため、後続の要求をレート制限する前に、サーバーは 1 秒以内にこのような要求を 2 つだけ受け付けます。 詳細については、[要求ユニット](request-units.md)に関する記事および[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)のページを参照してください。
   <a id="429"></a>
1. **レート制限と大きすぎる要求レートに対処する**

    クライアントがアカウントの予約済みスループットを超えようとしても、サーバーでパフォーマンスの低下が発生することはなく、予約済みのレベルを超えてスループット容量が使用されることもありません。 サーバーはいち早く RequestRateTooLarge (HTTP 状態コード 429) で要求を終了させ、要求を再試行するまでにユーザーが待機しなければならない時間 (ミリ秒) を示す [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) ヘッダーを返します。
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    SDK はすべてこの応答を暗黙的にキャッチし、サーバーが指定した retry-after ヘッダーを優先して要求を再試行します。 アカウントに複数のクライアントが同時アクセスしている状況でなければ、次回の再試行は成功します。

    複数のクライアントが要求レートを常に上回った状態で累積的に動作している場合、現在クライアントによって内部的に 9 に設定されている既定の再試行回数では不十分な可能性があります。この場合、クライアントは状態コード 429 を含む [DocumentClientException](/java/api/com.microsoft.azure.documentdb.documentclientexception) をアプリケーションにスローします。 既定の再試行回数は、[ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) インスタンスで [setRetryOptions](/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) を使って変更できます。 既定では、要求レートを超えて要求が続行されている場合に、30 秒の累積待機時間を過ぎると、状態コード 429 を含む DocumentClientException が返されます。 これは、現在の再試行回数が最大再試行回数 (既定値の 9 またはユーザー定義の値) より少ない場合でも発生します。

    自動再試行動作により、ほとんどのアプリケーションの回復性とユーザービリティが向上しますが、パフォーマンス ベンチマークの実行時 (特に待機時間の測定時) に問題が生じることがあります。  実験でサーバー スロットルが発生し、クライアント SDK によって警告なしに再試行が行われると、クライアントが監視する待機時間が急増します。 パフォーマンスの実験中に待機時間が急増するのを回避するには、各操作で返される使用量を測定し、予約済みの要求レートを下回った状態で要求が行われていることを確認します。 詳細については、 [要求ユニット](request-units.md)に関する記事を参照してください。
3. **スループットを向上させるためにサイズの小さいドキュメントに合わせて設計する**

    特定の操作の要求の使用量 (要求処理コスト) は、ドキュメントのサイズに直接関係します。 サイズの大きいドキュメントの操作は、サイズの小さいドキュメントの操作よりもコストがかかります。

## <a name="next-steps"></a>次のステップ
スケーリングと高パフォーマンスのためのアプリケーションの設計について詳しくは、「[Azure Cosmos DB でのパーティション分割とスケーリング](partitioning-overview.md)」をご覧ください。