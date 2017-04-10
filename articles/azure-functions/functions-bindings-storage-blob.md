---
title: "Azure Functions における Storage BLOB のバインド | Microsoft Docs"
description: "Azure Functions で Azure Storage のトリガーとバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 590cb831ad265d9b83713f573c92d8675e64db3d
ms.lasthandoff: 03/22/2017


---
# <a name="azure-functions-storage-blob-bindings"></a>Azure Functions における Storage BLOB のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Storage の BLOB バインドを構成したりコーディングしたりする方法について説明します。 Azure Functions は、Azure Storage BLOB のトリガー、入力、出力のバインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [BLOB 専用のストレージ アカウント](../storage/storage-create-storage-account.md#blob-storage-accounts)はサポートされていません。 Azure Functions では、BLOB で使用するための汎用的なストレージ アカウントが必要です。 
> 
> 

<a name="trigger"></a>

## <a name="storage-blob-trigger"></a>Storage BLOB トリガー
Azure Storage BLOB トリガーを使用して、新しい BLOB または更新された BLOB 用のストレージ コンテナーを監視し、変更が検出されたときに関数を実行できます。 

関数への Storage BLOB トリガーでは、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection":"<Name of app setting - see below>"
}
```

以下の点に注意してください。

* `path` については、「[名前のパターン](#pattern)」を参照し、BLOB 名のパターンを設定する方法を確認してください。
* `connection` にはストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントの作成や既存のストレージ アカウントの選択を行う際、**[統合]** タブの標準エディターによってこのアプリ設定が構成されます。 このアプリ設定を手動で作成するには、[アプリ設定の手動での構成]()に関する記事を参照してください。 

従量課金プランで実行しているときに Function App がアイドル状態になると、新しい BLOB の処理が最大で 10 分遅延する場合があります。 Function App が実行されると、BLOB はより迅速に処理されます。 この初期段階での遅延を回避するには、Always On を有効にした状態で App Service プランを使用するか、別のメカニズムを使用して、BLOB 名を含むキュー メッセージなどの BLOB 処理をトリガーします。 

また、詳細については、次の小見出しのいずれかを参照してください。

* [名前のパターン](#pattern)
* [BLOB の配信確認メッセージ](#receipts)
* [有害 BLOB の処理](#poison)

<a name="pattern"></a>

### <a name="name-patterns"></a>名前のパターン
BLOB 名のパターンは、 `path` プロパティで指定できます。 次に例を示します。

```json
"path": "input/original-{name}",
```

このパスは、*input* コンテナーの *original-Blob1.txt* という名前の BLOB を探し、関数コード内の `name` 変数の値は `Blob1` になります。

別の例:

```json
"path": "input/{blobname}.{blobextension}",
```

このパスは、*original-Blob1.txt* という名前の BLOB を探し、関数コード内の `blobname` 変数および `blobextension` 変数の値は *original-Blob1* と *txt* になります。

BLOB のファイルの種類を制限するには、ファイル拡張子に固定値を使用します。 次に例を示します。

```json
"path": "samples/{name}.png",
```

この場合、*samples* コンテナー内の *.png* BLOB のみが関数をトリガーします。

中かっこは、名前のパターン内の特殊文字です。 名前に中かっこがある BLOB 名を指定する場合は、中かっこを二重にします。 次に例を示します。

```json
"path": "images/{{20140101}}-{name}",
```

このパスは、*images* コンテナーの *{20140101}-soundfile.mp3* という名前の BLOB を探し、関数コード内の `name` 変数の値は *soundfile.mp3* になります。 

<a name="receipts"></a>

### <a name="blob-receipts"></a>BLOB の配信確認メッセージ
Azure Functions ランタイムでは、BLOB トリガー関数は、同一の新規または更新された BLOB について&2; 回以上呼び出されることはありません。 これは "*BLOB の配信確認メッセージ*" を維持して、特定の BLOB バージョンが処理されているかどうかを判断するためです。

BLOB の配信確認メッセージは、(`AzureWebJobsStorage` アプリ設定で指定した) Function App の Azure ストレージ アカウントの *azure-webjobs-hosts* というコンテナーに格納されています。 BLOB の配信確認メッセージには次の情報が含まれています。

* トリガーされた関数 ("*&lt;Function App 名>*.Functions.*&lt;関数名>*"。たとえば、"functionsf74b96f7.Functions.CopyBlob")
* コンテナーの名前
* BLOB の種類 ("BlockBlob" か "PageBlob")
* BLOB の名前
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

BLOB を強制的に再処理する場合は、*azure-webjobs-hosts* コンテナーからその BLOB の配信確認メッセージを手動で削除します。

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>有害 BLOB の処理
BLOB トリガー関数が失敗すると、Azure Functions はその関数を特定の BLOB に対して既定で最大 5 回再試行します (これは最初の試行を含む数字です)。 試行が 5 回とも失敗した場合、Functions は *webjobs-blobtrigger-poison* という名前の Storage キューにメッセージを追加します。 有害な BLOB のキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (形式: *&lt;Function App 名>*.Functions.*&lt;関数名>*)
* BLOB の種類 ("BlockBlob" か "PageBlob")
* コンテナー名
* BlobName
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>大規模なコンテナーをポーリングする BLOB
バインドで監視する BLOB コンテナーに 10,000 を超える BLOB が含まれる場合は、Functions ランタイムによりログ ファイルがスキャンされ、新規または変更された BLOB が監視されます。 このプロセスはリアルタイムではありません。 関数は、BLOB が作成されてから数分以上経過しないとトリガーされない可能性があります。 また、[ストレージ ログの作成は "ベスト エフォート"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ベースで行われます。 そのため、すべてのイベントがキャプチャされる保証はありません。 ある条件下では、ログが欠落する可能性があります。 大規模なコンテナーの BLOB トリガーの速度と信頼性の制限がアプリケーションで許容されない場合は、BLOB を作成するときに[キュー メッセージ](../storage/storage-dotnet-how-to-use-queues.md)を作成し、BLOB トリガーではなく[キュー トリガー](functions-bindings-storage-queue.md)を使って BLOB を処理することをお勧めします。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>トリガーの使用方法
C# 関数の場合、入力 BLOB データにバインドするには、関数のシグネチャで `<T> <name>` などの名前付きパラメーターを使用します。
`T` はデータの逆シリアル化先のデータ型です。`paramName` は[トリガー JSON](#trigger) で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力 BLOB データにアクセスします。

BLOB を、次のいずれかの型に逆シリアル化できます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でシリアル化された BLOB データに有効です。
  カスタム入力型を宣言した場合 (例: `FooType`)、Azure Functions は、指定した型に JSON データを逆シリアル化しようとします。
* 文字列 - テキスト BLOB データに有効です。

C# 関数では、次の型のどれにでもバインドすることができ、Functions ランタイムはその型を使用して BLOB データを逆シリアル化しようとします。

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

## <a name="trigger-sample"></a>トリガー サンプル
Storage BLOB トリガーを定義する、次の function.json があるとします。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

監視対象のコンテナーに追加される各 BLOB の内容を記録する、言語固有のサンプルを参照してください。

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>C でのトリガーの使用方法# #

```cs
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
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
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

<a name="input"></a>

## <a name="storage-blob-input-binding"></a>Storage BLOB の入力バインド
Azure Storage BLOB の入力バインドにより、関数でストレージ コンテナーの BLOB を使用できます。 

関数への Storage BLOB の入力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "blob",
  "direction": "in"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

以下の点に注意してください。

* `path` にはコンテナー名と BLOB 名を含める必要があります。 たとえば、関数内に[キュー トリガー](functions-bindings-storage-queue.md)がある場合は、`"path": "samples-workitems/{queueTrigger}"` を使用して、トリガー メッセージで指定された BLOB 名と同じ名前を持つ `samples-workitems` コンテナー内の BLOB をポイントできます。   
* `connection` にはストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントの作成や既存のストレージ アカウントの選択を行う際、**[統合]** タブの標準エディターによってこのアプリ設定が構成されます。 このアプリ設定を手動で作成するには、[アプリ設定の手動での構成]()に関する記事を参照してください。 

<a name="inputusage"></a>

## <a name="input-usage"></a>入力の使用方法
C# 関数の場合、入力 BLOB データにバインドするには、関数のシグネチャで `<T> <name>` などの名前付きパラメーターを使用します。
`T` はデータの逆シリアル化先のデータ型です。`paramName` は[入力バインド](#input)で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力 BLOB データにアクセスします。

BLOB を、次のいずれかの型に逆シリアル化できます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でシリアル化された BLOB データに有効です。
  カスタム入力型を宣言した場合 (例: `InputType`)、Azure Functions は、指定した型に JSON データを逆シリアル化しようとします。
* 文字列 - テキスト BLOB データに有効です。

C# 関数では、次の型のどれにでもバインドすることができ、Functions ランタイムはその型を使用して BLOB データを逆シリアル化しようとします。

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="inputsample"></a>

## <a name="input-sample"></a>入力サンプル
[Storage キュー トリガー](functions-bindings-storage-queue.md)、Storage BLOB 入力、Storage BLOB 出力を定義する、次の function.json があるとします。

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

### <a name="input-usage-in-c"></a>C での入力の使用方法# #

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

``` 
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Node.js での入力の使用方法

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

<a name="output"></a>

## <a name="storage-blob-output-binding"></a>Storage BLOB の出力バインド
Azure Storage BLOB の出力バインドにより、関数で Storage コンテナーに BLOB を書き込むことができます。 

関数への Storage BLOB の出力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "blob",
  "direction": "out",
  "path": "<Path of input blob - see below>",
  "connection": "<Name of app setting - see below>"
}
```

以下の点に注意してください。

* `path` には書き込み対象となるコンテナー名と BLOB 名を含める必要があります。 たとえば、関数内に[キュー トリガー](functions-bindings-storage-queue.md)がある場合は、`"path": "samples-workitems/{queueTrigger}"` を使用して、トリガー メッセージで指定された BLOB 名と同じ名前を持つ `samples-workitems` コンテナー内の BLOB をポイントできます。   
* `connection` にはストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントの作成や既存のストレージ アカウントの選択を行う際、**[統合]** タブの標準エディターによってこのアプリ設定が構成されます。 このアプリ設定を手動で作成するには、[アプリ設定の手動での構成]()に関する記事を参照してください。 

<a name="outputusage"></a>

## <a name="output-usage"></a>出力の使用方法
C# 関数の場合、出力 BLOB にバインドするには、関数のシグネチャで `out <T> <name>` などの名前付き `out` パラメーターを使用します。`T` はデータのシリアル化先のデータ型です。`paramName` は[出力バインド](#output)で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して出力 BLOB にアクセスします。

出力 BLOB には、次のいずれかの型で書き込むことができます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でのシリアル化に有効です。
  カスタム出力型を宣言した場合 (例: `out OutputType paramName`)、Azure Functions は、オブジェクトを JSON にシリアル化しようとします。 関数の終了時に出力パラメーターが null の場合、Functions ランタイムは BLOB を null オブジェクトとして作成します。
* 文字列 - (`out string paramName`) テキスト BLOB データに有効です。 Functions ランタイムは、関数の終了時に文字列パラメーターが null でない場合にのみ BLOB を作成します。

C# 関数の場合は、次の型のいずれかに出力することもできます。

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="outputsample"></a>

## <a name="output-sample"></a>出力サンプル
「[入力サンプル](#inputsample)」を参照してください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


