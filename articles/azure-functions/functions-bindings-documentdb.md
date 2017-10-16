---
title: "Functions の Azure Cosmos DB バインド | Microsoft Docs"
description: "Azure Functions で Azure Cosmos DB のトリガーとバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.openlocfilehash: ad058929eb888920823fddf549ada4ce2c6d9eee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-bindings-for-functions"></a>Functions の Azure Cosmos DB バインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Cosmos DB のバインドを構成したりコーディングしたりする方法について説明します。 Functions は、Azure Cosmos DB のトリガー、入力、出力のバインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Cosmos DB のサーバーレス コンピューティングの詳細については、「[Azure Cosmos DB: Azure Functions を使用したサーバーなしのデータベースのコンピューティング](..\cosmos-db\serverless-computing-database.md)」をご覧ください。

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Azure Cosmos DB のトリガー

Azure Cosmos DB のトリガーは [Azure Cosmos DB 変更フィード](../cosmos-db/change-feed.md)を使用して、パーティション間の変更をリッスンします。 トリガーは、パーティションに_リース_を保存するために使用する 2 つ目のコレクションを必要とします。

トリガーが機能するためには、監視されるコレクションと、リースを含むコレクションの両方が使用できる必要があります。

Azure Cosmos DB のトリガーは、次のプロパティをサポートしています。

|プロパティ  |Description  |
|---------|---------|
|**type** | `cosmosDBTrigger` に設定する必要があります。 |
|**name** | 変更されるドキュメントの一覧を表す、関数コードで使用する変数の名前。 | 
|**direction** | `in` に設定する必要があります。 このパラメーターは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**connectionStringSetting** | 監視されている Azure Cosmos DB アカウントに接続するために使用される接続文字列を含めたアプリ設定の名前。 |
|**databaseName** | 監視されているコレクションを使用する Azure Cosmos DB データベースの名前。 |
|**collectionName** | 監視されているコレクションの名前。 |
| **leaseConnectionStringSetting** | (省略可能) リース コレクションを保持するサービスへの接続文字列を含むアプリ設定の名前。 この値を設定しない場合、`connectionStringSetting` という値が使用されます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。 |
| **leaseDatabaseName** | (省略可能) リースの保存のために使用するコレクションを保持しているデータベースの名前。 この値を設定しない場合、`databaseName` の設定の値が使用されます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。 |
| **leaseCollectionName** | (省略可能) リースの保存のために使用するコレクションの名前。 この値を設定しない場合、`leases` という値が使用されます。 |
| **createLeaseCollectionIfNotExists** | (省略可能) `true` を設定すると、リース コレクションが存在していない場合に自動でリース コレクションを作成します。 既定値は `false` です。 |
| **leaseCollectionThroughput** | (省略可能) リース コレクションの作成に割り当てる要求ユニットの量を定義します。 この設定は、`createLeaseCollectionIfNotExists` が `true` に設定されている場合のみ使用できます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。

>[!NOTE] 
>リース コレクションに接続するために使用される接続文字列には書き込み権限が必要です。

これらのプロパティは、Azure Portal の関数の [統合] タブで、または `function.json` プロジェクト ファイルを編集することで、設定できます。

## <a name="using-an-azure-cosmos-db-trigger"></a>Azure Cosmos DB のトリガーの使用

このセクションには、Azure Cosmos DB のトリガーを使用する方法の例が含まれています。 その例は、次のようなトリガー メタデータを前提にしています。

```json
{
  "type": "cosmosDBTrigger",
  "name": "documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "<connection-app-setting>",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```
 
ポータルの関数アプリから Azure Cosmos DB のトリガーを作成する方法の例については、「[Azure Cosmos DB によってトリガーされる関数の作成](functions-create-cosmos-db-triggered-function.md)」をご覧ください。 

### <a name="trigger-sample-in-c"></a>C# でのトリガー サンプル #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>JavaScript でのトリガー サンプル
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>DocumentDB API 入力バインド
DocumentDB API 入力バインドでは、Azure Cosmos DB ドキュメントを取得して関数の名前付き入力パラメーターに渡します。 ドキュメント ID は、関数を呼び出したトリガーに基づいて決定することができます。 

DocumentDB API 入力バインドには、*function.json* に次のプロパティがあります。

|プロパティ  |Description  |
|---------|---------|
|**name**     | 関数のドキュメントを表すバインド パラメーターの名前。  |
|**type**     | `documentdb` に設定する必要があります。        |
|**databaseName** | ドキュメントを含むデータベース。        |
|**collectionName**  | ドキュメントを含むコレクションの名前。 |
|**id**     | 取得するドキュメントの ID。 このプロパティは、バインド パラメーターをサポートしています。 詳細については、「[バインド式でのカスタム入力プロパティへのバインド](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression)」を参照してください。 |
|**sqlQuery**     | 複数のドキュメントを取得するときに使用する Azure Cosmos DB SQL クエリ。 クエリでは、ランタイム バインドがサポートされます。例: `SELECT * FROM c where c.departmentId = {departmentId}`        |
|**接続**     |Azure Cosmos DB 接続文字列を含むアプリ設定の名前。        |
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
|**接続**     |Azure Cosmos DB 接続文字列を含むアプリ設定の名前。        |
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

ここで、各レコードに対して次の形式の Azure Cosmos DB ドキュメントを作成するとします。

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
