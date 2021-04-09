---
title: .NET プログラミング ガイド - Azure Event Hubs （レガシー） | Microsoft Docs
description: この記事では、Azure .NET SDK を使用して Azure Event Hubs 用のコードを記述する方法について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 32c3c05b61d2ee8fc79d7c863ddbe84de5fe7e2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432742"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Azure Event Hubs の .NET プログラミング ガイド (レガシー Microsoft.Azure.EventHubs パッケージ)
この記事では、Azure Event Hubs を使用してコードを作成する一般的なシナリオについて説明します。 Event Hubs の予備知識があることを前提としています。 Event Hub の概要/概念については、「 [Event Hubs 概要](./event-hubs-about.md)」を参照してください。

> [!WARNING]
> このガイドは、以前の **Microsoft.Azure.EventHubs** パッケージに関するものです。 最新の[Azure.Messaging.EventHubs](event-hubs-dotnet-standard-getstarted-send.md)パッケージを使用するには、コードを[移行](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)することをお勧めします。  


## <a name="event-publishers"></a>イベント発行元

イベントは HTTP POST か AMQP 1.0 接続を使用して、イベント ハブに送信します。 何をいつ利用するかは、解決対象の具体的なシナリオによります。 AMQP 1.0 接続は Service Bus の仲介型接続として課金され、頻繁にメッセージ量が多くなり、低遅延の要件があるシナリオに適しています。固定のメッセージング チャンネルが提供されるためです。

.NET のマネージド API を使用する場合、Event Hubs にデータを発行するための主なコンストラクトは [EventHubClient][] クラスと [EventData][] クラスになります。 [EventHubClient][] は、イベントがイベント ハブに送信されるときに使われる AMQP 通信チャンネルを提供します。 [EventData][] クラスはイベントを表し、イベント ハブにメッセージを発行するために使用されます。 このクラスには、本文、いくつかのメタデータ (Properties)、イベントに関するヘッダー情報 (SystemProperties) が含まれます。 その他のプロパティは [EventData][] オブジェクトに追加され、イベント ハブに渡されます。

## <a name="get-started"></a>はじめに
Event Hubs をサポートする .NET クラスが [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet パッケージ内に用意されています。 Visual Studio ソリューション エクスプローラーまたは Visual Studio の [パッケージ マネージャー コンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)を使用してインストールできます。 これを行うには、 [パッケージ マネージャー コンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) のウィンドウに次のコマンドを入力します。

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>イベント ハブの作成

Azure Portal、Azure PowerShell、または Azure CLI を使用して、Event Hubs を作成できます。 詳細については、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](event-hubs-create.md)」をご覧ください。

## <a name="create-an-event-hubs-client"></a>Event Hub クライアントの作成

Event Hubs とやり取りするための主要クラスは [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient] です。 次の例のように、[CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) メソッドを使用してこのクラスをインスタンス化できます。

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>イベント ハブにイベントを送信する

[EventHubClient][] インスタンスを作成し、それを [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) メソッドで送信することで、イベント ハブにイベントを非同期で送信します。 このメソッドは [EventData][] インスタンス パラメーターを 1 つ受け取り、それをイベント ハブに非同期的に送信します。

## <a name="event-serialization"></a>イベントのシリアル化

[EventData][] クラスには[オーバーロードされたコンストラクターが 2 つ](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor)あります。これらのコンストラクターは、イベント データ のペイロードを表すさまざまなパラメーター、バイト配列、またはバイト配列を受け取ります。 JSON と共に [EventData][] を使用するときには、JSON でエンコードされた文字列のバイト配列を取得するのに **Encoding.UTF8.GetBytes()** を使用できます。 次に例を示します。

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>パーティション キー

> [!NOTE]
> パーティションをよく知らない場合は、[この記事](event-hubs-features.md#partitions)を参照してください。 

イベント データを送信するときに、パーティション割り当てを生成するためにハッシュされる値を指定できます。 [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) プロパティを使用して、パーティションを指定します。 ただし、パーティションを使用するという決定は、可用性と整合性のどちらを優先するかを選択することを意味します。 詳細については、[可用性と一貫性](event-hubs-availability-and-consistency.md)に関するページを参照してください。

## <a name="batch-event-send-operations"></a>イベントのバッチ送信処理

イベントをバッチ送信すると、スループット向上の役に立ちます。 [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API を使用して、[SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) 呼び出し用のデータ オブジェクトを後で追加できるバッチを作成できます。

単一のバッチは、イベントの 1 MB 制限を超えてはなりません。 また、バッチの各メッセージでは同じ発行元 ID が使用されます。 バッチが最大イベント サイズを超えないようにすることは送信元の責任となります。 最大イベント サイズを超えると、クライアント **送信** エラーが発生します。 ヘルパー メソッド [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) を使用して、バッチが 1 MB を超えないようにします。 [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API から空の [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) を取得し、[TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) を使用してイベントを追加し、バッチを構築します。 

## <a name="send-asynchronously-and-send-at-scale"></a>非同期送信と大規模送信

イベントは、イベント ハブに非同期に送信されます。 非同期送信を利用すると、クライアントがイベントを送信できる速度が上がります。 [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)は [Task](/dotnet/api/system.threading.tasks.task) オブジェクトを返します。 クライアントで [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) クラスを使用して、クライアント側の再試行オプションを制御できます。

## <a name="event-consumers"></a>イベント コンシューマー
[EventProcessorHost][] クラスは Event Hubs からのデータを処理します。 .NET プラットフォームでのイベント リーダーを作成するときには、この実装を使用すべきです。 [EventProcessorHost][] はイベント プロセッサ実装のためにスレッドセーフでマルチプロセスの安全なランタイム環境を提供します。さらに、その環境では、チェックポイント処理とパーティション リースの管理が提供されます。

[EventProcessorHost][] クラスを使用するために、[IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) を実装できます。 このインターフェイスには 4 つのメソッドが含まれています。

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

イベント処理を開始するには、 [EventProcessorHost][]をインスタンス化し、イベント ハブの適切なパラメーターを提供します。 次に例を示します。

> [!NOTE]
> EventProcessorHost およびその関連クラスは **Microsoft.Azure.EventHubs.Processor** パッケージ内に用意されています。 [この記事](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package)の手順に従うか、[パッケージ マネージャー コンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)のウィンドウで `Install-Package Microsoft.Azure.EventHubs.Processor` コマンドを発行して、パッケージをご自分の Visual Studio プロジェクトに追加します。

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

次に、[RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) を呼び出して、[IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) の実装をランタイムに登録します。

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

この時点で、ホストは「どん欲な」アルゴリズムを利用して、イベント ハブにあるすべてのパーティションでリースの取得を試行します。 これらのリースは一定の期間存続しますが、その後、更新する必要があります。 新しいノード (この場合は worker インスタンス) がオンラインになると、新しいノードはリースを予約し、時間と共にリースの追加取得を試行し、負荷がノード間を移動します。

![イベント プロセッサ ホスト](./media/event-hubs-programming-guide/IC759863.png)

時間と共に、均衡が確立されます。 この動的機能により、スケールアップとスケールダウンの両方で、CPU に基づく自動スケールがコンシューマーに適用されます。 イベント ハブにはメッセージ カウントの直接的概念がないため、平均的な CPU 利用率が、多くの場合、バックエンドまたはコンシューマー スケールを測定する最良のメカニズムとなります。 発行元がコンシューマーが処理できる数を超えたイベントを発行し始めた場合、コンシューマーの CPU 増加を利用し、worker インスタンス カウントを自動拡張できます。

[EventProcessorHost][] クラスは Azure ストレージベースのチェックポイント処理メカニズムも実装します。 このメカニズムはパーティションごとにオフセットを保存します。そのため、各コンシューマーは前回のコンシューマーが保存した内容から、最後のチェックポイントを判断できます。 パーティションがリースによってノード間を移動するにつれて、負荷移動を円滑にする同期メカニズムとなります。

## <a name="publisher-revocation"></a>発行元失効

イベント プロセッサ ホストの高度なランタイム機能に加え、Event Hubs サービスは特定の発行元がイベント ハブにイベントを送信するのを防ぐ目的で[発行元失効](/rest/api/eventhub/revoke-publisher)を有効にします。 このような機能は、発行元のトークンが侵害されたり、ソフトウェア更新によって不適切な動作が発生したりする場合に便利です。 そのような状況では、SAS トークンの一部である発行元 ID を利用してイベントの発行をブロックできます。

> [!NOTE]
> 現時点では、この機能 ([発行元失効](/rest/api/eventhub/revoke-publisher)) は REST API でのみサポートされています。


## <a name="next-steps"></a>次のステップ

Event Hubs シナリオに関する詳細については、次のリンク先を参照してください。

* [Event Hubs API 概要](./event-hubs-samples.md)
* [Event Hubs とは](./event-hubs-about.md)
* [Event Hubs における可用性と一貫性](event-hubs-availability-and-consistency.md)
* [イベント プロセッサ ホスト API リファレンス](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
