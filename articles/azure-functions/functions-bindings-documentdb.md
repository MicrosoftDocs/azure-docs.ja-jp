---
title: "Azure Functions における DocumentDB のバインド | Microsoft Docs"
description: "Azure Functions で Azure DocumentDB のバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: e476a80a3846b8c80c35d6803d5518727f008824
ms.lasthandoff: 03/06/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Azure Functions における DocumentDB のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure DocumentDB のバインドを構成したりコーディングしたりする方法について説明します。 Azure Functions は、DocumentDB の入力および出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

DocumentDB の詳細については、[DocumentDB の概要](../documentdb/documentdb-introduction.md)に関するページおよび [DocumentDB コンソール アプリケーションの作成](../documentdb/documentdb-get-started.md)に関するページを参照してください。

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>DocumentDB 入力バインド
DocumentDB 入力バインドでは、DocumentDB ドキュメントを取得して関数の名前付き入力パラメーターに渡します。 ドキュメント ID は、関数を呼び出したトリガーに基づいて決定することができます。 

関数への DocumentDB 入力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

以下の点に注意してください。

* `id` は、キュー メッセージの文字列値をドキュメント ID として使用する `{queueTrigger}` と同様のバインドをサポートします。
* `connection` は、(値 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>` を使用して) DocumentDB アカウントのエンドポイントを指すアプリ設定の名前である必要があります。 Functions ポータルの UI を使用して DocumentDB アカウントを作成すると、アカウント作成プロセスによってアプリ設定が作成されます。 既存の DocumentDB アカウントを使用するには、[このアプリ設定を手動で構成する](functions-how-to-use-azure-function-app-settings.md)必要があります。 
* 指定されたドキュメントが見つからない場合、関数の名前付き入力パラメーターは `null` に設定されます。 

## <a name="input-usage"></a>入力の使用方法
このセクションでは、DocumentDB 入力バインドを関数のコードで使用する方法について説明します。

C# および F# の関数では、入力ドキュメント (名前付き入力パラメーター) に加えられた変更は、関数が正常に終了したときに、コレクションに自動的に送り返されます。 Node.js 関数では、入力バインド内のドキュメントに対する更新はコレクションに送り返されません。 ただし、`context.bindings.<documentName>In` と `context.bindings.<documentName>Out` を使用して入力ドキュメントを更新できます。 その方法については、[Node.js サンプル](#innodejs)を参照してください。

<a name="inputsample"></a>

## <a name="input-sample"></a>入力サンプル
function.json の `bindings` 配列に次の DocumentDB 入力バインドがあるとします。

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

この入力バインドを使用してドキュメントのテキスト値を更新する、言語固有のサンプルを参照してください。

* [C#](#incsharp)
* [F#](#infsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C での入力サンプル# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F での入力サンプル# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

`FSharp.Interop.Dynamic` と `Dynamitey` の NuGet 依存関係を指定する `project.json` ファイルを追加する必要があります。

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

`project.json` ファイルの追加方法については、[のパッケージ管理](functions-reference-fsharp.md#package)に関するセクションを参照してください。

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js での入力サンプル

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a id="docdboutput"></a>DocumentDB 出力バインド
DocumentDB 出力バインドを使用すると、Azure DocumentDB データベースに新しいドキュメントを記述できます。 

出力バインドでは、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

以下の点に注意してください。

* データベースとコレクションが存在しない場合にこれを作成するには、`createIfNotExists` を `true` に設定します。 既定値は `false` です。 新しいコレクションは予約済みのスループットで作成されます。これにより、価格に影響が及びます。 詳細については、「 [DocumentDB の価格](https://azure.microsoft.com/pricing/details/documentdb/)」を参照してください。
* `connection` は、(値 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>` を使用して) DocumentDB アカウントのエンドポイントを指すアプリ設定の名前である必要があります。 Functions ポータルの UI を使用して DocumentDB アカウントを作成すると、アカウント作成プロセスによって新しいアプリ設定が作成されます。 既存の DocumentDB アカウントを使用するには、[このアプリ設定を手動で構成する]()必要があります。 

## <a name="output-usage"></a>出力の使用方法
このセクションでは、DocumentDB 出力バインドを関数のコードで使用する方法について説明します。

関数の出力パラメーターに書き込むと、既定で新しい文書がデータベースに生成され、自動的に生成された GUID が文書 ID として割り当てられます。 出力ドキュメントのドキュメント ID は、出力パラメーターの `id` JSON プロパティを指定することによって指定できます。 

>[!Note]  
>既存のドキュメントの ID を指定した場合、既存のドキュメントは新しい出力ドキュメントによって上書きされます。 

出力には、次のいずれかの型で書き込むことができます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でのシリアル化に有効です。
  カスタム出力型を宣言した場合 (例: `out FooType paramName`)、Azure Functions は、オブジェクトを JSON にシリアル化しようとします。 関数の終了時に出力パラメーターが null の場合、Functions ランタイムは BLOB を null オブジェクトとして作成します。
* 文字列 - (`out string paramName`) テキスト BLOB データに有効です。 Functions ランタイムは、関数の終了時に文字列パラメーターが null でない場合にのみ BLOB を作成します。

C# 関数の場合は、次の型のいずれかに出力することもできます。

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

複数のドキュメントを出力するために `ICollector<T>` または `IAsyncCollector<T>` にバインドすることもできます。`T` は、サポートされている型のいずれかです。


<a name="outputsample"></a>

## <a name="output-sample"></a>出力サンプル
function.json の `bindings` 配列に次の DocumentDB 出力バインドがあるとします。

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "out"
}
```

さらに、次の形式で JSON を受信するキューのキュー入力バインドがあるとします。

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

ここで、各レコードに対して次の形式の DocumentDB ドキュメントを作成するとします。

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

この出力バインドを使用してドキュメントをデータベースに追加する、言語固有のサンプルを参照してください。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C での出力サンプル# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
{
  log.Info($"C# Queue trigger function processed: {myQueueItem}");

  dynamic employee = JObject.Parse(myQueueItem);

  employeeDocument = new {
    id = employee.name + "-" + employee.employeeId,
    name = employee.name,
    employeeId = employee.employeeId,
    address = employee.address
  };
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F での出力サンプル# #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

`FSharp.Interop.Dynamic` と `Dynamitey` の NuGet 依存関係を指定する `project.json` ファイルを追加する必要があります。

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

`project.json` ファイルの追加方法については、[のパッケージ管理](functions-reference-fsharp.md#package)に関するセクションを参照してください。

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js での出力サンプル

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```

