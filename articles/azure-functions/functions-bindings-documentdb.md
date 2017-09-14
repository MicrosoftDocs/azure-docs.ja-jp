---
title: "Azure Functions における Cosmos DB のバインド | Microsoft Docs"
description: "Azure Functions で Azure Cosmos DB のバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: fb79e2ad7514ae2cf48b9a5bd486e54b9b407bee
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-cosmos-db-bindings"></a>Azure Functions における Cosmos DB のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Cosmos DB のバインドを構成したりコーディングしたりする方法について説明します。 Azure Functions は、Cosmos DB の入力および出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Cosmos DB の詳細については、[Cosmos DB の概要](../documentdb/documentdb-introduction.md)と[Cosmos DB コンソール アプリケーションの作成](../documentdb/documentdb-get-started.md)に関するページを参照してください。

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>DocumentDB API 入力バインド
DocumentDB API 入力バインドでは、Cosmos DB ドキュメントを取得して関数の名前付き入力パラメーターに渡します。 ドキュメント ID は、関数を呼び出したトリガーに基づいて決定することができます。 

DocumentDB API 入力バインドには、*function.json* に次のプロパティがあります。

|プロパティ  |Description  |
|---------|---------|
|**name**     | 関数でドキュメントを表すバインド パラメーターの名前。  |
|**type**     | `documentdb` に設定する必要があります。        |
|**databaseName** | ドキュメントを含むデータベース。        |
|**collectionName**  | ドキュメントを含むコレクションの名前。 |
|**id**     | 取得するドキュメントの ID。 このプロパティは、バインド パラメーターをサポートしています。 詳細については、「[バインド式でのカスタム入力プロパティへのバインド](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression)」を参照してください。 |
|**sqlQuery**     | 複数のドキュメントを取得するときに使用する Cosmos DB SQL クエリ。 クエリでは、ランタイム バインドがサポートされます。例: `SELECT * FROM c where c.departmentId = {departmentId}`        |
|**接続**     |Cosmos DB 接続文字列を含むアプリ設定の名前。        |
|**direction**     | `in` に設定する必要があります。         |

設定できるのは **id** と **sqlQuery** のいずれかです。 どちらも設定しないと、コレクション全体が取得されます。

## <a name="using-a-documentdb-api-input-binding"></a>Azure DocumentDB API 入力バインドの使用

* C# および F# 関数では、関数が正常に終了したときに、名前付き入力パラメーターを介した入力ドキュメントへの変更がすべて自動的に保持されます。 
* JavaScript 関数の場合、関数の終了時に更新が自動的に行われることはありません。 代わりに、`context.bindings.<documentName>In` と `context.bindings.<documentName>Out` を使用して、更新を行います。 [JavaScript のサンプル](#injavascript)を参照してください。

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>1 つのドキュメントの入力サンプル
function.json の `bindings` 配列に次の DocumentDB API 入力バインドがあるとします。

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_COSMOSDB",     
  "direction": "in"
}
```

この入力バインドを使用してドキュメントのテキスト値を更新する、言語固有のサンプルを参照してください。

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C# での入力サンプル #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F# での入力サンプル #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

このサンプルには、`FSharp.Interop.Dynamic` と `Dynamitey` の NuGet 依存関係を指定する `project.json` ファイルが必要です。

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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>JavaScript での入力サンプル

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>複数のドキュメントの入力サンプル

SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得するとします。 

この例では、キュー トリガーには `departmentId` パラメーターが用意されており、`{ "departmentId" : "Finance" }` のキュー メッセージは財務部門のすべてのレコードを返します。 *function.json* で次を使用します。

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}"
    "connection": "CosmosDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>C# での複数のドキュメントの入力サンプル

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>JavaScript での複数のドキュメントの入力サンプル

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }       
    context.done();
};
```

## <a id="docdboutput"></a>DocumentDB API 出力バインド
DocumentDB API 出力バインドを使用すると、Azure Cosmos DB データベースに新しいドキュメントを記述できます。 *function.json* には次のプロパティがあります。

|プロパティ  |Description  |
|---------|---------|
|**name**     | 関数のドキュメントを表すバインド パラメーターの名前。  |
|**type**     | `documentdb` に設定する必要があります。        |
|**databaseName** | ドキュメントが作成されたコレクションを含むデータベース。     |
|**collectionName**  | ドキュメントが作成されたコレクションの名前。 |
|**createIfNotExists**     | コレクションが存在しないときに作成するかどうかを示すブール値。 既定値は *false* です。 新しいコレクションは予約済みのスループットで作成され、これが価格に影響を及ぼすためです。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。  |
|**接続**     |Cosmos DB 接続文字列を含むアプリ設定の名前。        |
|**direction**     | `out` に設定する必要があります。         |

## <a name="using-a-documentdb-api-output-binding"></a>DocumentDB API 出力バインドの使用
このセクションでは、DocumentDB API 出力バインドを関数のコードで使用する方法について説明します。

既定では、関数の出力パラメーターに書き込むと、ドキュメントがデータベースに作成されます。 このドキュメントには、自動的に生成された GUID がドキュメント ID として割り当てられます。 出力ドキュメントのドキュメント ID を指定するには、出力パラメーターに渡された JSON オブジェクトで `id` プロパティを指定します。 

>[!Note]  
>既存のドキュメントの ID を指定した場合、既存のドキュメントは新しい出力ドキュメントによって上書きされます。 

複数のドキュメントを出力するために `ICollector<T>` または `IAsyncCollector<T>` にバインドすることもできます。`T` は、サポートされている型のいずれかです。

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>DocumentDB API 出力バインドのサンプル
function.json の `bindings` 配列に次の DocumentDB API 出力バインドがあるとします。

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_COSMOSDB",     
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

ここで、各レコードに対して次の形式の Cosmos DB ドキュメントを作成するとします。

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
* [JavaScript](#outjavascript)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C# での出力サンプル #

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

### <a name="output-sample-in-f"></a>F# での出力サンプル #

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

このサンプルには、`FSharp.Interop.Dynamic` と `Dynamitey` の NuGet 依存関係を指定する `project.json` ファイルが必要です。

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>JavaScript での出力サンプル

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

