---
title: Azure Cosmos DB Java SDK v4 (com.azure.cosmos) を使用するようにアプリケーションを移行する
description: 古い Azure Cosmos DB Java SDK を使用している既存の Java アプリケーションを、Core (SQL) API 用の新しい Java SDK 4.0 (com.azure.cosmos パッケージ) にアップグレードする方法について説明します。
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: 92a9abec36bd75c594c67843286bf8fa067d7dba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658539"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4 を使用するようにアプリケーションを移行する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> この SDK の詳細については、Azure Cosmos DB Java SDK v4 の[リリース ノート](sql-api-sdk-java-v4.md)、[Maven リポジトリ](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、Azure Cosmos DB Java SDK v4 の[パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)、Azure Cosmos DB Java SDK v4 の[トラブルシューティング ガイド](troubleshoot-java-sdk-v4-sql.md)を参照してください。
>

この記事では、古い Azure Cosmos DB Java SDK を使用している既存の Java アプリケーションを、Core (SQL) API 用の新しい Azure Cosmos DB Java SDK 4.0 にアップグレードする方法について説明します。 Azure Cosmos DB Java SDK v4 は、`com.azure.cosmos` パッケージに対応しています。 次のいずれかの Azure Cosmos DB Java SDK からアプリケーションを移行する場合、このドキュメントに記載されている手順を使用できます。 

* Sync Java SDK 2.x.x
* Async Java SDK 2.x.x
* Java SDK 3.x.x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB Java SDK とパッケージのマッピング

次の表では、さまざまな Azure Cosmos DB Java SDK、パッケージ名、リリース情報を示します。

| Java SDK| リリース日 | バンドルされた API   | Maven Jar  | Java パッケージ名  |API リファレンス   | リリース ノート  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2.x.x  | 2018 年 6 月    | Async(RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [リリース ノート](sql-api-sdk-async-java.md) |
| Sync 2.x.x     | 2018 年 9 月    | 同期   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [リリース ノート](sql-api-sdk-java.md)  |
| 3.x.x    | 2019 年 7 月    | Async(Reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | 2020 年 6 月   | Async(Reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>SDK レベルの実装の変更

さまざまな SDK 間での主な実装の相違点を次に示します。

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava が Azure Cosmos DB Java SDK バージョン 3.x.x および 4.0 ではリアクターに置き換えられている

非同期プログラミングまたはリアクティブ プログラミングに慣れていない場合、非同期プログラミングとプロジェクト リアクターの概要については、「[リアクター パターン ガイド](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)」を参照してください。 このガイドは、以前に Azure Cosmos DB Sync Java SDK 2.x.x または Azure Cosmos DB Java SDK 3.x.x Sync API を使用している場合に役立つことがあります。

Azure Cosmos DB Async Java SDK 2.x.x を使用していて、4.0 SDK への移行を計画している場合、リアクターを使用するように RxJava のコードを変換するガイダンスについては、「[リアクターと RxJava の比較ガイド](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)」を参照してください。

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 は、Async API と Sync API の両方に対する直接接続モードを備えている

Azure Cosmos DB Sync Java SDK 2.x.x を使用している場合、Azure Cosmos DB Java SDK 4.0 では、Async API と Sync API の両方について、(HTTP ではなく) TCP に基づく直接接続モードが実装されていることに注意してください。

## <a name="api-level-changes"></a>API レベルの変更

以前の SDK (Java SDK 3.x.x、Async Java SDK 2.x.x、Sync Java SDK 2.x.x) と比較した Azure Cosmos DB Java SDK 4.x.x での API レベルの変更点を次に示します。

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Azure Cosmos DB Java SDK の名前付け規則":::

* Azure Cosmos DB Java SDK 3.x.x と 4.0 では、クライアント リソースが `Cosmos<resourceName>` として参照されています。 たとえば、`CosmosClient`、`CosmosDatabase`、`CosmosContainer` などです。 一方、バージョン 2.x.x の Azure Cosmos DB Java SDK には、一貫した名前付けスキームはありません。

* Azure Cosmos DB Java SDK 3.x.x と 4.0 では、Sync API と Async API の両方が提供されています。

  * **Java SDK 4.0**: クラス名で `Cosmos` の後に `Async` が追加されていない限り、すべてのクラスは Sync API に属します。

  * **Java SDK 3.x.x**: クラス名で `Cosmos` の後に `Async` が追加されていない限り、すべてのクラスは Async API に属します。

  * **Async Java SDK 2.x.x**: クラス名は Sync Java SDK 2.x.x に似ていますが、名前は *Async* で始まります。

### <a name="hierarchical-api-structure"></a>階層型の API 構造

次の 4.0 SDK コード スニペットで示すように、Azure Cosmos DB Java SDK 4.0 と 3.x.x では、クライアント、データベース、コンテナーが入れ子として編成された階層型 API 構造が導入されています。

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

Azure Cosmos DB Java SDK のバージョン 2.x では、リソースとドキュメントに対するすべての操作は、クライアント インスタンスを介して実行されます。

### <a name="representing-documents"></a>ドキュメントの表現

Azure Cosmos DB Java SDK 4.0 では、カスタム POJO と `JsonNodes` が、Azure Cosmos DB のドキュメントを読み書きするための 2 つのオプションです。

Azure Cosmos DB Java SDK 3.x.x では、`CosmosItemProperties` オブジェクトがパブリック API によって公開され、ドキュメント表現として提供されます。 このクラスは、バージョン 4.0 では公開されなくなりました。

### <a name="imports"></a>インポートする

* Azure Cosmos DB Java SDK 4.0 のパッケージは、`com.azure.cosmos` で始まります
* Azure Cosmos DB Java SDK 3.x.x のパッケージは、`com.azure.data.cosmos` で始まります
* Azure Cosmos DB Java SDK 2.x.x Sync API のパッケージは、`com.microsoft.azure.documentdb` で始まります

* Azure Cosmos DB Java SDK 4.0 では、入れ子になったパッケージ `com.azure.cosmos.models` に複数のクラスが配置されます。 これらのパッケージには次のものがあります。

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * 上記のすべてのパッケージに相当する Async API
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` など

### <a name="accessors"></a>アクセサー

Azure Cosmos DB Java SDK 4.0 では、インスタンス メンバーにアクセスするための `get` メソッドと `set` メソッドが公開されています。 たとえば、`CosmosContainer` インスタンスには、`container.getId()` メソッドと `container.setId()` メソッドがあります。

これは、fluent インターフェイスを公開する Azure Cosmos DB Java SDK 3.x.x とは異なります。 たとえば、`CosmosSyncContainer` インスタンスの `container.id()` は、`id` の値を取得または設定するためにオーバーロードされます。

## <a name="code-snippet-comparisons"></a>コード スニペットの比較

### <a name="create-resources"></a>リソースを作成する

次のコード スニペットは、4.0 と 3.x.x の Async API と 2.x.x Sync API のリソース作成方法の違いを示しています。

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync API](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-operations"></a>項目の操作

次のコード スニペットは、4.0 と 3.x.x の Async API と 2.x.x Sync API の項目操作の実行方法の違いを示しています。

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync API](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

### <a name="indexing"></a>インデックス作成

次のコード スニペットは、4.0 と 3.x.x の Async API と 2.x.x Sync API のインデックス作成方法の違いを示しています。

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync API](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="stored-procedures"></a>ストアド プロシージャ

次のコード スニペットは、4.0 と 3.x.x の Async API と 2.x.x Sync API のストアド プロシージャ作成方法の違いを示しています。

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync API](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```
---

### <a name="change-feed"></a>Change Feed

次のコード スニペットでは、4.0 と 3.x.x の Async API での変更フィード操作の実行方法の違いを示します。

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync API](#tab/java-v2-sync)

* この機能は、Java SDK v2 sync ではサポートされていません。 
---

### <a name="container-level-time-to-livettl"></a>コンテナー レベルの Time-To-Live (TTL)

次のコード スニペットは、4.0 と 3.x.x の Async API と 2.x.x Sync API を使用した場合の、コンテナー内のデータに対する Time-To-Live の作成方法の違いを示しています。

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync API](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-level-time-to-livettl"></a>項目レベルの Time-To-Live (TTL)

次のコード スニペットは、4.0 と 3.x.x の Async API と 2.x.x Sync API を使用した場合の項目に対する Time-To-Live の作成方法の違いを示しています。

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 2.x.x Sync API](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

## <a name="next-steps"></a>次のステップ

* V4 SDK を使用して Azure Cosmos DB SQL API データを管理するための [Java アプリを作成する](create-sql-api-java.md)
* [リアクター ベースの Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) について学習する
* 「[リアクターと RxJava の比較ガイド](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)」で、RxJava の非同期コードをリアクターの非同期コードに変換する方法について学習する
