---
title: Azure Cosmos DB SDK を使用してストアド プロシージャ、トリガー、およびユーザー定義関数を呼び出す方法
description: Azure Cosmos DB SDK を使用してストアド プロシージャ、トリガー、およびユーザー定義関数を呼び出す方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: e1215441b45e1fdba8d74c9149415f0da5c4c44a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360383"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を登録および使用する方法

Azure Cosmos DB の SQL API は、JavaScript で記述されたストアド プロシージャ、トリガー、およびユーザー定義関数 (UDF) の登録および呼び出しをサポートします。 SQL API [.NET](sql-api-sdk-dotnet.md)、[.NET Core](sql-api-sdk-dotnet-core.md)、[Java](sql-api-sdk-java.md)、[JavaScript](sql-api-sdk-node.md)、[Node.js](sql-api-sdk-node.md)、または [Python](sql-api-sdk-python.md) SDK を使用して、ストアド プロシージャを登録および起動できます。 ストアド プロシージャ、トリガー、およびユーザー定義関数を定義したら、それらを読み込み、データ エクスプローラーを使用して [Azure portal](https://portal.azure.com/) で表示できます。

## <a id="stored-procedures"></a>ストアド プロシージャの実行方法

ストアド プロシージャは JavaScript を使用して記述されます。 これらは Azure Cosmos コンテナー内部で作成、更新、読み取り、クエリの実行、および削除できます。 Azure Cosmos DB でストアド プロシージャを記述する方法の詳細については、[Azure Cosmos DB でストアド プロシージャを記述する方法](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)に関する記事を参照してください。

次の例では、Azure Cosmos DB SDK を使用してストアド プロシージャを登録および呼び出す方法を示します。 ソースについては、[ドキュメントを作成する](how-to-write-stored-procedures-triggers-udfs.md#create-an-item)に関する記事を参照してください。このストアド プロシージャは `spCreateToDoItem.js` として保存されています。

> [!NOTE]
> パーティション分割されたコンテナーの場合、ストアド プロシージャを実行するとき、要求オプションにパーティション キー値を指定する必要があります。 ストアド プロシージャは常に 1 つのパーティション キーに範囲設定されます。 別のパーティション キー値を持つ項目は、ストアド プロシージャから認識できません。 このことはトリガーにも該当します。

### <a name="stored-procedures---net-sdk"></a>ストアド プロシージャ - .NET SDK

次の例は、.NET SDK を使用してストアド プロシージャを登録する方法を示しています。

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

次のコードは、 .NET SDK を使用してストアド プロシージャを呼び出す方法を示しています。

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>ストアド プロシージャ - Java SDK

次の例は、Java SDK を使用してストアド プロシージャを登録する方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

次のコードは、Java SDK を使用してストアド プロシージャを呼び出す方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>ストアド プロシージャ - JavaScript SDK

次の例は、JavaScript SDK を使用してストアド プロシージャを登録する方法を示しています。

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

次のコードは、JavaScript SDK を使用してストアド プロシージャを呼び出す方法を示しています。

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>ストアド プロシージャ - Python SDK

次の例は、Python SDK を使用してストアド プロシージャを登録する方法を示しています。

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

次のコードは、Python SDK を使用してストアド プロシージャを呼び出す方法を示しています。

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>プリトリガーの実行方法

次の例では、Azure Cosmos DB SDK を使用してプリトリガーを登録および呼び出す方法を示します。 ソースについては、[プリトリガーの例](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers)に関する記事を参照してください。このプリトリガーは `trgPreValidateToDoItemTimestamp.js` として保存されています。

実行時には、プリトリガーは `PreTriggerInclude` を指定した後、List オブジェクト内のトリガーの名前を渡すことによって RequestOptions オブジェクトに渡されます。

> [!NOTE]
> トリガーの名前が List として渡される場合でも、操作ごとにトリガーを 1 つだけ実行することができます。

### <a name="pre-triggers---net-sdk"></a>プリトリガー - .NET SDK

次のコードは、.NET SDK を使用してプリトリガーを登録する方法を示しています。

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

次のコードは、.NET SDK を使用してプリトリガーを呼び出す方法を示しています。

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>プリトリガー - Java SDK

次のコードは、Java SDK を使用してプリトリガーを登録する方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

次のコードは、Java SDK を使用してプリトリガーを呼び出す方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>プリトリガー - JavaScript SDK

次のコードは、JavaScript SDK を使用してプリトリガーを登録する方法を示しています。

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

次のコードは、JavaScript SDK を使用してプリトリガーを呼び出す方法を示しています。

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>プリトリガー - Python SDK

次のコードは、Python SDK を使用してプリトリガーを登録する方法を示しています。

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

次のコードは、Python SDK を使用してプリトリガーを呼び出す方法を示しています。

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>ポストトリガーの実行方法

次の例では、Azure Cosmos DB SDK を使用してポストトリガーを登録する方法を示します。 ソースについては、[ポストトリガーの例](how-to-write-stored-procedures-triggers-udfs.md#post-triggers)に関する記事を参照してください。このポストトリガーは `trgPostUpdateMetadata.js` として保存されています。

### <a name="post-triggers---net-sdk"></a>ポストトリガー - .NET SDK

次のコードは、.NET SDK を使用してポストトリガーを登録する方法を示しています。

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

次のコードは、.NET SDK を使用してポストトリガーを呼び出す方法を示しています。

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>ポストトリガー - Java SDK

次のコードは、Java SDK を使用してポストトリガーを登録する方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

次のコードは、Java SDK を使用してポストトリガーを呼び出す方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>ポストトリガー - JavaScript SDK

次のコードは、JavaScript SDK を使用してポストトリガーを登録する方法を示しています。

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

次のコードは、JavaScript SDK を使用してポストトリガーを呼び出す方法を示しています。

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>ポストトリガー - Python SDK

次のコードは、Python SDK を使用してポストトリガーを登録する方法を示しています。

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

次のコードは、Python SDK を使用してポストトリガーを呼び出す方法を示しています。

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>ユーザー定義関数を操作する方法

次の例では、Azure Cosmos DB SDK を使用して、ユーザー定義関数を登録する方法を示します。 ソースについては、[ユーザー定義関数の例](how-to-write-stored-procedures-triggers-udfs.md#udfs)に関する記事を参照してください。このポストトリガーは `udfTax.js` として保存されています。

### <a name="user-defined-functions---net-sdk"></a>ユーザー定義関数 - .NET SDK

次のコードは、.NET SDK を使用してユーザー定義関数を登録する方法を示しています。

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

次のコードは、.NET SDK を使用してユーザー定義関数を呼び出す方法を示しています。

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>ユーザー定義関数 - Java SDK

次のコードは、Java SDK を使用してユーザー定義関数を登録する方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

次のコードは、Java SDK を使用してユーザー定義関数を呼び出す方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>ユーザー定義関数 - JavaScript SDK

次のコードは、JavaScript SDK を使用してユーザー定義関数を登録する方法を示しています。

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

次のコードは、JavaScript SDK を使用してユーザー定義関数を呼び出す方法を示しています。

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>ユーザー定義関数 - Python SDK

次のコードは、Python SDK を使用してユーザー定義関数を登録する方法を示しています。

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

次のコードは、Python SDK を使用してユーザー定義関数を呼び出す方法を示しています。

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>次の手順

Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を記述または作成する方法および概念について説明します。

- [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を操作する](stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB のJavaScript 言語統合クエリ API の操作](javascript-query-api.md)
- [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を記述する方法](how-to-write-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB で Javascript クエリ API を使用してストアド プロシージャおよびトリガーを記述する方法](how-to-write-javascript-query-api.md)
