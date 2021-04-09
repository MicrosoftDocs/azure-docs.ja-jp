---
title: Functions 2.x 以降に対する Azure Cosmos DB の入力バインド
description: Azure Functions で Azure Cosmos DB の入力バインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 21ca30b24c4824a2d303d02f3df712328885e199
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435207"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Azure Functions 2.x 以降に対する Azure Cosmos DB の入力バインド

Azure Cosmos DB 入力バインドでは、SQL API を使用して 1 つ以上の Azure Cosmos DB ドキュメントを取得して関数の入力パラメーターに渡します。 ドキュメント ID またはクエリ パラメーターは、関数を呼び出したトリガーに基づいて決定することができます。

セットアップと構成の詳細については、[概要](./functions-bindings-cosmosdb-v2.md)に関するページをご覧ください。

> [!NOTE]
> コレクションが[パーティション分割](../cosmos-db/partitioning-overview.md#logical-partitions)されている場合、参照操作でも、パーティション キーの値を指定する必要があります。
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

このセクションには、次の例が含まれています。

* [キュー トリガー、JSON からの ID の検索](#queue-trigger-look-up-id-from-json-c)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-c)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-c)
* [HTTP トリガー、SqlQuery を使用したルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP トリガー、SqlQuery を使用した複数のドキュメントの取得](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP トリガー、DocumentClient を使用した複数のドキュメントの取得](#http-trigger-get-multiple-docs-using-documentclient-c)

例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }
        
        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }
        
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>キュー トリガー、JSON からの ID の検索 

次の例は、単一のドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、JSON オブジェクトを含むキュー メッセージによってトリガーされます。 キュー トリガーにより、検索する ID とパーティション キー値を含む、`ToDoItemLookup` タイプのオブジェクトに JSON が解析されます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

>[!NOTE]
>HTTP クエリ文字列パラメーターは大文字と小文字が区別されます。
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、ルート データを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP トリガー、SqlQuery を使用したルート データからの ID の検索

次の例は、単一のドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、ルート データを使用して検索のための ID を指定する HTTP 要求によってトリガーされます。 該当の ID は、指定されたデータベースとコレクションからの `ToDoItem` ドキュメントの取得に使用されます。

例では、`SqlQuery` パラメーターでバインド式を使用する方法を示しています。 ここに示したようにルート データを `SqlQuery` パラメーターに渡すことはできますが、現在、[クエリ文字列の値を渡すことはできません](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583)。

> [!NOTE]
> ID だけでクエリを実行する必要がある場合、[前の例](#http-trigger-look-up-id-from-query-string-c)のように、参照を使用することをお勧めします。[要求ユニット](../cosmos-db/request-units.md)の消費が少なくなります。 ポイント読み取り操作 (GET) は ID によるクエリより[効率性に優れています](../cosmos-db/optimize-cost-reads-writes.md)。
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、ドキュメントの一覧を取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、HTTP 要求によってトリガーされます。 クエリは、`SqlQuery` 属性プロパティで指定されます。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP トリガー、DocumentClient を使用した複数のドキュメントの取得

次の例は、ドキュメントの一覧を取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、HTTP 要求によってトリガーされます。 コードでは、Azure Cosmos DB バインドによって提供される `DocumentClient` インスタンスを使用して、ドキュメントの一覧を読み取ります。 また、`DocumentClient` インスタンスは、書き込み操作に使用できます。

> [!NOTE]
> [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient) インターフェイスを使用してテストを容易にすることもできます。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

このセクションには、次の例が含まれています。

* [キュー トリガー、文字列からの ID の検索](#queue-trigger-look-up-id-from-string-c-script)
* [キュー トリガー、SqlQuery を使用した複数のドキュメントの取得](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP トリガー、SqlQuery を使用した複数のドキュメントの取得](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP トリガー、DocumentClient を使用した複数のドキュメントの取得](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP トリガーの例では、次のようなシンプルな `ToDoItem` タイプを参照します。

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

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>キュー トリガー、文字列からの ID の検索

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>キュー トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、*function.json* ファイルの Azure Cosmos DB 入力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得します。

キュー トリガーがパラメーター `departmentId` を提供します。 `{ "departmentId" : "Finance" }` のキュー メッセージは、金融部門のすべてのレコードを返します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のドキュメントを取得する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、ルート データを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、ドキュメントの一覧を取得する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、HTTP 要求によってトリガーされます。 クエリは、`SqlQuery` 属性プロパティで指定されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP トリガー、DocumentClient を使用した複数のドキュメントの取得

次の例は、ドキュメントの一覧を取得する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、HTTP 要求によってトリガーされます。 コードでは、Azure Cosmos DB バインドによって提供される `DocumentClient` インスタンスを使用して、ドキュメントの一覧を読み取ります。 また、`DocumentClient` インスタンスは、書き込み操作に使用できます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="java"></a>[Java](#tab/java)

このセクションには、次の例が含まれています。

* [HTTP トリガー、クエリ文字列からの ID の検索 - String パラメーター](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP トリガー、クエリ文字列からの ID の検索 - POJO パラメーター](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-java)
* [HTTP トリガー、SqlQuery を使用したルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP トリガー、ルート データからの複数のドキュメントの取得、SqlQuery を使用](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP トリガー、クエリ文字列からの ID の検索 - String パラメーター

次の例は、単一のドキュメントを取得する Java 関数を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションからドキュメントを String 形式で取得するために使用されます。

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Cosmos DB に由来する関数パラメーター上で `@CosmosDBInput` 注釈を使用します。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP トリガー、クエリ文字列からの ID の検索 - POJO パラメーター

次の例は、単一のドキュメントを取得する Java 関数を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションからドキュメントを取得するために使用されます。 このドキュメントはその後、前に作成した ```ToDoItem``` POJO のインスタンスに変換され、引数として関数に渡されます。

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する Java 関数を示しています。 関数は、ルート パラメーターを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションからドキュメントを取得し、```Optional<String>``` として返すために使用されます。

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP トリガー、SqlQuery を使用したルート データからの ID の検索

次の例は、単一のドキュメントを取得する Java 関数を示しています。 この関数は、ルート パラメーターを使用して検索する ID を指定する HTTP 要求によってトリガーされます。 クエリ条件によっては多数のドキュメントが返される可能性があるため、その ID は、指定されたデータベースとコレクションからドキュメントを取得し、結果セットを ```ToDoItem[]``` に変換するために使用されます。

> [!NOTE]
> ID だけでクエリを実行する必要がある場合、[前の例](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)のように、参照を使用することをお勧めします。[要求ユニット](../cosmos-db/request-units.md)の消費が少なくなります。 ポイント読み取り操作 (GET) は ID によるクエリより[効率性に優れています](../cosmos-db/optimize-cost-reads-writes.md)。
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP トリガー、ルート データからの複数のドキュメントの取得、SqlQuery を使用

次の例は、複数のドキュメントを取得する Java 関数を示しています。 この関数は、ルート パラメーター ```desc``` を使用して ```description``` フィールドで検索する文字列を指定する HTTP 要求によってトリガーされます。 この検索用語は、指定されたデータベースとコレクションからドキュメントのコレクションを取得し、結果セットを ```ToDoItem[]``` に変換して、それを引数として関数に渡すために使用されます。

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

このセクションには、次の例が含まれています。各例では、さまざまなソースから ID 値を指定して単一のドキュメントを読み取ります。

* [キュー トリガー、JSON からの ID の検索](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-javascript)
* [キュー トリガー、SqlQuery を使用した複数のドキュメントの取得](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>キュー トリガー、JSON からの ID の検索

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のドキュメントを取得する [JavaScript 関数](functions-reference-node.md)を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する [JavaScript 関数](functions-reference-node.md)を示しています。 関数は、ルート データを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>キュー トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、*function.json* ファイルの Azure Cosmos DB 入力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得します。

キュー トリガーがパラメーター `departmentId` を提供します。 `{ "departmentId" : "Finance" }` のキュー メッセージは、金融部門のすべてのレコードを返します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, input) {
  var documents = context.bindings.documents;
  for (var i = 0; i < documents.length; i++) {
    var document = documents[i];
    // operate on each document
  }
  context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* [キュー トリガー、JSON からの ID の検索](#queue-trigger-look-up-id-from-json-ps)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-id-query-string-ps)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-id-route-data-ps)
* [キュー トリガー、SqlQuery を使用した複数のドキュメントの取得](#queue-trigger-multiple-docs-sqlquery-ps)

### <a name="queue-trigger-look-up-id-from-json"></a>キュー トリガー、JSON からの ID の検索

次の例は、1つの Cosmos DB ドキュメントを読み取り、更新する方法を示しています。 ドキュメントの一意識別子は、キュー メッセージの JSON 値によって提供されます。

Cosmos DB 入力バインドは、関数の構成ファイル (_function.json_) で見つかったバインドの一覧の先頭に表示されます。

<a name="queue-trigger-look-up-id-from-json-ps"></a>

```json
{
  "name": "InputDocumentIn",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id": "{queueTrigger_payload_property}",
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "in"
},
{
  "name": "InputDocumentOut",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": false,
  "partitionKey": "{queueTrigger_payload_property}",
  "connectionStringSetting": "CosmosDBConnection",
  "direction": "out"
}
```

_run.ps1_ ファイルには、受信ドキュメントを読み取り、変更を出力する PowerShell コードが含まれます。

```powershell
param($QueueItem, $InputDocumentIn, $TriggerMetadata) 

$Document = $InputDocumentIn 
$Document.text = 'This was updated!' 

Push-OutputBinding -Name InputDocumentOut -Value $Document  
```

<a name="http-trigger-id-query-string-ps"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、Web API から 1つの Cosmos DB ドキュメントを読み取り、更新する方法を示しています。 ドキュメントの一意識別子は、バインドの `"Id": "{Query.Id}"` プロパティで定義されているように、HTTP 要求の querystring パラメーターを通じて提供されます。

Cosmos DB 入力バインドは、関数の構成ファイル (_function.json_) で見つかったバインドの一覧の先頭に表示されます。

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{Query.id}", 
      "PartitionKey": "{Query.partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ] 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    },
  ], 
  "disabled": false 
} 
```
  
_run.ps1_ ファイルには、受信ドキュメントを読み取り、変更を出力する PowerShell コードが含まれます。

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 

    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
 
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
}
```

<a name="http-trigger-id-route-data-ps"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、Web API から 1つの Cosmos DB ドキュメントを読み取り、更新する方法を示しています。 ドキュメントの一意識別子は、route パラメーターを通じて提供されます。 route パラメーターは、HTTP 要求バインドの `route` プロパティで定義され、Cosmos DB `"Id": "{Id}"` バインド プロパティで参照されます。

Cosmos DB 入力バインドは、関数の構成ファイル (_function.json_) で見つかったバインドの一覧の先頭に表示されます。

```json
{ 
  "bindings": [ 
    { 
      "type": "cosmosDB", 
      "name": "ToDoItem", 
      "databaseName": "ToDoItems", 
      "collectionName": "Items", 
      "connectionStringSetting": "CosmosDBConnection", 
      "direction": "in", 
      "Id": "{id}", 
      "PartitionKey": "{partitionKeyValue}" 
    },
    { 
      "authLevel": "anonymous", 
      "name": "Request", 
      "type": "httpTrigger", 
      "direction": "in", 
      "methods": [ 
        "get", 
        "post" 
      ], 
      "route": "todoitems/{partitionKeyValue}/{id}" 
    }, 
    { 
      "name": "Response", 
      "type": "http", 
      "direction": "out" 
    }
  ], 
  "disabled": false 
} 
```

_run.ps1_ ファイルには、受信ドキュメントを読み取り、変更を出力する PowerShell コードが含まれます。

```powershell
using namespace System.Net 

param($Request, $ToDoItem, $TriggerMetadata) 

Write-Host 'PowerShell HTTP trigger function processed a request' 

if (-not $ToDoItem) { 
    Write-Host 'ToDo item not found' 

    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::NotFound 
        Body = $ToDoItem.Description 
    }) 

} else { 
    Write-Host "Found ToDo item, Description=$($ToDoItem.Description)" 
  
    Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{ 
        StatusCode = [HttpStatusCode]::OK 
        Body = $ToDoItem.Description 
    }) 
} 
```

<a name="queue-trigger-multiple-docs-sqlquery-ps"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>キュー トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、複数の Cosmos DB ドキュメントを読み取る方法を示しています。 関数の構成ファイル (_function.json_) によって、`sqlQuery` を含むバインド プロパティが定義されます。 `sqlQuery` プロパティに指定された SQL ステートメントによって、関数に提供されるドキュメントのセットが選択されます。

```json
{ 
  "name": "Documents", 
  "type": "cosmosDB", 
  "direction": "in", 
  "databaseName": "MyDb", 
  "collectionName": "MyCollection", 
  "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}", 
  "connectionStringSetting": "CosmosDBConnection" 
} 
```

_run1.ps_ ファイルには、受信ドキュメントを読み取る PowerShell コードが含まれます。

```powershell
param($QueueItem, $Documents, $TriggerMetadata) 

foreach ($Document in $Documents) { 
    # operate on each document 
} 
```

# <a name="python"></a>[Python](#tab/python)

このセクションには、次の例が含まれています。各例では、さまざまなソースから ID 値を指定して単一のドキュメントを読み取ります。

* [キュー トリガー、JSON からの ID の検索](#queue-trigger-look-up-id-from-json-python)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-python)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-python)
* [キュー トリガー、SqlQuery を使用した複数のドキュメントの取得](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>キュー トリガー、JSON からの ID の検索

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

Python コードを次に示します。

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のドキュメントを取得する [Python 関数](functions-reference-python.md)を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する [Python 関数](functions-reference-python.md)を示しています。 関数は、ルート データを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>キュー トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、*function.json* ファイルの Azure Cosmos DB 入力バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数は、SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得します。

キュー トリガーがパラメーター `departmentId` を提供します。 `{ "departmentId" : "Finance" }` のキュー メッセージは、金融部門のすべてのレコードを返します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

Python コードを次に示します。

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

 ---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、[この後の構成に関するセクション](#configuration)をご覧ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)から、Cosmos DB に書き込むパラメーターに `@CosmosDBOutput` 注釈を使用します。 注釈パラメーターの型は `OutputBinding<T>` にするようにします。ここで、`T` は Java のネイティブ型または POJO のどちらかです。

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
|**direction**     | 該当なし | `in` に設定する必要があります。         |
|**name**     | 該当なし | 関数のドキュメントを表すバインド パラメーターの名前。  |
|**databaseName** |**DatabaseName** |ドキュメントを含むデータベース。        |
|**collectionName** |**CollectionName** | ドキュメントを含むコレクションの名前。 |
|**id**    | **Id** | 取得するドキュメントの ID。 このプロパティは、[バインド式](./functions-bindings-expressions-patterns.md)をサポートしています。 `id` プロパティと **sqlQuery** プロパティの両方は設定しないでください。 いずれも設定しない場合は、コレクション全体が取得されます。 |
|**sqlQuery**  |**SqlQuery**  | 複数のドキュメントを取得するときに使用する Azure Cosmos DB SQL クエリ。 このプロパティは、次の例のように実行時のバインドをサポートします。`SELECT * FROM c where c.departmentId = {departmentId}` `id` と `sqlQuery` プロパティの両方は設定しないでください。 いずれも設定しない場合は、コレクション全体が取得されます。|
|**connectionStringSetting**     |**ConnectionStringSetting**|Azure Cosmos DB 接続文字列を含むアプリ設定の名前。 |
|**partitionKey**|**PartitionKey**|参照用のパーティション キー値を指定します。 バインディング パラメーターを含めることもできます。 [パーティション分割](../cosmos-db/partitioning-overview.md#logical-partitions)されたコレクションの参照で必須です。|
|**preferredLocations**| **PreferredLocations**| (省略可能) Azure Cosmos DB サービスの geo レプリケートされたデータベース アカウントの優先される場所 (リージョン) を定義します。 複数の値はコンマで区切る必要があります。 たとえば、"East US,South Central US,North Europe" などです。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

関数が正常に終了したときに、名前付き入力パラメーターを介した入力ドキュメントへの変更がすべて自動的に保持されます。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

関数が正常に終了したときに、名前付き入力パラメーターを介した入力ドキュメントへの変更がすべて自動的に保持されます。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)の [@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) 注釈によって Cosmos DB データは関数に公開されます。 この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

関数の終了時に更新が自動的に行われることはありません。 代わりに、`context.bindings.<documentName>In` と `context.bindings.<documentName>Out` を使用して、更新を行います。 詳細については、「[JavaScript の例](#example)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

関数の終了時にドキュメントへの更新が自動的に行われることはありません。 関数でドキュメントを更新するには、[出力バインド](./functions-bindings-cosmosdb-v2-input.md)を使用します。 詳細については、「[PowerShell の例](#example)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

データは、`DocumentList` パラメーターを介して関数から使用できるようになります。 ドキュメントに加えられた変更は、自動的には保存されません。

---

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB ドキュメントが作成または変更されたときに関数を実行する (トリガー)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB ドキュメントに変更を保存する (出力バインド)](./functions-bindings-cosmosdb-v2-output.md)