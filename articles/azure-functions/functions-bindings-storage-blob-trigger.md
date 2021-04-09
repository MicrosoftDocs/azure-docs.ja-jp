---
title: Azure Functions の Azure Blob Storage トリガー
description: Azure Blob Storage のデータ変更時に Azure 関数を実行する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 1d83a828829d27d85749b3fa7b283cad9683bffc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455919"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure Functions の Azure Blob Storage トリガー

Blob ストレージ トリガーは、新しいまたは更新された BLOB が検出されたときに関数を開始します。 BLOB の内容は、[関数への入力](./functions-bindings-storage-blob-input.md)として提供されます。

Azure Blob Storage トリガーには、汎用ストレージ アカウントが必要です。 [階層型名前空間](../storage/blobs/data-lake-storage-namespace.md)を持つストレージ V2 アカウントもサポートされています。 BLOB 専用アカウントを使用する場合、またはアプリケーションに特別な必要性がある場合は、このトリガーの使用に代わる方法を検討してください。

セットアップと構成の詳細については、[概要](./functions-bindings-storage-blob.md)に関するページをご覧ください。

## <a name="polling"></a>ポーリング

ポーリングは、ログの検査と定期的なコンテナー スキャンの実行のハイブリッドとして機能します。 BLOB は、間隔の間で使用される継続トークンを使用して、一度に 10,000 のグループ単位でスキャンされます。

> [!WARNING]
> また、[ ストレージ ログは "ベスト エフォート"](/rest/api/storageservices/About-Storage-Analytics-Logging) ベースで作成されます。 すべてのイベントがキャプチャされる保証はありません。 ある条件下では、ログが欠落する可能性があります。
> 
> より高速で信頼性の高い BLOB 処理が必要な場合は、BLOB 作成時に[キュー メッセージ](../storage/queues/storage-dotnet-how-to-use-queues.md)を作成することを検討してください。 次に、BLOB トリガーの代わりに[キュー トリガー](functions-bindings-storage-queue.md)を使用して BLOB を処理します。 別のオプションは、Event Grid の使用です。「[Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](../event-grid/resize-images-on-storage-blob-upload-event.md)」のチュートリアルをご覧ください。
>

## <a name="alternatives"></a>代替

### <a name="event-grid-trigger"></a>Event Grid トリガー

[Event Grid トリガー](functions-bindings-event-grid.md)にも、[BLOB イベント](../storage/blobs/storage-blob-event-overview.md)のサポートが組み込まれています。 次のシナリオの場合は、Blob ストレージ トリガーではなく Event Grid を使用してください。

- **BLOB 専用ストレージ アカウント**: [BLOB 専用ストレージ アカウント](../storage/common/storage-account-overview.md#types-of-storage-accounts)は、BLOB の入力と出力のバインドでサポートされ、BLOB トリガーではサポートされません。

- **高スケール**: 高スケールとは、おおまかに言って、100,000 以上の BLOB を含むコンテナー、または 1 秒あたり 100 を超える BLOB の更新が発生するストレージ アカウントと定義できます。

- **待ち時間の最小化**: 関数アプリを従量課金プランで使用しているときに、関数アプリがアイドル状態になっている場合、新しい BLOB の処理が最大で 10 分間遅延する可能性があります。 この待機時間を避けるには、Always On が有効な App Service プランに切り替えることができます。 BLOB ストレージ アカウントで [Event Grid トリガー](functions-bindings-event-grid.md)を使用することもできます。 例については、[Event Grid のチュートリアル](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)を参照してください。

Event Grid の例については、[Event Grid を使用した画像のサイズ変更](../event-grid/resize-images-on-storage-blob-upload-event.md)に関するチュートリアルを参照してください。

### <a name="queue-storage-trigger"></a>Queue ストレージ トリガー

BLOB を処理するもう 1 つの方法は、作成または変更される BLOB に対応するキュー メッセージを作成し、[Queue storage トリガー](./functions-bindings-storage-queue.md)を使用して処理を開始することです。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、`samples-workitems` コンテナーで BLOB が追加または更新されたときにログを書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

BLOB トリガー パス `samples-workitems/{name}` の文字列 `{name}` は、トリガーする BLOB のファイル名にアクセスするために関数コードで使用できる、[バインディング式](./functions-bindings-expressions-patterns.md)を作成します。 詳しくは、後述の「[BLOB 名のパターン](#blob-name-patterns)」をご覧ください。

`BlobTrigger` 属性の詳細については、「[属性と注釈](#attributes-and-annotations)」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

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

BLOB トリガー パス `samples-workitems/{name}` の文字列 `{name}` は、トリガーする BLOB のファイル名にアクセスするために関数コードで使用できる、[バインディング式](./functions-bindings-expressions-patterns.md)を作成します。 詳しくは、後述の「[BLOB 名のパターン](#blob-name-patterns)」をご覧ください。

*function.json* ファイル プロパティについて詳しくは、これらのプロパティについて説明している「[構成](#configuration)」セクションをご覧ください。

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

# <a name="java"></a>[Java](#tab/java)

この関数は、`myblob` コンテナーで BLOB が追加または更新されたときにログを書き込みます。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

BLOB トリガー パス `samples-workitems/{name}` の文字列 `{name}` は、トリガーする BLOB のファイル名にアクセスするために関数コードで使用できる、[バインディング式](./functions-bindings-expressions-patterns.md)を作成します。 詳しくは、後述の「[BLOB 名のパターン](#blob-name-patterns)」をご覧ください。

*function.json* ファイル プロパティについて詳しくは、これらのプロパティについて説明している「[構成](#configuration)」セクションをご覧ください。

JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、`source` BLOB ストレージ コンテナーにファイルが追加されたときに実行される関数を作成する方法を示しています。

関数構成ファイル (_function.json_) には、`type` が `blobTrigger` で、`direction` が `in` に設定されたバインドが含まれています。

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "MyStorageAccountConnectionString"
    }
  ]
}
```

_run.ps1_ ファイルに関連するコードを次に示します。

```powershell
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

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

BLOB トリガー パス `samples-workitems/{name}` の文字列 `{name}` は、トリガーする BLOB のファイル名にアクセスするために関数コードで使用できる、[バインディング式](./functions-bindings-expressions-patterns.md)を作成します。 詳しくは、後述の「[BLOB 名のパターン](#blob-name-patterns)」をご覧ください。

*function.json* ファイル プロパティについて詳しくは、これらのプロパティについて説明している「[構成](#configuration)」セクションをご覧ください。

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)で、次の属性を使用して BLOB トリガーを構成します。

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  属性のコンストラクターは、監視するコンテナーを示すパス文字列と、必要に応じて [BLOB 名のパターン](#blob-name-patterns)を受け取ります。 次に例を示します。

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

  完全な例については、「[トリガー - 例](#example)」を参照してください。

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

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 詳細については、「[トリガー - 例](#example)」を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `BlobTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `blobTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 例外は、[使用方法](#usage)のセクションに記載しています。 |
|**name** | 該当なし | 関数コード内の BLOB を表す変数の名前。 |
|**path** | **BlobPath** |監視する[コンテナー](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)。  [BLOB 名パターン](#blob-name-patterns)の場合があります。 |
|**connection** | **接続** | このバインドに使用するストレージ接続文字列を含むアプリ設定の名前です。 アプリ設定の名前が "AzureWebJobs" で始まる場合は、ここで名前の残りの部分のみを指定できます。 たとえば、`connection` を "MyStorage" に設定した場合、Functions ランタイムは "AzureWebJobsMyStorage" という名前のアプリ設定を探します。 `connection` を空のままにした場合、Functions ランタイムは、アプリ設定内の `AzureWebJobsStorage` という名前の既定のストレージ接続文字列を使用します。<br><br>接続文字列は、[BLOB ストレージ アカウント](../storage/common/storage-account-overview.md#types-of-storage-accounts)ではなく汎用ストレージ アカウントに対するものである必要があります。<br><br>接続文字列の代わりに[バージョン 5.x またはそれ以降の拡張機能](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)を使用している場合は、接続を定義する構成セクションへの参照を指定できます。 「[接続](./functions-reference.md#connections)」を参照してください。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobTrigger` 属性を使用すると、関数をトリガーした BLOB にアクセスできます。 詳細については、「[トリガー - 例](#example)」を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`<NAME>` が *function.json* で定義されている値と一致する `context.bindings.<NAME>` を使用して BLOB データにアクセスします。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

_function.json_ ファイルのバインドの name パラメーターで指定されている名前と一致するパラメーターを使用して、BLOB データにアクセスします。

# <a name="python"></a>[Python](#tab/python)

[InputStream](/python/api/azure-functions/azure.functions.inputstream) に型指定したパラメーターを使用して BLOB データにアクセスします。 詳細については、「[トリガー - 例](#example)」を参照してください。

---

## <a name="blob-name-patterns"></a>BLOB 名のパターン

*function.json* の `path` プロパティまたは `BlobTrigger` 属性コンストラクターで BLOB 名のパターンを指定することができます。 名前のパターンは、[フィルターまたはバインド式](./functions-bindings-expressions-patterns.md)にすることができます。 以下のセクションで、例を示します。

> [!TIP]
> 名前パターンでは、コンテナー名に競合回避モジュールを含めることはできません。

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

BLOB 名が *original-Blob1.txt* の場合、関数コード内の `name` 変数の値は `Blob1.txt` です。

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

## <a name="metadata"></a>Metadata

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="java"></a>[Java](#tab/java)

メタデータは、Java では使用できません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

メタデータは、`$TriggerMetadata` パラメーターを介して使用できます。

# <a name="python"></a>[Python](#tab/python)

メタデータは、Python では使用できません。

---

## <a name="blob-receipts"></a>BLOB の配信確認メッセージ

Azure Functions ランタイムでは、BLOB トリガー関数は、同一の新規または更新された BLOB について 2 回以上呼び出されることはありません。 特定の BLOB バージョンが処理されているかどうかを判断するために、*BLOB の配信確認メッセージ* が維持されます。

Azure Functions では、BLOB の配信確認メッセージは (アプリ設定 `AzureWebJobsStorage` で指定した) 関数アプリの Azure ストレージ アカウント内の *azure-webjobs-hosts* というコンテナーに格納されます。 BLOB の配信確認メッセージには次の情報が含まれています。

* トリガーされた関数 (`<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>`。例: `MyFunctionApp.Functions.CopyBlob`)
* コンテナーの名前
* BLOB の種類 (`BlockBlob` または `PageBlob`)。
* BLOB の名前
* ETag (BLOB のバージョン識別子。例: `0x8D1DC6E70A277EF`)

BLOB を強制的に再処理する場合は、*azure-webjobs-hosts* コンテナーからその BLOB の配信確認メッセージを手動で削除します。 再処理がすぐに行われない場合がありますが、後で必ず行われます。 すぐに再処理するには、*azure-webjobs-hosts/blobscaninfo* 内の *scaninfo* BLOB を更新できます。 `LatestScan` プロパティの後に最後に変更されたタイムスタンプを持つすべての BLOB が再びスキャンされます。

## <a name="poison-blobs"></a>有害な BLOB

指定された BLOB に対する BLOB トリガー関数が失敗すると、Azure Functions は既定で最大 5 回その関数を再試行します。

試行が 5 回とも失敗した場合、Azure Functions は *webjobs-blobtrigger-poison* という名前のストレージ キューにメッセージを追加します。 再試行回数の最大値の設定は変更可能です。 同じ MaxDequeueCount 設定は、有害な BLOB の処理と有害キュー メッセージの処理に使用されます。 有害な BLOB のキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (`<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` の形式)
* BlobType (`BlockBlob` または `PageBlob`)
* コンテナー名
* BlobName
* ETag (BLOB のバージョン識別子。例: `0x8D1DC6E70A277EF`)

## <a name="concurrency-and-memory-usage"></a>コンカレンシーとメモリ使用量

BLOB トリガーはキューを内部的に使用するため、関数の同時呼び出しの最大数が [host.json のキュー構成設定](functions-host-json.md#queues)によって制御されます。 既定の設定では、コンカレンシーの数は 24 までに制限されています。 この制限は、BLOB トリガーを使用する各関数に個別に適用されます。

> [!NOTE]
> [バージョン 5.0.0 以降の Storage 拡張機能](functions-bindings-storage-blob.md#storage-extension-5x-and-higher)を使用しているアプリの場合、host.js のキューの構成はキュー トリガーにのみ適用されます。 BLOB トリガーのコンカレンシーは、代わりに [host.js の BLOB 構成](functions-host-json.md#blobs)によって制御されます。

[従量課金プラン](event-driven-scaling.md)では、1 つの仮想マシン (VM) の関数アプリのメモリが 1.5 GB に制限されています。 メモリは、同時実行される各関数インスタンスと、Functions ランタイム自体によって使用されます。 BLOB によってトリガーされる関数が BLOB 全体をメモリに読み込む場合、その関数が BLOB 用にのみ使用するメモリの最大量は 24 * 最大 BLOB サイズです。 たとえば、BLOB によってトリガーされる 3 つの関数を含む関数アプリの場合、既定の設定では、VM あたりの最大コンカレンシー数 3*24 = 72 関数呼び出しとなります。

JavaScript と Java の関数では BLOB 全体がメモリに読み込まれますが、C# 関数では `string`、または `Byte[]` にバインドした場合にこれが行われます。

## <a name="hostjson-properties"></a>host.json プロパティ

[host.json](functions-host-json.md#blobs) ファイルには、BLOB トリガーの動作を制御する設定が含まれています。 使用可能な設定の詳細については、「[host.json 設定](functions-bindings-storage-blob.md#hostjson-settings)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [関数の実行時に Blob Storage データを読み取る](./functions-bindings-storage-blob-input.md)
- [関数から Blob Storage データを書き込む](./functions-bindings-storage-blob-output.md)
