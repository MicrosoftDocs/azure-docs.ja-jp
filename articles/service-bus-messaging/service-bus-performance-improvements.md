---
title: Azure Service Bus を使用したパフォーマンス向上のためのベスト プラクティス | Microsoft Docs
description: Service Bus を使用して、ブローカー メッセージを交換する際のパフォーマンスを最適化する方法について説明します。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 37e2dcc13ed41911c8117dc1841a389c14e5867f
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848580"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Service Bus メッセージングを使用したパフォーマンス向上のためのベスト プラクティス

この記事では、ブローカー メッセージを交換する際のパフォーマンスを Azure Service Bus を使用して最適化する方法について説明しています。 この記事の前半では、パフォーマンスの向上に役立つさまざまなメカニズムについて説明します。 後半では、特定のシナリオでパフォーマンスを最大限に高めるための Service Bus の使用方法に関するガイダンスを示します。

この記事全体で、"クライアント" という用語は Service Bus にアクセスするすべてのエンティティを指します。 クライアントは送信側または受信側の役割を実行できます。 "送信側" という用語は、Service Bus キューまたはトピックのサブスクリプションにメッセージを送信する Service Bus キューまたはトピックのクライアントを指します。 "受信側" という用語は、Service Bus キューまたはサブスクリプションからメッセージを受信する Service Bus キューまたはサブスクリプションのクライアントを指します。

以下のセクションでは、パフォーマンスを向上するために Service Bus で利用される概念をいくつか紹介します。

## <a name="protocols"></a>プロトコル

Service Bus を使用すると、クライアントは次の 3 つのプロトコルのいずれかを使用してメッセージを送受信できます。

1. Advanced Message Queuing Protocol (AMQP)
2. Service Bus メッセージング プロトコル (SBMP)
3. HTTP

AMQP と SBMP は、メッセージング ファクトリが存在する限り、Service Bus への接続を維持するため、より効率的です。 また、バッチとプリフェッチも実装されます。 明示的に示されていない限り、この記事のすべてのコンテンツで AMQP または SBMP を使用するものとします。

## <a name="reusing-factories-and-clients"></a>ファクトリとクライアントの再利用

[QueueClient][QueueClient] や [MessageSender][MessageSender] などの Service Bus クライアント オブジェクトは、接続の内部管理も提供する [MessagingFactory][MessagingFactory] オブジェクトによって作成されます。 メッセージを送信した後にメッセージ ファクトリ、またはキュー、トピック、サブスクリプションのクライアントを閉じ、次のメッセージを送信するときにこれらを再作成しないことが推奨されています。 メッセージング ファクトリを閉じると Service Bus サービスの接続が削除され、ファクトリを再作成すると新しい接続が確立されます。 接続の確立は費用のかかる操作です。この操作は、同じファクトリとクライアント オブジェクトを複数の操作に再利用することで回避できます。 これらのクライアントオブジェクトは、同時実行の非同期操作のために、複数のスレッドから安全に使用できます。 

## <a name="concurrent-operations"></a>同時実行の操作

操作 (送信、受信、削除など) には時間がかかります。 この時間には、要求と応答の待機時間だけでなく、Service Bus サービスによる操作の処理時間も含まれます。 時間あたりの操作数を増やすには、操作を同時に実行する必要があります。 

クライアントは非同期操作を実行することによって、同時実行操作のスケジュールを設定します。 前の要求が完了する前に次の要求が開始されます。 次のコード スニペットは、非同期送信操作の例です。
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  次のコードは、非同期受信操作の例です。 完全なプログラムは[こちら](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues)を参照してください。
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>受信モード

キューまたはサブスクリプションのクライアントを作成するときに、受信モードとして、"*ピーク/ロック*" または "*受信して削除*" を指定できます。 既定の受信モードは [PeekLock][PeekLock] です。 このモードで操作するとき、クライアントは Service Bus からメッセージを受信する要求を送信します。 クライアントはメッセージを受信した後で、メッセージを完了する要求を送信します。

受信モードを [ReceiveAndDelete][ReceiveAndDelete] に設定すると、1 つの要求に両方の手順が連結されます。 これらの手順によって、操作全体の数が削減され、全体的なメッセージ スループットを改善できます。 この方法でパフォーマンスを改善すると、メッセージを失うリスクが生じます。

Service Bus は "受信して削除" 操作のトランザクションをサポートしません。 また、クライアントでメッセージの遅延または[配信不能](service-bus-dead-letter-queues.md)が必要なシナリオではピーク/ロック セマンティクスが必要になります。

## <a name="client-side-batching"></a>クライアント側のバッチ処理

クライアント側のバッチ処理により、キューまたはトピックのクライアントはメッセージの送信を一定期間遅らせることができます。 クライアントがこの期間内に追加のメッセージを送信すると、1 つのバッチで複数のメッセージが送信されます。 また、クライアント側のバッチ処理では、キューまたはサブスクリプションのクライアントが、複数の**完了**要求を 1 つの要求でバッチ処理します。 バッチ処理を使用できるのは、非同期の**送信**と**完了**操作のみです。 同期操作はすぐに Service Bus サービスに送信されます。 バッチ処理はピーク操作や受信操作では行われません。また、クライアント間でも行われません。

既定では、クライアントは 20 ミリ秒のバッチ間隔を使用します。 バッチ間隔を変更するには、メッセージング ファクトリを作成する前に、[BatchFlushInterval][BatchFlushInterval] プロパティを設定します。 この設定は、このファクトリによって作成されるすべてのクライアントに影響します。

バッチ処理を無効にするには、[BatchFlushInterval][BatchFlushInterval] プロパティを **TimeSpan.Zero** に設定します。 例: 

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

バッチ処理は課金対象のメッセージ操作数に影響を与えません。また、[Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) ライブラリを使用する Service Bus クライアント プロトコルでのみ利用できます。 HTTP プロトコルはバッチ処理をサポートしません。

## <a name="batching-store-access"></a>ストア アクセスのバッチ処理

キュー、トピック、またはサブスクリプションのスループットを高めるために、Service Bus はその内部ストアに書き込む際に複数のメッセージをバッチ処理します。 キューまたはトピックで有効になっている場合、ストアへのメッセージ書き込みがバッチ処理されます。 キューまたはサブスクリプションで有効になっている場合、ストアからのメッセージ削除がバッチ処理されます。 エンティティのバッチ処理ストア アクセスが有効になっている場合、Service Bus はそのエンティティに関するストア書き込み操作を最大 20 ミリ秒遅らせます。 

> [!NOTE]
> 20 ミリ秒のバッチ処理間隔の終了時に Service Bus でエラーがあった場合でも、バッチ処理のメッセージが失われるリスクがありません。 

この間隔中に発生した追加のストアの操作はバッチに追加されます。 バッチ処理ストア アクセスは**送信**操作と**完了**操作にのみ影響を与えます。受信操作には影響を与えません。 バッチ処理ストア アクセスはエンティティのプロパティです。 バッチ処理は、バッチ処理ストア アクセスが有効になっているすべてのエンティティで発生します。

新しいキュー、トピック、サブスクリプションを作成すると、バッチ処理ストア アクセスは既定で有効になります。 バッチ処理ストア アクセスを無効にするには、エンティティを作成する前に、[EnableBatchedOperations][EnableBatchedOperations] プロパティを **false** に設定します。 例: 

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

バッチ処理ストア アクセスは課金対象のメッセージ操作の数には影響を与えない、キュー、トピック、サブスクリプションのプロパティです。 受信モードから独立しており、クライアントと Service Bus サービスの間で使用されるプロトコルです。

## <a name="prefetching"></a>プリフェッチ

[プリフェッチ](service-bus-prefetch.md)により、キューまたはサブスクリプションのクライアントは受信操作の実行時にサービスから追加のメッセージを読み込むことができます。 クライアントはこれらのメッセージをローカル キャッシュに格納します。 キャッシュのサイズは、[QueueClient.PrefetchCount][QueueClient.PrefetchCount] プロパティまたは [SubscriptionClient.PrefetchCount][SubscriptionClient.PrefetchCount] プロパティによって決まります。 プリフェッチが有効になっているクライアントはそれぞれ独自のキャッシュを保持します。 キャッシュはクライアント間で共有されません。 クライアントが受信操作を開始するときに、そのクライアントのキャッシュが空の場合、サービスはメッセージのバッチを送信します。 バッチのサイズは、キャッシュのサイズと 256 KB のうちの少ない方と等しくなります。 クライアントが受信操作を開始するときに、そのクライアントのキャッシュにメッセージが含まれている場合、キャッシュからメッセージが取得されます。

メッセージがプリフェッチされると、サービスはプリフェッチされたメッセージをロックします。 このロックにより、別の受信側はプリフェッチされたメッセージを受信できなくなります。 受信側がメッセージを完了できない状態でロックの有効期限が切れた場合、他の受信側がメッセージを受信できるようになります。 プリフェッチされたメッセージのコピーはキャッシュに残ります。 受信側が有効期限の切れたキャッシュのコピーを使用している場合、そのメッセージを完了しようとしたときに例外を受け取ります。 既定では、メッセージのロックは 60 秒後に期限切れになります。 この値は 5 分まで拡張できます。 期限切れのメッセージの使用を防ぐには、キャッシュ サイズを常に、ロックのタイムアウト間隔内にクライアントが使用できるメッセージの数より小さくする必要があります。

60 秒間の既定のロック有効期限を使用する場合、[PrefetchCount][SubscriptionClient.PrefetchCount] の適切な値はファクトリの全受信側の最大処理レートの 20 倍になります。 たとえば、ファクトリが 3 つの受信側を作成すると、各受信側は 1 秒あたり最大 10 個のメッセージを処理できます。 プリフェッチ数が 20 X 3 X 10 = 600 を超えないようにしてください。 既定では、[PrefetchCount][QueueClient.PrefetchCount] は 0 に設定されます。これはサービスから追加のメッセージがフェッチされないことを意味します。

メッセージをプリフェッチすると、メッセージ操作全体の数、つまりラウンド トリップが減るため、キューまたはサブスクリプションの全体でのスループットが増えます。 ただし、最初のメッセージのフェッチには (メッセージ サイズの増加に起因して) 時間がかかります。 プリフェッチ済みのメッセージは、クライアントが既にダウンロードしているため、速く受信できます。

サーバーがクライアントにメッセージを送信するとき、メッセージの有効期間 (TTL) プロパティがサーバーによりチェックされます。 クライアントは、メッセージを受信するときに、メッセージの TTL プロパティをチェックしません。 代わりに、メッセージがクライアントによりキャッシュされたときにメッセージの TTL を経過している場合でも、メッセージを受信できます。

プリフェッチは課金対象のメッセージ操作数に影響を与えません。また、Service Bus クライアント プロトコルでのみ利用できます。 HTTP プロトコルはプリフェッチをサポートしません。 プリフェッチは同期受信操作と非同期受信操作の両方で使用できます。

## <a name="multiple-queues"></a>複数のキュー

予想される負荷をパーティション分割された 1 つのキューまたはトピックで処理できない場合、複数のメッセージング エンティティを使用する必要があります。 複数のエンティティを使用するときは、すべてのエンティティに同じクライアントを使用するのではなく、エンティティごとに専用のクライアントを作成します。

## <a name="development-and-testing-features"></a>開発およびテストの機能

Service Bus には、開発専用に使用され、**運用環境の構成では絶対に使用しない**ようにする必要がある機能が 1 つあります。それは、[TopicDescription.EnableFilteringMessagesBeforePublishing][] です。

新しい規則またはフィルターをトピックに追加したときに、[TopicDescription.EnableFilteringMessagesBeforePublishing][] を使用して、新しいフィルター式が予想どおりに動作していることを確認できます。

## <a name="scenarios"></a>シナリオ

次のセクションでは、一般的なメッセージング シナリオについて説明し、好ましい Service Bus 設定について概要を説明します。 スループット レートは小 (1 メッセージ/秒未満)、中 (1 メッセージ/秒以上、100 メッセージ/秒未満)、高 (100 メッセージ/秒以上) に分類されます。 クライアントの数は小 (5 以下)、中 (5 より大きく 20 以下)、大 (20 より大きい) に分類されます。

### <a name="high-throughput-queue"></a>高スループット キュー

目標: 1 つのキューのスループットを最大にします。 送信側と受信側の数は小です。

* キューへの全体的な送信レートを上げるには、複数のメッセージ ファクトリを使用して送信側を作成します。 送信側ごとに、非同期操作または複数のスレッドを使用します。
* キューからの全体的な受信レートを上げるには、複数のメッセージ ファクトリを使用して受信側を作成します。
* クライアント側のバッチ処理を活用するには非同期操作を使用します。
* バッチ処理間隔を 50 ミリ秒に設定して、Service Bus クライアント プロトコル伝送の数を減らします。 複数の送信側を使用する場合は、バッチ処理の間隔を 100 ミリ秒に増やします。
* バッチ処理ストア アクセスを有効なままにします。 このアクセスにより、メッセージをキューに書き込む全体的なレートが上がります。
* プリフェッチ数をファクトリの全受信側の最大処理レートの 20 倍に設定します。 この数の設定によって、Service Bus クライアント プロトコル伝送の数が減ります。
* パフォーマンスと可用性の改善のために、パーティション分割されたキューを使用します。

### <a name="multiple-high-throughput-queues"></a>複数の高スループット キュー

目標: 複数のキューの全体的なスループットを最大にします。 個々のキューのスループットは中または高です。

複数のキュー全体で最大のスループットを得るには、説明にある設定を使用し、1 つのキューのスループットを最大化します。 また、複数のファクトリを使用し、複数のキューと送受信するクライアントを作成します。

### <a name="low-latency-queue"></a>低待機時間のキュー

目標: キューまたはトピックのエンド ツー エンドの待機時間を最小限に抑えます。 送信側と受信側の数は小です。 キューのスループットは小または中です。

* クライアント側のバッチ処理を無効にします。 クライアントはすぐにメッセージを送信します。
* バッチ処理ストア アクセスを無効にします。 サービスはメッセージをストアに直ちに書き込みます。
* 1 つのクライアントを使用している場合、プリフェッチ数を受信側の処理レートの 20 倍に設定します。 複数のメッセージが同時にキューに到着すると、Service Bus クライアント プロトコルはそれらすべてを同時に送信します。 クライアントが次のメッセージを受信するとき、そのメッセージは既にローカル キャッシュにあります。 キャッシュは小にします。
* 複数のクライアントを使用している場合、プリフェッチ数を 0 に設定します。 この数を設定することで、最初のクライアントが最初のメッセージを処理している間に、2 番目のクライアントは 2 番目のメッセージを受信できます。
* パフォーマンスと可用性の改善のために、パーティション分割されたキューを使用します。

### <a name="queue-with-a-large-number-of-senders"></a>送信側の数が多いキュー

目標: 送信側の数が多いキューまたはトピックのスループットを最大にします。 送信側はそれぞれ中程度のレートでメッセージを送信します。 受信側の数は小です。

Service Bus によって、メッセージング エンティティに最大 1000 (または AMQP 使用で 5000) 件コンカレント接続できます。 この制限は名前空間レベルで適用され、キュー、トピック、サブスクリプションは名前空間あたりのコンカレント接続数の上限によって制限されます。 キューの場合、この数は送信側と受信側で共有されます。 1000 件の接続すべてが送信側で必要な場合は、キューをトピックと 1 つのサブスクリプションで置き換えます。 トピックは送信側から最大 1000 件のコンカレント接続を受け入れるのに対して、サブスクリプションは受信側からさらに 1000 件のコンカレント接続を受け入れます。 1000 件を超える同時接続が送信側で必要な場合は、送信側は HTTP 経由で Service Bus プロトコルにメッセージを送信する必要があります。

スループットを最大化するには、次の手順を実行します。

* 各送信側が異なるプロセスに存在する場合、プロセスごとに 1 つのファクトリのみを使用します。
* クライアント側のバッチ処理を活用するには非同期操作を使用します。
* 20 ミリ秒の既定のバッチ処理間隔を使用して、Service Bus クライアント プロトコル伝送の数を減らします。
* バッチ処理ストア アクセスを有効なままにします。 このアクセスによって、メッセージをキューまたはトピックに書き込む全体的なレートが上がります。
* プリフェッチ数をファクトリの全受信側の最大処理レートの 20 倍に設定します。 この数の設定によって、Service Bus クライアント プロトコル伝送の数が減ります。
* パフォーマンスと可用性の改善のために、パーティション分割されたキューを使用します。

### <a name="queue-with-a-large-number-of-receivers"></a>受信側の数が多いキュー

目標: 受信側の数が多いキューまたはサブスクリプションの受信レートを最大にします。 各受信側は中程度のレートでメッセージを受信します。 送信側の数は小です。

Service Bus によって、エンティティに最大 1000 件コンカレント接続できるようになります。 キューが 1000 件を超える受信側を必要とする場合は、キューをトピックと複数のサブスクリプションで置き換えます。 各サブスクリプションは最大 1000 件のコンカレント接続をサポートします。 または、受信側は HTTP プロトコル経由でキューにアクセスできます。

スループットを最大化するには、次の手順を実行します。

* 各受信側が異なるプロセスに存在する場合、プロセスごとに 1 つのファクトリのみを使用します。
* 受信側は同期操作または非同期操作を使用できます。 各受信側の受信レートを中にすると、受信側のスループットは完了要求のクライアント側のバッチ処理の影響を受けません。
* バッチ処理ストア アクセスを有効なままにします。 このアクセスによって、エンティティ全体の負荷が軽減されます。 これにより、メッセージをキューまたはトピックに書き込む全体的なレートも下がります。
* プリフェッチ数を小さい値 (PrefetchCount = 10 など) に設定します。 この数の設定によって、他の受信側が大量のメッセージをキャッシュしている間に受信側がアイドル状態になることを防止できます。
* パフォーマンスと可用性の改善のために、パーティション分割されたキューを使用します。

### <a name="topic-with-a-small-number-of-subscriptions"></a>サブスクリプションの数が少ないトピック

目標: サブスクリプションの数が少ないトピックのスループットを最大にします。 メッセージは多くのサブスクリプションで受信されます。これはすべてのサブスクリプションの受信レートを合わせると送信レートを超えることを意味します。 送信側の数は小です。 サブスクリプションあたりの受信側の数は小です。

スループットを最大化するには、次の手順を実行します。

* トピックへの全体的な送信レートを上げるには、複数のメッセージ ファクトリを使用して送信側を作成します。 送信側ごとに、非同期操作または複数のスレッドを使用します。
* サブスクリプションからの全体的な受信レートを上げるには、複数のメッセージ ファクトリを使用して受信側を作成します。 受信側ごとに、非同期操作または複数のスレッドを使用します。
* クライアント側のバッチ処理を活用するには非同期操作を使用します。
* 20 ミリ秒の既定のバッチ処理間隔を使用して、Service Bus クライアント プロトコル伝送の数を減らします。
* バッチ処理ストア アクセスを有効なままにします。 このアクセスによって、メッセージをトピックに書き込む全体的なレートが上がります。
* プリフェッチ数をファクトリの全受信側の最大処理レートの 20 倍に設定します。 この数の設定によって、Service Bus クライアント プロトコル伝送の数が減ります。
* パフォーマンスと可用性の改善のために、パーティション分割されたトピックを使用します。

### <a name="topic-with-a-large-number-of-subscriptions"></a>サブスクリプションの数が多いトピック

目標: サブスクリプションの数が多いトピックのスループットを最大にします。 メッセージは多くのサブスクリプションで受信されます。これはすべてのサブスクリプションの受信レートを合わせると送信レートをはるかに超えることを意味します。 送信側の数は小です。 サブスクリプションあたりの受信側の数は小です。

すべてのメッセージがすべてのサブスクリプションに送信される場合、通常、多数のサブスクリプションを含むトピックの全体的なスループットは低下します。 スループットが低下する原因は、各メッセージが数回受信され、トピックとそのすべてのサブスクリプションに含まれるメッセージがすべて同じストアに保存されることにあります。 サブスクリプションあたりの送信側の数と受信側の数は小であるとします。 Service Bus はトピックあたり最大 2,000 のサブスクリプションをサポートします。

スループットを最大化するには、次の手順を試します。

* クライアント側のバッチ処理を活用するには非同期操作を使用します。
* 20 ミリ秒の既定のバッチ処理間隔を使用して、Service Bus クライアント プロトコル伝送の数を減らします。
* バッチ処理ストア アクセスを有効なままにします。 このアクセスによって、メッセージをトピックに書き込む全体的なレートが上がります。
* プリフェッチ数を予想される受信レート (秒単位) の 20 倍に設定します。 この数の設定によって、Service Bus クライアント プロトコル伝送の数が減ります。
* パフォーマンスと可用性の改善のために、パーティション分割されたトピックを使用します。

## <a name="next-steps"></a>次の手順

Service Bus のパフォーマンスの最適化の詳細については、[パーティション分割されたメッセージング エンティティ][Partitioned messaging entities]に関する記事をご覧ください。

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
