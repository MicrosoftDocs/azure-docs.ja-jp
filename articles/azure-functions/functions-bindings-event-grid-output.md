---
title: Azure Functions の Azure Event Grid 出力バインディング
description: Azure Functions で Event Grid イベントを送信する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 888afdc2764fed9f0b2c8b548c3e2b1c48e9a31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97094678"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions の Azure Event Grid 出力バインディング

イベントをカスタム トピックに書き込むには、Event Grid 出力バインディングを使用します。 有効な[カスタム トピックのアクセス キー](../event-grid/security-authenticate-publishing-clients.md)が必要です。

セットアップと構成の詳細については、[概要](./functions-bindings-event-grid.md)を参照してください。

> [!NOTE]
> Event Grid 出力バインディングでは、Shared Access Signature (SAS トークン) はサポートされていません。 トピックのアクセス キーを使用する必要があります。

> [!IMPORTANT]
> Event Grid 出力バインディングは、Functions 2.x 以降でのみ使用することができます。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、メソッドの戻り値を出力として使用してメッセージを Event Grid のカスタム トピックに書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

次の例では、`IAsyncCollector` インターフェイスを使用してメッセージのバッチを送信する方法を示します。

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイル内の Event Grid 出力バインディング データを示しています。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

1 つのイベントを作成する C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

複数のイベントを作成する C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

Event Grid 出力バインディングは、Java では使用できません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイル内の Event Grid 出力バインディング データを示しています。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

単一のイベントを作成する JavaScript コードを次に示します。

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

複数のイベントを作成する JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、Event Grid イベント メッセージを出力するように関数を構成する方法を示しています。 `type` が `eventGrid` に設定されているセクションでは、Event Grid 出力バインディングを確立するのに必要な値が構成されます。

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

関数では、`Push-OutputBinding` を使用して、Event Grid 出力バインディングを介してカスタム トピックにイベントを送信します。

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 次に、`topicEndpointUri` によって指定されたとおり、イベントがカスタム トピックに送信されます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

`EventGridOutputEvent` を設定してカスタム トピックにイベントを送信する Python サンプルを次に示します。

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) 属性を使用します。

属性のコンストラクターは、カスタム トピックの名前を含むアプリ設定の名前と、トピック キーを含むアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[出力 - 構成](#configuration)」を参照してください。 `EventGrid` 属性の例を次に示します。

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

完全な例については、「[例](#example)」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

Event Grid 出力バインディングは、Java では使用できません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

Event Grid 出力バインディングは、Python では使用できません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `EventGrid` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "eventGrid" に設定する必要があります。 |
|**direction** | 該当なし | "out" に設定する必要があります。 このパラメーターは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | イベントを表す関数コードに使用される変数の名前。 |
|**topicEndpointUri** |**TopicEndpointUri** | カスタム トピックの URI を含むアプリ設定の名前 (例: `MyTopicEndpointUri`)。 |
|**topicKeySetting** |**TopicKeySetting** | カスタム トピックのアクセス キーを含むアプリ設定の名前。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> `TopicEndpointUri` 構成プロパティの値は、確実に、カスタム トピックの URI を含むアプリ設定の名前に設定してください。 このプロパティにカスタム トピックの URI を直接指定しないでください。

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

`out EventGridEvent paramName` などのメソッド パラメーターを使用してメッセージを送信します。 複数のメッセージを書き込むには、`out EventGridEvent` の代わりに `ICollector<EventGridEvent>` または `IAsyncCollector<EventGridEvent>` を使用します。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

`out EventGridEvent paramName` などのメソッド パラメーターを使用してメッセージを送信します。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 複数のメッセージを書き込むには、`out EventGridEvent` の代わりに `ICollector<EventGridEvent>` または `IAsyncCollector<EventGridEvent>` を使用します。

# <a name="java"></a>[Java](#tab/java)

Event Grid 出力バインディングは、Java では使用できません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>` を使用して出力イベントにアクセスします。`<name>` は *function.json* の `name` プロパティで指定された値です。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

`Push-OutputBinding` コマンドレットを使用して出力イベントにアクセスし、Event Grid 出力バインディングにイベントを送信します。

# <a name="python"></a>[Python](#tab/python)

Event Grid 出力バインディングは、Python では使用できません。

---

## <a name="next-steps"></a>次のステップ

* [Event Grid イベントをディスパッチする](./functions-bindings-event-grid-trigger.md)
