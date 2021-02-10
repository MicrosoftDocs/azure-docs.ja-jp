---
title: 可用性と一貫性 - Azure Event Hubs | Microsoft Docs
description: パーティションを使用して Azure Event Hubs で最大限の可用性と一貫性を実現する方法
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882197"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs における可用性と一貫性
この記事では、Azure Event Hubs でサポートされる可用性と整合性に関する情報を提供します。 

## <a name="availability"></a>可用性
Azure Event Hubs により、データセンター内の複数の障害ドメインにまたがるクラスター間で、個々のマシン、さらにはラック全体の致命的な障害のリスクが分散されます。 透過的な障害検出およびフェールオーバー メカニズムが実装されるため、通常はそのような障害が発生したときに顕著な中断なしに、保証されたサービスレベル内でサービスが動作し続けます。 [可用性ゾーン](../availability-zones/az-overview.md)の有効化オプションを使用して Event Hubs 名前空間が作成されている場合、物理的に分離された 3 つの施設間で停止リスクがさらに分散されます。また、サービスには、施設全体の致命的な損失全体に瞬時に対処するための十分な容量が予約されています。 詳細については、「[Azure Event Hubs geo ディザスター リカバリー](event-hubs-geo-dr.md)」を参照してください。

クライアント アプリケーションによってイベント ハブにイベントが送信されると、イベントはイベント ハブのパーティション間に自動的に分散されます。 何らかの理由でパーティションが使用できない場合、イベントは残りのパーティション間で分散されます。 この動作により、アップ タイムを最大にすることができます。 最大のアップ タイム時間を必要とするユース ケースでは、特定のパーティションにイベントを送信する代わりに、このモデルを使用することをお勧めします。 詳細については、「[パーティション](event-hubs-scalability.md#partitions)」をご覧ください。

## <a name="consistency"></a>一貫性
シナリオによっては、イベントの順序付けが重要になる場合があります。 たとえば、バックエンド システムで、delete コマンドの前に update コマンドを処理したいとします。 このシナリオでは、順序が維持されるように、クライアント アプリケーションによって特定のパーティションにイベントが送信されます。 コンシューマー アプリケーションによってパーティションからこれらのイベントが使用されるときに、それらは順番どおりに読み取られます。 

この構成では、送信先の特定のパーティションが使用できない場合は、エラー応答が受信される点に注意してください。 比較のポイントとして、1 つのパーティションにアフィニティがない場合は、Event Hubs サービスによって次の利用可能なパーティションにイベントが送信されます。

順番を保証しつつ、アップ タイムも最大化するためのソリューションの 1 つは、イベント処理アプリケーションの一部としてイベントを集計することです。 それを実現する最も簡単な方法は、カスタム シーケンス番号プロパティを使用してイベントにスタンプを付けることです。

このシナリオでは、プロデューサー クライアントによってイベント ハブ内の利用可能なパーティションのいずれかにイベントが送信され、アプリケーションの対応するシーケンス番号が設定されます。 このソリューションでは処理アプリケーションで状態を保持する必要がありますが、送信者には、使用できる可能性の高いエンドポイントが提示されます。

## <a name="appendix"></a>付録

### <a name="net-examples"></a>.NET の例

#### <a name="send-events-to-a-specific-partition"></a>特定のパーティションにイベントを送信する
イベントにパーティション キーを設定するか、`PartitionSender` オブジェクトを使用して (古い Microsoft.Azure.Messaging ライブラリを使用している場合) イベントを特定のパーティションにのみ送信します。 これにより、これらのイベントがパーティションから読み取られる際に、読み取られる順番が保証されます。 

新しい **Azure.Messaging.EventHubs** ライブラリを使用している場合、[パーティションへのイベント発行のための PartitionSender から EventHubProducerClient へのコードの移行](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition)に関するページを参照してください。

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 以降)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 以前)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

### <a name="set-a-sequence-number"></a>シーケンス番号を設定する
次の例では、カスタム シーケンス番号プロパティを使用してイベントにスタンプを付けます。 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 以降)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 以前)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

この例では、イベント ハブ内の利用可能なパーティションの 1 つにイベントを送信し、アプリケーションの対応するシーケンス番号を設定します。 このソリューションでは処理アプリケーションで状態を保持する必要がありますが、送信者には、使用できる可能性の高いエンドポイントが提示されます。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs サービスの概要](./event-hubs-about.md)
* [イベント ハブの作成](event-hubs-create.md)
