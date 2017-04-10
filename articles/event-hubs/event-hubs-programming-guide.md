---
title: "Azure Event Hubs のプログラミング ガイド |Microsoft Docs"
description: "Azure .NET SDK を使用し、Azure Event Hubs でプログラミングする方法について説明します。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2dfc38070e5c9bbdfc4c74e2465894a221657564
ms.openlocfilehash: 1ee20b8f546c43d0351a2065b0628bb9d6b31736
ms.lasthandoff: 02/24/2017


---
# <a name="event-hubs-programming-guide"></a>Event Hubs のプログラミング ガイド
この記事では、Azure Event Hubs と Azure .NET SDK を使用してコードを作成する一般的なシナリオについて説明します。 Event Hubs の予備知識があることを前提としています。 Event Hub の概要/概念については、「 [Event Hubs 概要](event-hubs-what-is-event-hubs.md)」を参照してください。

## <a name="event-publishers"></a>イベント発行元
イベントは、HTTP POST か AMQP 1.0 接続を使用して Event Hub に送信します。 何をいつ利用するかは、解決対象の具体的なシナリオによります。 AMQP 1.0 接続は Service Bus の仲介型接続として課金され、頻繁にメッセージ量が多くなり、低遅延の要件があるシナリオに適しています。固定のメッセージング チャンネルが提供されるためです。

Event Hubs は [NamespaceManager][] クラスで作成し、管理します。 .NET のマネージ API を使用する場合、Event Hubs にデータを発行するための主なコンストラクトは [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) クラスと [EventData][] クラスになります。 [EventHubClient][] は、イベントが Event Hub に送信されるときに使われる AMQP 通信チャンネルを提供します。 [EventData][] クラスはイベントを表し、Event Hub にメッセージを発行するために使用されます。 このクラスには、本文、いくつかのメタデータ、イベントに関するヘッダー情報が含まれます。 その他のプロパティは [EventData][] オブジェクトに追加され、Event Hub に渡されます。

## <a name="get-started"></a>作業開始
Event Hubs をサポートする .NET クラスが Microsoft.ServiceBus.dll アセンブリに用意されています。 Service Bus API を参照し、すべての Service Bus 依存関係を備えたアプリケーションを構成する最も簡単な方法は [Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus)をダウンロードすることです。 あるいは、Visual Studio の [パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) を利用できます。 これを行うには、 [パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) のウィンドウに次のコマンドを入力します。

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Event Hub を作成する
[NamespaceManager][] クラスを使用し、Event Hubs を作成できます。 次に例を示します。

```csharp
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

ほとんどの場合、 [CreateEventHubIfNotExists][] メソッドを使用し、サービスの再起動時に例外発生を回避することが推奨されます。 次に例を示します。

```csharp
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

[CreateEventHubIfNotExists][]など、すべての Event Hub 作成操作で、該当する名前空間の **管理** アクセス許可が必要になります。 発行元またはコンシューマー アプリケーションのアクセス許可を制限したい場合、本番のコードでアクセス許可を制限して資格情報を利用するときにこれらの作成操作呼び出しを避けることができます。

[EventHubDescription](/dotnet/api/microsoft.servicebus.messaging.eventhubdescription) クラスには、承認規則、メッセージの保有期間、パーティション ID、状態、パスなど、Event Hub の詳細が含まれています。 このクラスを使用し、Event Hub のメタデータを更新できます。

## <a name="create-an-event-hubs-client"></a>Event Hub クライアントの作成
Event Hubs とやり取りするための主要クラスは [Microsoft.ServiceBus.Messaging.EventHubClient][] です。 このクラスは送信機能と受信機能の両方を提供します。 次の例のように、 [Create](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.create) メソッドを利用してこのクラスをインスタンス化できます。

```csharp
var client = EventHubClient.Create(description.Path);
```

このメソッドは App.config ファイルの `appSettings` セクションにある、 Service Bus の接続情報を使用します。 Service Bus 接続情報を格納するために使用される `appSettings` XML の例については、 [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) メソッドの文書を参照してください。

接続文字列からクライアントを作成することもできます。 この選択肢は Azure worker ロールを使用する場合に効果があります。worker の設定プロパティに文字列を格納できるためです。 次に例を示します。

```csharp
EventHubClient.CreateFromConnectionString("your_connection_string");
```

接続文字列は前のメソッドの App.config ファイルに表示される形式と同じ形式になります。

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[key]
```

最後になりますが、[EventHubClient][] オブジェクトは [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) インスタンスから作成することもできます。その例を以下に示します。

```csharp
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

これは重要なことですが、1 つのメッセージング ファクトリ インスタンスから作成された追加の [EventHubClient][] オブジェクトたちが、同一の内部 TCP 接続を再利用することに注意してください。 そのため、これらのオブジェクトでは、スループットにクライアント側の制限があります。 [Create](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) メソッドでは&1; つのメッセージング ファクトリが再利用されます。 単一の送信元からの非常に高いスループットを必要とする場合には、複数メッセージング ファクトリを作成して、それぞれのメッセージ ファクトリから&1; つの [EventHubClient][] オブジェクトを作成するようにできます。

## <a name="send-events-to-an-event-hub"></a>Event Hub にイベントを送信する
[EventData][] インスタンスを作成し、それを [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) メソッドで送信することで Event Hub にイベントを送信します。 このメソッドは [EventData][] インスタンス パラメーターを&1; つ受け取り、それを Event Hub に同期的に送信します。

## <a name="event-serialization"></a>イベントのシリアル化
[EventData][] クラスには[オーバーロードされたコンストラクターが&4; つ](/dotnet/api/microsoft.servicebus.messaging.eventdata#constructors_)あります。これらのコンストラクターは、オブジェクトとシリアライザー、またはバイト配列やストリームといった、さまざまなパラメーターを受け取ります。 [EventData][] クラスをインスタンス化し、その後、本文のストリームを設定することもできます。 JSON と共に [EventData][] を使用するときには、JSON でエンコードされた文字列のバイト配列を取得するのに **Encoding.UTF8.GetBytes()** を使用できます。

## <a name="partition-key"></a>パーティション キー
[EventData][] クラスには [PartitionKey][] プロパティがあります。このプロパティを利用して、送信元は、パーティションを割り当てるためにハッシュ化される値を指定できます。 パーティション キーを利用することで、同じキーを持つすべてのイベントが Event Hub の同じパーティションに送信されることが保証されます。 一般的なパーティション キーには、ユーザー セッション ID や一意な送信元 ID が含まれます。 [PartitionKey][] プロパティは任意であり、[Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) メソッドまたは [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendAsync_Microsoft_ServiceBus_Messaging_EventData_) メソッドの利用時に指定できます。 [PartitionKey][] の値を指定しない場合、送信されたイベントはラウンドロビン モデルを利用して各パーティションに配信されます。

### <a name="availability-considerations"></a>可用性に関する考慮事項

パーティション キーを使用するかどうかは任意であり、慎重に検討する必要があります。 多くの場合、イベントの順序設定が必要であればパーティション キーの使用をお勧めします。 パーティション キーを使用すると、これらのパーティションでは単一のノードに対する可用性が必要になるため、時間が経つにつれて、コンピューティング ノードの再起動時やパッチ適用時などに障害が発生する可能性があります。 そのため、パーティション ID を設定した場合にそのパーティションがなんらかの理由で使用不能になると、そのパーティション内のデータにアクセスできなくなります。 高可用性がもっとも重要な場合は、パーティション キーを指定しないでください。指定を行うと、以前に説明したラウンドロビン モデルを使用してイベントがパーティションに送信されるようになります。 このシナリオでは、可用性 (パーティション ID なし) と整合性 (イベントをパーティション ID に固定) のどちらを優先するかを明確に選択することになります。

別の検討事項として、イベントの処理の遅れへの対処があります。 場合によっては、処理が遅れないようにするよりも、データを破棄して再試行した方が良いこともあります。前者では、ダウンストリームの処理がさらに遅れる可能性があります。 たとえば、株式相場表示機では最新のデータが揃うまで待つ方が適切ですが、ライブ チャットや VOIP のシナリオでは不完全でもデータを素早く用意する必要があります。

こうした可用性に関する考慮事項を踏まえて、これらのシナリオでは次のエラー処理方法のいずれかを選択してください。

- 停止 (問題が解決するまで Event Hubs からの読み取りを停止する)
- 破棄 (重要ではないメッセージを破棄する)
- 再試行 (表示されるメッセージが適切になるまで再試行する)
- [配信不可](../service-bus-messaging/service-bus-dead-letter-queues.md) (キューまたは別の Event Hub を使用して、処理できないメッセージのみを配信不能にする)

## <a name="batch-event-send-operations"></a>イベントのバッチ送信処理
イベントをバッチ送信すると、スループットが劇的に上がります。 [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) メソッドは [EventData][] 型の **IEnumerable** パラメーターを受け取り、バッチ全体をアトミック操作として Event Hub に送信します。

```csharp
public void SendBatch(IEnumerable<EventData> eventDataList);
```

単一のバッチがイベントの 256 KB 制限を超えてはならないことに注意してください。 また、バッチの各メッセージでは同じ発行元 ID が使用されます。 バッチが最大イベント サイズを超えないようにすることは送信元の責任となります。 超えた場合、クライアント **送信** エラーが生成されます。

## <a name="send-asynchronously-and-send-at-scale"></a>非同期送信と大規模送信
Event Hub にイベントを非同期送信することもできます。 非同期送信を利用すると、クライアントがイベントを送信する速度が上がります。 [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) メソッドと [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) メソッドの両方で [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) オブジェクトを返す非同期バージョンを利用できます。 この手法ではスループットが上がりますが、Event Hubs でスロットルされていてもクライアントがイベントの送信を続けるので、適切に実装されていない場合、クライアントに障害が発生したり、メッセージが失われたりすることがあります。 また、クライアントの [RetryPolicy](/dotnet/api/microsoft.servicebus.messaging.cliententity#Microsoft_ServiceBus_Messaging_ClientEntity_RetryPolicy) プロパティを使用し、クライアント側の再試行オプションを制御できます。

## <a name="create-a-partition-sender"></a>パーティション送信元の作成
パーティション キーを持たない Event Hub にイベントを送信するのが最も一般的ですが、特定のパーティションにイベントを直接送信することもあります。 For example:

```csharp
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_CreatePartitionedSender_System_String_) は [EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) オブジェクトを返します。このオブジェクトを使用し、特定の Event Hub パーティションにイベントを発行できます。

## <a name="event-consumers"></a>イベント コンシューマー
Event Hubs にはイベント使用のために&2; つの主要モデルが用意されています。ダイレクト レシーバーと [EventProcessorHost][] のような上位の抽象構造です。 ダイレクト レシーバーは、コンシューマー グループ内のパーティションについて、自前でアクセス調整を行う責任があります。

### <a name="direct-consumer"></a>ダイレクト コンシューマー
コンシューマー グループ内の&1; つのパーティションから読み取るための最も直接的な方法は [EventHubReceiver](/dotnet/apie/microsoft.servicebus.messaging.eventhubreceiver) クラスを使用することです。 このクラスのインスタンスを作成するには、 [EventHubConsumerGroup](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup) クラスのインスタンスを使用する必要があります。 次の例では、コンシューマー グループの受信者を作成するときにパーティション ID を指定する必要があります。

```csharp
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

[CreateReceiver](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup#methods_summary) メソッドには、作成されるリーダーの制御を調整するいくつかのオーバーロードがあります。 これらのメソッドでは文字列とタイムスタンプのいずれかをオフセットとして指定します。返されたストリームにこの指定したオフセットを含めるか、その後ろから開始するかを指定できます。 レシーバーを作成したら、そのレシーバーでイベントの受信を開始できます。 [Receive](/dotnet/api/microsoft.servicebus.messaging.eventhubreceiver#methods_summary) メソッドには、バッチ サイズや待機時間など、受信処理のパラメーターを制御するオーバーロードが&4; つあります。 これらのメソッドの非同期バージョンを利用し、コンシューマーのスループットを上げることができます。 次に例を示します。

```csharp
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

1 つの特定のパーティションについては、メッセージは Event Hub に送信された順序で受信されます。 オフセットはパーティション内でのメッセージ識別に使用される文字列トークンです。

いかなるタイミングでも、コンシューマー グループ内の 1 つのパーティションに 5 つ以上のリーダーを同時接続することはできないことに注意してください。 リーダーが接続または切断された場合、サービスが切断を認識するまで、セッションが数分間アクティブな状態のままになることがあります。 その間にパーティションに再接続すると、失敗することがあります。 Event Hubs のダイレクト レシーバーの完全な記述例については、[Event Hubs Direct Receivers](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) のサンプルを参照してください。

### <a name="event-processor-host"></a>イベント プロセッサ ホスト
[EventProcessorHost][] クラスは Event Hubs からのデータを処理します。 .NET プラットフォームでのイベント リーダーを作成するときには、この実装を使用すべきです。 [EventProcessorHost][] はイベント プロセッサ実装のためにスレッドセーフでマルチプロセスの安全なランタイム環境を提供します。さらに、その環境では、チェックポイント処理とパーティション リースの管理が提供されます。

[EventProcessorHost][] クラスを使用するために、[IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) を実装できます。 このインターフェイスには&3; つのメソッドが含まれています。

* [OpenAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_OpenAsync_Microsoft_ServiceBus_Messaging_PartitionContext_)
* [CloseAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_CloseAsync_Microsoft_ServiceBus_Messaging_PartitionContext_Microsoft_ServiceBus_Messaging_CloseReason_)
* [ProcessEventsAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_ProcessEventsAsync_Microsoft_ServiceBus_Messaging_PartitionContext_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)

イベント処理を開始するには、 [EventProcessorHost][]をインスタンス化し、Event Hub の適切なパラメーターを提供します。 次に、[RegisterEventProcessorAsync](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost#Microsoft_ServiceBus_Messaging_EventProcessorHost_RegisterEventProcessorAsync__1) を呼び出し、[IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) 実装をランタイムに登録します。 この時点で、ホストは「どん欲な」アルゴリズムを利用して Event Hub にあるすべてのパーティションでリースの取得を試行します。 これらのリースは一定の期間存続し、その後、更新する必要があります。 新しいノード (この場合は worker インスタンス) がオンラインになると、新しいノードはリースを予約し、時間と共にリースの追加取得を試行し、負荷がノード間を移動します。

![イベント プロセッサ ホスト](./media/event-hubs-programming-guide/IC759863.png)

時間と共に、均衡が確立されます。 この動的機能により、スケールアップとスケールダウンの両方で、CPU に基づく自動スケールがコンシューマーに適用されます。 Event Hubs にはメッセージ カウントの直接的概念がないため、平均的な CPU 利用率が、多くの場合、バックエンドまたはコンシューマー スケールを測定する最良のメカニズムとなります。 発行元がコンシューマーが処理できる数を超えたイベントを発行し始めた場合、コンシューマーの CPU 増加を利用し、worker インスタンス カウントを自動拡張できます。

[EventProcessorHost][] クラスは Azure ストレージベースのチェックポイント処理メカニズムも実装します。 このメカニズムはパーティションごとにオフセットを保存します。そのため、各コンシューマーは前回のコンシューマーが保存した内容から、最後のチェックポイントを判断できます。 パーティションがリースによってノード間を移動するにつれて、負荷移動を円滑にする同期メカニズムとなります。

## <a name="publisher-revocation"></a>発行元失効
[EventProcessorHost][]の高度なランタイム機能に加え、Event Hubs は特定の発行元が Event Hub にイベントを発行するのを防ぐ目的で発行元失効を有効にします。 このような機能は特に、発行元のトークンが侵害された、あるいはソフトウェア更新により不適切な動作が発生している場合に便利です。 そのような状況では、SAS トークンの一部である発行元 ID を利用してイベントの発行をブロックできます。

発行元失効の詳細のほか、発行元として Event Hubs に送信する方法の詳細については、[Event Hubs の大規模で安全な発行](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)に関するサンプルを参照してください。

## <a name="next-steps"></a>次のステップ
Event Hubs シナリオに関する詳細については、次のリンク先を参照してください。

* [Event Hubs API 概要](event-hubs-api-overview.md)
* [Event Hubs とは](event-hubs-what-is-event-hubs.md)
* [イベント プロセッサ ホスト API リファレンス](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.servicebus.messaging.eventhubclient
[EventData]: /dotnet/api/microsoft.servicebus.messaging.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost

