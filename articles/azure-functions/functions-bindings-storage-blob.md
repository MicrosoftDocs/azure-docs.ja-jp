---
title: "Azure Functions における Blob Storage のバインディング | Microsoft Docs"
description: "Azure Functions で Azure Storage のトリガーとバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b123578dbac48018f674f85ec923e4c6e65fb9f8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions における Blob Storage のバインディング
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure BLOB のストレージ バインドを構成したり、操作したりする方法について説明します。 Azure Functions は、Azure BLOB ストレージのトリガー、入力、出力のバインドをサポートしています。 すべてのバインドで使用できる機能については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [BLOB 専用のストレージ アカウント](../storage/common/storage-create-storage-account.md#blob-storage-accounts)はサポートされていません。 Blob ストレージのトリガーとバインドには、汎用のストレージ アカウントが必要です。 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>BLOB ストレージ トリガーとバインド

Azure BLOB ストレージ トリガーを使用すると、新規または更新された BLOB が検出されたときに関数コードが呼び出されます。 BLOB のコンテンツは、関数への入力として提供されます。

Functions ポータルの [**統合**] タブを使用して、BLOB ストレージ トリガーを定義します。 ポータルは、*function.json* の**バインド** セクションに次の定義を作成します。

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

BLOB 入力バインドと出力バインドは、`blob` のバインド型を使用して定義されます。

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* `path` プロパティは、バインド式とフィルター パラメーターをサポートしています。 「[名前のパターン](#pattern)」を参照してください。
* `connection` プロパティには、ストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントを選択すると、[**統合**] タブの標準エディターによってこのアプリ設定が構成されます。

> [!NOTE]
> 従量課金プランで BLOB トリガーを使用していると、関数アプリがアイドル状態になったあと、新しい BLOB の処理が最大で 10 分遅延する場合があります。 関数アプリが実行されると、BLOB は直ちに処理されます。 この初期段階の遅延を避けるために、次のオプションのいずれかを検討してください。
> - 常時接続が有効な App Service プランを使用する。
> - BLOB 名を含むキュー メッセージなど、別のメカニズムを使用して BLOB 処理をトリガーする。 例については、「[BLOB 入力バインドを持つキュー トリガー](#input-sample)」を参照してください。

<a name="pattern"></a>

### <a name="name-patterns"></a>名前のパターン
BLOB 名のパターンを `path` プロパティで指定できます。このパターンをフィルターまたはバインド式にすることができます。 「[バインド式とパターン](functions-triggers-bindings.md#binding-expressions-and-patterns)」を参照してください。

たとえば、文字列 "original" で始まる BLOB にフィルターを適用するには、次の定義を使用します。 このパスでは、*input* コンテナーの *original-Blob1.txt* という名前の BLOB を探し、関数コード内の `name` 変数の値は `Blob1` です。

```json
"path": "input/original-{name}",
```

BLOB ファイル名と拡張子を別々にバインドするには、2 つのパターンを使用します。 このパスでも、*original-Blob1.txt* という名前の BLOB を探し、関数コード内の `blobname` 変数および `blobextension` 変数の値は *original-Blob1* と *txt* です。

```json
"path": "input/{blobname}.{blobextension}",
```

BLOB のファイルの種類を制限するには、ファイル拡張子に固定値を使用します。 たとえば、.png ファイルでのみトリガーするには、次のパターンを使用します。

```json
"path": "samples/{name}.png",
```

中かっこは、名前のパターン内の特殊文字です。 名前に中かっこがある BLOB 名を指定する場合は、中かっこを 2 つ使用して中かっこをエスケープできます。 次の例では、*images* コンテナーの *{20140101}-soundfile.mp3* という名前の BLOB を探し、関数コード内の `name` 変数の値は *soundfile.mp3* です。 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>トリガー メタデータ

BLOB トリガーは、いくつかのメタデータ プロパティを提供します。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 値は、[CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) と同じセマンティクスを持ちます。

- **BlobTrigger**。 「 `string`」と入力します。 トリガーする BLOB パス
- **Uri**。 「 `System.Uri`」と入力します。 プライマリ ロケーションの BLOB URI。
- **Properties**。 「 `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`」と入力します。 Blob のシステム プロパティ。
- **Metadata**。 「 `IDictionary<string,string>`」と入力します。 BLOB のユーザー定義メタデータ。

<a name="receipts"></a>

### <a name="blob-receipts"></a>BLOB の配信確認メッセージ
Azure Functions ランタイムでは、BLOB トリガー関数は、同一の新規または更新された BLOB について 2 回以上呼び出されることはありません。 特定の BLOB バージョンが処理されているかどうかを判断するために、*BLOB の配信確認メッセージ*が維持されます。

Azure Functions では、BLOB の配信確認メッセージは (アプリ設定 `AzureWebJobsStorage` で指定した) 関数アプリの Azure ストレージ アカウント内の *azure-webjobs-hosts* というコンテナーに格納されます。 BLOB の配信確認メッセージには次の情報が含まれています。

* トリガーされた関数 ("*&lt;関数アプリ名>*.Functions.*&lt;関数名>*"。たとえば、"MyFunctionApp.Functions.CopyBlob")
* コンテナーの名前
* BLOB の種類 ("BlockBlob" か "PageBlob")
* BLOB の名前
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

BLOB を強制的に再処理する場合は、*azure-webjobs-hosts* コンテナーからその BLOB の配信確認メッセージを手動で削除します。

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>有害 BLOB の処理
指定された BLOB に対する BLOB トリガー関数が失敗すると、Azure Functions は既定で最大 5 回その関数を再試行します。 

試行が 5 回とも失敗した場合、Azure Functions は *webjobs-blobtrigger-poison* という名前のストレージ キューにメッセージを追加します。 有害な BLOB のキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (形式: *&lt;Function App 名>*.Functions.*&lt;関数名>*)
* BLOB の種類 ("BlockBlob" か "PageBlob")
* コンテナー名
* BlobName
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>大規模なコンテナーをポーリングする BLOB
監視対象の BLOB コンテナーに 10,000 を超える BLOB が含まれる場合は、Functions ランタイムによりログ ファイルがスキャンされ、新規または変更された BLOB が監視されます。 このプロセスはリアルタイムではありません。 関数は、BLOB が作成されてから数分以上経過しないとトリガーされない可能性があります。 また、[ ストレージ ログは "ベスト エフォート"](/rest/api/storageservices/About-Storage-Analytics-Logging) ベースで作成されます。 そのため、すべてのイベントがキャプチャされる保証はありません。 ある条件下では、ログが欠落する可能性があります。 より高速で信頼性の高い BLOB 処理が必要な場合は、BLOB 作成時に[キュー メッセージ](../storage/queues/storage-dotnet-how-to-use-queues.md)を作成することを検討してください。 次に、BLOB トリガーの代わりに[キュー トリガー](functions-bindings-storage-queue.md)を使用して BLOB を処理します。

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>BLOB トリガーおよび入力バインドの使用
.NET 関数では、`Stream paramName` のようなメソッド パラメーターを使用して BLOB データにアクセスします。 ここでは、`paramName` は[トリガー構成](#trigger)で指定した値です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力 BLOB データにアクセスします。

.NET の場合、以下のリストのいずれかの型にバインドできます。 入力バインドとして使用する場合、これらの型の一部は、*function.json* で `inout` バインド方向を必要とします。 この方向は標準のエディターではサポートされていないため、高度なエディターを使用する必要があります。

* `TextReader`
* `Stream`
* `ICloudBlob` ("inout" バインド方向が必要)
* `CloudBlockBlob` ("inout" バインド方向が必要)
* `CloudPageBlob` ("inout" バインド方向が必要)
* `CloudAppendBlob` ("inout" バインド方向が必要)

テキスト BLOB がある場合は、.NET `string` 型にバインドすることもできます。 BLOB 全体のコンテンツがメモリに読み込まれるため、これが推奨されるのは、BLOB のサイズが小さい場合のみです。 通常、`Stream` 型または `CloudBlockBlob` 型の使用が推奨されます。

## <a name="trigger-sample"></a>トリガー サンプル
BLOB ストレージ トリガーを定義する、次の function.json があるとします。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

監視対象のコンテナーに追加される各 BLOB の内容を記録する、言語固有のサンプルを参照してください。

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>C# での BLOB トリガー例 #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Node.js でのトリガー例

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>BLOB 出力バインドの使用

.NET 関数では、関数シグネチャの `out string` パラメーターを使用するか、次のリストのいずれかの型を使用する必要があります。 Node.js 関数の場合、`context.bindings.<name>` を使用して出力 BLOB にアクセスします。

.NET 関数の場合、次のいずれかの型に出力できます。

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>BLOB 入力と出力を持つキュー トリガーのサンプル
[Queue Storage トリガー](functions-bindings-storage-queue.md)、BLOB ストレージ入力、BLOB ストレージ出力を定義する、次の function.json があるとします。 `queueTrigger` メタデータ プロパティが BLOB 入力および出力 `path` プロパティで使用されていることにご注目ください。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

入力 BLOB を出力 BLOB にコピーする言語固有のサンプルを参照してください。

* [C#](#incsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>C# での BLOB バインドの例 #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Node.js での BLOB バインドの例

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


