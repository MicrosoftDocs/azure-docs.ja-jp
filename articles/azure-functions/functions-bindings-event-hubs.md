---
title: "Azure Functions におけるイベント ハブのバインド | Microsoft Docs"
description: "Azure Functions で Azure Event Hub のバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/02/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: c8e9f9709d13295c9414e525f1f60abf0d0accb7
ms.openlocfilehash: 0bfbfd3828aacdee0b6630ced034f2c1e0451abd


---
# <a name="azure-functions-event-hub-bindings"></a>Azure Functions における Event Hub のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md) のバインドを構成およびコーディングする方法について説明します。
Azure Functions は、イベント ハブのトリガーおよび出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Event Hubs を初めて使用する場合は、「[Azure Event Hub の概要](../event-hubs/event-hubs-what-is-event-hubs.md)」を参照してください。

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>イベント ハブ トリガー
イベント ハブ トリガーを使用して、イベント ハブのイベント ストリームに送信されたイベントに応答します。 トリガーをセットアップするには、イベント ハブへの読み取りアクセスが必要です。

関数へのイベント ハブ トリガーでは、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the Event Hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup` は、ハブのイベントにサブスクライブするための[コンシューマー グループ](../event-hubs/event-hubs-what-is-event-hubs.md#event-consumers)の設定に使用される、省略可能なプロパティです。 省略した場合は、`$Default` コンシューマー グループが使用されます。  
`connection` は、イベント ハブの名前空間への接続文字列を含むアプリ設定の名前である必要があります。
この接続文字列をコピーするには、イベント ハブ自体ではなく、"*名前空間*" の **[接続情報]** をクリックします。 この接続文字列には、トリガーをアクティブにするために少なくとも読み取りアクセス許可が必要です。

[その他の設定](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)を host.json ファイルで指定して、イベント ハブ トリガーをさらに微調整することができます。  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>トリガーの使用方法
イベント ハブ トリガー関数がトリガーされると、それをトリガーするメッセージが文字列として関数に渡されます。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>トリガー サンプル
function.json の `bindings` 配列に次のイベント ハブ トリガーがあるとします。

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

イベント ハブ トリガーのメッセージ本文を記録する、言語固有のサンプルを参照してください。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C でのトリガー サンプル# #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F でのトリガー サンプル# #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js でのトリガー サンプル

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hub-output-binding"></a>イベント ハブ出力バインド
イベント ハブ出力バインドを使用して、イベント ハブのイベント ストリームにイベントを書き込みます。 イベントを書き込むには、イベント ハブへの送信アクセス許可が必要です。

出力バインドでは、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection` は、イベント ハブの名前空間への接続文字列を含むアプリ設定の名前である必要があります。
この接続文字列をコピーするには、イベント ハブ自体ではなく、"*名前空間*" の **[接続情報]** をクリックします。 この接続文字列には、イベント ストリームにメッセージを送信するための送信アクセス許可が必要です。

## <a name="output-usage"></a>出力の使用方法
このセクションでは、Event Hub 出力バインドを関数のコードで使用する方法について説明します。

次のパラメーターの種類で構成済みイベント ハブにメッセージを出力できます。

* `out string`
* `ICollector<string>` (複数のメッセージを出力する場合)
* `IAsyncCollector<string>` (`ICollector<T>` の非同期バージョン)

<a name="outputsample"></a>

## <a name="output-sample"></a>出力サンプル
function.json の `bindings` 配列に次のイベント ハブ出力バインドがあるとします。

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

イベント ストリームにイベントを書き込む、言語固有のサンプルを参照してください。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C での出力サンプル# #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

または、複数のメッセージを作成するには次のようにします。

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F での出力サンプル# #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Node.js での出力サンプル

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

複数のメッセージを送信する場合は、次のようになります。

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]



<!--HONumber=Jan17_HO3-->


