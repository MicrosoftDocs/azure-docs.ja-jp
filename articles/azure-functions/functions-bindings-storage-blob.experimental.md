---
title: Azure Functions における Azure Blob Storage のバインド
description: Azure Functions で Azure Blob Storage のトリガーとバインディングを使用する方法について説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: azure-functions
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: e25c69ecbac20a660eb09a6d1c8cb3a5a82895a2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097249"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Functions における Azure Blob Storage のバインド

この記事では、Azure Functions で Azure Blob Storage のバインディングを操作する方法について説明します。 Azure Functions は、BLOB のトリガー、入力、出力のバインディングをサポートしています。 この記事には、以下の各バインディングのセクションがあります。

* [BLOB トリガー](#trigger)
* [BLOB 入力バインディング](#input)
* [BLOB 出力バインディング](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> BLOB 専用ストレージ アカウントの場合、高スケールの場合、または待機時間を短縮する場合は、Blob ストレージ トリガーではなく、Event Grid トリガーを使用してください。 詳しくは、「[トリガー](#trigger)」セクションをご覧ください。

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Blob バインディングは [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージのバージョン 2.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

BLOB ストレージ バインディングは [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>トリガー

Blob ストレージ トリガーは、新しいまたは更新された BLOB が検出されたときに関数を開始します。 BLOB のコンテンツは、関数への入力として提供されます。

[Event Grid トリガー](functions-bindings-event-grid.md)には、[BLOB イベント](../storage/blobs/storage-blob-event-overview.md)のサポートが組み込まれており、新しいまたは更新された BLOB が検出されたときに関数を開始するために使用できます。 例については、[Event Grid を使用したイメージのサイズ変更](../event-grid/resize-images-on-storage-blob-upload-event.md)に関する記事を参照してください。

次のシナリオの場合は、Blob ストレージ トリガーではなく Event Grid を使用してください。

* BLOB ストレージ アカウント
* 高スケール
* 待機時間の最小化

### <a name="blob-storage-accounts"></a>BLOB ストレージ アカウント

BLOB の入力と出力のバインドでは､[BLOB ストレージ アカウント](../storage/common/storage-account-overview.md#types-of-storage-accounts)がサポートされていますが､BLOB トリガーではサポートされていません。 Blob Storage のトリガーには、汎用のストレージ アカウントが必要です。

### <a name="high-scale"></a>高スケール

高スケールとは、おおまかに言って、100,000 以上の BLOB を含むコンテナー、または 1 秒あたり 100 を超える BLOB の更新が発生するストレージ アカウントと定義できます。

### <a name="latency-issues"></a>待機時間に関する問題

関数アプリを従量課金プランで使用しているときに、関数アプリがアイドル状態になっている場合、新しい BLOB の処理が最大で 10 分間遅延する可能性があります。 この待機時間を避けるには、Always On が有効な App Service プランに切り替えることができます。 BLOB ストレージ アカウントで [Event Grid トリガー](functions-bindings-event-grid.md)を使用することもできます。 例については、[Event Grid のチュートリアル](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)を参照してください。 

### <a name="queue-storage-trigger"></a>Queue ストレージ トリガー

Event Grid 以外の BLOB を処理するための別の方法として、Queue ストレージ トリガーがありますが、それには BLOB イベントのサポートが組み込まれていません。 BLOB を作成または更新するとき、メッセージ キューを作成する必要があります。 それが行われていることを前提とする例については、[この記事で後述する BLOB 入力バインドの例](#input---example)をご覧ください。

## <a name="trigger---example"></a>トリガー - 例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

次の例は、`samples-workitems` コンテナーで BLOB が追加または更新されたときにログを書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

BLOB トリガー パス `samples-workitems/{name}` の文字列 `{name}` は、トリガーする BLOB のファイル名にアクセスするために関数コードで使用できる、[バインディング式](./functions-bindings-expressions-patterns.md)を作成します。 詳しくは、後述の「[BLOB 名のパターン](#trigger---blob-name-patterns)」をご覧ください。

`BlobTrigger` 属性について詳しくは、[トリガー - 属性](#trigger---attributes) に関する記事をご覧ください。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの BLOB トリガー バインドと、そのバインドを使用するコードを示しています。 関数は、`samples-workitems` [コンテナー](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)で BLOB が追加または更新されたときにログを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

BLOB トリガー パス `samples-workitems/{name}` の文字列 `{name}` は、トリガーする BLOB のファイル名にアクセスするために関数コードで使用できる、[バインディング式](./functions-bindings-expressions-patterns.md)を作成します。 詳しくは、後述の「[BLOB 名のパターン](#trigger---blob-name-patterns)」をご覧ください。

*function.json* ファイル プロパティについて詳しくは、これらのプロパティについて説明している「[構成](#trigger---configuration)」セクションをご覧ください。

`Stream` にバインドする C# スクリプト コードを次に示します。

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

`CloudBlockBlob` にバインドする C# スクリプト コードを次に示します。

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの BLOB トリガー バインドと、バインドを使用する [JavaScript](functions-reference-node.md) コードを示しています。 関数は、`samples-workitems` コンテナーで BLOB が追加または更新されたときにログを書き込みます。

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

BLOB トリガー パス `samples-workitems/{name}` の文字列 `{name}` は、トリガーする BLOB のファイル名にアクセスするために関数コードで使用できる、[バインディング式](./functions-bindings-expressions-patterns.md)を作成します。 詳しくは、後述の「[BLOB 名のパターン](#trigger---blob-name-patterns)」をご覧ください。

*function.json* ファイル プロパティについて詳しくは、これらのプロパティについて説明している「[構成](#trigger---configuration)」セクションをご覧ください。

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

次の例は、*function.json* ファイルの BLOB トリガー バインドと、バインドを使用する [Python コード](functions-reference-python.md)を示しています。 関数は、`samples-workitems` [コンテナー](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)で BLOB が追加または更新されたときにログを書き込みます。

*function.json* ファイルを次に示します。

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

BLOB トリガー パス `samples-workitems/{name}` の文字列 `{name}` は、トリガーする BLOB のファイル名にアクセスするために関数コードで使用できる、[バインディング式](./functions-bindings-expressions-patterns.md)を作成します。 詳しくは、後述の「[BLOB 名のパターン](#trigger---blob-name-patterns)」をご覧ください。

*function.json* ファイル プロパティについて詳しくは、これらのプロパティについて説明している「[構成](#trigger---configuration)」セクションをご覧ください。

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

次の例は、*function.json* ファイルの BLOB トリガー バインドと、バインドを使用する [Java コード](functions-reference-java.md)を示しています。 関数は、`myblob` コンテナーで BLOB が追加または更新されたときにログを書き込みます。

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Java コードを次に示します。

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="trigger---attributes"></a>トリガー - 属性

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)で、次の属性を使用して BLOB トリガーを構成します。

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  属性のコンストラクターは、監視するコンテナーを示すパス文字列と、必要に応じて [BLOB 名のパターン](#trigger---blob-name-patterns)を受け取ります。 次に例を示します。

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  次の例で示すように、`Connection` プロパティを設定して、使用するストレージ アカウントを指定できます。

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  完全な例については、「[トリガー - 例](#trigger---example)」を参照してください。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  使用するストレージ アカウントを指定する別の方法を提供します。 コンストラクターは、ストレージ接続文字列を含むアプリ設定の名前を受け取ります。 属性は、パラメーター、メソッド、またはクラス レベルで適用できます。 次の例では、クラス レベルとメソッド レベルを示します。

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

使用するストレージ アカウントは、次の順序で決定されます。

* `BlobTrigger` 属性の `Connection` プロパティ。
* `BlobTrigger` 属性と同じパラメーターに適用された `StorageAccount` 属性。
* 関数に適用される `StorageAccount` 属性。
* クラスに適用される `StorageAccount` 属性。
* 関数アプリの既定のストレージ アカウント ("AzureWebJobsStorage" アプリ設定)。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="pythontabpython"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobTrigger` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 詳細については、「[トリガー - 例](#trigger---example)」を参照してください。

---

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `BlobTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `blobTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 例外は、[使用方法](#trigger---usage)のセクションに記載しています。 |
|**name** | 該当なし | 関数コード内の BLOB を表す変数の名前。 |
|**path** | **BlobPath** |監視する[コンテナー](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)。  [BLOB 名パターン](#trigger---blob-name-patterns)の場合があります。 |
|**connection** | **Connection** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列は、[BLOB ストレージ アカウント](../storage/common/storage-account-overview.md#types-of-storage-accounts)ではなく汎用ストレージ アカウントに対するものである必要があります。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>トリガー - 使用方法

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name from function.json>` を使用して BLOB データにアクセスします。

# <a name="pythontabpython"></a>[Python](#tab/python)

[InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python) に型指定したパラメーターを使用して BLOB データにアクセスします。 詳細については、「[トリガー - 例](#trigger---example)」を参照してください。

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobTrigger` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 詳細については、「[トリガー - 例](#trigger---example)」を参照してください。

---

## <a name="trigger---blob-name-patterns"></a>トリガー - BLOB 名のパターン

*function.json* の `path` プロパティまたは `BlobTrigger` 属性コンストラクターで BLOB 名のパターンを指定することができます。 名前のパターンは、[フィルターまたはバインド式](./functions-bindings-expressions-patterns.md)にすることができます。 以下のセクションで、例を示します。

### <a name="get-file-name-and-extension"></a>ファイル名と拡張子の取得

次の例は、BLOB ファイル名と拡張子を別々にバインドする方法を示します。

```json
"path": "input/{blobname}.{blobextension}",
```

BLOB の名前が *original-Blob1.txt* の場合、関数コード内の `blobname` 変数と `blobextension` 変数の値は *original-Blob1* と *txt* です。

### <a name="filter-on-blob-name"></a>BLOB 名のフィルター

次の例は、文字列 "original-" で始まる `input` コンテナーの BLOB でのみトリガーします。

```json
"path": "input/original-{name}",
```

BLOB 名が *original-Blob1.txt* の場合、関数コード内の `name` 変数の値は `Blob1` です。

### <a name="filter-on-file-type"></a>ファイルの種類のフィルター

次の例は、 *.png* ファイルでのみトリガーします。

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>ファイル名の中かっこのフィルター

ファイル名の中かっこを検索するには、2 つの中かっこを使用して中かっこをエスケープします。 次の例は、名前に中かっこを含む BLOB をフィルター処理します。

```json
"path": "images/{{20140101}}-{name}",
```

BLOB の名前が *{20140101}-soundfile.mp3* の場合、関数コード内の `name` 変数の値は *soundfile.mp3* です。

## <a name="trigger---metadata"></a>トリガー - メタデータ

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

メタデータは、Python では使用できません。

# <a name="javatabjava"></a>[Java](#tab/java)

メタデータは、Java では使用できません。

---

## <a name="trigger---blob-receipts"></a>トリガー - BLOB の配信確認メッセージ

Azure Functions ランタイムでは、BLOB トリガー関数は、同一の新規または更新された BLOB について 2 回以上呼び出されることはありません。 特定の BLOB バージョンが処理されているかどうかを判断するために、*BLOB の配信確認メッセージ*が維持されます。

Azure Functions では、BLOB の配信確認メッセージは (アプリ設定 `AzureWebJobsStorage` で指定した) 関数アプリの Azure ストレージ アカウント内の *azure-webjobs-hosts* というコンテナーに格納されます。 BLOB の配信確認メッセージには次の情報が含まれています。

* トリガーされた関数 (" *&lt;関数アプリ名>* .Functions. *&lt;関数名>* "。たとえば、"MyFunctionApp.Functions.CopyBlob")
* コンテナーの名前
* BLOB の種類 ("BlockBlob" か "PageBlob")
* BLOB の名前
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

BLOB を強制的に再処理する場合は、*azure-webjobs-hosts* コンテナーからその BLOB の配信確認メッセージを手動で削除します。 再処理がすぐに行われない場合がありますが、後で必ず行われます。

## <a name="trigger---poison-blobs"></a>トリガー - 有害 BLOB

指定された BLOB に対する BLOB トリガー関数が失敗すると、Azure Functions は既定で最大 5 回その関数を再試行します。

試行が 5 回とも失敗した場合、Azure Functions は *webjobs-blobtrigger-poison* という名前のストレージ キューにメッセージを追加します。 有害な BLOB のキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (形式: *&lt;Function App 名>* .Functions. *&lt;関数名>* )
* BLOB の種類 ("BlockBlob" か "PageBlob")
* コンテナー名
* BlobName
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>トリガー - コンカレンシーとメモリ使用量

BLOB トリガーはキューを内部的に使用するため、関数の同時呼び出しの最大数が [host.json のキュー構成設定](functions-host-json.md#queues)によって制御されます。 既定の設定では、コンカレンシーの数は 24 までに制限されています。 この制限は、BLOB トリガーを使用する各関数に個別に適用されます。

[従量課金プラン](functions-scale.md#how-the-consumption-and-premium-plans-work)では、1 つの仮想マシン (VM) の関数アプリのメモリが 1.5 GB に制限されています。 メモリは、同時実行される各関数インスタンスと、Functions ランタイム自体によって使用されます。 BLOB によってトリガーされる関数が BLOB 全体をメモリに読み込む場合、その関数が BLOB 用にのみ使用するメモリの最大量は 24 * 最大 BLOB サイズです。 たとえば、BLOB によってトリガーされる 3 つの関数を含む関数アプリの場合、既定の設定では、VM あたりの最大コンカレンシー数 3*24 = 72 関数呼び出しとなります。

JavaScript 関数と Java 関数は BLOB 全体をメモリに読み込みますが、C# 関数は `string`、`Byte[]`、または POCO にバインドした場合に BLOB 全体をメモリに読み込みます。

## <a name="trigger---polling"></a>トリガー - ポーリング

監視対象の BLOB コンテナーに 10,000 を超える BLOB が含まれる (すべてのコンテナー全体で) 場合は、Functions ランタイムによりログ ファイルがスキャンされ、新規または変更された BLOB が監視されます。 このプロセスによって遅延が発生することがあります。 関数は、BLOB が作成されてから数分以上経過しないとトリガーされない可能性があります。

> [!WARNING]
> また、[ ストレージ ログは "ベスト エフォート"](/rest/api/storageservices/About-Storage-Analytics-Logging) ベースで作成されます。 すべてのイベントがキャプチャされる保証はありません。 ある条件下では、ログが欠落する可能性があります。
> 
> より高速で信頼性の高い BLOB 処理が必要な場合は、BLOB 作成時に[キュー メッセージ](../storage/queues/storage-dotnet-how-to-use-queues.md)を作成することを検討してください。 次に、BLOB トリガーの代わりに[キュー トリガー](functions-bindings-storage-queue.md)を使用して BLOB を処理します。 別のオプションは、Event Grid の使用です。「[Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](../event-grid/resize-images-on-storage-blob-upload-event.md)」のチュートリアルをご覧ください。
>

## <a name="input"></a>入力

Blob Storage 入力バインディングを使用して BLOB を読み取ります。

## <a name="input---example"></a>入力 - 例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

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

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

## <a name="input---attributes"></a>入力 - 属性

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、[トリガー - 属性](#trigger---attributes)をご覧ください。

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="pythontabpython"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobInput` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 この属性と共にバイト配列を使用する場合は、`dataType` を `binary` に設定します。 詳細については、「[入力 - 例](#input---example)」を参照してください。

---

## <a name="input---configuration"></a>入力 - 構成

次の表は、*function.json* ファイルと `Blob` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `blob` に設定する必要があります。 |
|**direction** | 該当なし | `in` に設定する必要があります。 例外は、[使用方法](#input---usage)のセクションに記載しています。 |
|**name** | 該当なし | 関数コード内の BLOB を表す変数の名前。|
|**path** |**BlobPath** | BLOB へのパス。 |
|**connection** |**Connection**| このバインドに使用する[ストレージ接続文字列](../storage/common/storage-configure-connection-string.md)を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列は、[BLOB のみのストレージ アカウント](../storage/common/storage-account-overview.md#types-of-storage-accounts)ではなく汎用ストレージ アカウントに対するものである必要があります。|
|該当なし | **Access (アクセス)** | 読み取りと書き込みのどちらを行うかを示します。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>入力 - 使用方法

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name from function.json>` を使用して BLOB データにアクセスします。

# <a name="pythontabpython"></a>[Python](#tab/python)

[InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python) に型指定したパラメーターを使用して BLOB データにアクセスします。 詳細については、「[入力 - 例](#input---example)」を参照してください。

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobInput` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 この属性と共にバイト配列を使用する場合は、`dataType` を `binary` に設定します。 詳細については、「[入力 - 例](#input---example)」を参照してください。

---

## <a name="output"></a>Output

Blob Storage 出力バインディングを使用して BLOB を書き込みます。

## <a name="output---example"></a>出力 - 例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

次の例は、1 つの BLOB トリガーと 2 つの出力 BLOB バインディングを使用する[C# 関数](functions-dotnet-class-library.md)です。 関数は、*sample-images* コンテナーのイメージ BLOB の作成によってトリガーされます。 イメージ BLOB の小規模および中規模サイズのコピーを作成します。

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
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
```

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

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

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

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

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

## <a name="output---attributes"></a>出力 - 属性

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="pythontabpython"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobOutput` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 この属性と共にバイト配列を使用する場合は、`dataType` を `binary` に設定します。 詳細については、「[出力 - 例](#output---example)」を参照してください。

---

完全な例については、「[出力 - 例](#output---example)」を参照してください。

`StorageAccount` 属性を使用して、クラス、メソッド、またはパラメーターのレベルでストレージ アカウントを指定できます。 詳細については、[トリガー - 属性](#trigger---attributes)をご覧ください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `Blob` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `blob` に設定する必要があります。 |
|**direction** | 該当なし | 出力バインディングの場合は `out` に設定する必要があります。 例外は、[使用方法](#output---usage)のセクションに記載しています。 |
|**name** | 該当なし | 関数コード内の BLOB を表す変数の名前。  `$return` に設定して、関数の戻り値を参照します。|
|**path** |**BlobPath** | BLOB コンテナーへのパス。 |
|**connection** |**Connection**| このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列は、[BLOB のみのストレージ アカウント](../storage/common/storage-account-overview.md#types-of-storage-accounts)ではなく汎用ストレージ アカウントに対するものである必要があります。|
|該当なし | **Access (アクセス)** | 読み取りと書き込みのどちらを行うかを示します。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C# スクリプト](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript では、`context.bindings.<name from function.json>` を使用して BLOB データにアクセスします。

# <a name="pythontabpython"></a>[Python](#tab/python)

関数のパラメーターを次の型で宣言し、BLOB ストレージに書き込むことができます。

* `func.Out(str)` は文字列
* `func.Out(func.InputStream)` はストリーム

詳細については、「[出力 - 例](#output---example)」を参照してください。

# <a name="javatabjava"></a>[Java](#tab/java)

`@BlobOutput` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 この属性と共にバイト配列を使用する場合は、`dataType` を `binary` に設定します。 詳細については、「[出力 - 例](#output---example)」を参照してください。

---

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド |  リファレンス |
|---|---|
| BLOB | [BLOB エラー コード](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB、テーブル、キュー |  [ストレージ エラー コード](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB、テーブル、キュー |  [トラブルシューティング](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>次の手順

* [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
