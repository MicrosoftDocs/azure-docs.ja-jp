---
title: Azure Functions における Azure Queue storage の出力バインド
description: Azure Functions で Azure Queue storage メッセージを作成する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 5d94625e3eb121e556b28038cf59626be1332966
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455807"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Azure Functions における Azure Queue storage の出力バインド

Azure Functions は、出力バインドを設定することによって、新しい Azure Queue storage メッセージを作成できます。

セットアップと構成の詳細については、[概要](./functions-bindings-storage-queue.md)に関するページをご覧ください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、受け取った HTTP 要求ごとにキュー メッセージを作成する [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの HTTP トリガー バインディングと、バインディングを使用する [C# スクリプト (.csx)](functions-reference-csharp.md) コードを示しています。 この関数では、受け取った HTTP 要求ごとに **CustomQueueMessage** ペイロードを使用してキュー項目を作成します。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

単一のキュー メッセージを作成する C# スクリプト コードを次に示します。

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

一度で複数のメッセージを送信するには、`ICollector` または `IAsyncCollector` パラメーターを使用します。 HTTP 要求データを含む 1 つのメッセージと、ハードコーディングされた値を含む 1 つのメッセージという、複数のメッセージを送信する C# スクリプト コードを次に示します。

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 次の例は、HTTP 要求によってトリガーされたときにキュー メッセージを作成する Java 関数を示しています。

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Queue Storage に書き込まれる関数のパラメーター上で `@QueueOutput` 注釈を使用します。  パラメーターの型は `OutputBinding<T>` にする必要があります。`T` は POJO の Java の任意のネイティブ型です。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの HTTP トリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数では、受け取った HTTP 要求ごとにキュー項目を作成します。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

一度で複数のメッセージを送信するには、`myQueueItem` 出力バインドのメッセージ配列を定義します。 次の JavaScript コードは、受け取った HTTP 要求ごとにハードコーディングされた値を含む 2 つのキュー メッセージを送信します。

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次のコードの例は、HTTP によってトリガーされる関数からキュー メッセージを出力する方法を示しています。 `queue` の `type` がある構成セクションで、出力バインディングを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

このバインディング構成で、PowerShell 関数は `Push-OutputBinding` を使用してキュー メッセージを作成できます。 この例では、クエリ文字列または本文のパラメーターからメッセージが作成されます。

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

一度に複数のメッセージを送信するには、メッセージ配列を定義し、`Push-OutputBinding` を使用してキュー出力バインドにメッセージを送信します。

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

次の例では、ストレージ キューに 1 つの値と複数の値を出力する方法を示します。 *function.json* で必要な構成は、どちらでも同じです。

ストレージ キュー バインディングは *function.json* で定義され、そこで *type* は `queue` に設定されます。

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
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

キューに個々のメッセージを設定するには、 `set` メソッドに 1 つの値を渡します。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

キュー上で複数のメッセージを作成するには、パラメーターを適切なリスト型として宣言し、値の配列 (リスト型と一致する) を `set` メソッドに渡します。

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs) を使用します。

この属性は、`out` パラメーターまたは関数の戻り値に適用されます。 次の例のように、属性のコンストラクターはキューの名前を受け取ります。

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

完全な例については、「[出力 - 例](#example)」を参照してください。

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、「トリガー - 属性」をご覧ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`QueueOutput` 注釈を使用すると、関数の出力としてメッセージを書き込むことができます。 次の例は、キュー メッセージを作成する HTTP トリガー関数を示しています。

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| プロパティ    | 説明 |
|-------------|-----------------------------|
|`name`       | 関数シグネチャのパラメーター名を宣言します。 関数がトリガーされると、このパラメーターの値にはキュー メッセージの内容が含められます。 |
|`queueName`  | ストレージ アカウントのキュー名を宣言します。 |
|`connection` | ストレージ アカウントの接続文字列を示します。 |

`QueueOutput` 注釈に関連するパラメーターは、[OutputBinding\<T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) インスタンスとして型指定されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `Queue` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `queue` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `out` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のキューを表す変数の名前。 `$return` に設定して、関数の戻り値を参照します。|
|**queueName** |**QueueName** | キューの名前。 |
|**connection** | **接続** |このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。<br><br>たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "MyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列の代わりに[バージョン 5.x またはそれ以降の拡張機能](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)を使用している場合は、接続を定義する構成セクションへの参照を指定できます。 「[接続](./functions-reference.md#connections)」を参照してください。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Default

`out T paramName` などのメソッドのパラメーターを使用して、単一のキュー メッセージを書き込みます。 `out` パラメーターではなくメソッドの戻り値の型を使用することができます。`T` は次に示すいずれかの型の場合があります。

* JSON としてシリアル化可能なオブジェクト
* `string`
* `byte[]`
* [CloudQueueMessage] 

`CloudQueueMessage` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

C# と C# スクリプトで複数のキュー メッセージを書き込むには、次のいずれかの型を使用します。 

* `ICollector<T>` または `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>その他の型

[Storage 拡張機能の 5.0.0 またはそれ以降のバージョン](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)を使用するアプリでは、[Azure SDK for .NET](/dotnet/api/overview/azure/storage.queues-readme) の型を使用することもできます。 このバージョンでは、次の型を優先して、レガシ `CloudQueue` および `CloudQueueMessage` 型のサポートがなくなります。

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- 複数のキュー メッセージを書き込むための [QueueClient](/dotnet/api/azure.storage.queues.queueclient)

これらの型の使用例については、[拡張機能の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)に関するページを参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

### <a name="default"></a>Default

`out T paramName` などのメソッドのパラメーターを使用して、単一のキュー メッセージを書き込みます。 `paramName` は *function.json* の `name` プロパティで指定された値です。 `out` パラメーターではなくメソッドの戻り値の型を使用することができます。`T` は次に示すいずれかの型の場合があります。

* JSON としてシリアル化可能なオブジェクト
* `string`
* `byte[]`
* [CloudQueueMessage] 

`CloudQueueMessage` にバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

C# と C# スクリプトで複数のキュー メッセージを書き込むには、次のいずれかの型を使用します。 

* `ICollector<T>` または `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>その他の型

[Storage 拡張機能の 5.0.0 またはそれ以降のバージョン](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)を使用するアプリでは、[Azure SDK for .NET](/dotnet/api/overview/azure/storage.queues-readme) の型を使用することもできます。 このバージョンでは、次の型を優先して、レガシ `CloudQueue` および `CloudQueueMessage` 型のサポートがなくなります。

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- 複数のキュー メッセージを書き込むための [QueueClient](/dotnet/api/azure.storage.queues.queueclient)

これらの型の使用例については、[拡張機能の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)に関するページを参照してください。

# <a name="java"></a>[Java](#tab/java)

[QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) 注釈を使用して関数からイベント ハブ メッセージを出力するには、次の 2 つのオプションがあります。

- **戻り値**:関数自体に注釈を適用すると、関数の戻り値がキュー メッセージとして永続化されます。

- **命令型**:メッセージ値を明示的に設定するには、[`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 型の特定のパラメーターに注釈を適用します。 ここで、`T` は POJO または任意のネイティブ Java 型です。 この構成では、`setValue` メソッドに値を渡すと、その値がキュー メッセージとして保持されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

出力キュー項目は、`context.bindings.<NAME>` を介して使用できます。ここで、`<NAME>` は *function.json* で定義されている名前と一致します。 キュー項目ペイロードには、文字列または JSON のシリアル化可能なオブジェクトを使用できます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

キュー メッセージへの出力は `Push-OutputBinding` 経由で利用できます。この場合、*function.json* ファイルのバインドの `name` パラメーターで指定された名前と一致する引数を渡します。

# <a name="python"></a>[Python](#tab/python)

関数からキュー メッセージを出力するには、次の 2 つのオプションがあります。

- **戻り値**:*function.json* 内の `name` プロパティを `$return` に設定します。 この構成では、関数の戻り値は Queue storage メッセージとして永続化されます。

- **命令型**:[Out](/python/api/azure-functions/azure.functions.out) 型として宣言されたパラメーターの [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) メソッドに値を渡します。 `set` に渡された値は、Queue storage メッセージとして永続化されます。

---

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド |  リファレンス |
|---|---|
| キュー | [キュー エラー コード](/rest/api/storageservices/queue-service-error-codes) |
| BLOB、テーブル、キュー | [ストレージ エラー コード](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB、テーブル、キュー |  [トラブルシューティング](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>次のステップ

- [キュー ストレージのデータが変更されたときに関数を実行する (トリガー)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
