---
title: Azure Functions における Azure Table Storage の出力バインド
description: Azure Functions で Azure Table Storage の出力バインドを使用する方法について理解します。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f793f96f55a258b2d7cb11f214984416557618df
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453004"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Azure Functions における Azure Table Storage の出力バインド

Azure Table Storage の出力バインドを使用して、Azure Storage アカウントのテーブルにエンティティを書き込みます。

> [!NOTE]
> この出力バインドでは、既存のエンティティの更新はサポートされていません。 既存のエンティティを更新するには、[Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) の `TableOperation.Replace` 操作を使ってください。

## <a name="example"></a>例

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

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

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

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、関数からテーブルに複数のエンティティを書き込む方法を示しています。

_function.json_ のバインド構成:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

_run.ps1_ の PowerShell コード:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
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

---

## <a name="attributes-and-annotations"></a>属性と注釈

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

完全な例については、[C# の例](#example)を参照してください。

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、[入力 - 属性](./functions-bindings-storage-table-input.md#attributes-and-annotations)を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、パラメーターで [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) 注釈を使用し、テーブル ストレージに値を書き込みます。

詳細については、[例を参照してください](#example)。

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
|**direction** | 該当なし | `out` に設定する必要があります。 このプロパティは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | テーブルまたはエンティティを表す関数コードに使用される変数の名前。 `$return` に設定して、関数の戻り値を参照します。| 
|**tableName** |**TableName** | テーブルの名前。| 
|**partitionKey** |**PartitionKey** | 書き込むテーブル エンティティのパーティション キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#usage)」セクションを参照してください。| 
|**rowKey** |**RowKey** | 書き込むテーブル エンティティの行キー。 このプロパティを使用する方法のガイダンスについては、「[使用方法](#usage)」セクションを参照してください。| 
|**connection** |**接続** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "MyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

メソッド パラメーター `ICollector<T> paramName` または `IAsyncCollector<T> paramName` を使用して出力テーブル エンティティにアクセスします。`T` には、`PartitionKey` および `RowKey` のプロパティが含まれています。 多くの場合、これらのプロパティには `ITableEntity` の実装や `TableEntity` の継承が伴います。

代わりに、Azure Storage SDK で `CloudTable` メソッド パラメーターを使用してテーブルに書き込みます。 `CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

メソッド パラメーター `ICollector<T> paramName` または `IAsyncCollector<T> paramName` を使用して出力テーブル エンティティにアクセスします。`T` には、`PartitionKey` および `RowKey` のプロパティが含まれています。 多くの場合、これらのプロパティには `ITableEntity` の実装や `TableEntity` の継承が伴います。 `paramName` 値は *function.json* の `name` プロパティで指定されます。

代わりに、Azure Storage SDK で `CloudTable` メソッド パラメーターを使用してテーブルに書き込みます。 `CloudTable` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

# <a name="java"></a>[Java](#tab/java)

[TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput) 注釈を使用して関数から Table Storage 行を出力するには、次の 2 つのオプションがあります。

- **戻り値**:関数自体に注釈を適用すると、関数の戻り値が Table Storage 行として永続化されます。

- **命令型**:メッセージ値を明示的に設定するには、[`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 型の特定のパラメーターに注釈を適用します。この場合、`T` には `PartitionKey` と `RowKey` のプロパティが含まれます。 多くの場合、これらのプロパティには `ITableEntity` の実装や `TableEntity` の継承が伴います。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>` を使用して出力イベントにアクセスします。`<name>` は *function.json* の `name` プロパティで指定された値です。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

テーブル データに書き込むには、`Push-OutputBinding` コマンドレットを使用して、`-Name TableBinding` パラメーターと、行データに等しい `-Value` パラメーターを設定します。 詳細については、[PowerShell の例](#example)を参照してください。

# <a name="python"></a>[Python](#tab/python)

関数から Table Storage 行メッセージを出力するには、次の 2 つのオプションがあります。

- **戻り値**:*function.json* 内の `name` プロパティを `$return` に設定します。 この構成では、関数の戻り値は Table Storage 行として永続化されます。

- **命令型**:[Out](/python/api/azure-functions/azure.functions.out) 型として宣言されたパラメーターの [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) メソッドに値を渡します。 `set` に渡された値は、イベント ハブ メッセージとして永続化されます。

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
