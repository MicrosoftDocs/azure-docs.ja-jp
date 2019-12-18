---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 27333f272ca5000fd3b09b305712875c065f6bc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924434"
---
## <a name="trigger"></a>トリガー

関数トリガーを使用して、イベント ハブのイベント ストリームに送信されたイベントに応答します。 トリガーを設定するには、基になるイベント ハブへの読み取りアクセスが必要です。 関数がトリガーされると、その関数に渡されるメッセージが文字列として型指定されます。

## <a name="trigger---scaling"></a>トリガー - スケーリング

イベントによってトリガーされる関数の各インスタンスには、1 つの [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスが対応します。 (Event Hubs によって提供される) トリガーにより、1 つの [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスだけが特定のパーティションのリースを取得できるようになります。

たとえば、次のようなイベント ハブを検討します。

* 10 個のパーティション。
* 1000 個のイベントがすべてのパーティション間で均等に分散され、各パーティション内に 100 個のメッセージがある。

関数が最初に有効化されたときに存在する関数インスタンスは 1 つのみです。 最初の関数インスタンス `Function_0` を呼び出しましょう。 `Function_0` 関数は、全 10 個のパーティションの 1 つのリースを保持する [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) の単一のインスタンスを持ちます。 このインスタンスは、パーティション 0 ～ 9 からイベントを読み取ります。 この後、次のいずれかが発生します。

* **新しい関数インスタンスは必要とされない**: `Function_0` は、Functions のスケーリング ロジックが有効になる前に、1000 個のイベントをすべて処理できます。 この場合、1000 個のメッセージはすべて `Function_0` によって処理されます。

* **別の関数インスタンスが追加される**:Functions のスケーリング ロジックによって、`Function_0` にその処理能力を超える数のメッセージがあると判断されると、新しい関数アプリ インスタンス (`Function_1`) が作成されます。 この新しい関数にも、[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) のインスタンスが関連付けられています。 基になる Event Hubs は、新しいホスト インスタンスがメッセージを読み取ろうとしていることを検出すると、ホスト インスタンス間でパーティションを負荷分散します。 たとえば、パーティション 0 ～ 4 を `Function_0` に割り当て、パーティション 5 ～ 9 を `Function_1` に割り当てることができます。

* **さらに N 個の関数インスタンスが追加される**:Functions のスケーリング ロジックによって、`Function_0` と `Function_1` のどちらにもその処理能力を超える数のメッセージがあると判断されると、新しい `Functions_N` 関数アプリ インスタンスが作成されます。  アプリは、`N` がイベント ハブ パーティションの数を超えた時点で作成されます。 この例では、Event Hubs は、パーティションを再び負荷分散します。この場合、 `Function_0`...`Functions_9` のインスタンス間で負荷分散します。

Functions がスケーリングすると、インスタンスの数 `N` が、イベント ハブ パーティションの数より多くなります。 これは、[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスが、他のインスタンスから解放されて使用可能になったパーティションのロックを確実に取得できるようにするためです。 関数インスタンスの実行時に使用されたリソースにのみ課金されます。 つまり、オーバー プロビジョニングには課金されません。

すべての関数の実行が (エラーの有無にかかわらず) 完了すると、関連付けられているストレージ アカウントにチェックポイントが追加されます。 チェックポイントの追加が成功したら、1000 個のすべてのメッセージが再度取得されることはありません。

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [C#](#trigger---c-example)
* [C# スクリプト (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、イベント ハブ トリガーのメッセージ本文をログに記録する [C# 関数](../articles/azure-functions/functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

関数コードの[イベント メタデータ](#trigger---event-metadata)にアクセスするには、(`Microsoft.Azure.EventHubs` の using ステートメントを必要とする) [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトにバインドします。 また、メソッド シグネチャ内のバインディング式を使用して、同じプロパティにアクセスすることもできます。  次の例は、同じデータを取得する 2 つの方法を示しています。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

イベントを一括で受け取るには、`string` または `EventData` を配列にします。  

> [!NOTE]
> 一括で受け取る場合、上記の例のように `DateTime enqueuedTimeUtc` を使用してメソッド パラメーターをバインドすることはできないため、各 `EventData` オブジェクトからパラメーターを受け取る必要があります。  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](../articles/azure-functions/functions-reference-csharp.md)を示しています。 この関数では、イベント ハブ トリガーのメッセージ本文を記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

#### <a name="version-2x-and-higher"></a>バージョン 2.x 以降

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>バージョン 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

C# スクリプト コードを次に示します。

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

関数コードの[イベント メタデータ](#trigger---event-metadata)にアクセスするには、(`Microsoft.Azure.EventHubs` の using ステートメントを必要とする) [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトにバインドします。 また、メソッド シグネチャ内のバインディング式を使用して、同じプロパティにアクセスすることもできます。  次の例は、同じデータを取得する 2 つの方法を示しています。

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

イベントを一括で受け取るには、`string` または `EventData` を配列にします。

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>トリガー - F# の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [F# 関数](../articles/azure-functions/functions-reference-fsharp.md)を示しています。 この関数では、イベント ハブ トリガーのメッセージ本文を記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。 

#### <a name="version-2x-and-higher"></a>バージョン 2.x 以降

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>バージョン 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

F# コードを次に示します。

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [JavaScript 関数](../articles/azure-functions/functions-reference-node.md)を示しています。 この関数は、[イベント メタデータ](#trigger---event-metadata)を読み取り、メッセージをログに記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

#### <a name="version-2x-and-higher"></a>バージョン 2.x 以降

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>バージョン 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

イベントをまとめて受け取るには、次の例に示すように、*function.json* ファイルで `cardinality` を `many` に設定します。

#### <a name="version-2x-and-higher"></a>バージョン 2.x 以降

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>バージョン 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>トリガー - Python の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [Python 関数](../articles/azure-functions/functions-reference-python.md)を示しています。 この関数は、[イベント メタデータ](#trigger---event-metadata)を読み取り、メッセージをログに記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>トリガー - Java の例

次の例は、*function.json* ファイルの Event Hub トリガー バインドと、そのバインドが使用される [Java 関数](../articles/azure-functions/functions-reference-java.md)を示しています。 この関数では、Event Hub トリガーのメッセージ本文を記録します。

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 [Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Event Hub に由来するパラメーター上で `EventHubTrigger` 注釈を使用します。 これらの注釈を使用したパラメーターによって、イベントを受信したときに関数が実行されます。  この注釈は、Java のネイティブ型、POJO、または Optional\<T> を使用した null 許容値で使用できます。

## <a name="trigger---attributes"></a>トリガー - 属性

[C# クラス ライブラリ](../articles/azure-functions/functions-dotnet-class-library.md)では、[EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 属性を使用します。

この属性のコンストラクターは、イベント ハブの名前とコンシューマー グループの名前のほか、接続文字列が含まれたアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[トリガー - 構成](#trigger---configuration)」セクションを参照してください。 `EventHubTriggerAttribute` 属性の例を次に示します。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

完全な例については、「[トリガー - C# の例](#trigger---c-example)」を参照してください。

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `EventHubTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `eventHubTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のイベント項目を表す変数の名前。 |
|**path** |**EventHubName** | Functions 1.x のみ。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**eventHubName** |**EventHubName** | Functions 2.x 以降。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**consumerGroup** |**ConsumerGroup** | ハブのイベントのサブスクライブに使用される[コンシューマー グループ](../articles/event-hubs/event-hubs-features.md#event-consumers)を設定する、省略可能なプロパティ。 省略した場合は、`$Default` コンシューマー グループが使用されます。 |
|**cardinality** | 該当なし | Javascript 用。 バッチ処理を有効にするには `many` に設定します。  省略するか、`one` に設定した場合、1 つのメッセージが関数に渡されます。 |
|**connection** |**Connection** | イベント ハブの名前空間への接続文字列が含まれたアプリ設定の名前。 この接続文字列をコピーするには、イベント ハブ自体ではなく、"[名前空間](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)" の **[接続情報]** をクリックします。 この接続文字列には、トリガーをアクティブにするために少なくとも読み取りアクセス許可が必要です。|
|**path**|**EventHubName**|イベント ハブの名前。 アプリ設定を介して参照できます `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>トリガー - イベント メタデータ

Event Hubs トリガーには、いくつかの[メタデータ プロパティ](../articles/azure-functions/./functions-bindings-expressions-patterns.md)があります。 これらのプロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらは [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) クラスのプロパティです。

|プロパティ|種類|説明|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` インスタンス。|
|`EnqueuedTimeUtc`|`DateTime`|エンキューされた時刻 (UTC)。|
|`Offset`|`string`|イベント ハブ パーティション ストリームを基準としたデータのオフセット。 オフセットは、Event Hubs ストリーム内のイベントのマーカーまたは識別子です。 この識別子は、Event Hubs ストリームのパーティション内で一意です。|
|`PartitionKey`|`string`|イベント データを送信するパーティション。|
|`Properties`|`IDictionary<String,Object>`|イベント データのユーザー プロパティ。|
|`SequenceNumber`|`Int64`|イベントの論理シーケンス番号。|
|`SystemProperties`|`IDictionary<String,Object>`|イベント データなどのシステム プロパティ。|

この記事の前半でこれらのプロパティを使用している[コード例](#trigger---example)を参照してください。

## <a name="trigger---hostjson-properties"></a>トリガー - host.json のプロパティ

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) ファイルには、Event Hubs トリガーの動作を制御する設定が含まれています。

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Output

Event Hubs 出力バインドを使用して、イベント ストリームにイベントを書き込みます。 イベントを書き込むには、イベント ハブへの送信アクセス許可が必要です。

出力バインディングを実装する前に、必要なパッケージ参照が用意されていることを確認してください。

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [C#](#output---c-example)
* [C# スクリプト (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>出力 - C# の例

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

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

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

### <a name="output---f-example"></a>出力 - F# の例

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [F# 関数](../articles/azure-functions/functions-reference-fsharp.md)を示しています。 この関数では、メッセージをイベント ハブに書き込みます。

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

F# コードを次に示します。

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>出力 - JavaScript の例

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

### <a name="output---python-example"></a>出力 - Python の例

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

### <a name="output---java-example"></a>出力 - Java の例

次の例は、現在の時刻を含むメッセージを Event Hub に書き込む Java 関数を示しています。

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Event Hub に公開されるパラメーター上で `@EventHubOutput` 注釈を使用します。  パラメーターの型は `OutputBinding<T>` にする必要があります。T は POJO または Java の任意のネイティブ型です。

## <a name="output---attributes"></a>出力 - 属性

[C# クラス ライブラリ](../articles/azure-functions/functions-dotnet-class-library.md)では、[EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) 属性を使用します。

この属性のコンストラクターは、イベント ハブの名前のほか、接続文字列が含まれたアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[出力 - 構成](#output---configuration)」を参照してください。 `EventHub` 属性の例を次に示します。

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

完全な例については、「[出力 - C# の例](#output---c-example)」を参照してください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `EventHub` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "eventHub" に設定する必要があります。 |
|**direction** | 該当なし | "out" に設定する必要があります。 このパラメーターは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 該当なし | イベントを表す関数コードに使用される変数の名前。 |
|**path** |**EventHubName** | Functions 1.x のみ。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**eventHubName** |**EventHubName** | Functions 2.x 以降。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**connection** |**Connection** | イベント ハブの名前空間への接続文字列が含まれたアプリ設定の名前。 この接続文字列をコピーするには、イベント ハブ自体ではなく、"*名前空間*" の **[接続情報]** をクリックします。 この接続文字列には、イベント ストリームにメッセージを送信するための送信アクセス許可が必要です。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

C# および C# スクリプトでは、`out string paramName` などのメソッド パラメーターを使用してメッセージを送信します。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 複数のメッセージを書き込むには、`out string` の代わりに `ICollector<string>` または `IAsyncCollector<string>` を使用します。

JavaScript では、`context.bindings.<name>` を使用して出力イベントにアクセスします。 `<name>` は *function.json* の `name` プロパティで指定された値です。

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| イベント ハブ | [運用ガイド](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x 以降でこのバインディングに使用可能なグローバル構成設定について説明します。 次の host.json ファイルの例には、このバインディングのバージョン 2.x 以降の設定のみが含まれています。 バージョン 2.x 以降でのグローバル構成設定の詳細については、[Azure Functions の host.json のリファレンス](../articles/azure-functions/functions-host-json.md)に関する記事を参照してください。

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](../articles/azure-functions/functions-host-json-v1.md)」を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|maxBatchSize|64|受信ループあたりで受信される最大イベント数。|
|prefetchCount|該当なし|基になる EventProcessorHost に使用される既定の PrefetchCount。|
|batchCheckpointFrequency|1|EventHub カーソル チェックポイントを作成する前に処理するイベント バッチ数。|
