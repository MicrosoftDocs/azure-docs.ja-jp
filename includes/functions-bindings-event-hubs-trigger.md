---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 145db7693db126d4e114e8c8a885ea7fd7809e69
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608914"
---
関数トリガーを使用して、イベント ハブのイベント ストリームに送信されたイベントに応答します。 トリガーを設定するには、基になるイベント ハブへの読み取りアクセスが必要です。 関数がトリガーされると、その関数に渡されるメッセージが文字列として型指定されます。

## <a name="scaling"></a>Scaling

イベントによってトリガーされる関数の各インスタンスには、1 つの [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスが対応します。 (Event Hubs によって提供される) トリガーにより、1 つの [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスだけが特定のパーティションのリースを取得できるようになります。

たとえば、次のようなイベント ハブを検討します。

* 10 個のパーティション。
* 1000 個のイベントがすべてのパーティション間で均等に分散され、各パーティション内に 100 個のメッセージがある。

関数が最初に有効化されたときに存在する関数インスタンスは 1 つのみです。 最初の関数インスタンス `Function_0` を呼び出しましょう。 `Function_0` 関数は、全 10 個のパーティションの 1 つのリースを保持する [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) の単一のインスタンスを持ちます。 このインスタンスは、パーティション 0 ～ 9 からイベントを読み取ります。 この後、次のいずれかが発生します。

* **新しい関数インスタンスは必要とされない**: `Function_0` は、Functions のスケーリング ロジックが有効になる前に、1000 個のイベントをすべて処理できます。 この場合、1000 個のメッセージはすべて `Function_0` によって処理されます。

* **別の関数インスタンスが追加される**:Functions のスケーリング ロジックによって、`Function_0` にその処理能力を超える数のメッセージがあると判断されると、新しい関数アプリ インスタンス (`Function_1`) が作成されます。 この新しい関数にも、[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) のインスタンスが関連付けられています。 基になる Event Hubs は、新しいホスト インスタンスがメッセージを読み取ろうとしていることを検出すると、ホスト インスタンス間でパーティションを負荷分散します。 たとえば、パーティション 0 ～ 4 を `Function_0` に割り当て、パーティション 5 ～ 9 を `Function_1` に割り当てることができます。

* **さらに N 個の関数インスタンスが追加される**:Functions のスケーリング ロジックによって、`Function_0` と `Function_1` のどちらにもその処理能力を超える数のメッセージがあると判断されると、新しい `Functions_N` 関数アプリ インスタンスが作成されます。  アプリは、`N` がイベント ハブ パーティションの数を超えた時点で作成されます。 この例では、Event Hubs は、パーティションを再び負荷分散します。この場合、 `Function_0`...`Functions_9` のインスタンス間で負荷分散します。

スケーリングが発生すると、`N` インスタンスはイベント ハブ パーティションの数よりも大きい数になります。 このパターンは、[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) インスタンスが、他のインスタンスから解放されて使用可能になったパーティションのロックを確実に取得するために使用されます。 関数インスタンスの実行時に使用されたリソースにのみ課金されます。 つまり、オーバー プロビジョニングには課金されません。

すべての関数の実行が (エラーの有無にかかわらず) 完了すると、関連付けられているストレージ アカウントにチェックポイントが追加されます。 チェックポイントの追加が成功したら、1000 個のすべてのメッセージが再度取得されることはありません。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

次の例は、イベント ハブ トリガーのメッセージ本文をログに記録する [C# 関数](../articles/azure-functions/functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

関数コードの[イベント メタデータ](#event-metadata)にアクセスするには、(`Microsoft.Azure.EventHubs` の using ステートメントを必要とする) [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトにバインドします。 また、メソッド シグネチャ内のバインディング式を使用して、同じプロパティにアクセスすることもできます。  次の例は、同じデータを取得する 2 つの方法を示しています。

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

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](../articles/azure-functions/functions-reference-csharp.md)を示しています。 この関数では、イベント ハブ トリガーのメッセージ本文を記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

### <a name="version-2x-and-higher"></a>バージョン 2.x 以降

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>バージョン 1.x

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

関数コードの[イベント メタデータ](#event-metadata)にアクセスするには、(`Microsoft.Azure.EventHubs` の using ステートメントを必要とする) [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトにバインドします。 また、メソッド シグネチャ内のバインディング式を使用して、同じプロパティにアクセスすることもできます。  次の例は、同じデータを取得する 2 つの方法を示しています。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [JavaScript 関数](../articles/azure-functions/functions-reference-node.md)を示しています。 この関数は、[イベント メタデータ](#event-metadata)を読み取り、メッセージをログに記録します。

次の例は、*function.json* ファイル内の Event Hubs バインディング データを示しています。

### <a name="version-2x-and-higher"></a>バージョン 2.x 以降

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>バージョン 1.x

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

### <a name="version-2x-and-higher"></a>バージョン 2.x 以降

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

### <a name="version-1x"></a>バージョン 1.x

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

# <a name="python"></a>[Python](#tab/python)

次の例は、*function.json* ファイルのイベント ハブ トリガー バインドと、そのバインドが使用される [Python 関数](../articles/azure-functions/functions-reference-python.md)を示しています。 この関数は、[イベント メタデータ](#event-metadata)を読み取り、メッセージをログに記録します。

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

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = ', event.metadata[key])
```

# <a name="java"></a>[Java](#tab/java)

次の例は、イベント ハブ トリガーのメッセージ本文をログに記録するイベント ハブ トリガー バインドを示しています。

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

 [Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Event Hub に由来するパラメーター上で `EventHubTrigger` 注釈を使用します。 これらの注釈を使用したパラメーターによって、イベントを受信したときに関数が実行されます。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

 ---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](../articles/azure-functions/functions-dotnet-class-library.md)では、[EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 属性を使用します。

この属性のコンストラクターは、イベント ハブの名前とコンシューマー グループの名前のほか、接続文字列が含まれたアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[トリガー - 構成](#configuration)」セクションを参照してください。 `EventHubTriggerAttribute` 属性の例を次に示します。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

完全な例については、「[トリガー - C# の例](#example)」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

Java [関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Event Hub に由来するパラメーター上で [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) 注釈を使用します。 これらの注釈を使用したパラメーターによって、イベントを受信したときに関数が実行されます。 この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `EventHubTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `eventHubTrigger` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | `in` に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のイベント項目を表す変数の名前。 |
|**path** |**EventHubName** | Functions 1.x のみ。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 |
|**eventHubName** |**EventHubName** | Functions 2.x 以降。 イベント ハブの名前。 イベント ハブの名前は接続文字列にも存在し、その値が実行時にこのプロパティをオーバーライドします。 [アプリ設定](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings) `%eventHubName%` を介して参照できます |
|**consumerGroup** |**ConsumerGroup** | ハブのイベントのサブスクライブに使用される[コンシューマー グループ](../articles/event-hubs/event-hubs-features.md#event-consumers)を設定する、省略可能なプロパティ。 省略した場合は、`$Default` コンシューマー グループが使用されます。 |
|**cardinality** | 該当なし | すべての C# 以外の言語で使用されます。 バッチ処理を有効にするには `many` に設定します。  省略するか、`one` に設定した場合、1 つのメッセージが関数に渡されます。<br><br>C# では、トリガーにこの型の配列がある場合は常に、このプロパティが自動的に割り当てられます。|
|**connection** |**Connection** | イベント ハブの名前空間への接続文字列が含まれたアプリ設定の名前。 この接続文字列をコピーするには、イベント ハブ自体ではなく、"[名前空間](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)" の **[接続情報]** をクリックします。 この接続文字列には、トリガーをアクティブにするために少なくとも読み取りアクセス許可が必要です。<br><br>[バージョン 5.x またはそれ以降の拡張機能](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)を使用している場合は、接続文字列の代わりに、接続を定義する構成セクションへの参照を指定できます。 「[接続](../articles/azure-functions/functions-reference.md#connections)」を参照してください。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Default

トリガーするイベント ハブには、次のパラメーターの型を使用できます。

* `string`
* `byte[]`
* `POCO`
* `EventData` - EventData の既定のプロパティは、[Microsoft.Azure.EventHubs 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)に用意されています。

### <a name="additional-types"></a>その他の型 
5\.0.0 以降のバージョンのイベント ハブ拡張機能を使用するアプリでは、[Microsoft.Azure.EventHubs 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)の代わりに [Azure.Messaging.EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) の `EventData` 型を使用します。 このバージョンでは、次の型を優先して、レガシ `Body` 型のサポートがなくなります。

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

### <a name="default"></a>Default

トリガーするイベント ハブには、次のパラメーターの型を使用できます。

* `string`
* `byte[]`
* `POCO`
* `EventData` - EventData の既定のプロパティは、[Microsoft.Azure.EventHubs 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)に用意されています。

### <a name="additional-types"></a>その他の型 
5\.0.0 以降のバージョンのイベント ハブ拡張機能を使用するアプリでは、[Microsoft.Azure.EventHubs 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet)の代わりに [Azure.Messaging.EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) の `EventData` 型を使用します。 このバージョンでは、次の型を優先して、レガシ `Body` 型のサポートがなくなります。

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="java"></a>[Java](#tab/java)

詳細については、Java の[トリガー例](#example)を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

詳細については、Javascript の[トリガー例](#example)を参照してください。

# <a name="python"></a>[Python](#tab/python)

詳細については、Python の[トリガー例](#example)を参照してください。


---


## <a name="event-metadata"></a>イベント メタデータ

Event Hubs トリガーには、いくつかの[メタデータ プロパティ](../articles/azure-functions/./functions-bindings-expressions-patterns.md)があります。 メタデータ プロパティは、他のバインドのバインド式の一部として、またはコードのパラメーターとして使用できます。 これらのプロパティは、[EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) クラスに由来します。

|プロパティ|種類|説明|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` のインスタンスです。|
|`EnqueuedTimeUtc`|`DateTime`|エンキューされた時刻 (UTC)。|
|`Offset`|`string`|イベント ハブ パーティション ストリームを基準としたデータのオフセット。 オフセットは、Event Hubs ストリーム内のイベントのマーカーまたは識別子です。 この識別子は、Event Hubs ストリームのパーティション内で一意です。|
|`PartitionKey`|`string`|イベント データを送信するパーティション。|
|`Properties`|`IDictionary<String,Object>`|イベント データのユーザー プロパティ。|
|`SequenceNumber`|`Int64`|イベントの論理シーケンス番号。|
|`SystemProperties`|`IDictionary<String,Object>`|イベント データなどのシステム プロパティ。|

この記事の前半でこれらのプロパティを使用している[コード例](#example)を参照してください。