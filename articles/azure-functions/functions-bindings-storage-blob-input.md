---
title: Azure Functions における Azure Blob Storage の入力バインド
description: Azure 関数に Azure Blob Storage の入力バインド データを提供する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: cd69e89954fab2256ffc7c23e22d3b8d44ab2a11
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455875"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Azure Functions における Azure Blob Storage の入力バインド

入力バインドを使用すると、Azure 関数への入力として BLOB Storage データを読み取ることができます。

セットアップと構成の詳細については、[概要](./functions-bindings-storage-blob.md)に関する記事を参照してください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、キュー トリガーと入力 BLOB バインディングを使用する [C# 関数](functions-dotnet-class-library.md)です。 キュー メッセージには BLOB の名前が含まれ、関数は BLOB のサイズをログに記録します。

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

<!--Same example for input and output. -->

次の例は、*function.json* ファイルの BLOB 入出力バインディングと、バインディングを使用する [C# スクリプト (.csx)](functions-reference-csharp.md) コードを示しています。 関数は、テキスト BLOB のコピーを作成します。 関数は、コピーする BLOB の名前を含むキュー メッセージによってトリガーされます。 新しい BLOB の名前は *{originalblobname}-Copy* です。

*function.json* ファイルでは、`queueTrigger` メタデータ プロパティは `path` プロパティ内の BLOB 名の指定に使用されます。

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

このセクションには、次の例が含まれています。

* [HTTP トリガー、クエリ文字列から BLOB を検索する](#http-trigger-look-up-blob-name-from-query-string)
* [キュー トリガー、キュー メッセージから BLOB 名前を受け取る](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP トリガー、クエリ文字列から BLOB を検索する

 次の例では、Java 関数が `HttpTrigger` 注釈を利用し、BLOB ストレージ コンテナーのファイル名を含むパラメーターを受け取ります。 `BlobInput` 注釈によってファイルが読み取られ、その内容が `byte[]` として関数に渡されます。

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>キュー トリガー、キュー メッセージから BLOB 名前を受け取る

 次の例では、Java 関数が `QueueTrigger` 注釈を利用し、BLOB ストレージ コンテナーのファイル名を含むメッセージを受け取ります。 `BlobInput` 注釈によってファイルが読み取られ、その内容が `byte[]` として関数に渡されます。

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が BLOB に由来するパラメーター上で `@BlobInput` 注釈を使用します。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

次の例は、*function.json* ファイルの BLOB 入出力バインドと、そのバインドを使用する [JavaScript](functions-reference-node.md) コードを示しています。 関数は、BLOB のコピーを作成します。 関数は、コピーする BLOB の名前を含むキュー メッセージによってトリガーされます。 新しい BLOB の名前は *{originalblobname}-Copy* です。

*function.json* ファイルでは、`queueTrigger` メタデータ プロパティは `path` プロパティ内の BLOB 名の指定に使用されます。

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、_function.json_ ファイルで定義されている BLOB 入力バインドを示しています。このバインドにより、受信 BLOB データを [PowerShell](functions-reference-powershell.md) 関数で使用できるようになります。

json 構成を次に示します。

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

関数コードを次に示します。

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

次の例は、*function.json* ファイルの BLOB 入出力バインドと、バインドを使用する [Python スクリプト](functions-reference-python.md) コードを示しています。 関数は、BLOB のコピーを作成します。 関数は、コピーする BLOB の名前を含むキュー メッセージによってトリガーされます。 新しい BLOB の名前は *{originalblobname}-Copy* です。

*function.json* ファイルでは、`queueTrigger` メタデータ プロパティは `path` プロパティ内の BLOB 名の指定に使用されます。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

使用されるバインドは、`dataType` プロパティによって決まります。 次の値は、さまざまなバインド方法をサポートするために使用できます。

| バインド値 | Default | 説明 | 例 |
| --- | --- | --- | --- |
| `undefined` | Y | リッチ バインドを使用します | `def main(input: func.InputStream)` |
| `string` | × | 汎用バインドを使用し、入力型を `string` としてキャストします | `def main(input: str)` |
| `binary` | × | 汎用バインドを使用し、入力 BLOBを `bytes` Python オブジェクトとしてキャストします | `def main(input: bytes)` |

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)を使用します。

次の例で示すように、属性のコンストラクターは、BLOB へのパスと、読み取りまたは書き込みを示す `FileAccess` パラメーターを受け取ります。

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、[トリガーに関するページの「属性と注釈」](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)をご覧ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`@BlobInput` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 この属性と共にバイト配列を使用する場合は、`dataType` を `binary` に設定します。 詳細については、「[入力 - 例](#example)」を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `Blob` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `blob` に設定する必要があります。 |
|**direction** | 該当なし | `in` に設定する必要があります。 例外は、[使用方法](#usage)のセクションに記載しています。 |
|**name** | 該当なし | 関数コード内の BLOB を表す変数の名前。|
|**path** |**BlobPath** | BLOB へのパス。 |
|**connection** |**接続**| このバインドに使用する[ストレージ接続文字列](../storage/common/storage-configure-connection-string.md)を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列は、[BLOB のみのストレージ アカウント](../storage/common/storage-account-overview.md#types-of-storage-accounts)ではなく汎用ストレージ アカウントに対するものである必要があります。<br><br>[バージョン 5.x またはそれ以降の拡張機能](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)を使用している場合は、接続文字列の代わりに、接続を定義する構成セクションへの参照を指定できます。 「[接続](./functions-reference.md#connections)」を参照してください。|
|**dataType**| 該当なし | 動的に型指定される言語の場合は、基になるデータ型を指定します。 設定可能な値は、`string`、`binary`、または `stream` です。 詳細については、[トリガーとバインドの概念](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions)に関する記事を参照してください。 |
|該当なし | **Access (アクセス)** | 読み取りと書き込みのどちらを行うかを示します。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobInput` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 この属性と共にバイト配列を使用する場合は、`dataType` を `binary` に設定します。 詳細については、「[入力 - 例](#example)」を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`<NAME>` が *function.json* で定義されている値と一致する `context.bindings.<NAME>` を使用して BLOB データにアクセスします。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

_function.json_ ファイルのバインドの name パラメーターで指定されている名前と一致するパラメーターを使用して、BLOB データにアクセスします。

# <a name="python"></a>[Python](#tab/python)

[InputStream](/python/api/azure-functions/azure.functions.inputstream) に型指定したパラメーターを使用して BLOB データにアクセスします。 詳細については、「[入力 - 例](#example)」を参照してください。

---

## <a name="next-steps"></a>次のステップ

- [Blob Storage データが変更されたときに関数を実行する](./functions-bindings-storage-blob-trigger.md)
- [関数から BLOB Storage データを書き込む](./functions-bindings-storage-blob-output.md)
