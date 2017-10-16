---
title: "Azure Cosmos DB での Change Feed サポートの使用 | Microsoft Docs"
description: "Azure Cosmos DB の Change Feed サポートを使用して、ドキュメントの変更を追跡し、トリガーなどのイベント ベースの処理を実行したり、キャッシュや分析システムを最新の状態に維持したりします。"
keywords: Change Feed
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.openlocfilehash: 16bd85065f77612ac342ae4a8b500e0c7fa2a078
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Azure Cosmos DB での Change Feed サポートの使用
[Azure Cosmos DB](../cosmos-db/introduction.md) は、グローバルにレプリケートされた柔軟な高速データベース サービスです。大量のトランザクション データや運用データを格納するために使用され、読み取りと書き込みで数ミリ秒 (1 桁) の予測可能な待機時間を実現します。 このため、IoT、ゲーム、小売、操作ログといったアプリケーションに最適です。 これらのアプリケーションの一般的な設計パターンでは、Azure Cosmos DB のデータの変更を追跡し、その変更内容に基づいて、具体化されたビューの更新、リアルタイム分析の実行、コールド ストレージへのデータのアーカイブ、特定のイベントに関する通知のトリガーなどのアクションを実行します。 Azure Cosmos DB の **Change Feed サポート**により、これらの各パターンに対応する効率的でスケーラブルなソリューションを構築できます。

Change Feed サポートを使用すると、Azure Cosmos DB コレクション内のドキュメントの一覧が、ドキュメントの更新日時順に並べ替えられて表示されます。 このフィードを利用すれば、コレクション内のデータに対する変更をリッスンし、以下のようなアクションを実行できます。

* ドキュメントが挿入または変更された場合に API の呼び出しをトリガーする
* 更新内容に対してリアルタイムの (ストリーム) 処理を実行する
* キャッシュ、検索エンジン、データ ウェアハウスとデータを同期する

Azure Cosmos DB での変更は保持され、非同期的に処理できます。また、1 つ以上のコンシューマーに分散させて並列処理することもできます。 Change Feed の API と、これらの API を使用してスケーラブルなリアルタイム アプリケーションを構築する方法を見ていきましょう。 この記事では、Azure Cosmos DB の Change Feed と DocumentDB API を使用する方法について説明します。 

![Azure Cosmos DB の Change Feed を使用してリアルタイム分析とイベント ドリブンのコンピューティング シナリオを強化](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> 現時点では、Change Feed がサポートしているのは DocumentDB API だけです。Graph API と Table API は現在サポートされていません。

## <a name="use-cases-and-scenarios"></a>ユース ケースとシナリオ
Change Feed を使用すると、大量の書き込みが発生する大規模なデータセットを効率的に処理できるので、データセット全体に対してクエリを実行する代わりに、Change Feed を使用して変更を特定できます。 たとえば、次のタスクを効率よく実行できます。

* Azure Cosmos DB に格納されたデータで、キャッシュ、検索インデックス、データ ウェアハウスを更新する。
* アプリケーション レベルのデータの階層化とアーカイブを実装する ("ホット データ" を Azure Cosmos DB に格納し、"コールド データ" を [Azure Blob Storage](../storage/common/storage-introduction.md) または [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) にエイジアウトする)。
* [Apache Hadoop](run-hadoop-with-hdinsight.md) を使用してデータの一括分析を実装する。
* Azure Cosmos DB を使用して [Azure でラムダ パイプライン](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)を実装する。 Azure Cosmos DB は、取り込みとクエリの両方を処理し、低 TCO でラムダ アーキテクチャを実装できる、スケーラブルなデータベース ソリューションを提供します。 
* パーティション スキームが異なる別の Azure Cosmos DB アカウントへの移行をダウンタイムなしに実行する。

**取り込みとクエリに対応する Azure Cosmos DB を使用したラムダ パイプライン:**

![取り込みとクエリに対応する Azure Cosmos DB ベースのラムダ パイプライン](./media/change-feed/lambda.png)

Azure Cosmos DB を使用すると、デバイス、センサー、インフラストラクチャ、アプリケーションからイベント データを受信して格納し、[Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md)、[Apache Storm](../hdinsight/hdinsight-storm-overview.md)、または [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md) を使用して、これらのイベントをリアルタイムで処理できます。 

[サーバーレス](https://azure.microsoft.com/services/app-service/) Web やモバイル アプリ内で、お客様のプロファイル、設定、または場所への変更などのイベントを追跡し、[Azure Functions](http://azure.com/serverless) や [App Services](../azure-functions/functions-bindings-documentdb.md) を使用して各デバイスにプッシュ通知を送信するなど特定のアクションをトリガーできます。 たとえば、Azure Cosmos DB を使用してゲームを構築する場合、Change Feed を使用して完了したゲームのスコアに基づくリアルタイムのスコアボードを実装できます。

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>Azure Cosmos DB での Change Feed のしくみ
Azure Cosmos DB では、Azure Cosmos DB コレクションの更新を増分的に読み取ることができます。 この Change Feed には、次の特長があります。

* 変更は Azure Cosmos DB 内で保持され、非同期的に処理できます。
* コレクション内のドキュメントへの変更は、Change Feed ですぐに利用できます。
* ドキュメントに加えられた変更は Change Feed 内に 1 回だけ出現し、クライアントがそれらのチェックポイント処理ロジックを管理します。 Change Feed プロセッサ ライブラリは、自動チェックポイント処理と "最低 1 回" というセマンティクスを提供します。
* 変更ログには、特定のドキュメントの最新の変更のみが含まれます。 途中の変更は利用できない場合があります。
* Change Feed はパーティション キー値ごとに変更日時順に並べ替えられます。 パーティション キー値が異なる場合、順序は保証されません。
* 変更の同期は任意の時点から行うことが可能です。つまり、変更内容を利用できるデータ保持期間は固定されていません。
* 変更内容は、パーティション キーの範囲として利用できます。 この機能により、大規模コレクションの変更を複数のコンシューマーやサーバーで並行処理できるようになります。
* アプリケーションは、同じコレクションに対して複数の Change Feed を同時に要求できます。

Azure Cosmos DB の Change Feed は、すべてのアカウントで既定で有効になっています。 Azure Cosmos DB の他の操作と同様に、書き込みリージョンや Change Feed から読み取る[読み取りリージョン](distribute-data-globally.md)で[プロビジョニング スループット](request-units.md)を使用できます。 Change Feed には、コレクション内のドキュメントに加えられた挿入操作や更新操作が含まれています。 削除の代わりに、ドキュメント内で「論理的な削除」フラグを設定することで削除をキャプチャできます。 または、[TTL 機能](time-to-live.md)からドキュメントの有限の有効期限 (24 時間など) を設定し、そのプロパティの値を使用して削除をキャプチャできます。 この場合は、TTL の有効期限よりも短い期間に含まれる変更を処理する必要があります。 Change Feed は、ドキュメント コレクション内の各パーティション キー範囲で利用できるため、1 人または複数のコンシューマーの間で分散し並列処理できます。 

![Azure Cosmos DB の Change Feed の分散処理](./media/change-feed/changefeedvisual.png)

クライアント コードで Change Feed を実装する場合、方法がいくつかあります。 以降のセクションでは、Azure Cosmos DB REST API と DocumentDB SDK を使用して Change Feed を実装する方法を説明します。 ただし、.NET アプリケーションの場合、新しい [Change Feed Processor ライブラリ](#change-feed-processor)を使用して、Change Feed のイベントを処理することをお勧めします。このライブラリを使用すると、パーティション全体での変更の読み取りが簡素化され、複数のスレッドが並列で動作することが可能になります。 

## <a id="rest-apis"></a>REST API と DocumentDB SDK の使用
Azure Cosmos DB では、**コレクション**と呼ばれるストレージとスループットの弾力性のあるコンテナーを提供します。 コレクション内のデータはスケーラビリティとパフォーマンスのために、[パーティション キー](partition-data.md)を使用して論理的にグループ化されます。 Azure Cosmos DB には、ID による検索 (Read/Get)、クエリ、読み取りフィード (スキャン) など、このデータにアクセスするためのさまざまな API が用意されています。 Change Feed は、DocumentDB の `ReadDocumentFeed` API に 2 つの新しい要求ヘッダーを設定することで取得でき、複数のパーティション キー範囲で並列処理できます。

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
ReadDocumentFeed のしくみを簡単に説明します。 Azure Cosmos DB は、`ReadDocumentFeed` API を使用して、コレクション内のドキュメントのフィードの読み取りをサポートします。 たとえば、次の要求は、`serverlogs` コレクション内でドキュメントのページを返します。 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

結果は `x-ms-max-item-count` を使用して制限でき、読み取りは前回の応答で返された `x-ms-continuation` ヘッダーで要求を再送信することで再開できます。 単一のクライアントから実行する場合、`ReadDocumentFeed` はパーティション全体の結果を逐次に反復処理します。 

**ドキュメント フィードの逐次読み取り**

また、サポートされている [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) のいずれかを使用してドキュメントのフィードを取得できます。 たとえば、次のスニペットは、.NET で [ReadDocumentFeedAsync メソッド](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet)を使用する方法を示しています。

```csharp
FeedResponse<dynamic> feedResponse = null;
do
{
    feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
}
while (feedResponse.ResponseContinuation != null);
```

### <a name="distributed-execution-of-readdocumentfeed"></a>ReadDocumentFeed の分散実行
数テラバイト以上のデータを含んでいるか、大量の更新を取り込むコレクションでは、1 台のクライアント マシンにフィードの読み取りを直列処理させるのが現実的に難しい場合があります。 これらのビッグ データ シナリオをサポートするために、Azure Cosmos DB では、複数のクライアント リーダー/コンシューマーに `ReadDocumentFeed` 呼び出しを透過的に分散する API を提供します。 

**ドキュメント フィードの分散読み取り**

増分の変更をスケーラブルに処理できるように、Azure Cosmos DB はパーティション キー範囲に基づいた Change Feed API のスケールアウト モデルをサポートします。

* `ReadPartitionKeyRanges` 呼び出しを実行するコレクションのパーティション キー範囲の一覧を取得できます。 
* 各パーティション キー範囲では、`ReadDocumentFeed` を実行して範囲内のパーティション キーでドキュメントを読み取ることができます。

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>コレクションのパーティション キー範囲を取得
コレクション内で `pkranges` リソースを要求することによって、パーティション キー範囲を取得できます。 次のような要求で `serverlogs` コレクションのパーティション キー範囲の一覧を取得できます。

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

この要求では、パーティション キー範囲についてのメタデータを含む次の応答が返されます。

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**パーティション キー範囲のプロパティ**: それぞれのパーティション キー範囲には、次の表のメタデータ プロパティが含まれています。

<table>
    <tr>
        <th>ヘッダー名</th>
        <th>説明</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>パーティション キー範囲の ID です。 これは、各コレクション内で一意の固定 ID です。</p>
            <p>パーティション キー範囲別の変更を読み取るために次の呼び出しで使用する必要があります。</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>パーティション キー範囲のパーティション キー ハッシュ最大値です。 内部使用のみ。</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>パーティション キー範囲のパーティション キー ハッシュ最小値です。 内部使用のみ。</td>
    </tr>       
</table>

サポートされている [ Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) のいずれかを使用してこれを実行できます。 たとえば、次のスニペットは、.NET で [ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync?view=azure-dotnet) メソッドを使用してパーティション キー範囲を取得する方法を示しています。

```csharp
string pkRangesResponseContinuation = null;
List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

do
{
    FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri, 
        new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
}
while (pkRangesResponseContinuation != null);
```

Azure Cosmos DB では、オプションの `x-ms-documentdb-partitionkeyrangeid` ヘッダーを設定することで、パーティション キー範囲ごとにドキュメントを取得できます。 

### <a name="performing-an-incremental-readdocumentfeed"></a>増分 ReadDocumentFeed の実行
ReadDocumentFeed は、Azure Cosmos DB コレクションの変更の増分処理について、次のシナリオ/タスクをサポートします。

* 最初から (つまりコレクション作成時から) のドキュメントへのすべての変更を読み取る。
* 現在時刻から今後のドキュメントの更新に対するすべての変更またはユーザーが指定した時刻以降の変更を読み取る。
* コレクションの論理バージョン (ETag) からのドキュメントへのすべての変更を読み取る。 増分のフィード読み取り要求から返された ETag に基づいてコンシューマーのチェックポイントを作成できます。

変更内容にはドキュメントへの挿入と更新が含まれます。 削除をキャプチャするには、ドキュメント内で「論理的な削除」プロパティを使用するか、[組み込み型 TTL プロパティ](time-to-live.md)を使用し、Change Feed で保留中の削除を通知する必要があります。

次の表に、ReadDocumentFeed 操作の[要求ヘッダー](/rest/api/documentdb/common-documentdb-rest-request-headers.md)と[応答ヘッダー](/rest/api/documentdb/common-documentdb-rest-response-headers.md)を示します。

**増分 ReadDocumentFeed の要求ヘッダー**:

<table>
    <tr>
        <th>ヘッダー名</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>「増分フィード」を設定するか、省略する必要があります</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>ヘッダーなし: 最初 (コレクション作成時) からのすべての変更を返します</p>
            <p>"*": コレクションに含まれるデータへのすべての新しい変更を返します</p>           
            <p>&lt;etag&gt;: ETag がコレクションに設定されている場合、その論理タイムスタンプ以降に行われたすべての変更を返します</p>
        </td>
    </tr>
    <tr>    
        <td>If-Modified-Since</td> 
        <td>RFC 1123 時刻形式。If-None-Match が指定されている場合は無視されます</td> 
    </tr> 
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>読み取りデータのパーティション キー範囲の ID です。</td>
    </tr>
</table>

**増分 ReadDocumentFeed の応答ヘッダー**:

<table> <tr>
        <th>ヘッダー名</th>
        <th>説明</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>応答で返された最後のドキュメントの論理シーケンス番号 (LSN)。</p>
            <p>増分 ReadDocumentFeed は、If-None-Match でこの値を再送信することで再開できます。</p>
        </td>
    </tr>
</table>

これは、論理バージョン/Etag `28535` とパーティション キー範囲 = `16` からコレクション内のすべての増分変更を返す要求のサンプルです。

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

変更は、パーティション キー範囲に含まれる各パーティション キー値内の時間に基づいて並べ替えられます。 パーティション キー値が異なる場合、順序は保証されません。 結果が 1 ページに収まりきらない場合、前回の応答から `etag` に等しい価値の `If-None-Match` ヘッダーで要求を再送信すると、結果の次のページを読み取ることができます。 ストアド プロシージャまたはトリガー内でトランザクションを使用して複数のドキュメントが挿入または更新された場合、それらのドキュメントはすべて同じ応答ページ内で返されます。

> [!NOTE]
> Change Feed を使用すると、ストアド プロシージャまたはトリガー内で複数のドキュメントが挿入または更新された場合に、`x-ms-max-item-count` で指定した数よりも多くの項目が 1 ページで返される場合があります。 

.NET SDK (1.17.0) を使用している場合は、`CreateDocumentChangeFeedQuery` が呼び出されたときに `StartTime` 以降に変更されたドキュメントを直接返すように `ChangeFeedOptions`の `StartTime` フィールドを設定します。 BREST API を使用する `If-Modified-Since` を指定することで、要求は、ドキュメント自体ではなく、応答ヘッダー内に継続トークンまたは `etag` を返します。 指定した時刻に変更されたドキュメントでを返すには、`If-None-Match` を指定した次回の要求で継続トークン `etag` を使用して実際のドキュメントを返す必要があります。 

.NET SDK には、コレクションに加えられた変更にアクセスする、[CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery?view=azure-dotnet) および [ChangeFeedOptions](/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) ヘルパー クラスが用意されています。 次のスニペットは、単一のクライアントから .NET SDK を使用して開始からのすべての変更を取得する方法を示します。

```csharp
private async Task<Dictionary<string, string>> GetChanges(
    DocumentClient client,
    string collection,
    Dictionary<string, string> checkpoints)
{
    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

    foreach (PartitionKeyRange pkRange in partitionKeyRanges)
    {
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);

        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collection,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = 1
            });

        while (query.HasMoreResults)
        {
            FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>();

            foreach (DeviceReading changedDocument in readChangesResponse)
            {
                Console.WriteLine(changedDocument.Id);
            }

            checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
        }
    }

    return checkpoints;
}
```
次のスニペットは、Change Feed サポートと上記の関数を使用して、Azure Cosmos DB で変更をリアルタイムで処理する方法を示します。 最初の呼び出しは、コレクション内のすべてのドキュメントを返し、2 つ目の呼び出しは最後のチェックポイント以降に作成された 2 つのドキュメントのみが返されます。

```csharp
// Returns all documents in the collection.
Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

// Returns only the two documents created above.
checkpoints = await GetChanges(client, collection, checkpoints);
```

クライアント側のロジックを使用して Change Feed にフィルターを適用し、選択的にイベントを処理することもできます。 たとえば、これはクライアント側の LINQ を使用して、デバイス センサーから温度変更イベントのみを処理するスニペットです。

```csharp
FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>;

foreach (DeviceReading changedDocument in 
    readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
{
    // trigger an action, like call an API
}
```

## <a id="change-feed-processor"></a>Change Feed Processor ライブラリ
もう 1 つの方法は、[Azure Cosmos DB Change Feed Processor ライブラリ](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed)を使用することです。このライブラリを使用すると、Change Feed のイベント処理を複数のコンシューマーに簡単に分散させることができます。 このライブラリは、.NET プラットフォームで Change Feed リーダーを作成する場合に最適です。 他の Cosmos DB SDK に含まれているメソッドで Change Feed Processor ライブラリを使用すると、次のワークフローが簡素化されます。 

* データが複数のパーティションに保存されている場合の Change Feed からの更新のプル
* コレクション間でのデータの移動またはレプリケート
* データと Change Feed の更新によってトリガーされるアクションの並列実行 

Cosmos SDK の API を使用すると、各パーティションの Change Feed の更新に正確にアクセスできますが、Change Feed Processor ライブラリを使用すると、パーティション全体での変更の読み取りが簡素化され、複数のスレッドが並列で動作することが可能になります。 各コンテナーから変更を手動で読み取り、パーティションごとに継続トークンを保存するのではなく、Change Feed Processor は、リース メカニズムを使用してパーティション全体での変更の読み取りを自動的に管理します。

このライブラリは、NuGet パッケージ ([Microsoft.Azure.Documents.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)) として提供されており、Github [サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)としてソース コードから入手できます。 

### <a name="understanding-change-feed-processor-library"></a>Change Feed Processor ライブラリについて 

Change Feed Processor の実装には、監視対象コレクション、リース コレクション、プロセッサ ホスト、コンシューマーの 4 つの主要コンポーネントがあります。 

**監視対象コレクション:** 監視対象コレクションは、Change Feed の生成元のデータです。 監視対象コレクションに対する挿入と変更は、コレクションの Change Feed に反映されます。 

**リース コレクション:** リース コレクションは、複数の worker 間で Change Feed の処理を調整します。 個別のコレクションを使用して、リース (パーティションごとに 1 リース) が保存されます。 このリース コレクションは、Change Feed Processor が実行されている場所の近くに書き込みリージョンを持つ別のアカウントに保存すると便利です。 リース オブジェクトには次の属性があります。 
* Owner: リースを所有するホストを指定します。
* Continuation: 特定のパーティションの Change Feed 内の位置 (継続トークン) を指定します。
* Timestamp: リースの最終更新時刻。タイムスタンプを使用して、リースを期限切れと見なすかどうかを確認できます。 

**プロセッサ ホスト:** 各ホストは、アクティブなリースを保持するホストの他のインスタンスの数に基づいて、処理するパーティションの数を決定します。 
1.  すべてのホスト間でワークロードのバランスを取るために、ホストは起動時にリースを取得します。 ホストは定期的にリースを更新するので、リースはアクティブな状態に維持されます。 
2.  ホストは、読み取りを実行するたびにリースに対して前回の継続トークンのチェックポイント処理を実行します。 同時実行の安全性を確保するために、ホストはリースを更新するたびに ETag をチェックします。 他のチェックポイント戦略もサポートされています。  
3.  シャットダウン時に、ホストはすべてのリースを解放します。ただし、継続情報は保持されるので、後で保存済みのチェックポイントから読み取りを再開できます。 

現時点では、ホストの数がパーティション (リース) の数を超えることはできません。

**コンシューマー:** コンシューマー (worker) は、各ホストによって開始された Change Feed の処理を実行するスレッドです。 プロセッサ ホストごとに複数のコンシューマーを使用できます。 各コンシューマーは、割り当てられているパーティションから Change Feed を読み取り、変更と期限切れのリースをホストに通知します。

Change Feed Processor のこの 4 つの要素の連携のしくみについて理解を深めるために、次の図の例を見てみましょう。 監視対象コレクションでは、ドキュメントを保存し、パーティション キーとして "city" を使用します。 青色のパーティションには、"A-E" の "city" フィールドを含むドキュメントが格納されていることがわかります (他のパーティションも同様)。 2 つのホストがあり、各ホストは 4 つのパーティションから並列で読み取る 2 つのコンシューマーを使用しています。 矢印は、コンシューマーが Change Feed の特定の場所から読み取ることを示しています。 最初のパーティションでは、濃い青はまだ読み取られていない変更を表し、薄い青は Change Feed で既に読み取られた変更を表しています。 ホストはリース コレクションを使用して "Continuation" 値を保存することで、各コンシューマーの現在の読み取り位置を追跡します。 

![Azure Cosmos DB Change Feed プロセッサ ホストの使用](./media/change-feed/changefeedprocessornew.png)

### <a name="using-change-feed-processor-library"></a>Change Feed Processor ライブラリの使用 
次のセクションでは、移動元コレクションから移動先コレクションへの変更のレプリケートのコンテキストで Change Feed Processor ライブラリを使用する方法について説明します。 ここでは、移動元コレクションは Change Feed Processor の監視対象コレクションです。 

**Change Feed Processor NuGet パッケージをインストールして含める** 

Change Feed Processor NuGet パッケージをインストールする前に、まず以下をインストールします。 
* Microsoft.Azure.DocumentDB バージョン 1.13.1 以上。 
* Newtonsoft.Json バージョン 9.0.1 以上。`Microsoft.Azure.DocumentDB.ChangeFeedProcessor` をインストールし、参照として含めます。

**監視対象、リース、移動先の各コレクションを作成する** 

Change Feed Processor ライブラリを使用するには、プロセッサ ホストを実行する前に、リース コレクションを作成する必要があります。 ここでも、リース コレクションは、Change Feed Processor が実行されている場所の近くに書き込みリージョンを持つ別のアカウントに保存することをお勧めします。 このデータ移動の例では、Change Feed Processor ホストを実行する前に、移動先コレクションを作成する必要があります。 サンプル コードでは、ヘルパー メソッドを呼び出して、監視対象、リース、移動先の各コレクションを作成しています (これらのコレクションがまだ存在しない場合)。 

> [!WARNING]
> コレクションの作成は料金に影響します。これは、Azure Cosmos DB と通信するためにアプリケーションのスループットを予約するためです。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)をご覧ください。
> 
> 

*プロセッサ ホストの作成*

`ChangeFeedProcessorHost` クラスはイベント プロセッサ実装のためにスレッドセーフでマルチプロセスの安全なランタイム環境を提供します。さらに、その環境では、チェックポイント処理とパーティション リースの管理が提供されます。 `ChangeFeedProcessorHost` クラスを使用するには、`IChangeFeedObserver` を実装します。 このインターフェイスには 3 つのメソッドが含まれています。

* `OpenAsync`: この関数は、Change Feed のオブザーバーを開いたときに呼び出されます。 コンシューマー/オブザーバーを開いたときに特定のアクションを実行するように関数を変更できます。  
* `CloseAsync`: この関数は、Change Feed のオブザーバーを終了したときに呼び出されます。 コンシューマー/オブザーバーを終了したときに特定のアクションを実行するように関数を変更できます。  
* `ProcessChangesAsync`: この関数は、Change Feed でドキュメントの新しい変更が使用可能になったときに呼び出されます。 Change Feed が更新されるたびに特定のアクションを実行するように関数を変更できます。  

例では、`DocumentFeedObserver` クラスを使用して `IChangeFeedObserver` インターフェイスを実装しています。 ここでは、`ProcessChangesAsync` 関数によって、Change Feed から移動先コレクションにドキュメントが Upsert (更新) されます。 この例は、データ セットのパーティション キーを変更するために、コレクション間でデータを移動する際に役立ちます。 

*プロセッサー ホストの実行*

イベント処理を開始する前に、Change Feed オプションと Change Feed のホスト オプションをカスタマイズできます。 
```csharp
    // Customizable change feed option and host options 
    ChangeFeedOptions feedOptions = new ChangeFeedOptions();

    // ie customize StartFromBeginning so change feed reads from beginning
    // can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
    feedOptions.StartFromBeginning = true;

    ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();

    // ie. customizing lease renewal interval to 15 seconds
    // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
    feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

```
カスタマイズ可能なフィールドを次の表に示します。 

**Change Feed オプション**:
<table>
    <tr>
        <th>プロパティ名</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>MaxItemCount</td>
        <td>Azure Cosmos DB データベース サービスでの列挙操作で返される項目の最大数を取得または設定します。</td>
    </tr>
    <tr>
        <td>PartitionKeyRangeId</td>
        <td>Azure Cosmos DB データベース サービスでの現在の要求のパーティション キー範囲の ID を取得または設定します。</td>
    </tr>
    <tr>
        <td>RequestContinuation</td>
        <td>Azure Cosmos DB データベース サービスの要求の継続トークンを取得または設定します。</td>
    </tr>
        <tr>
        <td>SessionToken</td>
        <td>Azure Cosmos DB データベース サービスのセッションの整合性で使用するセッション トークンを取得または設定します。</td>
    </tr>
        <tr>
        <td>StartFromBeginning</td>
        <td>Azure Cosmos DB データベース サービスの Change Feed が先頭から開始されるか (true)、現在の位置から開始されるか (false) を取得または設定します。 既定では、現在の位置から開始されます (false)。</td>
    </tr>
</table>

**Change Feed のホスト オプション**:
<table>
    <tr>
        <th>プロパティ名</th>
        <th>型</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>LeaseRenewInterval</td>
        <td>timespan</td>
        <td>ChangeFeedEventHost インスタンスによって現在保持されているパーティションのすべてのリースの間隔。</td>
    </tr>
    <tr>
        <td>LeaseAcquireInterval</td>
        <td>timespan</td>
        <td>パーティションが既知のホスト インスタンス間で均等に分散されているかどうかを計算するタスクを開始する間隔。</td>
    </tr>
    <tr>
        <td>LeaseExpirationInterval</td>
        <td>timespan</td>
        <td>パーティションを表すリースでリースを取得する間隔。 この期間内にリースが更新されていない場合、リースは期限切れとなり、パーティションの所有権が別の ChangeFeedEventHost インスタンスに移動します。</td>
    </tr>
    <tr>
        <td>FeedPollDelay</td>
        <td>timespan</td>
        <td>現在の変更がすべて排除された後、パーティションでフィードの新しい変更をポーリングするまでの遅延。</td>
    </tr>
    <tr>
        <td>CheckpointFrequency</td>
        <td>CheckpointFrequency</td>
        <td>リースをチェックポイント処理する頻度。</td>
    </tr>
    <tr>
        <td>MinPartitionCount</td>
        <td>int</td>
        <td>ホストのパーティションの最小数。</td>
    </tr>
    <tr>
        <td>MaxPartitionCount</td>
        <td>int</td>
        <td>ホストが対応できるパーティションの最大数。</td>
    </tr>
    <tr>
        <td>DiscardExistingLeases</td>
        <td>ブール値</td>
        <td>ホストの起動時に、既存のすべてのリースを削除し、ホストが最初から開始するかどうか。</td>
    </tr>
</table>


イベント処理を開始するには、`ChangeFeedProcessorHost` をインスタンス化して、Azure Cosmos DB コレクションの適切なパラメーターを提供します。 次に、`RegisterObserverAsync` を呼び出して、`IChangeFeedObserver` 実装 (この例では DocumentFeedObserver) をランタイムに登録します。 この時点で、ホストは "貪欲" アルゴリズムを使用して Azure Cosmos DB コレクションのすべてのパーティション キー範囲でリースの取得を試みます。 これらのリースは一定の期間存続しますが、その後、更新する必要があります。 新しいノード (この例では worker インスタンス) がオンラインになると、それらのノードがリースを予約します。時間と共に各ホストが取得しようとするリースが増加し、それに伴って負荷がノード間を移動します。 

サンプル コードでは、ファクトリ クラス (DocumentFeedObserverFactory.cs) を使用して、オブザーバーとそのオブザーバーを登録するための `RegistObserverFactoryAsync` を作成しています。 

```csharp
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
    {
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);

        await host.RegisterObserverFactoryAsync(docObserverFactory);

        Console.WriteLine("Running... Press enter to stop.");
        Console.ReadLine();

        await host.UnregisterObserversAsync();
    }
```
時間と共に、均衡が確立されます。 この動的機能により、スケールアップとスケールダウンの両方で、CPU ベースの自動スケールがコンシューマーに適用されます。 コンシューマーが処理できる速度より速く Azure Cosmos DB で変更を利用できれば、コンシューマーの CPU 増加を利用して、worker インスタンス数の自動スケールを実行できます。

## <a name="next-steps"></a>次のステップ
* [GitHub の Azure Cosmos DB Change Feed コード サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)を使ってみる
* [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) または [REST API](/rest/api/documentdb/) を使用してコーディングを開始する
