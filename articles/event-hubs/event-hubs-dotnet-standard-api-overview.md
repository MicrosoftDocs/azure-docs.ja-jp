---
title: "Azure Event Hubs .NET Standard API の概要 | Microsoft Docs"
description: ".NET Standard API の概要"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: a173f8e4-556c-42b8-b856-838189f7e636
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: f77755bed5463c0c87f698ded5c80d824efbd8b0
ms.openlocfilehash: 101727b54aa198411efaa5bfa7c0859bcfeb5417

---

# <a name="event-hubs-net-standard-api-overview"></a>Event Hubs .NET Standard API の概要
この記事は主要な Event Hubs .NET Standard クライアント API についてまとめてあります。 現在&2; つの .NET Standard クライアント ライブラリがあります。
* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
  *  このライブラリは、基本的なランタイム操作がすべて用意されています。
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
  * このライブラリは、処理済みイベントを追跡するための機能を追加します。これにより、最も簡単に Event Hub から読み取ることができます。

## <a name="event-hub-client"></a>Event Hub クライアント
[**EventHubClient**](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) は、イベントの送信、受信側の作成、ランタイム情報の取得に使用する主要オブジェクトです。 このクライアントは、特定の Event Hub にリンクされ、Event Hubs エンドポイントへの新しい接続を作成します。

### <a name="create-an-event-hub-client"></a>Event Hub クライアントの作成
[**EventHubClient**](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) オブジェクトは接続文字列から作成されます。 新しいクライアントを最も簡単にインスタンス化する方法を次の例に示します。

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("{Event Hub connection string}");
```

接続文字列をプログラムで編集するには、[**EventHubsConnectionStringBuilder**](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) クラスを使用して、接続文字列をパラメーターとして [**EventHubClient.CreateFromConnectionString**](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_) に渡します。

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("{Event Hub connection string}")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>イベントの送信
Event Hub にイベントを送信するには、[**EventData**](/dotnet/api/microsoft.azure.eventhubs.eventdata) クラスを使用します。 本文は `byte` 配列または `byte` 配列セグメントにする必要があります。

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>イベントの受信
Event Hubs からイベントを受信する場合は、[**EventProcessorHost**](##Event-Processor-Host-APIs) を使用することをお勧めします。これにより、オフセットとパーティション情報を自動的に追跡する機能を利用できます。 ただし、主要 Event Hubs ライブラリを使用して、柔軟にイベントを受信することもできます。

#### <a name="create-a-receiver"></a>受信側の作成
受信側は特定のパーティションに関連付けられています。したがって、Event Hub のすべてのイベントを受信するには、複数のインスタンスを作成する必要があります。 一般に、パーティション情報は、パーティション ID をハード コーディングするのではなく、プログラムによって取得することをお勧めします。 それには、[**GetRuntimeInformationAsync**](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync) メソッドを使用します。

```csharp

// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition ids
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

イベントは Event Hub から削除されることはないため (期限切れになるだけです)、適切な開始ポイントを指定する必要があります。 次の例は、使用できる組み合わせを示しています。

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant 'PartitionReceiver.EndOfStream' will only receive all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>イベントの使用
```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>イベント プロセッサ ホスト API
この API は、利用可能な worker にパーティションを分散することによって、利用不可になる可能性がある worker プロセスに回復性を与えます。

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{Event Hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes of the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

[IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) の実装例を次に示します。

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>次のステップ
Event Hubs シナリオに関する詳細については、次のリンク先を参照してください。

* [Azure Event Hubs とは](event-hubs-what-is-event-hubs.md)
* [使用可能な Event Hubs API](event-hubs-api-overview.md)

.NET API リファレンスはここにあります。

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)


<!--HONumber=Feb17_HO1-->


