---
title: "Azure Functions における Storage キュー バインド | Microsoft Docs"
description: "Azure Functions で Azure Storage のトリガーとバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/18/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 770cac8809ab9f3d6261140333ec789ee1390daf
ms.openlocfilehash: bf9bd2a1b5acdf5a4a4f862bef693f8c60c63a33


---
# <a name="azure-functions-storage-queue-bindings"></a>Azure Functions における Storage キュー バインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Storage のキュー バインドを構成したりコーディングしたりする方法について説明します。 Azure Functions は、Azure Storage キューのトリガーおよび出力のバインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>Storage キュー トリガー
Azure Storage キュー トリガーを使用すると、ストレージ キューの新しいメッセージを監視し、それに対応できます。 

関数への Storage キュー トリガーでは、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

`connection` にはストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントを作成するか、既存のストレージ アカウントを選択するときに、**[統合]** タブでこのアプリ設定を構成できます。 このアプリ設定を手動で作成するには、[App Service 設定の管理](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)に関するページをご覧ください。

[その他の設定](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)は host.json ファイルで指定でき、ストレージ キュー トリガーをさらに微調整することができます。  

### <a name="handling-poison-queue-messages"></a>有害キュー メッセージの処理
キュー トリガー関数が失敗すると、Azure Functions は、その関数を特定のキュー メッセージに対して (最初の試行を含め) 最大&5; 回再試行します。 試行が&5; 回とも失敗した場合、Functions は、*&lt;originalqueuename>-poison* という名前の Storage キューにメッセージを追加します。 メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。 

有害メッセージを手動で処理するには、処理のためにメッセージが取得された回数を、`dequeueCount` を確認することで取得できます (「[キュー トリガー メタデータ](#meta)」を参照してください)。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>トリガーの使用方法
C# 関数の場合、入力メッセージにバインドするには、関数のシグネチャで `<T> <name>` などの名前付きパラメーターを使用します。
`T` はデータの逆シリアル化先のデータ型です。`paramName` は[トリガー バインド](#trigger)で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して入力 BLOB データにアクセスします。

キュー メッセージを、次のいずれかの型に逆シリアル化できます。

* [Object](https://msdn.microsoft.com/library/system.object.aspx) - JSON でシリアル化されたメッセージに使用されます。 カスタム入力型を宣言した場合、ランタイムは、JSON オブジェクトを逆シリアル化しようとします。 
* String
* Byte array
* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) (C# のみ)

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>キュー トリガー メタデータ
関数にキュー メタデータを取得するには、次の変数名を使用します。

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (文字列としてキュー メッセージ テキストを取得する別の方法)

キュー メタデータの使用方法については「[トリガー サンプル](#triggersample)」を参照してください。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>トリガー サンプル
Storage キュー トリガーを定義する、次の function.json があるとします。

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

キュー メタデータを取得し、記録する言語固有のサンプルを参照してください。

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C でのトリガー サンプル# #
```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js でのトリガー サンプル

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>Storage キュー出力バインド
Azure Storage キューの出力バインドにより、関数で Storage キューにメッセージを書き込むことができます。 

関数への Storage キューの出力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

`connection` にはストレージ接続文字列を含むアプリ設定の名前を含める必要があります。 Azure Portal では、ストレージ アカウントの作成や既存のストレージ アカウントの選択を行う際、**[統合]** タブの標準エディターによってこのアプリ設定が構成されます。 このアプリ設定を手動で作成するには、[App Service 設定の管理](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)に関するページをご覧ください。

<a name="outputusage"></a>

## <a name="output-usage"></a>出力の使用方法
C# 関数の場合、キュー メッセージを書き込むには、関数のシグネチャで `out <T> <name>` などの名前付き `out` パラメーターを使用します。 この場合、`T` は、メッセージのシリアル化先のデータ型です。`paramName` は、[出力バインド](#output)で指定した名前です。 Node.js 関数の場合、`context.bindings.<name>` を使用して出力にアクセスします。

キュー メッセージを、次のいずれかのデータ型を使用してコードで出力できます。

* 任意の [Object](https://msdn.microsoft.com/library/system.object.aspx): `out MyCustomType paramName`  
JSON のシリアル化に使用されます。  カスタム出力型を宣言した場合、ランタイムは、オブジェクトを JSON にシリアル化しようとします。 関数の終了時に出力パラメーターが null の場合、ランタイムはキュー メッセージを null オブジェクトとして作成します。
* String: `out string paramName`  
テスト メッセージで使用されます。 ランタイムは、関数の終了時に文字列パラメーターが null でない場合にのみメッセージを作成します。
* Byte array: `out byte[]` 

この追加の出力型は C# 関数でサポートされます。

* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx): `out CloudQueueMessage` 
* `ICollector<T>` または `IAsyncCollector<T>`。ここで、`T` は、サポートされている型の&1; つです。

<a name="outputsample"></a>

## <a name="output-sample"></a>出力サンプル
[Storage キュー トリガー](functions-bindings-storage-queue.md)、Storage BLOB 入力、Storage BLOB 出力を定義する、次の function.json があるとします。

手動トリガーを使用して、入力をキュー メッセージに書き込むストレージ キュー出力バインドの *function.json* の例を次に示します。

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "queue",
      "name": "myQueueItem",
      "queueName": "myqueue",
      "connection": "my_storage_connection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

入力キュー メッセージごとに出力キュー メッセージを書き込む言語固有のサンプルを参照してください。

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C での出力サンプル# #

```cs
public static void Run(string input, out string myQueueItem, TraceWriter log)
{
    myQueueItem = "New message: " + input;
}
```

複数のメッセージを送信する場合は、次のようになります。

```cs
public static void Run(string input, ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Message 1: " + input);
    myQueueItem.Add("Message 2: " + "Some other message.");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js での出力サンプル

```javascript
module.exports = function(context) {
    // Define a new message for the myQueueItem output binding.
    context.bindings.myQueueItem = "new message";
    context.done();
};
```

複数のメッセージを送信する場合は、次のようになります。

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>次のステップ

ストレージ キューのトリガーとバインドを使用する関数の例については、「[Azure サービスにバインドする Azure Function を作成する](functions-create-an-azure-connected-function.md)」を参照してください。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Jan17_HO3-->


