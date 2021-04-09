---
title: Azure Functions における Azure Blob Storage の出力バインド
description: Azure 関数に Azure Blob Storage の出力バインド データを提供する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: eaa8a4c600864f636d49813d415621d46130fff7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381659"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Functions における Azure Blob Storage の出力バインド

出力バインドを使用すると、Azure 関数内で Blob Storage データを変更および削除できます。

セットアップと構成の詳細については、[概要](./functions-bindings-storage-blob.md)を参照してください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、1 つの BLOB トリガーと 2 つの出力 BLOB バインディングを使用する[C# 関数](functions-dotnet-class-library.md)です。 関数は、*sample-images* コンテナーのイメージ BLOB の作成によってトリガーされます。 イメージ BLOB の小規模および中規模サイズのコピーを作成します。

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

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

* [HTTP トリガー、OutputBinding を使用する](#http-trigger-using-outputbinding-java)
* [キュー トリガー、関数戻り値を使用する](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP トリガー、OutputBinding を使用する (Java)

 次の例では、Java 関数が `HttpTrigger` 注釈を利用し、BLOB ストレージ コンテナーのファイル名を含むパラメーターを受け取ります。 `BlobInput` 注釈によってファイルが読み取られ、その内容が `byte[]` として関数に渡されます。 `BlobOutput` 注釈が `OutputBinding outputItem` にバインドされます。OutputBinding が関数によって使用され、構成済みのストレージ コンテナーに入力 BLOB の内容が書き込まれます。

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>キュー トリガー、関数戻り値を使用する (Java)

 次の例では、Java 関数が `QueueTrigger` 注釈を利用し、BLOB ストレージ コンテナーのファイル名を含むメッセージを受け取ります。 `BlobInput` 注釈によってファイルが読み取られ、その内容が `byte[]` として関数に渡されます。 `BlobOutput` 注釈が関数戻り値にバインドされます。この関数戻り値がランタイムによって使用され、構成済みのストレージ コンテナーに入力 BLOB の内容が書き込まれます。

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 [Java 関数ランタイム ライブラリ ](/java/api/overview/azure/functions/runtime) で、その値が Blob Storage のオブジェクトに書き込まれる関数のパラメーター上で `@BlobOutput` 注釈を使用します。  パラメーターの型は `OutputBinding<T>` にする必要があります。T は POJO の Java の任意のネイティブ型です。

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

次の例は、[PowerShell 関数](functions-reference-powershell.md)からの出力として、受信 BLOB のコピーを作成する方法を示しています。

関数の構成ファイル (*function.json*) で、`trigger` メタデータ プロパティを使用して `path` プロパティ内の出力 BLOB 名を指定します。

> [!NOTE]
> 無限ループを回避するために、入力パスと出力パスが異なることを確認してください。

```json
{
  "bindings": [
    {
      "name": "myInputBlob",
      "path": "data/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in",
      "type": "blobTrigger"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "data/copy/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

PowerShell コードを次に示します。

```powershell
# Input bindings are passed in via param block.
param([byte[]] $myInputBlob, $TriggerMetadata)
Write-Host "PowerShell Blob trigger function Processed blob Name: $($TriggerMetadata.Name)"
Push-OutputBinding -Name myOutputBlob -Value $myInputBlob
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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)を使用します。

次の例で示すように、属性のコンストラクターは、BLOB へのパスと、読み取りまたは書き込みを示す `FileAccess` パラメーターを受け取ります。

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 この属性と共にバイト配列を使用する場合は、`dataType` を `binary` に設定します。 詳細については、「[出力 - 例](#example)」を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

完全な例については、「[出力 - 例](#example)」を参照してください。

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、[トリガー - 属性](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)をご覧ください。

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `Blob` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `blob` に設定する必要があります。 |
|**direction** | 該当なし | 出力バインディングの場合は `out` に設定する必要があります。 例外は、[使用方法](#usage)のセクションに記載しています。 |
|**name** | 該当なし | 関数コード内の BLOB を表す変数の名前。  `$return` に設定して、関数の戻り値を参照します。|
|**path** |**BlobPath** | BLOB コンテナーへのパス。 |
|**connection** |**Connection**| このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列は、[BLOB のみのストレージ アカウント](../storage/common/storage-account-overview.md#types-of-storage-accounts)ではなく汎用ストレージ アカウントに対するものである必要があります。<br><br>[バージョン 5.x またはそれ以降の拡張機能](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)を使用している場合は、接続文字列の代わりに、接続を定義する構成セクションへの参照を指定できます。 「[接続](./functions-reference.md#connections)」を参照してください。|
|該当なし | **Access (アクセス)** | 読み取りと書き込みのどちらを行うかを示します。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 この属性と共にバイト配列を使用する場合は、`dataType` を `binary` に設定します。 詳細については、「[出力 - 例](#example)」を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<BINDING_NAME>` を使用して BLOB データにアクセスします。バインド名は _function.json_ ファイルで定義されています。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

_function.json_ ファイルのバインドの name パラメーターで指定されている名前と一致するパラメーターを使用して、BLOB データにアクセスします。

# <a name="python"></a>[Python](#tab/python)

関数のパラメーターを次の型で宣言し、BLOB ストレージに書き込むことができます。

* `func.Out(str)` は文字列
* `func.Out(func.InputStream)` はストリーム

詳細については、「[出力 - 例](#example)」を参照してください。

---

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド |  リファレンス |
|---|---|
| BLOB | [BLOB エラー コード](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB、テーブル、キュー |  [ストレージ エラー コード](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB、テーブル、キュー |  [トラブルシューティング](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>次のステップ

- [Blob Storage データの変更時に関数を実行する](./functions-bindings-storage-blob-trigger.md)
- [関数が実行されたときに Blob Storage データを読み取る](./functions-bindings-storage-blob-input.md)
