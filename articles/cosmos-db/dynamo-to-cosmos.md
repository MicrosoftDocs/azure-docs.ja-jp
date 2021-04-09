---
title: アプリケーションを Amazon DynamoDB から Azure Cosmos DB に移行する
description: .NET アプリケーションを Amazon DynamoDB から Azure Cosmos DB に移行する方法について説明します
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334227"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>アプリケーションを Amazon DynamoDB から Azure Cosmos DB に移行する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB は、グローバルに分散された、スケーラブルなフル マネージド データベースです。 短い待機時間でのデータへのアクセスが保証されます。 Azure Cosmos DB の詳細については、[概要](introduction.md)に関する記事をご覧ください。 この記事では、最小限のコード変更で .NET アプリケーションを DynamoDB から Azure Cosmos DB に移行する方法について説明します。

## <a name="conceptual-differences"></a>概念上の相違点

Azure Cosmos DB と DynamoDB での主な概念の違いは次のとおりです。

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|適用なし|  データベース |
|テーブル      |  コレクション |
|  Item |  ドキュメント |
|属性|フィールド|
|セカンダリ インデックス|セカンダリ インデックス|
|主キー – パーティション キー|パーティション キー|
|主キー – ソート キー| 任意 |
|ストリーム|ChangeFeed|
|書き込みコンピューティング ユニット|要求ユニット (柔軟で、読み取りにも書き込みにも使用できます)|
|読み取りコンピューティング ユニット    |要求ユニット (柔軟で、読み取りにも書き込みにも使用できます)|
|グローバル テーブル| 不要。 Azure Cosmos アカウントのプロビジョニング中にリージョンを直接選択することができます (リージョンは後で変更できます)|

## <a name="structural-differences"></a>構造的な相違点

Azure Cosmos DB では、DynamoDB の場合と比較して、JSON 構造がより単純です。 違いを以下の例に示します

**DynamoDB**:

次の JSON オブジェクトは、DynamoDB でのデータ形式を表したものです

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

次の JSON オブジェクトは、Azure Cosmos DB でのデータ形式を表したものです

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>データの移行

Azure Cosmos DB へのデータ移行については、さまざまなオプションが用意されています。 詳細については、「[オンプレミスまたはクラウドのデータを Azure Cosmos DB に移行するためのオプション](cosmosdb-migrationchoices.md)」をご覧ください。

## <a name="migrate-your-code"></a>コードの移行

この記事では、アプリケーションのコードを Azure Cosmos DB に移行する方法について説明します。これは、データベース移行の重要な側面です。 学習曲線を減らすために、以下のセクションでは、Amazon DynamoDB と Azure Cosmos DB における同等のコード スニペットを、交互に並べながら比較していきます。

ソース コードをダウンロードするには、次のリポジトリを複製してください。

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>前提条件

- .NET Framework 4.7.2
- Visual Studio 2019
- Azure Cosmos DB SQL API アカウントへのアクセス
- Amazon DynamoDB のローカル インストール
- Java 8
- ダウンロード可能なバージョンの Amazon DynamoDB をポート8000で実行する (コードは変更して構成できます)

### <a name="set-up-your-code"></a>コードを設定する

プロジェクトに次の "NuGet パッケージ" を追加します。

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>接続を確立する

**DynamoDB**:

Amazon DynamoDB では、次のコードを使用して接続します。

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Azure Cosmos DB を接続するには、コードを次のように更新します。

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Azure Cosmos DB で接続を最適化する**

Azure Cosmos DB では、次のオプションを使用して接続を最適化できます。

* **ConnectionMode** - 直接接続モードを使用して、Azure Cosmos DB サービスのデータ ノードに接続します。 ゲートウェイ モードは、論理アドレスを初期化してキャッシュし、更新時に更新する場合にのみ使用します。 詳細については、[接続モード](sql-sdk-connection-modes.md)に関する記事をご覧ください。

* **ApplicationRegion** - このオプションは、Azure Cosmos DB とのやり取りに使用する、優先の geo レプリケート リージョンを設定するために使用します。 詳細については、[グローバル分散](distribute-data-globally.md)に関する記事をご覧ください。

* **ConsistencyLevel** - このオプションは、既定の整合性レベルを上書きするために使用されます。 詳細については、[整合性レベル](consistency-levels.md)に関する記事を参照してください。

* **BulkExecutionMode** - このオプションは、*AllowBulkExecution* プロパティを true に設定して一括操作を実行するために使用されます。 詳細については、[一括インポート](tutorial-sql-api-dotnet-bulk-import.md)に関する記事をご覧ください。

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>コンテナーをプロビジョニングする

**DynamoDB**:

データを Amazon DynamoDB に格納するには、最初にテーブルを作成する必要があります。 このプロセスでは、次のコードに示すように、スキーマ、キーの種類、および属性を定義します。

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

Amazon DynamoDB では、書き込みコンピューティング ユニットと読み取りコンピューティング ユニットをプロビジョニングする必要があります。 一方、Azure Cosmos DB では、スループットを[要求ユニット (RU/秒)](request-units.md) として指定します。これは、任意の操作に対して動的に使用できます。 データは、データベース、コンテナー、項目という順序で整理されます。 スループットは、データベース レベル、コレクション レベル、またはその両方で指定できます。

データベースを作成するには:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

コンテナーを作成するには:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>データを読み込む

**DynamoDB**:

次のコードは、Amazon DynamoDB でデータを読み込む方法を示したものです。 moviesArray は、JSON ドキュメントのリストで構成されます。後で、JSON ドキュメントを反復処理し、Amazon DynamoDB に読み込む必要があります。

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

Azure Cosmos DB では、ストリームを選択し、`moviesContainer.CreateItemStreamAsync()` を使用して書き込むことができます。 ただし、このサンプルでは、型キャスト機能を例示するために、JSON を *MovieModel* 型に逆シリアル化しています。 コードはマルチスレッド化されるのですが、その際、Azure Cosmos DB の分散アーキテクチャが使用され、読み込みが高速化されます。

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>ドキュメントの作成

**DynamoDB**:

Amazon DynamoDB での新規ドキュメントの書き込みは、タイプ セーフではありません。次の例では、ドキュメントの種類として newItem を使用しています。

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB では、データ モデルを通じてタイプ セーフが確保されます。 ここでは、"MovieModel" という名前のデータ モデルを使用しています。

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

Azure Cosmos DB では、newItem が MovieModel になります。

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>ドキュメントを読み取る

**DynamoDB**:

Amazon DynamoDB で読み取りを行うには、次のようにプリミティブを定義する必要があります。

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

一方、Azure Cosmos DB では、クエリが自然です (linq)。

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

上記の例のドキュメント コレクションは、次のようになります。

- タイプ セーフ
- 自然なクエリ オプションが提供されます。

### <a name="update-an-item"></a>項目を更新します

**DynamoDB**:Amazon DynamoDB で項目を更新するには:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

Azure Cosmos DB では、更新は Upsert 操作として扱われます。つまり、ドキュメントが存在しない場合は、ドキュメントが挿入されます。

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>ドキュメントの削除

**DynamoDB**:

Amazon DynamoDB で項目を削除するには、やはりプリミティブを使用する必要があります。

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

Azure Cosmos DB では、ドキュメントを取得し、それらを非同期に削除できます。

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>ドキュメントにクエリを実行する

**DynamoDB**:

Amazon DynamoDB では、データを照会するために API 関数が必要です。

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

Azure Cosmos DB では、シンプルな SQL クエリでプロジェクションとフィルター処理を行うことができます。

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

範囲操作 ("between" など) の場合、Amazon DynamoDB ではスキャンを実行する必要があります。

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

Azure Cosmos DB では、SQL クエリと単一行のステートメントを使って処理できます。

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>コンテナーを削除する

**DynamoDB**:

Amazon DynamoDB でテーブルを削除するには、次のように指定します。

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Azure Cosmos DB でコレクションを削除するには、次のように指定します。

```csharp
await moviesContainer.DeleteContainerAsync();
```
その後、必要であれば、データベースも削除します。

```csharp
await cosmosDatabase.DeleteAsync();
```

ご覧のように、Azure Cosmos DB では自然なクエリ (SQL) がサポートされており、操作が非同期なので非常に簡単です。 複雑なコードも、Azure Cosmos DB に簡単に移行することができ、移行後の取り扱いも簡単になります。

### <a name="next-steps"></a>次の手順

- [パフォーマンスの最適化](performance-tips.md)について学習する。
- [読み取りと書き込みの最適化](key-value-store-cost.md)について学習する
- [Cosmos DB での監視](monitor-cosmos-db.md)について学習する

