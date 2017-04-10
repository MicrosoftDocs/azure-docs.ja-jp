---
title: "Azure Functions における Service Bus トリガーとバインド | Microsoft Docs"
description: "Azure Functions で Azure Service Bus トリガーとバインドを使用する方法を説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: 6aed248b91d25572c4eae691f4e5392e37c01400
ms.openlocfilehash: e2d81d140c194a33ea6f1462effb09a9e283d3af
ms.lasthandoff: 02/22/2017


---
# <a name="azure-functions-service-bus-bindings"></a>Azure Functions における Service Bus のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Service Bus のバインドを構成およびコーディングする方法について説明します。 Azure Functions は、Notification Hubs キューおよびトピックのトリガーおよび出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Service Bus トリガー
Service Bus トリガーを使用して、Service Bus キューまたはトピックからのメッセージに応答します。 

関数への Notification Hubs キューおよびトピック トリガーでは、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

* "*キュー*" トリガー:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* "*トピック*" トリガー:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

以下の点に注意してください。

* `connection` に対しては、Service Hub 名前空間への接続文字列を含む[アプリ設定を Function App で作成]()し、トリガーの `connection` プロパティでアプリ設定の名前を指定します。 接続文字列は、「[管理資格情報の取得](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)」の手順に従って取得します。
  接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。
  `connection` を空にした場合、既定の Service Bus 接続文字列が `AzureWebJobsServiceBus` という名前のアプリ設定に指定されているとものと見なされます。
* `accessRights` で使用できる値は `manage` および `listen` です。 既定値は `manage` で、`connection` が**管理**アクセス許可を持つことを示します。 **管理**アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を `listen` に設定します。 そうしないと、Functions ランタイムは、管理権限を必要とする操作を試行し、失敗する可能性があります。

## <a name="trigger-behavior"></a>トリガーの動作
* **シングル スレッド** - Functions ランタイムは、既定で複数のメッセージを同時に処理します。 一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、*host.json* ファイルで `serviceBus.maxConcurrentCalls` を 1 に設定します。 
  *host.json* の詳細については、「[フォルダー構造](functions-reference.md#folder-structure)」および「[host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)」を参照してください。
* **有害メッセージの処理** - Service Bus では、Azure Functions の構成やコードで制御または構成することができない、独自の有害メッセージを処理します。 
* **PeekLock 動作** - Functions ランタイムは、[`PeekLock` モード](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)でメッセージを受信して、関数が正常に終了した場合はメッセージの `Complete` を呼び出し、関数が失敗した場合は `Abandon` を呼び出します。 
  関数の実行時間が `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>トリガーの使用方法
このセクションでは、Service Hub トリガーを関数のコードで使用する方法について説明します。 

C# および F# では、Service Bus トリガー メッセージを、次のいずれかの入力型に逆シリアル化できます。

* `string` - 文字列メッセージに有効です。
* `byte[]` - バイナリ データに有効です。
* すべての[オブジェクト](https://msdn.microsoft.com/library/system.object.aspx) - JSON でシリアル化されたデータに有効です。
  カスタム入力型を宣言した場合 (例: `FooType`)、Azure Functions は、指定した型に JSON データを逆シリアル化しようとします。
* `BrokeredMessage` - [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) メソッドで逆シリアル化されたメッセージを返します。

Node.js では、Service Bus トリガー メッセージは、文字列として (JSON メッセージの場合は JavaScript オブジェクトとして) 関数に渡されます。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>トリガー サンプル
次の function.json があるとします。

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Service Bus キュー メッセージを処理する、言語固有のサンプルを参照してください。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C でのトリガー サンプル# #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F でのトリガー サンプル# #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js でのトリガー サンプル

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Service Bus 出力バインド
関数への Notification Hubs キューおよびトピック出力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

* "*キュー*" 出力:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>"
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* "*トピック*" 出力:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>"
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

以下の点に注意してください。

* `connection` に対しては、Service Hub 名前空間への接続文字列を含む[アプリ設定を Function App で作成]()し、出力バインドの `connection` プロパティでアプリ設定の名前を指定します。 接続文字列は、「[管理資格情報の取得](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)」の手順に従って取得します。
  接続文字列は、特定のキューまたはトピックに限らず、Service Bus 名前空間のものである必要があります。
  `connection` を空にした場合、既定の Service Bus 接続文字列が `AzureWebJobsServiceBus` という名前のアプリ設定に指定されているとものと見なされます。
* `accessRights` で使用できる値は `manage` および `listen` です。 既定値は `manage` で、`connection` が**管理**アクセス許可を持つことを示します。 **管理**アクセス許可を持たない接続文字列を使用する場合は、`accessRights` を `listen` に設定します。 そうしないと、Functions ランタイムは、管理権限を必要とする操作を試行し、失敗する可能性があります。

<a name="outputusage"></a>

## <a name="output-usage"></a>出力の使用方法
C# および F# では、Azure Functions は、次の型のいずれかから Service Bus キュー メッセージを作成できます。

* すべての[オブジェクト](https://msdn.microsoft.com/library/system.object.aspx) - パラメーター定義は `out T paramName` のようになります (C#)。
  Functions は、オブジェクトを JSON メッセージに逆シリアル化します。 関数が終了したときに出力値が null の場合、Functions は、null オブジェクトでメッセージを作成します。
* `string` - パラメーター定義は `out string paraName` のようになります (C#)。 関数が終了したときにパラメーター値が null でない場合は、Functions はメッセージを作成します。
* `byte[]` - パラメーター定義は `out byte[] paraName` のようになります (C#)。 関数が終了したときにパラメーター値が null でない場合は、Functions はメッセージを作成します。
* `BrokeredMessage` - パラメーター定義は `out BrokeredMessage paraName` のようになります (C#)。 関数が終了したときにパラメーター値が null でない場合は、Functions はメッセージを作成します。

C# 関数で複数のメッセージを作成するには、`ICollector<T>` または `IAsyncCollector<T>` を使用できます。 `Add` メソッドを呼び出すときに、メッセージが作成されます。

Node.js では、文字列、バイト配列、または (JSON に逆シリアル化された) Javascript オブジェクトを `context.binding.<paramName>` に割り当てることができます。

<a name="outputsample"></a>

## <a name="output-sample"></a>出力サンプル
Service Bus キュー出力を定義する、次の function.json があるとします。

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Service Bus キューにメッセージを送信する、言語固有のサンプルを参照してください。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C での出力サンプル# #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

または、複数のメッセージを作成するには次のようにします。

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F での出力サンプル# #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js での出力サンプル

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

または、複数のメッセージを作成するには次のようにします。

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


