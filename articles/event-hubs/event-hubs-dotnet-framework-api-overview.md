---
title: "Azure Event Hubs .NET Framework API の概要 | Microsoft Docs"
description: "主要な Event Hubs .NET Framework クライアント API のまとめ。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7f3b6cc0-9600-417f-9e80-2345411bd036
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: 0e59d117bd9a1355b5b58c76cf9b39ec3005b122


---
# <a name="event-hubs-net-framework-api-overview"></a>Event Hubs .NET Framework API の概要
この記事は主要な Event Hubs .NET Framework クライアント API についてまとめてあります。 2 つのカテゴリがあります。管理 API とランタイム API です。 ランタイム API はメッセージの送受信に必要なすべての操作で構成されています。 管理操作では、エンティティを作成、更新、削除することで Event Hubs エンティティの状態を管理できます。

監視シナリオは管理とランタイムの両方にまたがります。 .NET API に関する詳細なリファレンス ドキュメントについては、[Service Bus .NET](/dotnet/api) と [EventProcessorHost API](/dotnet/api) に関するリファレンスを参照してください。

## <a name="management-apis"></a>管理 API
次の管理操作を実行するには、Event Hubs 名前空間の **管理** 権限が必要になります。

### <a name="create"></a>作成
```csharp
// Create the Event Hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>更新
```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>削除
```csharp
await namespaceManager.DeleteEventHubAsync("Event Hub name");
```

## <a name="run-time-apis"></a>ランタイム API
### <a name="create-publisher"></a>発行元の作成
```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>メッセージの発行
```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>コンシューマーの作成
```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>メッセージの使用
```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>イベント プロセッサ ホスト API
この API は、利用可能な worker にパーティションを分散することによって、利用不可になる可能性がある worker プロセスに回復性を与えます。

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

[IEventProcessor](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) インターフェイスは次のように定義されています。

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
Event Hubs シナリオに関する詳細については、次のリンク先を参照してください。

* [Azure Event Hubs とは](event-hubs-what-is-event-hubs.md)
* [Event Hubs のプログラミング ガイド](event-hubs-programming-guide.md)

.NET API リファレンスはここにあります。

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.ServiceBus.EventProcessorHost](/dotnet/api/microsoft.azure.servicebus.eventprocessorhost)



<!--HONumber=Feb17_HO1-->


