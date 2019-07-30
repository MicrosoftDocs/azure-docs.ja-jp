---
title: プログラミング ガイド - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure .NET SDK を使用して Azure Event Hubs 用のコードを記述する方法について説明します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 2af076153725dc91caaf07b710acf21ebc143fb0
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273660"
---
# <a name="programming-guide-for-azure-event-hubs"></a>Azure Event Hubs のプログラミング ガイド
この記事では、Azure Event Hubs を使用してコードを作成する一般的なシナリオについて説明します。 Event Hubs の予備知識があることを前提としています。 Event Hub の概要/概念については、「 [Event Hubs 概要](event-hubs-what-is-event-hubs.md)」を参照してください。

## <a name="event-publishers"></a>イベント発行元

イベントは HTTP POST か AMQP 1.0 接続を使用して、イベント ハブに送信します。 何をいつ利用するかは、解決対象の具体的なシナリオによります。 AMQP 1.0 接続は Service Bus の仲介型接続として課金され、頻繁にメッセージ量が多くなり、低遅延の要件があるシナリオに適しています。固定のメッセージング チャンネルが提供されるためです。

.NET のマネージド API を使用する場合、Event Hubs にデータを発行するための主なコンストラクトは [EventHubClient][] クラスと [EventData][] クラスになります。 [EventHubClient][] は、イベントがイベント ハブに送信されるときに使われる AMQP 通信チャンネルを提供します。 [EventData][] クラスはイベントを表し、イベント ハブにメッセージを発行するために使用されます。 このクラスには、本文、いくつかのメタデータ、イベントに関するヘッダー情報が含まれます。 その他のプロパティは [EventData][] オブジェクトに追加され、イベント ハブに渡されます。

## <a name="get-started"></a>作業開始
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

[EventData][] クラスには[オーバーロードされたコンストラクターが 2 つ](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor)あります。これらのコンストラクターは、イベント データ のペイロードを表すさまざまなパラメーター、バイト配列、またはバイト配列を受け取ります。 JSON と共に [EventData][] を使用するときには、JSON でエンコードされた文字列のバイト配列を取得するのに **Encoding.UTF8.GetBytes()** を使用できます。 例:

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

イベント データを送信するときに、パーティション割り当てを生成するためにハッシュされる値を指定できます。 [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) プロパティを使用して、パーティションを指定します。 ただし、パーティションを使用するという決定は、可用性と整合性のどちらを優先するかを選択することを意味します。 

### <a name="availability-considerations"></a>可用性に関する考慮事項

パーティション キーを使用するかどうかは任意であり、慎重に検討する必要があります。 イベントを発行するときにパーティション キーを指定しないと、ラウンド ロビン割り当てが使用されます。 多くの場合、イベントの順序設定が必要であればパーティション キーの使用をお勧めします。 パーティション キーを使用すると、これらのパーティションでは単一のノードに対する可用性が必要になるため、時間が経つにつれて、コンピューティング ノードの再起動時やパッチ適用時などに障害が発生する可能性があります。 そのため、パーティション ID を設定した場合にそのパーティションがなんらかの理由で使用不能になると、そのパーティション内のデータにアクセスできなくなります。 高可用性がもっとも重要な場合は、パーティション キーを指定しないでください。指定を行うと、以前に説明したラウンドロビン モデルを使用してイベントがパーティションに送信されるようになります。 このシナリオでは、可用性 (パーティション ID なし) と整合性 (イベントをパーティション ID に固定) のどちらを優先するかを明確に選択することになります。

別の検討事項として、イベントの処理の遅れへの対処があります。 場合によっては、処理が遅れないようにするよりも、データを破棄して再試行した方が良いこともあります。前者では、ダウンストリームの処理がさらに遅れる可能性があります。 たとえば、株式相場表示機では最新のデータが揃うまで待つ方が適切ですが、ライブ チャットや VOIP のシナリオでは不完全でもデータを素早く用意する必要があります。

こうした可用性に関する考慮事項を踏まえて、これらのシナリオでは次のエラー処理方法のいずれかを選択してください。

- 停止 (問題が解決するまで Event Hubs からの読み取りを停止する)
- 破棄 (重要ではないメッセージを破棄する)
- 再試行 (表示されるメッセージが適切になるまで再試行する)

可用性と一貫性の間のトレードオフに関する情報と詳細については、「[Event Hubs における可用性と一貫性](event-hubs-availability-and-consistency.md)」を参照してください。 

## <a name="batch-event-send-operations"></a>イベントのバッチ送信処理

イベントをバッチ送信すると、スループット向上の役に立ちます。 [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API を使用して、[SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) 呼び出し用のデータ オブジェクトを後で追加できるバッチを作成できます。

単一のバッチは、イベントの 1 MB 制限を超えてはなりません。 また、バッチの各メッセージでは同じ発行元 ID が使用されます。 バッチが最大イベント サイズを超えないようにすることは送信元の責任となります。 超えた場合、クライアント **送信** エラーが生成されます。 ヘルパー メソッド [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) を使用して、バッチが 1 MB を超えないようにします。 [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API から空の [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) を取得し、[TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) を使用してイベントを追加し、バッチを構築します。 

## <a name="send-asynchronously-and-send-at-scale"></a>非同期送信と大規模送信

イベントは、イベント ハブに非同期に送信されます。 非同期送信を利用すると、クライアントがイベントを送信できる速度が上がります。 [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)は [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) オブジェクトを返します。 クライアントで [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) クラスを使用して、クライアント側の再試行オプションを制御できます。

## <a name="event-consumers"></a>イベント コンシューマー
[EventProcessorHost][] クラスは Event Hubs からのデータを処理します。 .NET プラットフォームでのイベント リーダーを作成するときには、この実装を使用すべきです。 [EventProcessorHost][] はイベント プロセッサ実装のためにスレッドセーフでマルチプロセスの安全なランタイム環境を提供します。さらに、その環境では、チェックポイント処理とパーティション リースの管理が提供されます。

[EventProcessorHost][] クラスを使用するために、[IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) を実装できます。 このインターフェイスには 4 つのメソッドが含まれています。

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

イベント処理を開始するには、 [EventProcessorHost][]をインスタンス化し、イベント ハブの適切なパラメーターを提供します。 例:

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

[EventProcessorHost][] の高度なランタイム機能に加え、Event Hubs は特定の発行元がイベント ハブにイベントを発行するのを防ぐ目的で発行元失効を有効にします。 このような機能は、発行元のトークンが侵害されたり、ソフトウェア更新によって不適切な動作が発生したりする場合に便利です。 そのような状況では、SAS トークンの一部である発行元 ID を利用してイベントの発行をブロックできます。

発行元失効の詳細のほか、発行元として Event Hubs に送信する方法の詳細については、[Event Hubs の大規模で安全な発行](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)に関するサンプルを参照してください。

## <a name="next-steps"></a>次の手順

Event Hubs シナリオに関する詳細については、次のリンク先を参照してください。

* [Event Hubs API 概要](event-hubs-api-overview.md)
* [Event Hubs とは](event-hubs-what-is-event-hubs.md)
* [Event Hubs における可用性と一貫性](event-hubs-availability-and-consistency.md)
* [イベント プロセッサ ホスト API リファレンス](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
