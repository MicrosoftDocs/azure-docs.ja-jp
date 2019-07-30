---
title: イベント プロセッサ ホストを使用してイベントを受信する - Azure Event Hubs | Microsoft Docs
description: この記事では、チェックポイント処理、リース、および並列でのイベントの読み込みの管理を簡素化する、Azure Event Hubs のイベント プロセッサ ホストについて説明します。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 07/16/2019
ms.author: shvija
ms.openlocfilehash: 013200295f3a6a48d6d96663f98bce506808cd70
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277370"
---
# <a name="event-processor-host"></a>イベント プロセッサ ホスト

Azure Event Hubs は、数百万件のイベントを低コストでストリーム配信するために使用できる、強力なテレメトリ インジェスト サービスです。 この記事では、チェックポイント処理、リース処理、および並列イベント リーダーの管理を簡素化するインテリジェントなコンシューマー エージェントである "*イベント プロセッサ ホスト*" (EPH) を使用して、取り込まれたイベントを使用する方法について説明します。  

Event Hubs をスケーリングするための鍵となるのは、パーティション分割されたコンシューマーのアイデアです。 [競合コンシューマー](https://msdn.microsoft.com/library/dn568101.aspx) パターンとは対照的に、パーティション分割されたコンシューマー パターンは、競合のボトルネックを除去し、エンド ツー エンドの並列処理を容易にすることによって、高スケールを可能にします。

## <a name="home-security-scenario"></a>ホーム セキュリティのシナリオ

シナリオの例として、10 万件の家を監視するホーム セキュリティ企業を考えてみましょう。 この会社では、各家庭に設置された動体検知器、ドア/窓オープン センサー、ガラス破損検知器などのさまざまなセンサーから常にデータを取得しています。 この会社では、住民がほぼリアルタイムで自宅の様子を監視できる Web サイトを開設しています。

各センサーにより、データがイベント ハブにプッシュされます。 イベント ハブは、16 個のパーティションで構成されます。 使用側では、これらのイベントを読み取り、統合し (フィルター、集計など)、集計をストレージ BLOB にダンプし、ユーザー フレンドリな Web ページに投影できるメカニズムが必要です。

## <a name="write-the-consumer-application"></a>コンシューマー アプリケーションの作成

分散環境でコンシューマーを設計する場合、シナリオで次の要件を処理する必要があります。

1. **スケール:** 複数のコンシューマーを作成します。それぞれのコンシューマーは、いくつかの Event Hubs のパーティションからの読み取りの所有権を保持します。
2. **負荷分散:** コンシューマーを動的に増減します。 たとえば、新しいセンサーの種類 (たとえば、一酸化炭素検知器) が各家庭に追加されると、イベントの数が増加します。 その場合は、オペレーター (人間) がコンシューマー インスタンスの数を増やします。 すると、コンシューマーのプールにより、それ自体が所有するパーティションの数を再調整して、新しく追加されたコンシューマーと負荷を共有することができます。
3. **失敗時のシームレスな再開:** ホストとなっている仮想マシンが突然クラッシュしたなどの理由でコンシューマー (**コンシューマー A**) が失敗した場合、**コンシューマー A** が所有しているパーティションを他のコンシューマーが選択して続行できる必要があります。 また、"*チェックポイント*" または "*オフセット*" と呼ばれる継続ポイントは、**コンシューマー A** が失敗した正確なポイントであるか、その少し前のポイントである必要があります。
4. **イベントの使用:** 前の 3 つのポイントはコンシューマーの管理を扱っています。これに加えて、たとえば、イベントを集計して BLOB ストレージにアップロードするなど、イベントを使用して実用的な操作を行うコードが必要になります。

そのための独自のソリューションを構築する代わりに、Event Hubs では、[IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) インターフェイスと [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) クラスを介してこの機能が提供されます。

## <a name="ieventprocessor-interface"></a>IEventProcessor インターフェイス

最初に、使用するアプリケーションでは、[IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) インターフェイスを実装します。これには、[OpenAsync、CloseAsync、ProcessErrorAsync、および ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods) という 4 つのメソッドが含まれます。 このインターフェイスには、Event Hubs が送信するイベントを使用する実際のコードが含まれています。 簡単な実装を次のコードに示します。

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

次に、[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) インスタンスをインスタンス化します。 オーバーロードに応じて、コンストラクターで [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) インスタンスを作成するときに次のパラメーターが使用されます。

- **hostName:** 各コンシューマー インスタンスの名前。 **EventProcessorHost** の各インスタンスは、コンシューマー グループ内でこの変数の一意の値を持つ必要があります。したがって、この値をハードコーディングしないでください。
- **eventHubPath:** イベント ハブの名前。
- **consumerGroupName:** Event Hubs は、既定のコンシューマー グループの名前として " **$既定**" を使用しますが、処理の特定の側面についてコンシューマー グループを作成することをお勧めします。
- **eventHubConnectionString:** イベント ハブへの接続文字列。この値は、Azure portal から取得できます。 この接続文字列には、イベント ハブに対する**リッスン** アクセス許可が付与されている必要があります。
- **storageConnectionString:** 内部リソースの管理に使用されるストレージ アカウント。

最後に、コンシューマーは [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) インスタンスを Event Hubs サービスに登録します。 EventProcessorHost のインスタンスでイベント プロセッサ クラスを登録すると、イベント処理が開始されます。 この登録によって、Event Hubs サービスは、コンシューマー アプリがいくつかのパーティションからイベントを使用することを期待し、使用するイベントをプッシュするたびに [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 実装コードを呼び出すようになります。 


### <a name="example"></a>例

例として、イベントを使用する専用の 5 つの仮想マシン (VM) があり、各 VM に実際の使用操作を行う単純なコンソール アプリケーションがあるとします。 各コンソール アプリケーションは、1 つの [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) インスタンスを作成し、それを Event Hubs サービスに登録します。

この例のシナリオでは、5 個の **EventProcessorHost** インスタンスに 16 個のパーティションが割り当てられているとします。 いくつかの **EventProcessorHost** インスタンスは、他のインスタンスよりも多くパーティションを所有している可能性があります。 **EventProcessorHost** インスタンスが所有する各パーティションに対して、`SimpleEventProcessor` クラスのインスタンスが作成されます。 したがって、`SimpleEventProcessor` のインスタンスが全体で 16 個存在し、各パーティションに 1 つずつ割り当てられます。

この例を次のリストに示します。

- 16 個の Event Hubs パーティション。
- 5 つの VM。各 VM に 1 つのコンシューマー アプリ (Consumer.exe など)。
- Consumer.exe によって各 VM に 1 つずつ 5 つの EPH インスタンスが登録されている。
- 16 個の `SimpleEventProcessor` オブジェクトが 5 つの EPH インスタンスによって作成されている。
- 1 つの EPH インスタンスが 4 つのパーティションを所有している可能性があるため、1 つの Consumer.exe アプリに 4 つの `SimpleEventProcessor` オブジェクトが含まれている可能性がある。

## <a name="partition-ownership-tracking"></a>パーティションの所有権の追跡

EPH インスタンス (またはコンシューマー) のパーティションの所有権は、追跡のために提供される Azure Storage アカウントを使って追跡されます。 追跡結果は、次のように簡単な表として視覚化することができます。 実際の実装は、提供されている Storage アカウントの BLOB を調べることで確認できます。

| **コンシューマー グループ名** | **パーティション ID** | **ホスト名 (所有者)** | **リース (または所有権) の取得時刻** | **パーティション (チェックポイント) 内のオフセット** |
| --- | --- | --- | --- | --- |
| $既定 | 0 | Consumer\_VM3 | 2018-04-15T01:23:45 | 156 |
| $既定 | 1 | Consumer\_VM4 | 2018-04-15T01:22:13 | 734 |
| $既定 | 2 | Consumer\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $既定 | 15 | Consumer\_VM3 | 2018-04-15T01:22:56 | 976 |

ここで、各ホストは、一定期間 (リース期間) のパーティションの所有権を取得します。 (VM がシャットダウンしたなど) ホストに障害が発生した場合、リースは期限切れになります。 他のホストがパーティションの所有権の取得を試み、そのうちの 1 つのホストが成功します。 このプロセスにより、新しい所有者でパーティションのリースがリセットされます。 このように、一度に 1 つのリーダーのみが、コンシューマー グループ内の特定のパーティションから読み取ることができます。

## <a name="receive-messages"></a>メッセージを受信する

[ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) の呼び出しごとに、イベントのコレクションが配信されます。 これらのイベントを処理するのは開発者の責任です。 プロセッサ ホストによってすべてのメッセージが 1 回以上処理されることを確認する場合は、独自の再試行保持コードを作成する必要があります。 ただし、有害メッセージについて注意してください。

物事は迅速に済ませることをお勧めします。つまり、できる限り最小限の処理に留めます。 代わりに、コンシューマー グループを使用します。 ストレージへの書き込みとルーティングを行う必要がある場合、2 つのコンシューマー グループを使用し、2 つの [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 実装を別個に実行する方が良いやり方です。

処理中のある時点で、読み取ったものと完了したものを追跡することをお勧めします。 読み取りを再開する必要がある場合は、ストリームの先頭に戻らずに済むように追跡することが重要です。 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) は、"*チェックポイント*" を使用してこの追跡を簡素化します。 チェックポイントは、問題なくメッセージが処理されている、特定のコンシューマー グループ内の特定のパーティションの場所またはオフセットです。 **EventProcessorHost** でチェックポイントをマークするには、[PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) オブジェクトの [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) メソッドを呼び出します。 この操作は [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) メソッド内で実行しますが、[CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) 内でも実行できます。

## <a name="checkpointing"></a>チェックポイント機能

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) メソッドには 2 つのオーバーロードがあります。最初のパラメーターのないオーバーロードは、[ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) によって返されたコレクション内の最も大きなイベント オフセットに対してチェックポイント処理します。 このオフセットは "上限" マークです。呼び出すと、最近のすべてのイベントが処理されていると見なされます。 この方法でこのメソッドを使用する場合は、他のイベント処理コードから処理が返された後にこのメソッドを呼び出す必要があることに注意してください。 2 番目のオーバーロードでは、チェックポイント処理する [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) インスタンスを指定できます。 この方法では、異なる種類のウォーターマークを使用してチェックポイント処理できます。 このウォーターマークを使用すると、処理が完了していることが確実な最も低いシーケンスのイベントを表す "下限" マークを実装することができます。 このオーバーロードは、オフセット管理の柔軟性を可能にするために提供されています。

チェックポイントを実行すると、パーティションに固有の情報 (具体的には、オフセット) を含む JSON ファイルが、コンストラクターで [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) に提供されたストレージ アカウントに書き込まれます。 このファイルは常に更新されています。 コンテキストでのチェックポイント処理を検討することが重要です。すべてのメッセージをチェックポイント処理するのは賢明ではありません。 チェックポイント処理に使用されるストレージ アカウントはおそらくこの負荷を処理しません。しかし、より重要なこととして、すべての単一イベントをチェックポイント処理することは、キューに格納されたメッセージング パターンを暗示しています。その場合は、イベント ハブよりも Service Bus キューの方がより適切なオプションになる可能性があります。 Event Hubs の背後にあるのは、"1 回以上" 大規模な配信を受ける、という考え方です。 ダウンストリームのシステムにべき等性を持たせることで、同じイベントが複数回受信される結果になるエラーまたは再起動から容易に復旧できます。

## <a name="thread-safety-and-processor-instances"></a>スレッドの安全性とプロセッサのインスタンス

既定では、[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) はスレッド セーフであり、[IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) のインスタンスに対して同期的に動作します。 パーティションのイベントが到着すると、そのパーティションの **IEventProcessor** インスタンスで [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) が呼び出され、そのパーティションのそれ以降の **ProcessEventsAsync** の呼び出しがブロックされます。 後続のメッセージと **ProcessEventsAsync** の呼び出しは、メッセージ ポンプが他のスレッドのバックグラウンドで引き続き実行されるため、バックグラウンドで待機します。 このスレッド セーフにより、スレッド セーフなコレクションが不要になり、パフォーマンスが大幅に向上します。

## <a name="shut-down-gracefully"></a>正常にシャットダウンする

最後に、[EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) を使用して、すべてのパーティション リーダーをクリーンにシャットダウンします。これは、[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) のインスタンスをシャットダウンするときに必ず呼び出す必要があります。 そうしないと、リースの期限切れとエポックの競合が原因で、**EventProcessorHost** の他のインスタンスを開始するときに遅延が発生する可能性があります。 エポック管理の詳細は、この記事の[エポック](#epoch)に関するセクションにあります。 

## <a name="lease-management"></a>リース管理
EventProcessorHost のインスタンスでイベント プロセッサ クラスを登録すると、イベント処理が開始されます。 ホスト インスタンスは、可能性があるすべてのホスト インスタンス間で均等に分散パーティションに集約する方法で、他のホスト インスタンスから一部を取得、イベント ハブの一部のパーティションでリースを取得します。 リースされたパーティションごとに、ホスト インスタンスは、指定されたイベント プロセッサ クラスのインスタンスを作成し、そのパーティションからイベントを受信し、イベント プロセッサーのインスタンスに渡します。 多くのインスタンスが追加されより多くのリースが取り込まれると、EventProcessorHost は最終的にすべてのコンシューマー間で負荷を分散します。

前に説明したように、追跡テーブルは、[EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) の自動スケールの本質を大幅に簡素化します。 **EventProcessorHost** のインスタンスは、開始されると、可能な限り多くのリースを取得し、イベントの読み取りを開始します。 リースの期限が近づくと、**EventProcessorHost** は、予約を通じてリースの更新を試みます。 リースが更新可能な場合、プロセッサは読み取りを継続します。そうでない場合、リーダーは閉じられ、[CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) が呼び出されます。 **CloseAsync** は、このようなパーティションの最終的なクリーンアップを実行するのに適しています。

**EventProcessorHost** には [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) プロパティが含まれています。 このプロパティにより、リース管理を制御できます。 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) の実装を登録する前に、これらのオプションを設定してください。

## <a name="control-event-processor-host-options"></a>イベント プロセッサ ホストのオプションを制御する

さらに、[RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) の 1 つのオーバーロードは、[EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) オブジェクトをパラメーターとして受け取ります。 このパラメーターを使用して、[EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) 自体の動作を制御します。 [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) は、4 つのプロパティと 1 つのイベントを定義します。

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) の呼び出しで受け取るコレクションの最大サイズ。 このサイズは最小サイズではありません。最大サイズのみです。 受信するメッセージが少ない場合、**ProcessEventsAsync** は、使用可能な数だけ実行されます。
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): クライアントが受信する必要があるメッセージの上限を決定するための、基になる AMQP チャネルによって使用される値。 この値は、[MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize) 以上である必要があります。
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): このパラメーターが **true** の場合、[ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) は、パーティション上のイベントを受け取るための基になる呼び出しがタイムアウトすると呼び出されます。このメソッドは、パーティションの非アクティブ期間中に時間ベースのアクションを実行する場合に便利です。
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): リーダーがパーティションの読み取りを開始したときに初期オフセットを提供するために呼び出される関数ポインターまたはラムダ式を設定できるようにします。 このオフセットを指定しない場合、リーダーは、オフセットを含む JSON ファイルが [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) コンストラクターに提供されたストレージ アカウントに保存されていない限り、最も古いイベントから開始します。 このメソッドは、リーダーの起動時の動作を変更する場合に便利です。 このメソッドが呼び出されたとき、オブジェクト パラメーターには、リーダーが開始されているパーティション ID が含まれます。
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) で発生する基になるすべての例外の通知を受け取ることができます。 期待どおりの動作が得られない場合は、このイベントの確認から始めると良いでしょう。

## <a name="epoch"></a>エポック

受信エポックのしくみは次のようになっています。

### <a name="with-epoch"></a>エポックあり
エポックはパーティション/リースの所有権を適用する目的でサービスによって使用される一意の識別子 (エポック値) です。 [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) メソッドを利用し、エポックベースのレシーバーを作成します。 このメソッドにより、エポックベースのレシーバーが作成されます。 指定のコンシューマー グループからの特定のイベント ハブ パーティションに対してレシーバーが作成されます。

このエポック機能によって、ある時点においてコンシューマー グループにレシーバーが 1 つだけになることが保証されます。この機能では、次のルールが使用されます。

- コンシューマー グループにレシーバーが存在しない場合、ユーザーは任意のエポック値でレシーバーを作成できます。
- エポック値 e1 のレシーバーが存在し、エポック値 e2 で新しいレシーバーが作成されるとき、e1 <= e2 であれば、e1 のレシーバーは自動的に接続解除され、e2 のレシーバーが正常に作成されます。
- エポック値 e1 のレシーバーが存在し、エポック値 e2 で新しいレシーバーが作成されるとき、e1 > e2 であれば、e2 のレシーバーは作成できず、エポック e1 のレシーバーが既に存在するという旨のエラーが表示されます。

### <a name="no-epoch"></a>エポックなし
[CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) メソッドを利用し、エポックベースではないレシーバーを作成します。 

ストリーム処理では、1 つのコンシューマー グループで複数のレシーバーを作成することがあります。 このようなシナリオをサポートする目的で、エポックなしでレシーバーを作成できます。その場合、コンシューマー グループで最大 5 つの同時レシーバーが許可されます。

### <a name="mixed-mode"></a>混在モード
同じコンシューマー グループでエポックありでレシーバーを作成し、その後エポックなしに切り替える、あるいはその逆を行うことは使用方法として推奨されません。 しかしながら、そのような動作が行われる場合、このサービスでは次のルールで動作が処理されます。

- エポック e1 でレシーバーが既に作成されているとき、イベントをたくさん受信し、新しいレシーバーがエポックなしで作成される場合、新しいレシーバーの作成に失敗します。 エポック レシーバーはシステム内で常に優先されます。
- エポック e1 でレシーバーが既に作成されていて接続が解除され、新しい MessagingFactory で新しいレシーバーがエポックなしで作成された場合、新しいレシーバーの作成に成功します。 ただし、このシステムでは 10 分後には "レシーバー切断" が検出されます。
- 1 つまたは複数のレシーバーがエポックなしで作成されているとき、新しいレシーバーがエポック e1 で作成される場合、古いレシーバーの接続が解除されます。


## <a name="next-steps"></a>次の手順

イベント プロセッサ ホストについて学習した後は、Event Hubs の詳細について次の記事を参照してください。

* [Event Hubs のチュートリアル](event-hubs-dotnet-standard-getstarted-send.md)を開始する
* [Event Hubs のプログラミング ガイド](event-hubs-programming-guide.md)
* [Event Hubs における可用性と一貫性](event-hubs-availability-and-consistency.md)
* [Event Hubs の FAQ](event-hubs-faq.md)
* [GitHub にある Event Hubs のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples)
