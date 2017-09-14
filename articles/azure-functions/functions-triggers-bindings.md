---
title: "Azure Functions でのトリガーとバインドの使用 | Microsoft Docs"
description: "Azure Functions で、トリガーとバインドを使用してコード実行をオンライン イベントおよびクラウドベース サービスに接続する方法について説明します。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: cc41debb2523df77be4db05817a4c7ac55604439
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017

---

# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions でのトリガーとバインドの概念
Azure Functions では、*トリガー*と*バインド*を使用して、Azure やその他のサービスで発生したイベントに応答するコードを記述できます。 この記事では、サポートされているすべてのプログラミング言語でのトリガーとバインドの概念的な概要を説明します。 ここでは、すべてのバインドに共通する機能について説明します。

## <a name="overview"></a>概要

トリガーとバインドは、関数を呼び出す方法と、その関数で処理するデータを定義する宣言型の方法です。 *トリガー*は、関数を呼び出す方法を定義します。 1 つの関数には 1 つのトリガーしか含められません。 トリガーにはデータが関連付けられていて、通常そのデータは、その関数をトリガーしたペイロードです。 

入出力*バインド*によって、コード内からデータに接続する宣言型の方法が提供されます。 トリガーと同様に、関数の構成に接続文字列やその他のプロパティを指定します。 バインドは省略可能で、関数は複数の入出力バインドを持つことができます。 

トリガーとバインドを使用すると、より汎用性があり、操作するサービスの詳細をハードコードしないコードを記述できます。 サービスからのデータは、単純に関数コードの入力値になります。 別のサービスにデータを出力する (Azure Table Storage での新しい行の作成など) には、メソッドの戻り値を使用します。 また、複数の値を出力する必要がある場合は、ヘルパー オブジェクトを使用します。 トリガーとバインドには **name** プロパティがあります。このプロパティは、バインドにアクセスするためにコード内で使用する識別子です。

トリガーとバインドは、Azure Functions ポータルの **[統合]** タブで構成できます。 この UI は内部的に、function ディレクトリ内の *function.json* という名前のファイルを変更します。 **詳細エディター**に変更すると、このファイルを編集できます。

次の表に、Azure Functions でサポートされているトリガーとバインドを示します。 

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

### <a name="example-queue-trigger-and-table-output-binding"></a>例: キュー トリガーとテーブルの出力バインド

Azure Queue Storage に新しいメッセージが表示されるたびに、Azure Table Storage に新しい行を書き込みたいと仮定します。 このシナリオは、Azure キュー トリガーとテーブルの出力バインドを使用して実装できます。 

キュー トリガーには、**[統合]** タブ内の以下の情報が必要です。

* キューのストレージ アカウント接続文字列を含むアプリ設定の名前
* キュー名
* `order` など、キュー メッセージの内容を読み取るためのコード内の識別子

Azure Table Storage に書き込むには、以下の詳細を含む出力バインドを使用します。

* テーブルのストレージ アカウント接続文字列を含む、アプリ設定の名前
* テーブル名
* 関数から出力項目または戻り値を作成するための、コード内の識別子

バインドでは、*function.json* がサービス シークレットを含まないというベスト プラクティスを適用する、接続文字列のアプリ設定を使用します。

その後、指定した識別子を使用して、コードで Azure Storage と統合します。

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

次に、上記のコードに対応する *function.json* を示します。 関数の実装言語に関係なく、同じ構成を使用できることに注意してください。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```
Azure ポータルで *function.json* の内容を表示して編集するには、関数の **[統合]** タブにある **[詳細エディター]** オプションをクリックします。

Azure Storage との統合のコード例と詳細については、「[Azure Functions における Azure Storage のトリガーとバインド](functions-bindings-storage.md)」を参照してください。

### <a name="binding-direction"></a>バインドの方向

すべてのトリガーとバインドには `direction` プロパティがあります。

- トリガーの場合、方向は常に `in` です
- 入出力バインドは `in` と `out` を使用します
- 一部のバインドは、特殊な方向の `inout` をサポートしてします。 `inout` を使用する場合、**[統合]** タブで使用できるのは**詳細エディター**のみです。

## <a name="using-the-function-return-type-to-return-a-single-output"></a>単一出力を返すための関数の戻り値の型の使用

前の例は、関数の戻り値を使用してバインドに出力を提供する方法を示しています。これは、特殊な名前のパラメーター `$return` を使用して実現されます。 (このパラメーターは、C#、JavaScript、F# などの、戻り値がある言語でのみサポートされています。)1 つの関数に複数の出力バインドがある場合は、1 つの出力バインドに対してのみ `$return` を使用します。 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

以下の例は、C#、JavaScript、および F# で、戻り値の型が出力バインドでどのように使用されるかを示しています。

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>dataType プロパティのバインド

.NET では、型を使用して、入力データのデータ型を定義します。 たとえば、キュー トリガーのテキストにバインドするには `string` を、バイナリとして読み取るにはバイト配列を使用します。

JavaScript など、動的に型指定された言語については、バインディングの定義で `dataType` プロパティを使用します。 たとえば、バイナリ形式で HTTP 要求のコンテンツを読み取るには、`binary` 型を使用します。

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` のその他のオプションは、`stream` と `string` です。

## <a name="resolving-app-settings"></a>アプリケーション設定の解決
ベスト プラクティスとしては、シークレットや接続文字列は、構成ファイルではなくアプリ設定を使用して管理する必要があります。 これにより、これらのシークレットへのアクセスが制限され、パブリックなソース管理レポジトリに *function.json* を安全に格納できるようにします。

環境に基づいて構成を変更するときには、アプリ設定も常に役立ちます。 たとえば、テスト環境で、別のキューや Blob Storage コンテナーを監視することもできます。

アプリ設定は、`%MyAppSetting%` のように値がパーセント記号で囲まれたときには常に解決されます。 トリガーやバインドの `connection` プロパティは特殊ケースであり、値はアプリ設定として自動的に解決されることに注意してください。 

次の例は、アプリ設定 `%input-queue-name%` を使用してトリガーの対象となるキューを定義するキュー トリガーです。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

## <a name="trigger-metadata-properties"></a>トリガーのメタデータ プロパティ

トリガーによって提供されるデータ ペイロード (関数をトリガーしたキュー メッセージなど) に加え、多くのトリガーは追加のメタデータ値を提供します。 これらの値は、C# および F# で入力パラメーターとして使用したり、JavaScript で `context.bindings` オブジェクトのプロパティとして使用したりできます。 

たとえば、キュー トリガーは以下のプロパティをサポートしています。

* QueueTrigger - 有効な文字列の場合はメッセージの内容をトリガーします
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

各トリガーのメタデータ プロパティの詳細については、対応するリファレンス トピックを参照してください。 ドキュメントは、ポータルの **[統合]** タブの、バインド構成領域の下の **[ドキュメント]** セクションでも参照できます。  

たとえば、Blob トリガーには一定の遅延があるため、キュー トリガーを使用して関数を実行できます ([Blob Storage トリガー](functions-bindings-storage-blob.md#storage-blob-trigger)に関するページを参照)。 キュー メッセージにはトリガーする対象の Blob ファイル名が含まれます。 `queueTrigger` メタデータ プロパティを使用する場合は、この動作をすべて、コードではなく構成に指定できます。

```json
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
```

次のセクションで説明するように、別のバインドの*バインド式*でも、トリガーのメタデータ プロパティを使用できます。

## <a name="binding-expressions-and-patterns"></a>バインド式とパターン

トリガーとバインドの最も強力な機能の 1 つは、*バインド式*です。 バインド内にパターン式を定義して、それを他のバインドやコードで使用することができます。 トリガーのメタデータは、前のセクションのサンプルに示したように、バインド式でも使用できます。

たとえば、**[新しい関数]** ページの **Image Resizer** テンプレートのように、特定の Blob Storage コンテナー内のイメージのサイズを変更するとします。 **[新しい関数]** -> [言語] として **[C#]** -> [シナリオ]として **[サンプル]** -> **ImageResizer CSharp** の順に選択します。 

*function.json* の定義は次のようになります。

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

`filename` パラメーターは、Blob トリガーの定義と Blob 出力バインドの両方で使用されることに注意してください。 このパラメーターは、関数コードでも使用できます。

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->


### <a name="random-guids"></a>ランダム GUID
Azure Functions には、`{rand-guid}` バインド式を使用してバインドに GUID を生成するための便利な構文があります。 次の例では、これを使用して一意の Blob 名を生成しています。 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>現在の時刻

バインド式 `DateTime` を使用できます。これは、`DateTime.UtcNow` に解決されます。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>バインド式でのカスタム入力プロパティへのバインド

バインド式は、トリガー ペイロード自体に定義されているプロパティも参照できます。 たとえば、webhook で提供されるファイル名から Blob Storage ファイルに動的にバインドすることもできます。

たとえば、次の *function.json* では、トリガー ペイロードの `BlobName` という名前のプロパティを使用しています。

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson",
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

C# および F# でこれを実現するには、トリガー ペイロードで逆シリアル化されるフィールドを定義する POCO を定義する必要があります。

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript では、JSON のシリアル化解除は自動的に実行され、プロパティを直接使用することができます。

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

## <a name="configuring-binding-data-at-runtime"></a>実行時のデータ バインドの構成

C# および他の .NET 言語では、*function.json* の宣言型のバインドではなく命令型のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 詳細については、C# 開発者用リファレンスの[命令型のバインドによる実行時のバインド](functions-reference-csharp.md#imperative-bindings)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
特定のバインドの詳細については、以下の記事を参照してください。

- [HTTP と webhook](functions-bindings-http-webhook.md)
- [Timer](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [イベント ハブ](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Cosmos DB](functions-bindings-documentdb.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [外部ファイル](functions-bindings-external-file.md)

