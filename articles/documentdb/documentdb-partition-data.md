---
title: "Azure DocumentDB でのパーティション分割とスケーリング | Microsoft Docs"
description: "Azure DocumentDB でのパーティション分割の仕組み、パーティション分割とパーティション キーを構成する方法、およびアプリケーションに適したパーティション キーを選択する方法について説明します。"
services: documentdb
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: ed0910e4843ab8ab80dae1c16b15487d92017e24
ms.lasthandoff: 03/17/2017


---
# <a name="partitioning-partition-keys-and-scaling-in-documentdb"></a>DocumentDB でのパーティション分割、パーティション キー、およびスケーリング

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) は、高速で予測可能なパフォーマンスの実現を支援し、アプリケーションの規模の拡大に合わせてシームレスにスケール設定できるように設計されています。 この記事では、DocumentDB でのパーティション分割のしくみの概要と、DocumentDB コレクションを構成してアプリケーションを効率的に拡大/縮小する方法について説明します。

この記事を読むと、次の質問に回答できるようになります。   

* Azure DocumentDB でパーティション分割はどのように機能するか。
* DocumentDB でパーティション分割をどのように構成できるか。
* パーティション キーとは何か、また、アプリケーションに適切なパーティション キーをどのように選択するか。

最初に、[DocumentDB Performance Testing Driver サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark)からプロジェクトをダウンロードしてコードを入手してください。 

パーティション分割とパーティション キーについては、Scott Hanselman と DocumentDB プリンシパル エンジニアリング マネージャー Shireesh Thota による次の Azure Friday ビデオでも取り上げています。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-documentdb"></a>DocumentDB でのパーティション分割
DocumentDB では、ミリ秒の応答時間順に任意の規模でスキーマのない JSON ドキュメントを格納し、クエリ実行できます。 DocumentDB は、 **コレクション**と呼ばれるデータを格納するためのコンテナーを提供します。 コレクションは論理リソースであり、1 つ以上の物理パーティションまたはサーバーにまたがることができます。 パーティション数は、ストレージのサイズやコレクションのプロビジョニング済みスループットに基づいて DocumentDB によって決定されます。 DocumentDB のすべてのパーティションには固定された量の SSD を使用したストレージが関連付けられ、またパーティションは可用性を高めるためにレプリケートされます。 パーティションの管理は Azure DocumentDB で完全に行われるため、複雑なコードを記述してパーティションを管理する必要がありません。 DocumentDB コレクションは、ストレージとスループットに関して **事実上制限がありません** 。 

パーティション分割は、アプリケーションに対して完全に透過的です。 DocumentDB では、すばやい読み取りと書き込み、SQL および LINQ クエリ、JavaScript ベースのトランザクション ロジック、一貫性レベル、単一のコレクション リソースへの REST API 呼び出しを介したきめ細やかなアクセス制御がサポートされます。 サービスでは、複数のパーティション間でのデータの分散、適切なパーティションへのクエリ要求のルーティングが処理されます。 

しくみ DocumentDB でコレクションを作成するときに、**パーティション キー プロパティ**の構成値を指定できます。 これは、DocumentDB が複数のサーバーまたはパーティション間にデータを分散するために使用できるドキュメント内の JSON プロパティ (またはパス) です。 DocumentDB は、パーティション キーの値をハッシュし、ハッシュの結果を使用して、JSON ドキュメントの格納先となるパーティションを特定します。 同じパーティション キーを持つすべてのドキュメントは、同じパーティションに格納されます。 

たとえば、従業員とその部門に関するデータを DocumentDB に格納するアプリケーションがあるとします。 部門別のデータをスケールアウトするために、パーティション キー プロパティとして `"department"` を選択しましょう。 DocumentDB 内のすべてのドキュメントには、パーティション キー値が同じである (`"Marketing`" など) 各ドキュメントに一意の必須 `"id"` プロパティが含まれている必要があります。 コレクションに格納されたすべてのドキュメントには、パーティション キーと ID の一意の組み合わせ (`{ "Department": "Marketing", "id": "0001" }`、`{ "Department": "Marketing", "id": "0002" }`、`{ "Department": "Sales", "id": "0001" }` など) が必要です。 言い換えると、パーティション キーと ID の複合プロパティはコレクションのプライマリ キーとなります。

DocumentDB は、ストレージ サイズとプロビジョニング済みスループットに基づいて、各コレクションに少数の物理パーティションを作成します。 パーティション キーとして定義するプロパティは、論理パーティションです。 複数のパーティション キー値は通常、1 つの物理パーティションを共有しますが、1 つの値がパーティションにまたがることはありません。 1 つのパーティション キーに多数の値がある場合、DocumentDB はデータやプロビジョニング済みスループットの増加に応じて適切な負荷分散を実行できるので便利です。

たとえば、1 秒あたり 25,000 要求のスループットのコレクションを作成し、DocumentDB が 1 つの物理パーティションで 1 秒あたり 10,000 件の要求をサポートできるとします。 DocumentDB では、コレクションに 3 つの物理パーティション (P1、P2、P3) を作成します。 ドキュメントの挿入中または読み取り中に、DocumentDB サービスは対応する `Department` 値をハッシュして、データを 3 つのパーティション (P1、P2、P3) にマップします。 たとえば、"Marketing" と "Sales" を 1 にハッシュすると、これらは両方とも P1 に格納されます。 P1 がいっぱいになると、DocumentDB は P1 を 2 つの新しいパーティション P4 と P5 に分割します。 分割後、サービスは "Marketing" を P4、"Sales" を P5 に移動し、P1 を削除します。 パーティション間でのパーティション キーのこれらの移動は、アプリケーションに対して透過的であり、コレクションの可用性には影響しません。

## <a name="sharding-in-api-for-mongodb"></a>MongoDB 用 API でのシャーディング
MongoDB 用 API のシャード コレクションは、DocumentDB のパーティション分割コレクションと同じインフラストラクチャを使用しています。 パーティション分割コレクションと同じように、シャード コレクションも、任意の数のシャードを持つことができ、各シャードには固定容量の SSD ベースのストレージが関連付けられています。 シャード コレクションは、ストレージとスループットに関して、事実上制限がありません 。 MongoDB 用 API のシャード キーは DocumentDB のパーティション キーに相当します。シャード キーを決定する際は、「[パーティション キー](#partition-keys)」セクションと「[パーティション分割の設計](#designing-for-partitioning)」セクションを必ず参照してください。

<a name="partition-keys"></a>
## <a name="partition-keys"></a>パーティション キー
パーティション キーの選択は、設計時に行う必要のある重要な決定事項の 1 つです。 広範囲の値を持ち、場合によっては複数のパターン間に均等に分散されている可能性のある JSON プロパティ名を選択する必要があります。 

> [!NOTE]
> 多数の個別の値 (少なくとも数百から数千) を持つパーティション キーにすることをお勧めします。 多くの顧客は、DocumentDB を実質的にキー値ストアとして使用しています。ここで、一意の "ID" とは、数百万から数十億もあるパーティション キーのパーティション キーを意味します。
>

パーティション キーの定義とそれぞれに対応する JSON 値の例を次の表に示します。 パーティション キーは JSON のパスとして指定されます。たとえば、`/department` はプロパティ department を表します。 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>パーティション キーのパス</strong></p></td>
            <td valign="top"><p><strong>説明</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>doc.department の JSON 値に対応します。doc はドキュメントです。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>doc.properties.name の JSON 値に対応します。doc はドキュメント (入れ子になったプロパティ) です。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>doc.id の JSON 値に対応します (id  とパーティション キーは同じプロパティです)。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"department name"</p></td>
            <td valign="top"><p>doc["department name"] の JSON 値に対応します。doc はドキュメントです。</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> パーティション キーのパス構文は、インデックス作成ポリシー パスのパス指定に似ています。主な相違点は、値ではなくプロパティに対応するパスであること (つまり、最後にワイルド カードがないこと) です。 たとえば、department の値のインデックスを作成するには「/department/?」を指定しますが、 パーティション キーの定義としては「/department」を指定します。 パーティション キーのパスに対しては、インデックスが暗黙的に作成されます。インデックス作成ポリシーの上書きを使用して、パーティション キーのパスをインデックス作成から除外することはできません。
> 
> 

パーティション キーの選択が、アプリケーションのパフォーマンスにどのように影響するかを見てみましょう。

## <a name="partitioning-and-provisioned-throughput"></a>パーティション分割とプロビジョニング済みスループット
DocumentDB は、予測可能なパフォーマンス向けに設計されています。 コレクションを作成するときに、**1 秒あたりの[要求ユニット](documentdb-request-units.md) (RU)** でスループットを予約します。 各要求には、操作で使用されるシステム リソースの量 (CPU、IO など) に比例する、要求ユニットの負担が割り当てられます。 Session 一貫性での 1 KB のドキュメントの読み取りでは、1 要求ユニットを使用します。 読み取りは、格納された項目の数や、同時に実行する同時要求の数にかかわらず 1 RU になります。 サイズの大きいドキュメントでは、サイズに応じた、さらに多くの要求ユニットが必要になります。 アプリケーションにサポートする必要のあるエンティティのサイズと読み取りの数がわかっている場合は、アプリケーションの読み取りに必要な正確な量のスループットをプロビジョニングできます。 

DocumentDB では、ドキュメントの格納時に、パーティション キーの値に基づいて複数のパーティション間に均等にドキュメントを分散します。 スループットも使用可能なパーティションに均等に分散されます (つまり、パーティションあたりのスループット = (コレクションあたりの総スループット) / (パーティション数))。 

> [!NOTE]
> コレクションのフル スループットを達成するには、個別のパーティション キー値の数間に要求を均等に分散できるパーティション キーを選択する必要があります。
> 
> 

## <a name="single-partition-and-partitioned-collections"></a>単一パーティションとパーティション分割コレクション
DocumentDB では、単一のパーティションとパーティション分割コレクションの両方の作成がサポートされています。 

* **パーティション分割コレクション**は複数のパーティションにまたがることができ、無制限のストレージとスループットをサポートします。 コレクションのパーティション キーを指定する必要があります。 
* **単一パーティション コレクション**には低価格オプションがありますが、最大ストレージ容量と最大スループットの面で制限があります。 これらのコレクションに対して、パーティション キーを指定する必要はありません。 必要とするデータ ストレージと要求がごくわずかである状況を除き、すべてのシナリオで単一パーティション コレクションではなくパーティション分割コレクションを使用することをお勧めします。

![DocumentDB でのパーティション分割コレクション][2] 

パーティション分割コレクションでは、無制限のストレージとスループットがサポートされます。

次の表に、単一パーティション コレクションとパーティション分割コレクションの違いを示します。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>単一パーティション コレクション</strong></p></td>
            <td valign="top"><p><strong>パーティション分割コレクション</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>パーティション キー</p></td>
            <td valign="top"><p>なし</p></td>
            <td valign="top"><p>必須</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ドキュメントのプライマリ キー</p></td>
            <td valign="top"><p>"ID"</p></td>
            <td valign="top"><p>複合キー &lt;パーティション キー&gt; と "ID"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小ストレージ容量</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大ストレージ容量</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>無制限</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小スループット</p></td>
            <td valign="top"><p>400 要求ユニット/秒</p></td>
            <td valign="top"><p>2,500 要求ユニット/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大スループット</p></td>
            <td valign="top"><p>10,000 要求ユニット/秒</p></td>
            <td valign="top"><p>無制限</p></td>
        </tr>
        <tr>
            <td valign="top"><p>API のバージョン</p></td>
            <td valign="top"><p>すべて</p></td>
            <td valign="top"><p>API 2015-12-16 およびそれ以降</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-documentdb-sdks"></a>DocumentDB SDK の操作
Azure DocumentDB に、 [REST API バージョン 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx)による自動パーティション分割のサポートが追加されました。 パーティション分割コレクションを作成するには、サポートされたいずれかの SDK プラットフォーム(.NET、Node.js、Java、Python) で SDK バージョン 1.6.0 以降をダウンロードする必要があります。 

### <a name="creating-partitioned-collections"></a>パーティション分割コレクションの作成
次のサンプルでは、スループット 1 秒あたり 20,000 要求ユニットのデバイスの製品利用統計情報データを格納するコレクションを作成する .NET スニペットを示しています。 SDK により OfferThroughput 値が設定されます (これにより REST API に `x-ms-offer-throughput` 要求ヘッダーが設定されます)。 ここでは、パーティション キーとして `/deviceId` を設定します。 パーティション キーの選択は、残りのコレクション メタデータ (名前、インデックス作成ポリシーなど) と共に保存されます。

このサンプルでは、以下のことがわかっているため `deviceId` を選択しています。(a) デバイスの数が多いため、書き込みをパーティション全体に均等に分散し、膨大な量のデータを取り込むためにデータベースの規模を変更する可能性がある。(b) デバイスの最新の読み取りのフェッチなど、多くの要求は単一の deviceId にスコープが制限され、単一のパーティションから取得される。

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
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

> [!NOTE]
> SDK を使用してパーティション分割コレクションを作成するには、1 秒あたり 10,100 RU 以上のスループット値を指定する必要があります。 分割コレクションのスループット値を 2,500 ～ 10,000 に設定するには、SDK でまだこれらの新しい比較的小さい値を使用できないため、一時的に Azure Portal を使用する必要があります。
> 
> 

このメソッドでは、DocumentDB に対する REST API 呼び出しを行います。サービスにより、要求されたスループットに基づいた数のパーティションがプロビジョニングされます。 パフォーマンス ニーズの変化に応じて、コレクションのスループットを変更できます。 

### <a name="reading-and-writing-documents"></a>ドキュメントの読み取りと書き込み
では、DocumentDB にデータを挿入してみましょう。 デバイスの新しい読み取りをコレクションに挿入するための、デバイスの読み取りデータを含むサンプル クラスと CreateDocumentAsync への呼び出しを次に示します。

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

パーティション キーと ID でドキュメントを読み込んで、更新してから、最後の手順としてパーティション キーと ID でドキュメントを削除してみましょう。 読み取りには (REST API 内の `x-ms-documentdb-partitionkey` 要求ヘッダーに対応する) PartitionKey 値が含まれることにご注意ください。

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

### <a name="querying-partitioned-collections"></a>パーティション分割コレクションのクエリ
パーティション分割コレクション内のデータを照会する際に、DocumentDB により、フィルターに指定したパーティション キー値に対応するパーティションにクエリが自動ルーティングされます。 たとえば、ここでのクエリはパーティション キー "XMS-0001" を含むパーティションのみにルーティングされます。

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

DocumentDB は、SDK 1.12.0 以降の SQL を使用した、パーティション分割コレクションに対する[集計関数 ](documentdb-sql-query.md#Aggregates) `COUNT`、`MIN`、`MAX`、`SUM`、および `AVG` をサポートしています。 クエリには、1 つの集計演算子と、プロジェクション内の 1 つの値を含める必要があります。

### <a name="parallel-query-execution"></a>並列クエリの実行
DocumentDB SDK 1.9.0 以降では、並列クエリ実行オプションがサポートされています。そのため、多数のパーティションにタッチする必要がある場合でも、パーティションのコレクションに対して少ない待ち時間でクエリを実行できます。 たとえば、次のクエリはパーティション全体で並列に実行されるように構成されています。

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
次のパラメーターを調整することで、並列クエリの実行を管理できます。

* `MaxDegreeOfParallelism`を設定すると、並列処理次数、つまりコレクションのパーティションに同時ネットワーク接続できる数の上限を制御することができます。 このパラメーターを -1 に設定した場合、並列処理次数は SDK によって管理されます。 `MaxDegreeOfParallelism` が指定されていないか、0 (既定値) に設定されている場合、コレクションのパーティションへのネットワーク接続は 1 つのみです。
* `MaxBufferedItemCount` を設定すると、クエリの待ち時間とクライアント側のメモリ使用率のバランスを取ることができます。 このパラメーターを省略するか、このパラメーターに -1 を設定した場合、並列クエリの実行中にバッファリングされる項目の数は SDK によって管理されます。

コレクションが同じ状態の場合、並列クエリでは順次実行と同じ順序で結果が返されます。 並べ替え (ORDER BY、TOP、またはその両方) を含むクロスパーティション クエリを実行したときは、DocumentDB SDK からパーティション全体に並列クエリが発行され、部分的に並べ替えられた結果がクライアント側でマージされて、グローバルに並べ替えられた結果が作成されます。

### <a name="executing-stored-procedures"></a>ストアド プロシージャの実行
また、単一のドキュメントでデバイスの集計や最新状態を管理するといった場合に、同じデバイス ID を持つドキュメントに対してアトミック トランザクションを実行することもできます。 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
    
次のセクションでは、単一パーティション コレクションからパーティション分割コレクションへの移動方法について説明します。

## <a name="creating-an-api-for-mongodb-sharded-collection"></a>MongoDB シャード コレクション用 API の作成
MongoDB シャード コレクション用 API を作成する最も簡単な方法は、好みのツール、ドライバー、または SDK を使用することです。 この例では、Mongo シェルを使用してコレクションを作成します。

Mongo シェルで次のコマンドを実行します。

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
結果:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

<a name="migrating-from-single-partition"></a>

## <a name="migrating-from-single-partition-to-partitioned-collections-in-documentdb"></a>DocumentDB での単一パーティション コレクションからパーティション分割コレクションへの移行

> [!IMPORTANT]
> MongoDB 用 API をインポートする場合は、[こちらの手順](documentdb-mongodb-migrate.md)に従ってください。
> 
> 

単一パーティション コレクションを使用するアプリケーションのスループットを増やす必要がある場合 (10,000 RU/秒超)、またはデータ ストレージの容量を増やす必要がある場合 (10 GB 超) は、[DocumentDB データ移行ツール](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)を使用して、単一パーティション コレクションからパーティション分割コレクションにデータを移行できます。 

単一パーティション コレクションからパーティション分割コレクションへの移行方法

1. 単一パーティション コレクションから JSON にデータをエクスポートします。 詳細については、「 [JSON ファイルへのエクスポート](documentdb-import-data.md#export-to-json-file) 」を参照してください。
2. 次の例で示すように、パーティション キーの定義、および 1 秒あたり 2,500 要求ユニットを超えるスループットを指定して作成されたパーティション分割コレクションにデータをインポートします。 詳細については、「 [DocumentDB へのインポート](documentdb-import-data.md#DocumentDBSeqTarget) 」を参照してください。

![DocumentDB のパーティション分割コレクションへのデータの移行][3]  

> [!TIP]
> インポート時間を短縮するには、パーティション分割コレクションの利用可能なスループットを増やすために、[Number of Parallel Requests (並列要求の数)] を 100 以上に引き上げることをご検討ください。 
> 
> 

ここまで、基礎を学んできました。では、DocumentDB 内のパーティション キーを操作する際の重要な設計上の考慮事項をいくつか見てみましょう。

<a name="designing-for-partitioning"></a>
## <a name="designing-for-partitioning"></a>パーティション分割の設計
パーティション キーの選択は、設計時に行う必要のある重要な決定事項の 1 つです。 このセクションでは、コレクションのパーティション キーの選択に関わるトレードオフについていくつか説明します。

### <a name="partition-key-as-the-transaction-boundary"></a>トランザクション境界としてのパーティション キー
パーティション キーを選ぶ際は、トランザクションを使用できるようにすると同時に、ソリューションのスケーラビリティ確保のためにエンティティを複数のパーティション キーに分散させることができるような、バランスの取れた条件にする必要があります。 極端な例を挙げると、すべてのドキュメントに同じパーティション キーを設定することは可能ですが、この場合はソリューションのスケーラビリティが制限される可能性があります。 その一方で、各ドキュメントに対して固有のパーティション キーを割り当てることもできますが、この場合、スケーラビリティは向上するものの、ストアド プロシージャやトリガーを介したクロス ドキュメント トランザクションを使用できなくなる可能性があります。 理想的なパーティション キーとは、効率的なクエリの使用を可能にし、かつソリューションのスケーラビリティを確保するために十分な基数を持つものを指します。 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>ストレージとパフォーマンスのボトルネックの回避
また、書き込みが多くの個々の値全体に分散されるようにするプロパティを選択することが重要です。 同じパーティション キーに対する要求は、単一のパーティションのスループットを超えることはできず、超えた場合は調整されます。 そのため、アプリケーション内の **"ホット スポット"** にならないパーティション キーを選択することが重要です。 また、1 つのパーティション キーのすべてのデータをパーティション内に格納する必要があるため、同じ値のデータが大量にあるパーティション キーは使用しないようにすることをお勧めします。 

### <a name="examples-of-good-partition-keys"></a>適切なパーティション キーの例
次に、アプリケーションのパーティション キー選択のいくつかの例を示します。

* ユーザー プロファイルのバックエンドを実装する場合、ユーザー ID のパーティション キーを選択することをお勧めします。
* IoT データ (デバイス状態など) を格納する場合は、デバイス ID のパーティション キーを使用することをお勧めします。
* 時系列データのログ記録に DocumentDB を使用する場合は、ホスト名やプロセス ID がパーティション キーとして適しています。
* マルチ テナント アーキテクチャがある場合は、テナント ID のパーティション キーをお勧めします。

一部のユース ケース (前述の IoT、ユーザー プロファイルなど) では、パーティション キーはお使いの ID (ドキュメント キー) と同じになる場合があることに注意してください。 その他のケース (時系列データなど) では、パーティション キーは ID と異なる可能性があります。

### <a name="partitioning-and-loggingtime-series-data"></a>パーティション分割とログ記録/時系列データ
DocumentDB の最も一般的なユース ケースの 1 つは、ログ記録と製品利用統計情報です。 膨大な量のデータの読み取り/書き込みが必要になる場合があるため、適切なパーティション キーを選択することが重要です。 選択すべきパーティション キーは、実行する予定のクエリの読み取り速度、書き込み速度、および種類によって決まります。 適切なパーティション キーを選択する方法に関するヒントを次に示します。

* 長期間に少量の書き込みが累積し、タイムスタンプの範囲とその他のフィルターに基づくクエリを必要とするユース ケースでは、タイムスタンプのロールアップを使用する (たとえば、パーティション キーとして日付を使用する) ことをお勧めします。 これにより、1 つのパーティションから 1 つの日付のすべてのデータをクエリできます。 
* 書き込みが多いワークロードは一般的によくあります。このような場合には、DocumentDB が複数のパーティションに書き込みを均等に分散できるように、タイムスタンプに基づかないパーティション キーを使用する必要があります。 この場合は、ホスト名、プロセス ID、アクティビティ ID、または高基数の他のプロパティが適しています。 
* 3 番目の方法は、複数コレクションがあるハイブリッドです。この場合には、日または月ごとに 1 つのコレクションがあり、パーティション キーは、ホスト名のように細分化されたプロパティです。 これには、時間枠内に基づいてさまざまなスループットを設定できるメリットがあります。たとえば、現在の月の場合は読み取りと書き込みに対応するため、高スループットでコレクションをプロビジョニングするのに対して、過去の月の場合は読み取りのみに対応するため、スループットを低くすることができます。

### <a name="partitioning-and-multi-tenancy"></a>パーティション分割とマルチテナント方式
DocumentDB を使用したマルチテナント アプリケーションを実装する場合、主に 2 つのパターンで DocumentDB でテナントを実装します。テナントごとに 1 つのパーティション キーを使用する方法と、テナントごとに 1 つのコレクションを使用する方法です。 それぞれの長所と短所は次のとおりです。

* テナントあたり 1 つのパーティション キー: このモデルでは、テナントは、単一のコレクション内に併置されます。 ただし、1 つのテナント内のドキュメントのクエリや挿入は、1 つのパーティションに対して実行されます。 また、1 つのテナント内のすべてのドキュメント全体にトランザクション ロジックを実装できます。 複数のテナントで 1 つのコレクションが共有されるため、各テナント用に余分なヘッドルームをプロビジョニングするのではなく、単一のコレクション内のテナントのリソースをプールして、ストレージとスループットのコストを削減できます。 欠点は、テナントごとにパフォーマンスを分離できないことです。 パフォーマンス/スループットの増加はコレクション全体に適用されますが、テナントごとに適用されません。
* テナントあたり 1 つのコレクション: 各テナントは独自のコレクションを持ちます。 このモデルでは、テナントごとにパフォーマンスを予約できます。 DocumentDB の新しい消費量ベースの価格モデルを使用することで、このモデルは、テナント数が少ないマルチテナント アプリケーションでより高いコスト効果を得られます。

また、結合型/階層型の手法を使用して、小規模なテナントを併置したり、大規模なテナントを自身のコレクションに移行したりすることもできます。

## <a name="next-steps"></a>次のステップ
この記事では、Azure DocumentDB でのパーティション分割のしくみ、パーティション分割コレクションの作成方法、またアプリケーションに最適なパーティション キーの選択方法を説明しました。 

* DocumentDB のスケールとパフォーマンスのテストを行う。 サンプルについては、「 [Azure DocumentDB のパフォーマンスとスケールのテスト](documentdb-performance-testing.md) 」を参照してください。
* [SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使ってコーディングを開始します。
* [DocumentDB でのプロビジョニング済みスループット](documentdb-performance-levels.md)

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  



