---
title: "Service Bus メッセージング エンティティの自動転送 |Microsoft Docs"
description: "キューまたはサブスクリプションを別のキューまたはトピックにチェーンする方法。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: cbbd416a065b3284e85957cc024955d11524d3da
ms.lasthandoff: 01/12/2017


---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>自動転送を使用した Service Bus エンティティのチェーン
*自動転送*機能を使用すると、キューまたはサブスクリプションを同じ名前空間に属する別のキューまたはトピックにチェーンできます。 自動転送が有効な場合は、Service Bus は、一方のキューまたはサブスクリプション (転送元) にあるメッセージを自動的に削除し、もう一方のキューまたはトピック (転送先) に追加します。 ここで、転送先エンティティにメッセージを直接送信できることに注意してください。 配信不能キューなどのサブキューを別のキューまたはトピックにチェーンすることはできません。

## <a name="using-auto-forwarding"></a>自動転送の使用
ソースの [QueueDescription][QueueDescription] オブジェクトまたは [SubscriptionDescription][SubscriptionDescription] オブジェクトの [QueueDescription.ForwardTo][QueueDescription.ForwardTo] または [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] プロパティを、次の例のように設定することにより自動転送を有効にできます。

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

転送先エンティティは、ソース エンティティの作成時に存在している必要があります。 転送先エンティティが存在しない場合、Service Bus は、ソース エンティティを作成するように要求されたときに例外を返します。

自動転送を使用すると、個々のトピックをスケールアウトできます。 Service Bus は、[特定のトピックのサブスクリプションの数](service-bus-quotas.md)を 2,000 に制限します。 第&2; レベルのトピックを作成することで、追加のサブスクリプションに対応できます。 Service Bus のサブスクリプションの数に関する制限がない場合でも、トピックの&2; 番目のレベルを追加することで、トピック全体のスループットを向上させることができます。

![自動転送のシナリオ][0]

また、自動転送を使用して、メッセージの送信者と受信者を分離することができます。 たとえば、注文処理、在庫管理、顧客関係管理の&3; つのモジュールで構成される ERP システムがあるとします。 これらのモジュールはそれぞれ、対応するトピックにエンキューされるメッセージを生成します。 Alice と Bob は、顧客に関連するすべてのメッセージに関心のある営業担当者です。 これらのメッセージを受信するため、Alice と Bob は、ERP トピックごとに個人用のキューとサブスクリプションを作成して、自動的にすべてのメッセージが自分のキューに転送されるようにします。

![自動転送のシナリオ][1]

Alice が休暇中の場合、ERP トピックではなく、個人用のキューがいっぱいになります。 このシナリオでは、1 人の営業担当者がメッセージをまったく受信しないため、すべての ERP トピックがクォータに達しません。

## <a name="auto-forwarding-considerations"></a>自動転送に関する考慮事項
転送先エンティティに多数のメッセージが蓄積されクォータを超過した場合や、転送先エンティティが無効な場合、ソース エンティティは転送先に領域ができるまで (またはエンティティが再度有効になるまで)、[配信不能キュー](service-bus-dead-letter-queues.md)にメッセージを追加します。 これらのメッセージは、配信不能キューに残り続けるので、配信不能キューで明示的に受信して処理する必要があります。

多くのサブスクリプションを含む複合トピックにするために個別のトピックをチェーンするときには、第&1; レベルのトピックに中程度のサブスクリプションと、2 番目のレベルのトピックに多くのサブスクリプションが含まれるようにすることをお勧めします。 たとえば、第 1 レベルのトピックが 20 のサブスクリプションを持ち、そのそれぞれが 200 のサブスクリプションを持つ第 2 レベルのトピックにチェーンされている場合、第 1 レベルのトピックが 200 のサブスクリプションを持ち、そのそれぞれが 20 のサブスクリプションを持つ第 2 レベルのトピックにチェーンされている場合よりも高いスループットが実現されます。

Service Bus では、メッセージの転送ごとに&1; 操作を請求します。 たとえば、別のキューまたはトピックにメッセージを自動転送するようにそれぞれ構成されている 20 のサブスクリプションを持つトピックにメッセージを送信した場合、第 1 レベルのすべてのサブスクリプションがメッセージを受信すると、21 の操作として請求されます。

別のキューまたはトピックにチェーンされているサブスクリプションを作成するには、サブスクリプションの作成者は、転送元エンティティと転送先エンティティの両方に対する**管理**アクセス許可が必要です。 転送元トピックにメッセージを送信する場合は、転送元トピックに対する**送信**アクセス許可のみが必要です。

## <a name="next-steps"></a>次のステップ
自動転送の詳細については、次のリファレンス トピックを参照してください。

* [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Service Bus のパフォーマンスの向上について詳しくは、「[パーティション分割されたメッセージング エンティティ][Partitioned messaging entities]」をご覧ください。

[QueueDescription.ForwardTo]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md

