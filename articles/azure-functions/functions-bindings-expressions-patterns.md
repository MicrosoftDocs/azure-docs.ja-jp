---
title: Azure Functions のバインド式とパターン
description: 一般的なパターンに基づいてさまざまな Azure Functions バインド式を作成する方法について学習します。
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 7245b0c0fb1e96959ef5dca4992cf52a38accb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920292"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions のバインド式のパターン

[トリガーとバインド](./functions-triggers-bindings.md)の最も強力な機能の 1 つは、"*バインド式*" です。 *function.json* ファイルおよび関数パラメーターとコードでは、さまざまなソースの値に解決される式を使用できます。

ほとんどの式は、それを中かっこで囲むことによって識別されます。 たとえば、キュー トリガー関数では、`{queueTrigger}` はキュー メッセージ テキストに解決されます。 BLOB 出力バインディングの `path` プロパティが `container/{queueTrigger}` であり、その関数がキュー メッセージ `HelloWorld` によってトリガーされる場合は、`HelloWorld` という名前の BLOB が作成されます。

バインディング式の種類

* [アプリ設定](#binding-expressions---app-settings)
* [トリガー ファイル名](#trigger-file-name)
* [トリガー メタデータ](#trigger-metadata)
* [JSON ペイロード](#json-payloads)
* [新しい GUID](#create-guids)
* [現在の日付と時刻](#current-time)

## <a name="binding-expressions---app-settings"></a>バインディング式 - アプリ設定

ベスト プラクティスとしては、シークレットや接続文字列は、構成ファイルではなくアプリ設定を使用して管理する必要があります。 これにより、これらのシークレットへのアクセスが制限され、*function.json* などのファイルをパブリックなソース管理リポジトリに格納することが安全になります。

環境に基づいて構成を変更するときには、アプリ設定も常に役立ちます。 たとえば、テスト環境で、別のキューや Blob Storage コンテナーを監視することもできます。

アプリ設定のバインディング式は、他のバインディング式とはさまざまに区別されます。これらは中かっこではなく、パーセント記号で囲まれます。 たとえば、BLOB 出力バインディング パスが `%Environment%/newblob.txt` であり、`Environment` アプリ設定値が `Development` である場合は、BLOB が `Development` コンテナー内に作成されます。

関数がローカルに実行されている場合、アプリ設定値は *local.settings.json* ファイルから来ます。

> [!NOTE]
> トリガーとバインディングの `connection` プロパティは特殊なケースであり、値はアプリ設定 (パーセント記号なし) として自動的に解決されます。 

次の例は、アプリ設定 `%input_queue_name%` を使用してトリガーの対象となるキューを定義する Azure Queue Storage トリガーです。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input_queue_name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

クラス ライブラリでも同じ方法を使用できます。

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input_queue_name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>トリガー ファイル名

BLOB トリガーの `path` は、他のバインディングや関数コード内のトリガー BLOB の名前を参照するために使用できるパターンにすることができます。 このパターンにはまた、どの BLOB が関数呼び出しをトリガーできるかを指定するフィルター条件を含めることもできます。

たとえば、次の BLOB トリガーのバインディングでは、`path` パターンは `sample-images/{filename}` です。これは、`filename` という名前のバインディング式を作成します。

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

その後、出力バインディングで式 `filename` を使用して、作成される BLOB の名前を指定できます。

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

関数コードは、`filename` をパラメーター名として使用して、この同じ値にアクセスできます。

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

バインド式とパターンを使用するのと同じ機能がクラス ライブラリの属性に適用されます。 次の例では、属性コンストラクター パラメーターは前の *function.json* の例と同じ `path` 値です。 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

また、ファイル名の一部のための式を作成することもできます。 次の例の関数は、パターン (`anyname-anyfile.csv`) に一致するファイル名でのみトリガーされます。

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

式を使用する方法および BLOB パス文字列内のパターンの詳細については、[ストレージ BLOB バインディングのリファレンス](functions-bindings-storage-blob.md)に関するページを参照してください。

## <a name="trigger-metadata"></a>トリガー メタデータ

トリガーによって提供されるデータ ペイロード (関数をトリガーしたキュー メッセージの内容など) に加え、多くのトリガーは追加のメタデータ値を提供します。 これらの値は、C# および F# で入力パラメーターとして使用したり、JavaScript で `context.bindings` オブジェクトのプロパティとして使用したりできます。 

たとえば、Azure Queue Storage トリガーは、以下のプロパティをサポートしています。

* QueueTrigger - 有効な文字列の場合はメッセージの内容をトリガーします
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

これらのメタデータ値は、*function.json* ファイルのプロパティでアクセスできます。 たとえば、キュー トリガーを使用していて、読み取る BLOB の名前がキュー メッセージに含まれているとします。 次の例に示すように、*function.json* ファイルで、BLOB `path` プロパティの `queueTrigger` メタデータ プロパティを使用できます。

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
}
```

各トリガーのメタデータ プロパティの詳細については、対応するリファレンス記事を参照してください。 例については、[キュー トリガー メタデータ](functions-bindings-storage-queue-trigger.md#message-metadata)に関するセクションを参照してください。 ドキュメントは、ポータルの **[統合]** タブの、バインド構成領域の下の **[ドキュメント]** セクションでも参照できます。  

## <a name="json-payloads"></a>JSON ペイロード

トリガー ペイロードが JSON である場合は、同じ関数および関数コードで、他のバインディング用の構成内のそのプロパティを参照できます。

次の例は、JSON での BLOB 名を受信する Webhook 関数の *function.json* ファイルを示しています: `{"BlobName":"HelloWorld.txt"}`。 BLOB 入力バインディングが BLOB を読み取り、HTTP 出力バインディングが HTTP 応答で BLOB コンテンツを返します。 BLOB 入力バインディングが `BlobName` プロパティ (`"path": "strings/{BlobName}"`) を直接参照することによって BLOB 名を取得していることに注意してください。

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

C# および F# でこれが機能するには、次の例に示すように、逆シリアル化されるフィールドを定義するクラスが必要です。

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript では、JSON の逆シリアル化が自動的に実行されます。

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>ドット表記

JSON ペイロード内のいくつかのプロパティがプロパティを持つオブジェクトである場合は、ドット表記を使用してこれらを直接参照できます。 たとえば、次のような JSON があるとします。

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

`FileName` を `BlobName.FileName` として直接参照できます。 この JSON 形式では、前の例の `path` プロパティは次のようになります。

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

C# では、次の 2 つのクラスが必要になります。

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>GUID の作成

`{rand-guid}` バインド式は GUID を作成します。 `function.json` ファイル内の次の BLOB パスは、*50710cb5-84b9-4d87-9d83-a03d6976a682.txt* などの名前を持つ BLOB を作成します。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>現在の時刻

バインド式 `DateTime` は `DateTime.UtcNow` に解決されます。 `function.json` ファイル内の次の BLOB パスは、*2018-02-16T17-59-55Z.txt* などの名前を持つ BLOB を作成します。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>実行時のバインド

C# やその他の .NET 言語では、*function.json* の宣言型のバインドと属性ではなく、命令型のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 詳細については、[C# 開発者向けリファレンス](functions-dotnet-class-library.md#binding-at-runtime)または[C# スクリプト開発者向けリファレンス](functions-reference-csharp.md#binding-at-runtime)を参照してください。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure 関数の戻り値の使用](./functions-bindings-return-value.md)
