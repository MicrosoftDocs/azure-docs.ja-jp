---
title: Azure Functions における Azure Table Storage の入力バインド
description: Azure Functions で Azure Table Storage の入力バインドを使用する方法について理解します。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4fc2426189384856d2d2e95887cdabd2f9e9ebea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98033780"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Azure Functions における Azure Table Storage の入力バインド

Azure Table Storage の入力バインドを使用して、Azure Storage アカウントのテーブルを読み取ります。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>1 つのエンティティ

次の例は、1 つのテーブル行を読み取る [C# 関数](functions-dotnet-class-library.md)を示しています。 キューに送信されるすべてのメッセージについて、関数がトリガーされます。

行キー値 "{queueTrigger}" は、行キーがキュー メッセージ文字列から取得されることを示します。

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="cloudtable"></a>CloudTable

`CloudTable` は [Functions v2 以降のランタイム](functions-versions.md)でのみサポートされています。

Azure Storage SDK で `CloudTable` メソッド パラメーターを使用してテーブルを読み取ります。 Azure Functions ログ テーブルにクエリを実行する関数の例:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

CloudTable オブジェクトの使用方法の詳細については、[Azure Table Storage の概要](../cosmos-db/tutorial-develop-table-dotnet.md)ページをご覧ください。

`CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

### <a name="iqueryable"></a>IQueryable

`IQueryable` は [Functions v1 ランタイム](functions-versions.md)でのみサポートされています。

次の例は、`MyPoco` クラスが `TableEntity` から派生した複数のテーブル行を読み取る [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

### <a name="one-entity"></a>1 つのエンティティ

次の例は、*function.json* ファイルのテーブル入力バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 この関数は、キュー トリガーを使用して単一のテーブル行を読み取ります。 

*function.json* ファイルには `partitionKey` と `rowKey` が指定されています。 `rowKey` 値 "{queueTrigger}" は、行キーがキュー メッセージ文字列から取得されることを示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` は [バージョン 2.x 以降](functions-versions.md)の Functions ランタイムではサポートされていません。 代わりに、Azure Storage SDK で `CloudTable` メソッド パラメーターを使用してテーブルを読み取ります。 Azure Functions ログ テーブルにクエリを実行する関数の例:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

CloudTable オブジェクトの使用方法の詳細については、[Azure Table ストレージの概要](../cosmos-db/tutorial-develop-table-dotnet.md)ページをご覧ください。

`CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

### <a name="iqueryable"></a>IQueryable

次の例は、*function.json* ファイルのテーブル入力バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 この関数は、キュー メッセージに指定されているパーティション キーのエンティティを読み取ります。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

この C# スクリプト コードは、エンティティ型が `TableEntity`から派生できるように、Azure Storage SDK に参照を追加します。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

# <a name="java"></a>[Java](#tab/java)

次の例では、テーブル ストレージ内の指定したパーティション内にある person オブジェクトの一覧を返す、HTTP によってトリガーされる関数を示します。 この例では、パーティション キーは http ルートから抽出され、tableName と connection は関数の設定からのものです。 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

TableInput 注釈では、次の例のように、要求の JSON 本文からバインドを抽出することもできます。

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

次の例では、フィルターを使用して、Azure テーブル内の特定の名前を持つ人物についてクエリを実行し、一致候補の数を 10 件の結果に制限しています。

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルのテーブル入力バインドと、バインドを使用する [JavaScript コード](functions-reference-node.md)を示しています。 この関数は、キュー トリガーを使用して単一のテーブル行を読み取ります。 

*function.json* ファイルには `partitionKey` と `rowKey` が指定されています。 `rowKey` 値 "{queueTrigger}" は、行キーがキュー メッセージ文字列から取得されることを示します。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の関数では、キュー トリガーを使用して、関数への入力として 1 つのテーブル行を読み取ります。

この例では、バインド構成によってテーブルの `partitionKey` に明示的な値が指定され、式を使用して `rowKey` に渡しています。 `rowKey` 式の `{queueTrigger}` は、行キーがキュー メッセージ文字列から取得されることを示します。

_function.json_ のバインド構成:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

_run.ps1_ の PowerShell コード:

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

次の関数では、キュー トリガーを使用して、関数への入力として 1 つのテーブル行を読み取ります。

この例では、バインド構成によってテーブルの `partitionKey` に明示的な値が指定され、式を使用して `rowKey` に渡しています。 `rowKey` 式の `{id}` は、行キーがキュー メッセージ文字列から取得されることを示します。

_function.json_ ファイルのバインド構成:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

*\_\_init\_\_.py* ファイルの Python コード:

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

 [C# クラス ライブラリ](functions-dotnet-class-library.md)では、以下の属性を使用してテーブル入力バインディングを構成します。

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  この属性のコンストラクターは、テーブル名、パーティション キー、および行キーを受け取ります。 属性は、次の例のように、`out` パラメーターまたは関数の戻り値で使用できます。

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  完全な例については、C# の例を参照してください。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  使用するストレージ アカウントを指定する別の方法を提供します。 コンストラクターは、ストレージ接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

使用するストレージ アカウントは、次の順序で決定されます。

* `Table` 属性の `Connection` プロパティ。
* `Table` 属性と同じパラメーターに適用された `StorageAccount` 属性。
* 関数に適用される `StorageAccount` 属性。
* クラスに適用される `StorageAccount` 属性。
* 関数アプリの既定のストレージ アカウント ("AzureWebJobsStorage" アプリ設定)。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値がテーブル ストレージに由来するパラメーターで `@TableInput` 注釈を使用します。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `Table` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `table` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のテーブルまたはエンティティを表す変数の名前。 | 
|**tableName** | **TableName** | テーブルの名前。| 
|**partitionKey** | **PartitionKey** |省略可能。 読み取るテーブル エンティティのパーティション キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#usage)」セクションを参照してください。| 
|**rowKey** |**RowKey** | 省略可能。 読み取るテーブル エンティティの行キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#usage)」セクションを参照してください。| 
|**take** |**Take** | 省略可能。 JavaScript で読み取るエンティティの最大数。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#usage)」セクションを参照してください。| 
|**filter** |**Assert** | 省略可能。 JavaScript のテーブル入力の OData フィルター式。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#usage)」セクションを参照してください。| 
|**connection** |**接続** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 この設定には、"AzureWebJobs" というプレフィックスが付加されたアプリ設定または接続文字列名を指定できます。 たとえば、設定名が "AzureWebJobsMyStorage" の場合、ここでは "MyStorage" を指定できます。 Functions ランタイムでは、"AzureWebJobsMyStorage" という名前のアプリ設定が自動的に検索されます。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

* **1 行の読み取り**

  `partitionKey` と `rowKey` を設定します。 メソッド パラメーター `T <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 通常、`T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型です。 `filter` および `take` プロパティは、このシナリオでは使用しません。

* **1 つ以上の行の読み取り**

  メソッド パラメーター `IQueryable<T> <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 `T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型にする必要があります。 必要なフィルター処理があれば、`IQueryable` メソッドを使用して実行します。 `partitionKey`、`rowKey`、`filter`、`take` の各プロパティは、このシナリオでは使用しません。  

  > [!NOTE]
  > `IQueryable` は [Functions v2 ランタイム](functions-versions.md)ではサポートされていません。 代わりに、Azure Storage SDK で [CloudTable paramName メソッド パラメーターを使用](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)して、テーブルを読み取ります。 `CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

* **1 行の読み取り**

  `partitionKey` と `rowKey` を設定します。 メソッド パラメーター `T <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 通常、`T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型です。 `filter` および `take` プロパティは、このシナリオでは使用しません。

* **1 つ以上の行の読み取り**

  メソッド パラメーター `IQueryable<T> <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 `T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型にする必要があります。 必要なフィルター処理があれば、`IQueryable` メソッドを使用して実行します。 `partitionKey`、`rowKey`、`filter`、`take` の各プロパティは、このシナリオでは使用しません。  

  > [!NOTE]
  > `IQueryable` は [Functions v2 ランタイム](functions-versions.md)ではサポートされていません。 代わりに、Azure Storage SDK で [CloudTable paramName メソッド パラメーターを使用](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)して、テーブルを読み取ります。 `CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="java"></a>[Java](#tab/java)

[TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) 属性を使用すると、関数をトリガーしたテーブル行にアクセスできます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`filter` と `take` プロパティを設定します。 `partitionKey` と `rowKey` は設定しません。 `context.bindings.<BINDING_NAME>` を使用して入力テーブルの単一のエンティティ (または複数のエンティティ) にアクセスします。 逆シリアル化されたオブジェクトには `RowKey` プロパティと `PartitionKey` プロパティがあります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

データは、*function.json* ファイルの `name` キーによって指定された入力パラメーターに渡されます。 `partitionKey` と `rowKey` を指定すると、特定のレコードをフィルター処理できます。 詳細については、[PowerShell の例](#example)を参照してください。

# <a name="python"></a>[Python](#tab/python)

テーブル データは、JSON 文字列として関数に渡されます。 入力[例](#example)に示されているように `json.loads` を呼び出してメッセージを逆シリアル化します。

---

## <a name="next-steps"></a>次の手順

* [関数からテーブル ストレージ データを書き込む](./functions-bindings-storage-table-output.md)
