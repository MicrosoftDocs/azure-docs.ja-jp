---
title: "Azure Cosmos DB でのパーティション分割とスケーリング | Microsoft Docs"
description: "Azure Cosmos DB でのパーティション分割のしくみ、パーティション分割とパーティション キーを構成する方法、アプリケーションに適したパーティション キーを選択する方法について説明します。"
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81010d91ac7fe8fa7149c52ed56af304cf4e83d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="partitioning-in-azure-cosmos-db-using-the-documentdb-api"></a>DocumentDB API を使用した Azure Cosmos DB でのパーティション分割

[Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) は、世界規模で分散配置されるマルチモデルのデータベース サービスとして、高速で予測可能なパフォーマンスを実現し、アプリケーションの規模の拡大に合わせてシームレスにスケーリングできるように設計されています。 

この記事は、DocumentDB API で Cosmos DB コンテナー のパーティション分割を使用する方法の概要を示します。 Azure Cosmos DB API を使用したパーティション分割の概念とベスト プラクティスの概要については、[パーティション分割と水平スケーリング](../cosmos-db/partition-data.md)に関する記事をご覧ください。 

最初に、[GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark) からプロジェクトをダウンロードしてコードを入手してください。 

この記事を読むと、次の質問に回答できるようになります。   

* Azure Cosmos DB でパーティション分割はどのように機能するか。
* Cosmos DB でパーティション分割をどのように構成するか。
* パーティション キーとは何か、また、アプリケーションに適切なパーティション キーをどのように選択するか。

最初に、[Azure Cosmos DB Performance Testing Driver サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark)からプロジェクトをダウンロードしてコードを入手してください。 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>パーティション キー

DocumentDB API で、パーティション キー定義を JSON パスの形式で指定します。 パーティション キーの定義とそれぞれに対応する値の例を次の表に示します。 パーティション キーはパスとして指定されます。たとえば、`/department` はプロパティ department を表します。 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>パーティション キー</strong></p></td>
            <td valign="top"><p><strong>説明</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>doc.department の値に対応します。doc はアイテムです。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>doc.properties.name の値に対応します。doc はアイテム (入れ子になったプロパティ) です。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>doc.id の値に対応します (id とパーティション キーは同じプロパティです)。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"department name"</p></td>
            <td valign="top"><p>doc["department name"] の値に対応します。doc はアイテムです。</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> パーティション キーの構文は、インデックス作成ポリシー パスのパス指定に似ています。主な相違点は、値ではなくプロパティに対応するパスであること (つまり、最後にワイルド カードがないこと) です。 たとえば、department の値のインデックスを作成するには「/department/?」を指定しますが、 パーティション キーの定義としては「/department」を指定します。 パーティション キーに対しては、インデックスが暗黙的に作成されます。インデックス作成ポリシーの上書きを使用して、パーティション キーのパスをインデックス作成から除外することはできません。
> 
> 

パーティション キーの選択が、アプリケーションのパフォーマンスにどのように影響するかを見てみましょう。

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Azure Cosmos DB SDK の操作
Azure Cosmos DB に、[REST API バージョン 2015-12-16](/rest/api/documentdb/)による自動パーティション分割のサポートが追加されました。 パーティション分割コンテナーを作成するには、サポートされたいずれかの SDK プラットフォーム(.NET、Node.js、Java、Python、MongoDB) で SDK バージョン 1.6.0 以降をダウンロードする必要があります。 

### <a name="creating-containers"></a>コンテナーの作成
次のサンプルでは、スループット 1 秒あたり 20,000 要求ユニットのデバイスのテレメトリ データを格納するコンテナーを作成する .NET スニペットを示しています。 SDK により OfferThroughput 値が設定されます (これにより REST API に `x-ms-offer-throughput` 要求ヘッダーが設定されます)。 ここでは、パーティション キーとして `/deviceId` を設定します。 パーティション キーの選択は、残りのコンテナー メタデータ (名前、インデックス作成ポリシーなど) と共に保存されます。

このサンプルでは、以下のことがわかっているため `deviceId` を選択しています。(a) デバイスの数が多いため、書き込みをパーティション全体に均等に分散し、膨大な量のデータを取り込むためにデータベースの規模を変更する可能性がある。(b) デバイスの最新の読み取りのフェッチなど、多くの要求は単一の deviceId にスコープが制限され、単一のパーティションから取得される。

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

このメソッドでは、Cosmos DB に対する REST API 呼び出しを行います。サービスにより、要求されたスループットに基づいた数のパーティションがプロビジョニングされます。 パフォーマンス ニーズの変化に応じて、コンテナーのスループットを変更できます。 

### <a name="reading-and-writing-items"></a>アイテムの読み取りと書き込み
では、Cosmos DB にデータを挿入してみましょう。 デバイスの新しい読み取りをコンテナーに挿入するための、デバイスの読み取りデータを含むサンプル クラスと CreateDocumentAsync への呼び出しを次に示します。 これは、DocumentDB API を利用する例です。

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

パーティション キーと ID でアイテムを読み込んで、更新してから、最後の手順としてパーティション キーと ID でドキュメントを削除してみましょう。読み取りには (REST API 内の `x-ms-documentdb-partitionkey` 要求ヘッダーに対応する) PartitionKey 値が含まれることにご注意ください。

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>パーティション分割コンテナーのクエリ
パーティション分割コンテナー内のデータを照会する際に、Cosmos DB により、フィルターに指定したパーティション キー値に対応するパーティションにクエリが自動ルーティングされます。 たとえば、ここでのクエリはパーティション キー "XMS-0001" を含むパーティションのみにルーティングされます。

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
次のクエリにはパーティション キー (DeviceId) にフィルターがなく、パーティションのインデックスに対してこのクエリが実行されるすべてのパーティションにファン アウトされます。 パーティション全体に対して SDK にクエリを実行させるために、EnableCrossPartitionQuery (REST API 内の`x-ms-documentdb-query-enablecrosspartition` ) を指定する必要があることにご注意ください。

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB は、SDK 1.12.0 以降の SQL を使用した、パーティション分割コンテナーに対する[集計関数](documentdb-sql-query.md#Aggregates) `COUNT`、`MIN`、`MAX`、`SUM`、および `AVG` をサポートしています。 クエリには、1 つの集計演算子と、プロジェクション内の 1 つの値を含める必要があります。

### <a name="parallel-query-execution"></a>並列クエリの実行
Cosmos DB SDK 1.9.0 以降では、並列クエリ実行オプションがサポートされています。そのため、多数のパーティションにタッチする必要がある場合でも、パーティションのコレクションに対して少ない待ち時間でクエリを実行できます。 たとえば、次のクエリはパーティション全体で並列に実行されるように構成されています。

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
次のパラメーターを調整することで、並列クエリの実行を管理できます。

* `MaxDegreeOfParallelism` を設定すると、並列処理次数、つまりコンテナーのパーティションに同時ネットワーク接続できる数の上限を制御することができます。 このパラメーターを -1 に設定した場合、並列処理次数は SDK によって管理されます。 `MaxDegreeOfParallelism` が指定されていないか、0 (既定値) に設定されている場合、コンテナーのパーティションへのネットワーク接続は 1 つのみです。
* `MaxBufferedItemCount` を設定すると、クエリの待ち時間とクライアント側のメモリ使用率のバランスを取ることができます。 このパラメーターを省略するか、このパラメーターに -1 を設定した場合、並列クエリの実行中にバッファリングされる項目の数は SDK によって管理されます。

コレクションが同じ状態の場合、並列クエリでは順次実行と同じ順序で結果が返されます。 並べ替え (ORDER BY、TOP、またはその両方) を含むクロスパーティション クエリを実行したときは、Azure Cosmos DB SDK からパーティション全体に並列クエリが発行され、部分的に並べ替えられた結果がクライアント側でマージされて、グローバルに並べ替えられた結果が作成されます。

### <a name="executing-stored-procedures"></a>ストアド プロシージャの実行
また、単一のアイテムでデバイスの集計や最新状態を管理するといった場合に、同じデバイス ID を持つドキュメントに対してアトミック トランザクションを実行することもできます。 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
次のセクションでは、単一パーティション コンテナーからパーティション分割コンテナーへの移動方法について説明します。

## <a name="next-steps"></a>次のステップ
この記事では、DocumentDB API で Azure Cosmos DB コンテナーのパーティション分割を使用する方法の概要について説明しました。 [パーティション分割と水平スケーリング](../cosmos-db/partition-data.md)に関する記事で、Azure Cosmos DB API を使用したパーティション分割の概念とベスト プラクティスの概要についても確認してください。 

* Azure Cosmos DB のスケールとパフォーマンスのテストを行う。 サンプルについては、「[Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)」を参照してください。
* [SDK](documentdb-sdk-dotnet.md) または [REST API](/rest/api/documentdb/) を使ってコーディングを開始します。
* [Azure Cosmos DB におけるスループットのプロビジョニング](request-units.md)について理解します

