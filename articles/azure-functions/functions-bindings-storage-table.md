---
title: "Azure Functions における Storage テーブルのバインド | Microsoft Docs"
description: "Azure Functions で Azure Storage のバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 67c308f8216ebd291ae483c9b18d43528687e9a9
ms.lasthandoff: 03/29/2017


---
# <a name="azure-functions-storage-table-bindings"></a>Azure Functions における Storage テーブルのバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Storage のテーブル バインドを構成したりコーディングしたりする方法について説明します。 Azure Functions は、Azure Storage テーブルの入力および出力のバインドをサポートしています。

Storage テーブルのバインドは、次のシナリオをサポートしています。

* **C# または Node.js 関数での単一行の読み取り** - `partitionKey` と `rowKey` を設定します。 `filter` および `take` プロパティは、このシナリオでは使用しません。
* **C# 関数での複数行の読み取り** - Functions ランタイムは、テーブルにバインドされた `IQueryable<T>` オブジェクトを指定します。 型 `T` は `TableEntity` から派生するか、`ITableEntity` を実装する必要があります。 `partitionKey`、`rowKey`、`filter`、および `take` の各プロパティは、このシナリオでは使用しません。必要なフィルター処理は、`IQueryable` オブジェクトを使用して実行できます。 
* **ノード関数での複数行の読み取り** - `filter` プロパティと `take` プロパティを設定します。 `partitionKey` と `rowKey` は設定しません。
* **C# 関数での単一行または複数行の書き込み** - Functions ランタイムは、テーブルにバインドされた `ICollector<T>` または `IAsyncCollector<T>` を指定します。ここで、`T` は追加するエンティティのスキーマを指定します。 型 `T` は `TableEntity` から派生するか、`ITableEntity` を実装するのが一般的ですが、必須ではありません。 `partitionKey`、`rowKey`、`filter`、`take` の各プロパティは、このシナリオでは使用しません。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Storage テーブルの入力バインド
Azure Storage テーブルの入力バインドにより、関数で Storage テーブルを使用できます。 

関数への Storage テーブルの入力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

以下の点に注意してください。 

* 単一のエンティティを読み取るには、`partitionKey` と `rowKey` を一緒に使用します。 これらのプロパティは省略可能です。 
* `connection` にはストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントの作成や既存のストレージ アカウントの選択を行う際、**[統合]** タブの標準エディターによってこのアプリ設定が構成されます。 [このアプリケーション設定を手動で構成](functions-how-to-use-azure-function-app-settings.md#application-settings)することもできます。  

<a name="inputusage"></a>

## <a name="input-usage"></a>入力の使用方法
C# 関数の場合、入力テーブルの単一のエンティティ (または複数のエンティティ) にバインドするには、関数のシグネチャで `<T> <name>` などの名前付きパラメーターを使用します。
`T` はデータの逆シリアル化先のデータ型です。`paramName` は[入力バインド](#input)で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力テーブルの単一のエンティティ (または複数のエンティティ) にアクセスします。

入力データは Node.js または C# 関数で逆シリアル化できます。 逆シリアル化されたオブジェクトには `RowKey` プロパティと `PartitionKey` プロパティがあります。

また、C# 関数では、次の型のどれにでもバインドすることができ、Functions ランタイムはその型を使用してテーブル データを逆シリアル化しようとします。

* `ITableEntity` を実装するすべての型
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>入力サンプル
キュー トリガーを使用して単一のテーブル行を読み取る、次の function.json があるとします。 この JSON は、`PartitionKey` 
`RowKey` を指定します。 `"rowKey": "{queueTrigger}"` は、行キーがキュー メッセージ文字列から取得されることを示します。

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

単一のテーブル エンティティを読み込む言語固有のサンプルを参照してください。

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.JS](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>C での入力サンプル# #
```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>F での入力サンプル# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js での入力サンプル
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Storage テーブルの出力バインド
Azure Storage テーブルの出力バインドにより、関数で Storage テーブルにエンティティを書き込むことができます。 

関数への Storage テーブルの出力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

以下の点に注意してください。 

* 単一のエンティティを書き込むには、`partitionKey` と `rowKey` を一緒に使用します。 これらのプロパティは省略可能です。 関数コードでエンティティ オブジェクトを作成する際に `PartitionKey` と `RowKey` を指定することもできます。
* `connection` にはストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントの作成や既存のストレージ アカウントの選択を行う際、**[統合]** タブの標準エディターによってこのアプリ設定が構成されます。 [このアプリケーション設定を手動で構成](functions-how-to-use-azure-function-app-settings.md#application-settings)することもできます。 

<a name="outputusage"></a>

## <a name="output-usage"></a>出力の使用方法
C# 関数の場合、テーブルの出力にバインドするには、関数のシグネチャで `out <T> <name>` などの名前付き `out` パラメーターを使用します。`T` はデータのシリアル化先のデータ型です。`paramName` は[出力バインド](#output)で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用してテーブルの出力にアクセスします。

Node.js または C# 関数でオブジェクトをシリアル化できます。 C# 関数の場合は、次の型にもバインドできます。

* `ITableEntity` を実装するすべての型
* `ICollector<T>` (複数のエンティティを出力する場合。 [サンプル](#outcsharp)を参照してください)
* `IAsyncCollector<T>` (`ICollector<T>` の非同期バージョン)
* `CloudTable` (Azure Storage SDK を使用する場合。 [サンプル](#readmulti)を参照してください)

<a name="outputsample"></a>

## <a name="output-sample"></a>出力サンプル
次の *function.json* と *run.csx* の例は、複数のテーブル エンティティを書き込む方法をしています。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

複数のテーブル エンティティを作成する言語固有のサンプルを参照してください。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C での出力サンプル# #
```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F での出力サンプル# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js での出力サンプル
```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>サンプル: C での複数のテーブル エンティティの読み取り#  #
次の *function.json* および C# コード例では、キュー メッセージに指定されたパーティション キーのエンティティを読み取ります。

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# コードは、エンティティ型が `TableEntity`から派生できるように、Azure Storage SDK に参照を追加します。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


