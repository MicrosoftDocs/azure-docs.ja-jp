---
title: 可用性と一貫性 - Azure Event Hubs | Microsoft Docs
description: パーティションを使用して Azure Event Hubs で最大限の可用性と一貫性を実現する方法
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e014a33e94fe7f90569dd2ef1e9b620eef274842
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952866"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs における可用性と一貫性
この記事では、Azure Event Hubs でサポートされる可用性と整合性に関する情報を提供します。 

## <a name="availability"></a>可用性
Azure Event Hubs により、データセンター内の複数の障害ドメインにまたがるクラスター間で、個々のマシン、さらにはラック全体の致命的な障害のリスクが分散されます。 透過的な障害検出およびフェールオーバー メカニズムが実装されるため、通常はそのような障害が発生したときに顕著な中断なしに、保証されたサービスレベル内でサービスが動作し続けます。 

[可用性ゾーン](../availability-zones/az-overview.md)を有効にして Event Hubs 名前空間が作成されている場合、物理的に分離された 3 つの施設間で停止リスクがさらに分散されます。また、サービスには、施設全体の致命的な損失全体に瞬時に対処するための十分な容量が予約されています。 詳細については、「[Azure Event Hubs geo ディザスター リカバリー](event-hubs-geo-dr.md)」を参照してください。

パーティションの指定なしでクライアント アプリケーションによってイベント ハブにイベントが送信されると、イベントはイベント ハブのパーティション間に自動的に分散されます。 何らかの理由でパーティションが使用できない場合、イベントは残りのパーティション間で分散されます。 この動作により、アップ タイムを最大にすることができます。 最大のアップ タイム時間を必要とするユース ケースでは、特定のパーティションにイベントを送信する代わりに、このモデルを使用することをお勧めします。 

## <a name="consistency"></a>一貫性
シナリオによっては、イベントの順序付けが重要になる場合があります。 たとえば、バックエンド システムで、delete コマンドの前に update コマンドを処理したいとします。 このシナリオでは、順序が維持されるように、クライアント アプリケーションによって特定のパーティションにイベントが送信されます。 コンシューマー アプリケーションによってパーティションからこれらのイベントが使用されるときに、それらは順番どおりに読み取られます。 

この構成では、送信先の特定のパーティションが使用できない場合は、エラー応答が受信される点に注意してください。 比較のポイントとして、1 つのパーティションにアフィニティがない場合は、Event Hubs サービスによって次の利用可能なパーティションにイベントが送信されます。

したがって、高可用性が最も重要である場合は、(パーティション ID またはキーを使用して) 特定のパーティションをターゲットに設定しないでください。 パーティション ID またはキーを使用すると、イベント ハブの可用性がパーティション レベルにダウングレードされます。 このシナリオでは、可用性 (パーティション ID またはキーなし) と整合性 (イベントを特定のパーティションに固定) のどちらを優先するかを明確に選択することになります。 Event Hubs のパーティションの詳細については、「[パーティション](event-hubs-features.md#partitions)」を参照してください。

## <a name="appendix"></a>付録

### <a name="send-events-without-specifying-a-partition"></a>パーティションを指定せずにイベントを送信する
Event Hubs サービスで負荷を複数のパーティションに分散できるようにするために、パーティション情報を設定せずにイベント ハブにイベントを送信することをお勧めします。 さまざまなプログラミング言語でそれを行う方法を学習するには、次のクイック スタートを参照してください。 

- [.NET を使用したイベントの送信](event-hubs-dotnet-standard-getstarted-send.md)
- [Java を使用したイベントの送信](event-hubs-java-get-started-send.md)
- [JavaScript を使用したイベントの送信](event-hubs-python-get-started-send.md)
- [Python を使用したイベントの送信](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>特定のパーティションにイベントを送信する
このセクションでは、さまざまなプログラミング言語を使用して、特定のパーティションにイベントを送信する方法について説明します。 

### <a name="net"></a>[.NET](#tab/dotnet)
特定のパーティションにイベントを送信するには、[CreateBatchOptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions) に `PartitionId` または `PartitionKey` を指定して、[EventHubProducerClient.CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) メソッドを使用してバッチを作成します。 次のコードでは、パーティション キーを指定することによって、特定のパーティションにイベントのバッチを送信します。 Event Hubs によって、1 つのパーティション キー値を共有するすべてのイベントが一緒に格納され、到着順に配信されます。

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

また、[SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions) に **PartitionId** または **PartitionKey** を指定して、[EventHubProducerClient.SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) メソッドを使用することもできます。

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
特定のパーティションにイベントを送信するには、[createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions) に **パーティション ID** または **パーティション キー** を指定して、[createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) メソッドを使用してバッチを作成します。 次のコードでは、パーティション キーを指定することによって、特定のパーティションにイベントのバッチを送信します。 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

また、[SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions) に **パーティション ID** または **パーティション キー** を指定して、[EventHubProducerClient.send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) メソッドを使用することもできます。

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
特定のパーティションにイベントを送信するには、[`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) メソッドを使用してバッチを作成するときに、`partition_id` または `partition_key` 指定します。 次に、[`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) メソッドを使用して、イベント ハブのパーティションにバッチを送信します。 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

`partition_id` または `partition_key` パラメーターの値を指定することにより、[EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) メソッドを使用することもできます。

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
特定のパーティションにイベントを送信するには、`partitionId` または `partitionKey` を指定して、[EventHubProducerClient.CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) オブジェクトを使用して[バッチを作成します](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_)。 次に、[EventHubProducerClient.SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) メソッドを使用して、イベントハブにバッチを送信します。 

次の例を参照してください。

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

また、[SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions) に **パーティション ID** または **パーティション キー** を指定して、[EventHubProducerClient.sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) メソッドを使用することもできます。

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

- [Event Hubs サービスの概要](./event-hubs-about.md)
- [Event Hubs の用語](event-hubs-features.md)
