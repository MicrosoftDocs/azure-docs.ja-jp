---
title: "Azure Functions 外部ファイル バインディング (プレビュー) | Microsoft Docs"
description: "Azure Functions の外部ファイル バインディングを使用する"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 7e3b396d290212d3875385521bd7ae92da196b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-external-file-bindings-preview"></a>Azure Functions 外部ファイル バインディング (プレビュー)
この記事では、関数から組み込みのバインディングを利用し、各種 SaaS プロバイダー (OneDrive、Dropbox など) にあるファイルを操作する方法について説明します。 Azure Functions は、外部ファイルのトリガー、入力、および出力のバインディングをサポートしています。

このバインディングでは、SaaS プロバイダーへの API 接続を作成するか、または Function App のリソース グループにある既存の API 接続を使用します。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>サポートされているファイル接続

|コネクタ|トリガー|入力|出力|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|○|○|○
|[Dropbox](https://www.dropbox.com)|○|○|○
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|○|○|○
|[OneDrive](https://onedrive.live.com)|○|○|○
|[OneDrive for Business](https://onedrive.live.com/about/business/)|○|○|○
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|○|○|○
|[Google ドライブ](https://www.google.com/drive/)||○|○|

> [!NOTE]
> 外部ファイル接続を [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list) で使用することもできます

## <a name="external-file-trigger-binding"></a>外部ファイルのトリガー バインディング

Azure 外部ファイル トリガーを使用すると、リモート フォルダーを監視して、変更が検出されたときに関数コードを実行できます。

外部ファイル トリガーでは、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>名前のパターン
ファイル名のパターンは、`path` プロパティで指定できます。 参照されるフォルダーが SaaS プロバイダーに存在している必要があります。
次に例を示します。

```json
"path": "input/original-{name}",
```

このパスは、*input* フォルダーの *original-File1.txt* という名前のファイルを探し、関数コード内の `name` 変数の値は `File1.txt` になります。

別の例:

```json
"path": "input/{filename}.{fileextension}",
```

このパスは、*original-File1.txt* という名前のファイルを探し、関数コード内の `filename` 変数および `fileextension` 変数の値は *original-File1* と *txt* になります。

ファイルの種類を制限するには、ファイル拡張子に固定値を使用します。 次に例を示します。

```json
"path": "samples/{name}.png",
```

この場合、*samples* フォルダー内の *.png* ファイルのみが関数をトリガーします。

中かっこは、名前のパターン内の特殊文字です。 名前に中かっこがあるファイル名を指定する場合は、中かっこを二重にします。
次に例を示します。

```json
"path": "images/{{20140101}}-{name}",
```

このパスは、*images* フォルダーの *{20140101}-soundfile.mp3* という名前のファイルを探し、関数コード内の `name` 変数の値は *soundfile.mp3* になります。

<a name="receipts"></a>

<!--- ### File receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->
<a name="poison"></a>

### <a name="handling-poison-files"></a>有害ファイルの処理
外部ファイル トリガー関数が失敗すると、Azure Functions はその関数を特定のファイルに対して既定で最大 5 回再試行します (これは最初の試行を含む数字です)。
試行が 5 回とも失敗した場合、Functions は *webjobs-apihubtrigger-poison* という名前の Storage キューにメッセージを追加します。 有害なファイルのキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (形式: *&lt;Function App 名>*.Functions.*&lt;関数名>*)
* FileType
* FolderName
* FileName
* ETag (ファイルのバージョン識別子。たとえば、"0x8D1DC6E70A277EF")


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>トリガーの使用方法
C# 関数の場合、入力ファイル データにバインドするには、関数のシグネチャで `<T> <name>` などの名前付きパラメーターを使用します。
`T` はデータの逆シリアル化先のデータ型です。`paramName` は[トリガー JSON](#trigger) で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力ファイル データにアクセスします。

ファイルを、次のいずれかの型に逆シリアル化できます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でシリアル化されたファイル データに有効です。
  カスタム入力型を宣言した場合 (例: `FooType`)、Azure Functions は、指定した型に JSON データを逆シリアル化しようとします。
* 文字列 - テキスト ファイル データに有効です。

また、C# 関数では、次の型のどれにでもバインドすることができ、Functions ランタイムはその型を使用してファイル データを逆シリアル化しようとします。

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="trigger-sample"></a>トリガー サンプル
外部ファイル トリガーを定義する、次の function.json があるとします。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

監視対象のフォルダーに追加される各ファイルの内容を記録する、言語固有のサンプルを参照してください。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>C# でのトリガーの使用方法 #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Node.js でのトリガーの使用方法

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>外部ファイル入力バインディング
Azure 外部ファイル入力バインディングにより、外部フォルダー内のファイルを関数で使用できるようになります。

関数への外部ファイル入力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

以下の点に注意してください。

* `path` にフォルダー名とファイル名が含まれる必要があります。 たとえば、関数内に[キュー トリガー](functions-bindings-storage-queue.md)がある場合は、`"path": "samples-workitems/{queueTrigger}"` を使用して、トリガー メッセージで指定されたファイル名と同じ名前を持つ `samples-workitems` フォルダー内のファイルをポイントできます。   

<a name="inputusage"></a>

## <a name="input-usage"></a>入力の使用方法
C# 関数の場合、入力ファイル データにバインドするには、関数のシグネチャで `<T> <name>` などの名前付きパラメーターを使用します。
`T` はデータの逆シリアル化先のデータ型です。`paramName` は[入力バインド](#input)で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力ファイル データにアクセスします。

ファイルを、次のいずれかの型に逆シリアル化できます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でシリアル化されたファイル データに有効です。
  カスタム入力型を宣言した場合 (例: `InputType`)、Azure Functions は、指定した型に JSON データを逆シリアル化しようとします。
* 文字列 - テキスト ファイル データに有効です。

また、C# 関数では、次の型のどれにでもバインドすることができ、Functions ランタイムはその型を使用してファイル データを逆シリアル化しようとします。

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`


<a name="output"></a>

## <a name="external-file-output-binding"></a>外部ファイル出力バインディング
Azure 外部ファイル出力バインディングにより、関数で外部フォルダーにファイルを書き込むことが可能になります。

関数での外部ファイル出力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

以下の点に注意してください。

* `path` に書き込み先のフォルダー名とファイル名が含まれる必要があります。 たとえば、関数内に[キュー トリガー](functions-bindings-storage-queue.md)がある場合は、`"path": "samples-workitems/{queueTrigger}"` を使用して、トリガー メッセージで指定されたファイル名と同じ名前を持つ `samples-workitems` フォルダー内のファイルをポイントできます。   

<a name="outputusage"></a>

## <a name="output-usage"></a>出力の使用方法
C# 関数の場合、出力ファイルにバインドするには、関数のシグネチャで `out <T> <name>` などの名前付き `out` パラメーターを使用します。`T` はデータのシリアル化先のデータ型です。`paramName` は[出力バインド](#output)で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して出力ファイルにアクセスします。

出力ファイルには、次のいずれかの型で書き込むことができます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でのシリアル化に有効です。
  カスタム出力型を宣言した場合 (例: `out OutputType paramName`)、Azure Functions は、オブジェクトを JSON にシリアル化しようとします。 関数の終了時に出力パラメーターが null の場合、Functions ランタイムはファイルを null オブジェクトとして作成します。
* 文字列 - (`out string paramName`) テキスト ファイル データに有効です。 Functions ランタイムは、関数の終了時に文字列パラメーターが null でない場合にのみファイルを作成します。

C# 関数の場合は、次の型のいずれかに出力することもできます。

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>入力と出力のサンプル
[Storage キュー トリガー](functions-bindings-storage-queue.md)、外部ファイル入力、外部ファイル出力を定義する、次の function.json があるとします。

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
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

入力ファイルを出力ファイルにコピーする言語固有のサンプルを参照してください。

* [C#](#incsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>C# での使用方法 #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Node.js での使用方法

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
