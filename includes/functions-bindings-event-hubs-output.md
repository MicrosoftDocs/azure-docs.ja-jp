---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 9fca69804220021ca7935e562f2026c11749515a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623187"
---
Event Hubs 出力バインドを使用して、イベント ストリームにイベントを書き込みます。 イベントを書き込むには、イベント ハブへの送信アクセス許可が必要です。

出力バインディングを実装する前に、必要なパッケージ参照が用意されていることを確認してください。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

次の例は、メソッドの戻り値を出力として使用してメッセージをイベント ハブに書き込む [C# 関数](../articles/azure-functions/functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

次の例では、`IAsyncCollector` インターフェイスを使用してメッセージのバッチを送信する方法を示します。 これは、1 つのイベント ハブから送信されるメッセージを処理し、その結果を別のイベント ハブに送信する場合の一般的なシナリオです。

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](../articles/azure-functions/functions-reference-csharp.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 2.x 以降用で、2 つ目の例は Functions 1.x 用です。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

1 つのメッセージを作成する C# スクリプト コードを次に示します。

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

複数のメッセージを作成する C# スクリプト コードを次に示します。

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [JavaScript 関数](../articles/azure-functions/functions-reference-node.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 最初の例は Functions 2.x 以降用で、2 つ目の例は Functions 1.x 用です。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

1 つのメッセージを送信する JavaScript コードを次に示します。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

複数のメッセージを送信する JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [Python 関数](../articles/azure-functions/functions-reference-python.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

1 つのメッセージを送信する Python コードを次に示します。

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

次の例は、現在の時刻を含むメッセージを Event Hub に書き込む Java 関数を示しています。

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Event Hub に公開されるパラメーター上で `@EventHubOutput` 注釈を使用します。  パラメーターの型は `OutputBinding<T>` にする必要があります。T は POJO または Java の任意のネイティブ型です。

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](../articles/azure-functions/functions-dotnet-class-library.md)では、[EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) 属性を使用します。

この属性のコンストラクターは、イベント ハブの名前のほか、接続文字列が含まれたアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[出力 - 構成](#configuration)」を参照してください。 `EventHub` 属性の例を次に示します。

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

完全な例については、「[出力 - C# の例](#example)」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Event Hub に公開されるパラメーター上で [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 注釈を使用します。 パラメーターの型は `OutputBinding<T>` にする必要があります。`T` は POJO または Java の任意のネイティブ型です。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `EventHub` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "eventHub" に設定する必要があります。 |
|**direction** | 該当なし | "out" に設定する必要があります。 このパラメーターは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | イベントを表す関数コードに使用される変数の名前。 |
|**path** |**EventHubName** | Functions 1.x のみ。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**eventHubName** |**EventHubName** | Functions 2.x 以降。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**connection** |**Connection** | イベント ハブの名前空間への接続文字列が含まれたアプリ設定の名前。 この接続文字列をコピーするには、イベント ハブ自体ではなく、"[名前空間](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)" の **[接続情報]** をクリックします。 この接続文字列には、イベント ストリームにメッセージを送信するための送信アクセス許可が必要です。 <br><br>[バージョン 5.x またはそれ以降の拡張機能](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)を使用している場合は、接続文字列の代わりに、接続を定義する構成セクションへの参照を指定できます。 「[接続](../articles/azure-functions/functions-reference.md#connections)」を参照してください。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Default

Event Hub の出力バインドに次のパラメーター型を使用できます。

* `string`
* `byte[]`
* `POCO`
* `EventData` - EventData の既定のプロパティは、[Microsoft.Azure.EventHubs 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)に用意されています。

`out string paramName` などのメソッド パラメーターを使用してメッセージを送信します。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 複数のメッセージを書き込むには、`out string` の代わりに `ICollector<string>` または `IAsyncCollector<string>` を使用します。

### <a name="additional-types"></a>その他の型 
5\.0.0 以降のバージョンのイベント ハブ拡張機能を使用するアプリでは、[Microsoft.Azure.EventHubs 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)の代わりに [Azure.Messaging.EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) の `EventData` 型を使用します。 このバージョンでは、次の型を優先して、レガシ `Body` 型のサポートがなくなります。

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

### <a name="default"></a>Default

Event Hub の出力バインドに次のパラメーター型を使用できます。

* `string`
* `byte[]`
* `POCO`
* `EventData` - EventData の既定のプロパティは、[Microsoft.Azure.EventHubs 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)に用意されています。

`out string paramName` などのメソッド パラメーターを使用してメッセージを送信します。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 複数のメッセージを書き込むには、`out string` の代わりに `ICollector<string>` または `IAsyncCollector<string>` を使用します。

### <a name="additional-types"></a>その他の型 
5\.0.0 以降のバージョンのイベント ハブ拡張機能を使用するアプリでは、[Microsoft.Azure.EventHubs 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)の代わりに [Azure.Messaging.EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) の `EventData` 型を使用します。 このバージョンでは、次の型を優先して、レガシ `Body` 型のサポートがなくなります。

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>` を使用して出力イベントにアクセスします。`<name>` は *function.json* の `name` プロパティで指定された値です。

# <a name="python"></a>[Python](#tab/python)

関数からイベント ハブ メッセージを出力するには、次の 2 つのオプションがあります。

- **戻り値**:*function.json* 内の `name` プロパティを `$return` に設定します。 この構成では、関数の戻り値はイベント ハブ メッセージとして永続化されます。

- **命令型**:[Out](/python/api/azure-functions/azure.functions.out) 型として宣言されたパラメーターの [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) メソッドに値を渡します。 `set` に渡された値は、イベント ハブ メッセージとして永続化されます。

# <a name="java"></a>[Java](#tab/java)

[EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 注釈を使用して関数からイベント ハブ メッセージを出力するには、次の 2 つのオプションがあります。

- **戻り値**:関数自体に注釈を適用すると、関数の戻り値がイベントハブ メッセージとして永続化されます。

- **命令型**:メッセージ値を明示的に設定するには、[`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) 型の特定のパラメーターに注釈を適用します。 ここで、`T` は POJO または任意のネイティブ Java 型です。 この構成では、`setValue` メソッドに値を渡すと、その値がイベント ハブ メッセージとして保持されます。

---

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| イベント ハブ | [運用ガイド](/rest/api/eventhub/publisher-policy-operations) |