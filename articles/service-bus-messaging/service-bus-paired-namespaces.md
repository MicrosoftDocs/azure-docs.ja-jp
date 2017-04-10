---
title: "Azure Service Bus のペアの名前空間 | Microsoft Docs"
description: "ペアの名前空間の実装の詳細とコスト"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: d987aa22379ede44da1b791f034d713a49ad486a
ms.openlocfilehash: 84e125dffcac3f3a54250587c5238b50d3a6cb95
ms.lasthandoff: 02/16/2017


---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>ペアの名前空間の実装の詳細とコストの問題
[PairNamespaceAsync][PairNamespaceAsync] メソッドは、[SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] インスタンスを使用して、ユーザーに代わって表示可能なタスクを実行します。 この機能の使用はコストがかかる場合があるので、それらの動作の動作時にそれを予期できるように、それらのタスクについて理解していると便利です。 API は、ユーザーに代わって、次の動作を自動的に行います。

* バックログ キューの作成。
* キューまたはトピックと通信する [MessageSender][MessageSender] オブジェクトの作成。
* メッセージング エンティティが利用不可になると、そのエンティティが再度利用可能になるときを検出するために ping メッセージがエンティティに送信されます。
* 必要に応じて、バックログ キューからプライマリ キューにメッセージを移動する一連の “メッセージ ポンプ” を作成します。
* プライマリとセカンダリの [MessagingFactory][MessagingFactory] インスタンスの終了およびエラーを調整します。

この機能はおおよそ次のように動作します。プライマリ エンティティが正常な場合、動作の変更はありません。 [FailoverInterval][FailoverInterval] 期間が経過し、一時的でない [MessagingException][MessagingException] または [TimeoutException][TimeoutException] の後に、成功した送信がないことをプライマリ エンティティが確認すると、次の動作が発生します。

1. プライマリ エンティティへの送信操作は無効になり、ping が正常配信されるまで、システムはプライマリ エンティティを ping します。
2. ランダムなバックログ キューが選択されます。
3. [BrokeredMessage][BrokeredMessage] オブジェクトは、選択されたバックログ キューにルーティングされます。
4. 選択されたバックログ キューへの送信操作が失敗した場合、そのキューはローテーションから外され、新しいキューが選択されます。 [MessagingFactory][MessagingFactory] インスタンスのすべての送信者が、エラーを認識します。

次の図にシーケンスを示します。 まず、送信側がメッセージを送信します。

![ペアの名前空間][0]

プライマリ キューへの送信に失敗すると、送信者はランダムに選択されたバックログ キューにメッセージの送信を開始します。 同時に、ping タスクを開始します。

![ペアの名前空間][1]

この時点で、メッセージはまだセカンダリ キュー内にあり、プライマリ キューに配布されていません。 プライマリ キューが正常に戻ったとき、少なくとも&1; つのプロセスがサイホンを実行している必要があります。 サイホンは、すべての各種バックログ キューから、適切な送信先のエンティティ (キューおよびトピック) にメッセージを配信します。

![ペアの名前空間][2]

このトピックの残りの部分では、これらの部分のしくみの具体的な詳細を説明します。

## <a name="creation-of-backlog-queues"></a>バックログ キューの作成
[PairNamespaceAsync][PairNamespaceAsync] メソッドに渡される [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] オブジェクトは、使用するバックログ キューの数を示します。 次のプロパティが明示的に設定された各バックログ キューが作成されます (その他のすべての値は、[QueueDescription][QueueDescription]の既定値に設定されます)。

| パス | [primary namespace]/x-servicebus-transfer/[index] ここで [index] は [0, BacklogQueueCount) の値 |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int.MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1 分 |
| EnableDeadLetteringOnMessageExpiration |true |
| EnableBatchedOperations |true |

たとえば、名前空間 **contoso** 用に作成された最初のバックログ キューの名前は `contoso/x-servicebus-transfer/0` になります。

キューが作成される際、コードはそのようなキューが存在するかどうかをまず確認します。 キューが存在しない場合、キューは作成されます。 コードは "余分な" バックログ キューをクリーンアップしません。 具体的には、プライマリ名前空間 **contoso** のアプリケーションがバックログ キューを&5; つ要求する場合に、パスが `contoso/x-servicebus-transfer/7` のバックログ キューが存在する場合、その余分なバックログ キューは引き続き存在しますが、使用はされません。 システムでは、使用されない余分なバックログ キューの存在を明示的に許可します。 名前空間の所有者は、不要な未使用のバックログ キューをクリーンアップする必要があります。 これは、Service Bus が名前空間内のすべてのキューの存在目的を把握していないためです。 さらに、キューが指定した名前で存在するが、想定されている [QueueDescription][QueueDescription] と一致しない場合、既定の動作を自由に変更できます。 コードによって、バックログ キューへの変更が加えられる保証はありません。 変更は徹底的にテストしてください。

## <a name="custom-messagesender"></a>カスタム MessageSender
送信されるすべてのメッセージは、すべてのものが機能している場合は通常どおりに動作し、何かが "中断" している場合はバックログ キューにリダイレクトされる、内部 [MessageSender][MessageSender] オブジェクトを経由します。 一時的でないエラーを受け取ると、タイマーが開始されます。 フェールオーバーは、成功メッセージが送信されない [FailoverInterval][FailoverInterval] プロパティの値が構成されている、[TimeSpan][TimeSpan] 期間後に実行されます。 この時点で、エンティティごとに次が行われます。

* [PingPrimaryInterval][PingPrimaryInterval] ごとに ping タスクが実行され、エンティティが使用可能であるかが確認されます。 このタスクが成功すると、このエンティティを使用するすべてのクライアント コードが、プライマリ名前空間に新しいメッセージの送信を直ちに開始します。
* その他のすべての送信者から同じエンティティに送信されるそれ以降の要求は、[BrokeredMessage][BrokeredMessage] がバックログ キューにとどまるように変更されて送信されます。 この変更により、[BrokeredMessage][BrokeredMessage] オブジェクトから一部のプロパティが削除され、別の場所に保存されます。 次のプロパティがクリアされ、新しいエイリアスで追加されるため、Service Bus と SDK がメッセージを一貫して処理できるようになります。

| 古いプロパティ名 | 新しいプロパティ名 |
| --- | --- |
| SessionId |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

元の転送先のパスも x-path ms をという名前のプロパティとしてメッセージ内に保存されます。 この設計により、多くのエンティティのメッセージが&1; つのバックログ キューに共存できるようになります。 プロパティはサイホンによって変換されて戻されます。

カスタム [MessageSender][MessageSender] オブジェクトは、メッセージが 256 KB の制限に近づき、フェールオーバーが実行されると問題が発生する場合があります。 カスタム [MessageSender][MessageSender] オブジェクトは、すべてのキューおよびトピックのメッセージをまとめてバックログ キューに格納します。 このオブジェクトは、バックログ キュー内で、多くのプライマリからのメッセージを混在させます。 互いを認識しない多数のクライアント間で負荷分散する際、SDK はコードに作成された [QueueClient][QueueClient] または [TopicClient][TopicClient] ごとに、ランダムにバックログ キューを&1; つ選択します。

## <a name="pings"></a>Ping
ping メッセージは、その [ContentType][ContentType] プロパティが application/vnd.ms-servicebus-ping に設定され、[TimeToLive][TimeToLive] 値が 1 秒の空の [BrokeredMessage][BrokeredMessage] です。 この ping は Service Bus で&1; つの特別な特徴を持ちます。呼び出し元が [BrokeredMessage][BrokeredMessage] を要求した場合、サーバーは ping を配信しません。 そのため、これらのメッセージを受信し無視する方法を学習する必要はありません。 クライアントごとの [MessagingFactory][MessagingFactory] インスタンスごとに各エンティティ (一意のキューまたはトピック) が使用できないと見なされる場合に、それらが ping されます。 既定で、これは&1; 分間に&1; 回行われます。 Ping メッセージは通常の Service Bus メッセージと見なされ、帯域幅とメッセージの料金が発生します。 クライアントが使用可能なシステムであることを検出すると、メッセージは直ちに停止されます。

## <a name="the-syphon"></a>サイホン
アプリケーションの少なくとも&1; つの実行可能プログラムが、サイホンをアクティブに実行している必要があります。 サイホンは 15 分間続く長いポーリングを実行します。 すべてのエンティティが利用可能で、バックログ キューが 10 ある場合、サイホンをホストするアプリケーションは、1 時間に 40 回、1 日に 960 回、30 日で 28,880 回受信操作を呼び出します。 サイホンがメッセージをアクティブにバックログからプライマリ キューに移動する場合、各メッセージで次の料金が発生します (メッセージ サイズと帯域幅の標準の料金がすべてのステージで適用されます)。

1. バックログに送信します。
2. バックログから受信します。
3. プライマリに送信します。
4. プライマリから受信します。

## <a name="closefault-behavior"></a>終了およびエラー動作
サイホンをホストするアプリケーション内のプライマリまたはセカンダリ [MessagingFactory][MessagingFactory] で、(そのパートナーにエラーが発生したり、終了したりすることなく) エラーが発生するか、終了し、サイホンがこの状態を検出すると、サイホンが動作します。 その他の [MessagingFactory][MessagingFactory] が 5 秒以内に閉じない場合、サイホンはまだ開いている [MessagingFactory][MessagingFactory] をエラーとします。

## <a name="next-steps"></a>次のステップ
Service Bus の非同期メッセージングの詳細については、「[非同期メッセージング パターンと高可用性][Asynchronous messaging patterns and high availability]」を参照してください。 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png

