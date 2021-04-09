---
title: Azure Cosmos DB SDK でストアド プロシージャ、トリガー、およびユーザー定義関数を登録および使用する
description: Azure Cosmos DB SDK を使用してストアド プロシージャ、トリガー、およびユーザー定義関数を呼び出す方法について説明します
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 022a45199cfc2d467b1d0d408e86cb5d621070d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339854"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を登録および使用する方法
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB の SQL API は、JavaScript で記述されたストアド プロシージャ、トリガー、およびユーザー定義関数 (UDF) の登録および呼び出しをサポートします。 SQL API [.NET](sql-api-sdk-dotnet.md)、[.NET Core](sql-api-sdk-dotnet-core.md)、[Java](sql-api-sdk-java.md)、[JavaScript](sql-api-sdk-node.md)、[Node.js](sql-api-sdk-node.md)、または [Python](sql-api-sdk-python.md) SDK を使用して、ストアド プロシージャを登録および起動できます。 ストアド プロシージャ、トリガー、およびユーザー定義関数を定義したら、それらを読み込み、データ エクスプローラーを使用して [Azure portal](https://portal.azure.com/) で表示できます。

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>ストアド プロシージャの実行方法

ストアド プロシージャは JavaScript を使用して記述されます。 これらは Azure Cosmos コンテナー内部で作成、更新、読み取り、クエリの実行、および削除できます。 Azure Cosmos DB でストアド プロシージャを記述する方法の詳細については、[Azure Cosmos DB でストアド プロシージャを記述する方法](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)に関する記事を参照してください。

次の例では、Azure Cosmos DB SDK を使用してストアド プロシージャを登録および呼び出す方法を示します。 ソースについては、[ドキュメントを作成する](how-to-write-stored-procedures-triggers-udfs.md#create-an-item)に関する記事を参照してください。このストアド プロシージャは `spCreateToDoItem.js` として保存されています。

> [!NOTE]
> パーティション分割されたコンテナーの場合、ストアド プロシージャを実行するとき、要求オプションにパーティション キー値を指定する必要があります。 ストアド プロシージャは常に 1 つのパーティション キーに範囲設定されます。 別のパーティション キー値を持つ項目は、ストアド プロシージャから認識できません。 このことはトリガーにも該当します。

### <a name="stored-procedures---net-sdk-v2"></a>ストアド プロシージャ - .NET SDK V2

次の例は、.NET SDK V2 を使用してストアド プロシージャを登録する方法を示しています。

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

次のコードは、 .NET SDK V2 を使用してストアド プロシージャを呼び出す方法を示しています。

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, new[] { newItems });
```

### <a name="stored-procedures---net-sdk-v3"></a>ストアド プロシージャ - .NET SDK V3

次の例は、.NET SDK V3 を使用してストアド プロシージャを登録する方法を示しています。

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("myDatabase", "myContainer").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = storedProcedureId,
    Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
});
```

次のコードは、 .NET SDK V3 を使用してストアド プロシージャを呼び出す方法を示しています。

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), new[] { newItems });
```

### <a name="stored-procedures---java-sdk"></a>ストアド プロシージャ - Java SDK

次の例は、Java SDK を使用してストアド プロシージャを登録する方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItems'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItems.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

次のコードは、Java SDK を使用してストアド プロシージャを呼び出す方法を示しています。

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItems");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

List<ToDoItem> ToDoItems = new ArrayList<ToDoItem>();

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

ToDoItems.add(newItem)

newItem.category = "Personal";
newItem.name = "Doctor";
newItem.description = "Make appointment for check up";
newItem.isComplete = false;

ToDoItems.add(newItem)

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { ToDoItems };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>ストアド プロシージャ - JavaScript SDK

次の例は、JavaScript SDK を使用してストアド プロシージャを登録する方法を示しています。

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
await container.scripts.storedProcedures.create({
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
const sprocId = "spCreateToDoItems";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>ストアド プロシージャ - Python SDK

次の例は、Python SDK を使用してストアド プロシージャを登録する方法を示しています。

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/spCreateToDoItems.js') as file:
    file_contents = file.read()

sproc = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
created_sproc = container.scripts.create_stored_procedure(body=sproc) 
```

次のコードは、Python SDK を使用してストアド プロシージャを呼び出す方法を示しています。

```python
import uuid

new_id= str(uuid.uuid4())

# Creating a document for a container with "id" as a partition key.

new_item =   {
      "id": new_id, 
      "category":"Personal",
      "name":"Groceries",
      "description":"Pick up strawberries",
      "isComplete":False
   }
result = container.scripts.execute_stored_procedure(sproc=created_sproc,params=[[new_item]], partition_key=new_id) 
```

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>プリトリガーの実行方法

次の例では、Azure Cosmos DB SDK を使用してプリトリガーを登録および呼び出す方法を示します。 ソースについては、[プリトリガーの例](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers)に関する記事を参照してください。このプリトリガーは `trgPreValidateToDoItemTimestamp.js` として保存されています。

実行時には、プリトリガーは `PreTriggerInclude` を指定した後、List オブジェクト内のトリガーの名前を渡すことによって RequestOptions オブジェクトに渡されます。

> [!NOTE]
> トリガーの名前が List として渡される場合でも、操作ごとにトリガーを 1 つだけ実行することができます。

### <a name="pre-triggers---net-sdk-v2"></a>プリトリガー - .NET SDK V2

次のコードは、.NET SDK V2 を使用してプリトリガーを登録する方法を示しています。

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

次のコードは、.NET SDK V2 を使用してプリトリガーを呼び出す方法を示しています。

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

### <a name="pre-triggers---net-sdk-v3"></a>プリトリガー - .NET SDK V3

次のコードは、.NET SDK V3 を使用してプリトリガーを登録する方法を示しています。

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

次のコードは、.NET SDK V3 を使用してプリトリガーを呼び出す方法を示しています。

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
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
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

次のコードは、Python SDK を使用してプリトリガーを呼び出す方法を示しています。

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'pre_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>ポストトリガーの実行方法

次の例では、Azure Cosmos DB SDK を使用してポストトリガーを登録する方法を示します。 ソースについては、[ポストトリガーの例](how-to-write-stored-procedures-triggers-udfs.md#post-triggers)に関する記事を参照してください。このポストトリガーは `trgPostUpdateMetadata.js` として保存されています。

### <a name="post-triggers---net-sdk-v2"></a>ポストトリガー - .NET SDK V2

次のコードは、.NET SDK V2 を使用してポストトリガーを登録する方法を示しています。

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

次のコードは、.NET SDK V2 を使用してポストトリガーを呼び出す方法を示しています。

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

### <a name="post-triggers---net-sdk-v3"></a>ポストトリガー - .NET SDK V3

次のコードは、.NET SDK V3 を使用してポストトリガーを登録する方法を示しています。

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

次のコードは、.NET SDK V3 を使用してポストトリガーを呼び出す方法を示しています。

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
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
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPostValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPostValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

次のコードは、Python SDK を使用してポストトリガーを呼び出す方法を示しています。

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'post_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>ユーザー定義関数を操作する方法

次の例では、Azure Cosmos DB SDK を使用して、ユーザー定義関数を登録する方法を示します。 ソースについては、[ユーザー定義関数の例](how-to-write-stored-procedures-triggers-udfs.md#udfs)に関する記事を参照してください。このポストトリガーは `udfTax.js` として保存されています。

### <a name="user-defined-functions---net-sdk-v2"></a>ユーザー定義関数 - .NET SDK V2

次のコードは、.NET SDK V2 を使用してユーザー定義関数を登録する方法を示しています。

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

次のコードは、.NET SDK V2 を使用してユーザー定義関数を呼び出す方法を示しています。

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>ユーザー定義関数 - .NET SDK V3

次のコードは、.NET SDK V3 を使用してユーザー定義関数を登録する方法を示しています。

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

次のコードは、.NET SDK V3 を使用してユーザー定義関数を呼び出す方法を示しています。

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
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
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/udfTax.js') as file:
    file_contents = file.read()
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
udf = container.scripts.create_user_defined_function(udf_definition)
```

次のコードは、Python SDK を使用してユーザー定義関数を呼び出す方法を示しています。

```python
results = list(container.query_items(
    'query': 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を記述または作成する方法および概念について説明します。

- [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を操作する](stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB のJavaScript 言語統合クエリ API の操作](javascript-query-api.md)
- [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を記述する方法](how-to-write-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB で Javascript クエリ API を使用してストアド プロシージャおよびトリガーを記述する方法](how-to-write-javascript-query-api.md)
