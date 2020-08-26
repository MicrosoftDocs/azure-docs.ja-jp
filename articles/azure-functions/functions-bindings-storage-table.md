---
title: Azure Functions における Azure Table Storage のバインド
description: Azure Functions で Azure Table のバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: fd50e649257ba8849f49cd3aae85e3228b9eb94c
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612216"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions における Azure Table Storage のバインド

この記事では、Azure Functions で Azure Table Storage のバインドを使用する方法について説明します。 Azure Functions は、Azure Table ストレージの入力および出力のバインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Table ストレージ バインディングは [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ、バージョン 2.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>パッケージ - Functions 2.x 以降

Table ストレージ バインディングは [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>入力

Azure Table Storage の入力バインドを使用して、Azure Storage アカウントのテーブルを読み取ります。

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

### <a name="iqueryable"></a>IQueryable

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

### <a name="cloudtable"></a>CloudTable

`IQueryable` は [Functions v2 ランタイム](functions-versions.md)ではサポートされていません。 代わりに、Azure Storage SDK で `CloudTable` メソッド パラメーターを使用してテーブルを読み取ります。 Azure Functions ログ テーブルにクエリを実行する関数の例:

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

CloudTable オブジェクトの使用方法の詳細については、[Azure Table ストレージの概要](../cosmos-db/tutorial-develop-table-dotnet.md)ページをご覧ください。

`CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

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

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

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

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

この C# スクリプト コードは、エンティティ型が `TableEntity`から派生できるように、Azure Storage SDK に参照を追加します。

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
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
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
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

`CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。


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

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

1 つのテーブル行 

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

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
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

次の例では、フィルターを使用して、Azure テーブル内の特定の名前を持つ人物を照会し、一致候補の数を 10 件の結果に制限しています。

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

---

## <a name="input---attributes-and-annotations"></a>入力 - 属性と注釈

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

  完全な例については、「入力 - C# の例」を参照してください。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値がテーブル ストレージに由来するパラメーターで `@TableInput` 注釈を使用します。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

---

## <a name="input---configuration"></a>入力 - 構成

次の表は、*function.json* ファイルと `Table` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `table` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のテーブルまたはエンティティを表す変数の名前。 | 
|**tableName** | **TableName** | テーブルの名前。| 
|**partitionKey** | **PartitionKey** |省略可能。 読み取るテーブル エンティティのパーティション キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#input---usage)」セクションを参照してください。| 
|**rowKey** |**RowKey** | 省略可能。 読み取るテーブル エンティティの行キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#input---usage)」セクションを参照してください。| 
|**take** |**Take** | 省略可能。 JavaScript で読み取るエンティティの最大数。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#input---usage)」セクションを参照してください。| 
|**filter** |**Assert** | 省略可能。 JavaScript のテーブル入力の OData フィルター式。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#input---usage)」セクションを参照してください。| 
|**connection** |**接続** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 この設定には、"AzureWebJobs" というプレフィックスが付加されたアプリ設定または接続文字列名を指定できます。 たとえば、設定名が "AzureWebJobsMyStorage" の場合、ここでは "MyStorage" を指定できます。 Functions ランタイムでは、"AzureWebJobsMyStorage" という名前のアプリ設定が自動的に検索されます。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>入力 - 使用方法

# <a name="c"></a>[C#](#tab/csharp)

* **1 行の読み取り**

  `partitionKey` と `rowKey` を設定します。 メソッド パラメーター `T <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 通常、`T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型です。 `filter` および `take` プロパティは、このシナリオでは使用しません。

* **1 つ以上の行の読み取り**

  メソッド パラメーター `IQueryable<T> <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 `T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型にする必要があります。 必要なフィルター処理があれば、`IQueryable` メソッドを使用して実行します。 `partitionKey`、`rowKey`、`filter`、`take` の各プロパティは、このシナリオでは使用しません。  

  > [!NOTE]
  > `IQueryable` は [Functions v2 ランタイム](functions-versions.md)ではサポートされていません。 代わりに、Azure Storage SDK で [CloudTable paramName メソッド パラメーターを使用](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)して、テーブルを読み取ります。 `CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

* **1 行の読み取り**

  `partitionKey` と `rowKey` を設定します。 メソッド パラメーター `T <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 通常、`T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型です。 `filter` および `take` プロパティは、このシナリオでは使用しません。

* **1 つ以上の行の読み取り**

  メソッド パラメーター `IQueryable<T> <paramName>` を使用して、テーブル データにアクセスします。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 `T` は、`ITableEntity` を実装する型か、`TableEntity` から派生する型にする必要があります。 必要なフィルター処理があれば、`IQueryable` メソッドを使用して実行します。 `partitionKey`、`rowKey`、`filter`、`take` の各プロパティは、このシナリオでは使用しません。  

  > [!NOTE]
  > `IQueryable` は [Functions v2 ランタイム](functions-versions.md)ではサポートされていません。 代わりに、Azure Storage SDK で [CloudTable paramName メソッド パラメーターを使用](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)して、テーブルを読み取ります。 `CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`filter` と `take` プロパティを設定します。 `partitionKey` と `rowKey` は設定しません。 `context.bindings.<BINDING_NAME>` を使用して入力テーブルの単一のエンティティ (または複数のエンティティ) にアクセスします。 逆シリアル化されたオブジェクトには `RowKey` プロパティと `PartitionKey` プロパティがあります。

# <a name="python"></a>[Python](#tab/python)

テーブル データは、JSON 文字列として関数に渡されます。 入力[例](#input)に示されているように `json.loads` を呼び出してメッセージを逆シリアル化します。

# <a name="java"></a>[Java](#tab/java)

[TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) 属性を使用すると、関数をトリガーしたテーブル行にアクセスできます。

---

## <a name="output"></a>出力

Azure Table Storage の出力バインドを使用して、Azure Storage アカウントのテーブルにエンティティを書き込みます。

> [!NOTE]
> この出力バインドでは、既存のエンティティの更新はサポートされていません。 既存のエンティティを更新するには、[Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) の `TableOperation.Replace` 操作を使ってください。

# <a name="c"></a>[C#](#tab/csharp)

次の例は、HTTP トリガーを使用して 1 つのテーブル行を書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルのテーブル出力バインドと、バインドを使用する [C# スクリプト](functions-reference-csharp.md) コードを示しています。 この関数は複数のテーブル エンティティを書き込みます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルのテーブル出力バインドと、バインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は複数のテーブル エンティティを書き込みます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

次の例では、Table Storage の出力バインドを使用する方法を示します。 `table` バインドは、値を `name`、`tableName`、`partitionKey`、`connection` に割り当てて *function.json* で構成されます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

次の関数は、`rowKey` 値に対して一意の UUI を生成し、メッセージを Table Storage に保持します。

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

次の例は、HTTP トリガーを使用して 1 つのテーブル行を書き込む Java 関数を示しています。

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

次の例は、HTTP トリガーを使用して複数のテーブル行を書き込む Java 関数を示しています。

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>出力 - 属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs) を使用します。

この属性のコンストラクターは、テーブル名を受け取ります。 属性は、次の例のように、`out` パラメーターまたは関数の戻り値で使用できます。

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

完全な例については、「[出力 - C# の例](#output)」を参照してください。

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、[入力 - 属性](#input---attributes-and-annotations)を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、パラメーターで [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) 注釈を使用し、テーブル ストレージに値を書き込みます。

詳細については、[例を参照してください](#output)。

---

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `Table` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `table` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。|
|**direction** | 該当なし | `out` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | テーブルまたはエンティティを表す関数コードに使用される変数の名前。 `$return` に設定して、関数の戻り値を参照します。| 
|**tableName** |**TableName** | テーブルの名前。| 
|**partitionKey** |**PartitionKey** | 書き込むテーブル エンティティのパーティション キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#output---usage)」セクションを参照してください。| 
|**rowKey** |**RowKey** | 書き込むテーブル エンティティの行キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#output---usage)」セクションを参照してください。| 
|**connection** |**接続** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "MyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

# <a name="c"></a>[C#](#tab/csharp)

メソッド パラメーター `ICollector<T> paramName` または `IAsyncCollector<T> paramName` を使用して出力テーブル エンティティにアクセスします。`T` には、`PartitionKey` および `RowKey` のプロパティが含まれています。 多くの場合、これらのプロパティには `ITableEntity` の実装や `TableEntity` の継承が伴います。

代わりに、Azure Storage SDK で `CloudTable` メソッド パラメーターを使用してテーブルに書き込みます。 `CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

メソッド パラメーター `ICollector<T> paramName` または `IAsyncCollector<T> paramName` を使用して出力テーブル エンティティにアクセスします。`T` には、`PartitionKey` および `RowKey` のプロパティが含まれています。 多くの場合、これらのプロパティには `ITableEntity` の実装や `TableEntity` の継承が伴います。 `paramName` 値は *function.json* の `name` プロパティで指定されます。

代わりに、Azure Storage SDK で `CloudTable` メソッド パラメーターを使用してテーブルに書き込みます。 `CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>` を使用して出力イベントにアクセスします。`<name>` は *function.json* の `name` プロパティで指定された値です。

# <a name="python"></a>[Python](#tab/python)

関数から Table Storage 行メッセージを出力するには、次の 2 つのオプションがあります。

- **戻り値**:*function.json* 内の `name` プロパティを `$return` に設定します。 この構成では、関数の戻り値は Table Storage 行として永続化されます。

- **命令型**:[Out](/python/api/azure-functions/azure.functions.out?view=azure-python) 型として宣言されたパラメーターの [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) メソッドに値を渡します。 `set` に渡された値は、イベント ハブ メッセージとして永続化されます。

# <a name="java"></a>[Java](#tab/java)

[TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) 注釈を使用して関数から Table Storage 行を出力するには、次の 2 つのオプションがあります。

- **戻り値**:関数自体に注釈を適用すると、関数の戻り値が Table Storage 行として永続化されます。

- **命令型**:メッセージ値を明示的に設定するには、[`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 型の特定のパラメーターに注釈を適用します。この場合、`T` には `PartitionKey` と `RowKey` のプロパティが含まれます。 多くの場合、これらのプロパティには `ITableEntity` の実装や `TableEntity` の継承が伴います。

---

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| テーブル | [テーブル エラー コード](/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB、テーブル、キュー | [ストレージ エラー コード](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB、テーブル、キュー | [トラブルシューティング](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
