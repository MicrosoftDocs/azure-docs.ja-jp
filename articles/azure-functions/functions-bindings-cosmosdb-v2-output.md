---
title: Functions 2.x 以降に対する Azure Cosmos DB の出力バインド
description: Azure Functions で Azure Cosmos DB の出力バインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 779b66412319ec8422977a7e56570a4d16f89aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98071546"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>Azure Functions 2.x 以降に対する Azure Cosmos DB の出力バインド

Azure Cosmos DB 出力バインドを使用すると、SQL API を使って Azure Cosmos DB データベースに新しいドキュメントを記述できます。

セットアップと構成の詳細については、[概要](./functions-bindings-cosmosdb-v2.md)に関するページをご覧ください。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

このセクションには、次の例が含まれています。

* [キュー トリガー、1 つのドキュメントの書き込み](#queue-trigger-write-one-doc-c)
* [キュー トリガー、IAsyncCollector を使用したドキュメントの書き込み](#queue-trigger-write-docs-using-iasynccollector-c)

例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>キュー トリガー、1 つのドキュメントの書き込み

次の例は、キュー ストレージからのメッセージで提供されるデータを使用して、ドキュメントをデータベースに追加する[ C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>キュー トリガー、IAsyncCollector を使用したドキュメントの書き込み

次の例は、キュー メッセージ JSON で提供されるデータを使用して、ドキュメントのコレクションをデータベースに追加する[ C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

このセクションには、次の例が含まれています。

* [キュー トリガー、1 つのドキュメントの書き込み](#queue-trigger-write-one-doc-c-script)
* [キュー トリガー、IAsyncCollector を使用したドキュメントの書き込み](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>キュー トリガー、1 つのドキュメントの書き込み

次の例は、*function.json* ファイルの Azure Cosmos DB 出力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、次の形式で JSON を受信するキューのキュー入力バインドを使用します。

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

この関数は、各レコードに対して次の形式の Azure Cosmos DB ドキュメントを作成します。

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>キュー トリガー、IAsyncCollector を使用したドキュメントの書き込み

複数のドキュメントを作成するために `ICollector<T>` または `IAsyncCollector<T>` にバインドできます。`T` は、サポートされている型のいずれかです。

この例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

function.json ファイルを次に示します。

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

* [キュー トリガー、戻り値を使用したメッセージのデータベースへの保存](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP トリガー、戻り値を使用した 1 つのドキュメントのデータベースへの保存](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP トリガー、OutputBinding を使用した 1 つのドキュメントのデータベースへの保存](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP トリガー、OutputBinding を使用した複数のドキュメントのデータベースへの保存](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>キュー トリガー、戻り値を使用したメッセージのデータベースへの保存

次の例は、キュー ストレージのメッセージからのデータを使用して、ドキュメントをデータベースに追加する Java 関数を示しています。

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP トリガー、戻り値を使用した 1 つのドキュメントのデータベースへの保存

次の例は、シグネチャに ```@CosmosDBOutput``` の注釈が付けられ、型 ```String``` の戻り値を持つ Java 関数を示しています。 この関数によって返された JSON ドキュメントは、対応する CosmosDB コレクションに自動的に書き込まれます。

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP トリガー、OutputBinding を使用した 1 つのドキュメントのデータベースへの保存

次の例は、```OutputBinding<T>``` 出力パラメーターを使用して CosmosDB にドキュメントを書き込む Java 関数を示しています。 この例では、```outputItem``` パラメーターに関数シグネチャではなく ```@CosmosDBOutput``` の注釈を付ける必要があります。 ```OutputBinding<T>``` を使用すると、関数で CosmosDB にドキュメントを書き込むためのバインディングを利用しながら、関数の呼び出し元に JSON または XML ドキュメントなどの異なる値を返すこともできるようになります。

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP トリガー、OutputBinding を使用した複数のドキュメントのデータベースへの保存

次の例は、```OutputBinding<T>``` 出力パラメーターを使用して CosmosDB に複数のドキュメントを書き込む Java 関数を示しています。 この例では、```outputItem``` パラメーターには関数シグネチャではなく ```@CosmosDBOutput``` の注釈が付けられています。 出力パラメーター ```outputItem``` には、そのテンプレート パラメーターの型として ```ToDoItem``` オブジェクトの一覧が含まれています。 ```OutputBinding<T>``` を使用すると、関数で CosmosDB にドキュメントを書き込むためのバインディングを利用しながら、関数の呼び出し元に JSON または XML ドキュメントなどの異なる値を返すこともできるようになります。

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Cosmos DB に書き込まれるパラメーター上で `@CosmosDBOutput` 注釈を使用します。  注釈パラメーターの型は ```OutputBinding<T>``` である必要があります。ここで、T は Java のネイティブ型または POJO のどちらかです。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの Azure Cosmos DB 出力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、次の形式で JSON を受信するキューのキュー入力バインドを使用します。

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

この関数は、各レコードに対して次の形式の Azure Cosmos DB ドキュメントを作成します。

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

一括挿入の場合は、オブジェクトを最初に作成してから、stringify 関数を実行します。 JavaScript コードを次に示します。

```javascript
    module.exports = function (context) {
    
        context.bindings.employeeDocument = JSON.stringify([
        {
            "id": "John Henry-123456",
            "name": "John Henry",
            "employeeId": "123456",
            "address": "A town nearby"
        },
        {
            "id": "John Doe-123457",
            "name": "John Doe",
            "employeeId": "123457",
            "address": "A town far away"
        }]);
    
      context.done();
    };
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、出力バインドを使用して Cosmos DB にデータを書き込む方法を示しています。 バインドは、関数の構成ファイル (_functions.json_) で宣言され、キュー メッセージからデータを取得して Cosmos DB ドキュメントに書き込みます。

```json
{ 
  "name": "EmployeeDocument",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "direction": "out" 
} 
```

_run.ps1_ ファイルで、関数から返されるオブジェクトは、データベース内で保持されている `EmployeeDocument` オブジェクトにマップされます。

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name EmployeeDocument -Value @{ 
    id = $QueueItem.name + '-' + $QueueItem.employeeId 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

次の例は、関数の出力としてドキュメントを Azure CosmosDB データベースに書き込む方法を示しています。

バインディング定義は *function.json* で定義され、そこで *type* は `cosmosDB` に設定されます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

データベースに書き込むには、ドキュメント オブジェクトをデータベース パラメーターの `set` メソッドのに渡します。

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、「[出力 - 構成](#configuration)」をご覧ください。 メソッド シグネチャでの `CosmosDB` 属性の例を次に示します。

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`CosmosDBOutput` 注釈は、Cosmos DB にデータを書き込むために使用できます。 関数または個々の関数パラメーターに注釈を適用できます。 関数のメソッドで使用する場合、関数の戻り値は Cosmos DB に書き込まれるものです。 パラメーターで注釈を使用する場合、パラメーターの型は `OutputBinding<T>` として宣言する必要があります。ここで `T` はネイティブ Java 型または POJO です。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `CosmosDB` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**     | 該当なし | `cosmosDB` に設定する必要があります。        |
|**direction**     | 該当なし | `out` に設定する必要があります。         |
|**name**     | 該当なし | 関数のドキュメントを表すバインド パラメーターの名前。  |
|**databaseName** | **DatabaseName**|ドキュメントが作成されたコレクションを含むデータベース。     |
|**collectionName** |**CollectionName**  | ドキュメントが作成されたコレクションの名前。 |
|**createIfNotExists**  |**CreateIfNotExists**    | コレクションが存在しないときに作成するかどうかを示すブール値。 既定値は *false* です。新しいコレクションは予約済みのスループットで作成され、これが価格に影響を及ぼすためです。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。  |
|**partitionKey**|**PartitionKey** |`CreateIfNotExists` が true の場合に、作成されるコレクションのパーティション キー パスを定義します。|
|**collectionThroughput**|**CollectionThroughput**| `CreateIfNotExists` が true の場合に、作成されるコレクションの[スループット](../cosmos-db/set-throughput.md)を定義します。|
|**connectionStringSetting**    |**ConnectionStringSetting** |Azure Cosmos DB 接続文字列を含むアプリ設定の名前。        |
|**preferredLocations**| **PreferredLocations**| (省略可能) Azure Cosmos DB サービスの geo レプリケートされたデータベース アカウントの優先される場所 (リージョン) を定義します。 複数の値はコンマで区切る必要があります。 たとえば、"East US,South Central US,North Europe" などです。 |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| (省略可能) `PreferredLocations` と共に `true` に設定すると、Azure Cosmos DB サービスで[マルチ リージョン書き込み](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions)を利用できます。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

既定では、関数の出力パラメーターに書き込むと、ドキュメントがデータベースに作成されます。 このドキュメントには、自動的に生成された GUID がドキュメント ID として割り当てられます。 出力ドキュメントのドキュメント ID を指定するには、出力パラメーターに渡された JSON オブジェクトで `id` プロパティを指定します。

> [!Note]
> 既存のドキュメントの ID を指定した場合、既存のドキュメントは新しい出力ドキュメントによって上書きされます。

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| Cosmos DB | [CosmosDB のエラー コード](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x でこのバインディングに使用可能なグローバル構成設定について説明します。 バージョン 2.x でのグローバル構成設定の詳細については、[Azure Functions バージョン 2.x の host.json のリファレンス](functions-host-json.md)を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|GatewayMode|Gateway|Azure Cosmos DB サービスに接続する際に関数で使用される接続モード。 オプションは `Direct` と `Gateway` です|
|Protocol|Https|Azure Cosmos DB サービスに接続する際に関数で使用される接続プロトコル。  両方のモードの説明については[こちら](../cosmos-db/performance-tips.md#networking)を参照してください|
|leasePrefix|該当なし|アプリ内のすべての関数で使用するプレフィックスをリースします。|

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB ドキュメントが作成または変更されたときに関数を実行する (トリガー)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB ドキュメントを読み込む (入力バインド)](./functions-bindings-cosmosdb-v2-input.md)
