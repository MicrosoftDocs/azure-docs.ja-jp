---
title: Service Bus の非同期メッセージング |Microsoft Docs
description: Azure Service Bus が、キュー、トピック、およびサブスクリプションを使用して、ストア アンド フォワード メカニズムを通じて非同期をサポートする方法について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e37c18b95bca7ef1e6e8f0d74976bb73b214624a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500633"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>非同期メッセージング パターンと高可用性

非同期メッセージングはさまざまな方法で実装できます。 キュー、トピック、サブスクリプションにより、Azure Service Bus は格納と転送のメカニズムを通じて非同期性をサポートします。 通常の (同期) 操作では、キューとトピックにメッセージを送信し、キューとサブスクリプションからメッセージを受信します。 作成するアプリケーションは、これらのエンティティが常に使用できることに依存します。 さまざまな状況によってエンティティの状態が変化した場合、ほとんどのニーズを満たすことができる、機能を低減したエンティティを提供する方法が必要になります。

通常、アプリケーションは非同期メッセージング パターンを使用して数多くの通信シナリオを実現します。 サービスが実行されていない場合でも、クライアントがサービスにメッセージを送信できるアプリケーションを構築できます。 通信が急増することがあるアプリケーションでは、キューによって、通信をバッファーに書き込む場所を提供することで、負荷を分散できます。 最後に、簡単であるが効果的なロード バランサーによって、複数のコンピューター間でメッセージを分散できます。

これらのエンティティの可用性を維持するために、永続的なメッセージング システムでこれらのエンティティが使用不可能と表示される可能性があるさまざまな方法を考えてみます。 一般的に、次のような方法でアプリケーションを記述すると、エンティティは使用不可能になります。

* メッセージを送信できない。
* メッセージを受信できない。
* エンティティを管理できない (エンティティの作成、取得、更新、または削除)。
* サービスにアクセスできない。

これらの障害のそれぞれについて、アプリケーションが機能をある程度抑えたレベルで継続して動作できるようにするさまざまな障害モードが存在します。 たとえば、メッセージを送信できるが受信できないシステムであっても、顧客の注文を受信することはできるが、注文を処理することはできないなどです。 このトピックでは、発生する可能性のある問題と、それらの問題を軽減する方法について説明します。 Service Bus では、選択する必要がある数多くの緩和策が導入されており、このトピックではこれらのオプトイン軽減策の使用に関するルールについても説明します。

## <a name="reliability-in-service-bus"></a>Service Bus の信頼性
メッセージとエンティティの問題を処理する方法はいくつかあります。また、これらの緩和策の適切な使用に関するガイドラインがあります。 このガイドラインを理解するには、最初に Service Bus で障害が発生する可能性がある状況について理解する必要があります。 Azure システムの設計により、これらすべての問題は一時的である傾向があります。 大まかに言って、使用不可能となる原因は次のとおりです。

* Service Bus が依存している外部システムからの調整。 調整は、ストレージ リソースやコンピューティング リソースとのやり取りから発生します。
* Service Bus が依存しているシステムの問題。 たとえば、ストレージの特定の部分で問題が発生するなどです。
* 単一のサブシステムでの Service Bus の障害。 この状況では、コンピューティング ノードが一貫性のない状態になって再起動が必要になり、対応しているすべてのエンティティが他のノードに負荷を分散することになります。 これにより、メッセージの処理が一時的に低速になります。
* Azure データセンター内での Service Bus の障害。 これは、数分または数時間にわたってシステムにアクセスできなくなる "致命的な障害" です。

> [!NOTE]
> **ストレージ** という用語は、Azure ストレージと SQL Azure の両方を意味する場合があります。
> 
> 

Service Bus には、これらの問題に対する数多くの緩和策が用意されています。 以降のセクションでは、個々の問題とそれぞれの緩和策について説明します。

### <a name="throttling"></a>Throttling
Service Bus では、調整によりメッセージ レートを協調管理できます。 それぞれの Service Bus ノードが、個別に複数のエンティティを格納します。 これらの各エンティティにより、CPU、メモリ、ストレージ、その他のファセットに関してシステムへの要求が行われます。 これらのいずれかのファセットで、定義されたしきい値を超える使用が検出されると、Service Bus によって特定の要求が拒否される可能性があります。 呼び出し元は [ServerBusyException][ServerBusyException] を受け取り、10 秒後に再試行します。

緩和策として、コードでエラーを読み取り、メッセージの再試行を少なくとも 10 秒間停止します。 顧客アプリケーションのさまざまな部分でエラーが発生する可能性があるため、各部分が独立して再試行ロジックを実行する必要があります。 コードでキューまたはトピックに対してパーティション化を有効にすることにより、調整される可能性を低減できます。

### <a name="issue-for-an-azure-dependency"></a>Azure の依存関係の問題
Azure 内の他のコンポーネントで、サービスの問題が発生する場合があります。 たとえば、Service Bus が使用するシステムがアップグレードされた場合、そのシステムでは一時的に機能が低下することがあります。 このような種類の問題に対処するために、Service Bus では定期的に緩和策を調査し、実装しています。 これらの緩和策には副作用があります。 たとえば、ストレージに関する一時的な問題を処理するために、Service Bus はメッセージ送信操作が一貫して動作できるようにするシステムを実装しています。 緩和策の性質により、送信されたメッセージが影響するキューまたはサブスクリプションに表示されて受信操作の準備が整うまでに、最大 15 分かかることがあります。 一般的に、ほとんどのエンティティではこの問題は発生しません。 ただし、Azure 内の Service Bus のエンティティの数によっては、少数のサブセットの Service Bus の顧客でこの緩和策が必要になる場合があります。

### <a name="service-bus-failure-on-a-single-subsystem"></a>単一のサブシステムでの Service Bus の障害。
どのアプリケーションでも、状況によっては Service Bus の内部コンポーネントに不整合が生じる場合があります。 Service Bus がこれを検出すると、アプリケーションからデータを収集し、何が起こったかを診断する手助けとします。 データが収集されると、アプリケーションは一貫性のある状態に戻すために再起動されます。 このプロセスは比較的迅速に発生し、エンティティが使用できない状態は最大で数分続きます。ただし、通常のダウンタイムはこれよりもはるかに短くなります。

このような状況では、クライアント アプリケーションは [System.TimeoutException][System.TimeoutException] または [MessagingException][MessagingException] 例外を生成します。 Service Bus には、自動化されたクライアント再試行ロジックという形で、この問題の軽減策が備わっています。 再試行期間が終了してもメッセージが配信されない場合は、「[故障と障害の扱い][handling outages and disasters]」の記事に記載されている他の方法を試すことができます。

## <a name="next-steps"></a>次の手順
Service Bus での非同期メッセージングの基本について学習しました。詳細については、「[故障と障害の扱い][handling outages and disasters]」をお読みください。

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[handling outages and disasters]: service-bus-outages-disasters.md