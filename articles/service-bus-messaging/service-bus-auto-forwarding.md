---
title: Azure Service Bus メッセージング エンティティの自動転送
description: この記事では、Azure Service Bus キューまたはサブスクリプションを別のキューまたはトピックにチェーンする方法について説明します。
ms.topic: article
ms.date: 01/20/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 80bef52d568130fa800a1da661f4867abb3df02c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678990"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>自動転送を使用した Service Bus エンティティのチェーン

Service Bus の *自動転送* 機能を使用すると、キューまたはサブスクリプションを同じ名前空間に属する別のキューまたはトピックにチェーンできます。 自動転送が有効な場合、Service Bus は元のキューまたはサブスクリプション (転送元) にあるメッセージを自動的に削除し、そのメッセージを転送先のキューまたはトピックに入れます。 転送先エンティティには､メッセージを直接送信することもできます｡

> [!NOTE]
> Service Bus の Basic レベルでは、自動転送機能がサポートされていません。 Standard レベルと Premium レベルでは、この機能がサポートされています。 これらのレベルの違いについては、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。

## <a name="using-autoforwarding"></a>自動転送の利用

自動転送は､以下の例のように､ソースの [QueueDescription][QueueDescription] オブジェクトまたは [SubscriptionDescription][SubscriptionDescription] オブジェクトの [QueueDescription.ForwardTo][QueueDescription.ForwardTo] または [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] プロパティを、次の例のように設定することで有効にできます。

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

転送先エンティティは、ソース エンティティの作成時に存在している必要があります。 転送先エンティティが存在しない場合、Service Bus は、ソース エンティティを作成するように要求されたときに例外を返します。

自動転送を使用すると、トピックを個々にスケールアウトできます。 Service Bus は、[特定のトピックのサブスクリプションの数](service-bus-quotas.md)を 2,000 に制限します。 第 2 レベルのトピックを作成することで、追加のサブスクリプションに対応できます。 Service Bus のサブスクリプションの数に関する制限がない場合でも、トピックの 2 番目のレベルを追加することで、トピック全体のスループットを向上させることができます。

![自動転送シナリオの図。1 つの注文トピックを通じて処理されるメッセージを示しています。この注文トピックは、第 2 レベルの 3 つの注文トピックのいずれかに分岐されます。][0]

また、自動転送を使用して、メッセージの送信者と受信者を分離することもできます。 たとえば、注文処理、在庫管理、顧客関係管理の 3 つのモジュールで構成される ERP システムがあるとします。 これらのモジュールはそれぞれ、対応するトピックにエンキューされるメッセージを生成します。 Alice と Bob は、顧客に関連するすべてのメッセージに関心のある営業担当者です。 これらのメッセージを受信するため、Alice と Bob は、ERP トピックごとに個人用のキューとサブスクリプションを作成して、自動的にすべてのメッセージが自分のキューに転送されるようにします。

![自動転送シナリオの図。3 つの処理モジュールによって、3 つの対応するトピックを通じて 2 つの個別のキューに向けてメッセージが送信されています。][1]

Alice が休暇中の場合、ERP トピックではなく、個人用のキューがいっぱいになります。 このシナリオでは、1 人の営業担当者がメッセージをまったく受信しないため、すべての ERP トピックがクォータに達しません。

> [!NOTE]
> 自動転送が設定されると、**転送元と転送先、両方** の AutoDeleteOnIdle の値は自動的にデータ型の最大値に設定されます。
> 
>   - 転送元側では、自動転送は受信操作として行われます。 このため、自動転送設定がある転送元は、実際には "アイドル" になることがありません。
>   - 転送先側では、これは常に宛先がメッセージの転送先となるように実行されます。

## <a name="autoforwarding-considerations"></a>自動転送に関する考慮事項

転送先エンティティに蓄積されたメッセージが多すぎてクォータを超過した場合や、転送先エンティティが無効な場合、ソース エンティティは転送先に領域ができるまで (またはエンティティが再度有効になるまで)、[配信不能キュー](service-bus-dead-letter-queues.md)にメッセージを追加します。 これらのメッセージは、配信不能キューに残り続けるので、配信不能キューで明示的に受信して処理する必要があります。

多くのサブスクリプションを含む複合トピックにするために個別のトピックをチェーンするときには、第 1 レベルのトピックに中程度のサブスクリプションと、2 番目のレベルのトピックに多くのサブスクリプションが含まれるようにすることをお勧めします。 たとえば、第 1 レベルのトピックが 20 のサブスクリプションを持ち、そのそれぞれが 200 のサブスクリプションを持つ第 2 レベルのトピックにチェーンされている場合、第 1 レベルのトピックが 200 のサブスクリプションを持ち、そのそれぞれが 20 のサブスクリプションを持つ第 2 レベルのトピックにチェーンされている場合よりも高いスループットが実現されます。

Service Bus では、メッセージの転送ごとに 1 操作を請求します。 たとえば、別のキューまたはトピックにメッセージを自動転送するようにそれぞれ構成されている 20 のサブスクリプションを持つトピックにメッセージを送信した場合、第 1 レベルのすべてのサブスクリプションがメッセージを受信すると、21 の操作として請求されます。

別のキューまたはトピックにチェーンされているサブスクリプションを作成するには、サブスクリプションの作成者は、転送元エンティティと転送先エンティティの両方に対する **管理** アクセス許可が必要です。 転送元トピックにメッセージを送信する場合は、転送元トピックに対する **送信** アクセス許可のみが必要です。

4 ホップを超えるチェーンを作成しないでください。 4 ホップを超えるメッセージは配信不能です。

## <a name="next-steps"></a>次の手順

自動転送についての詳細は、次のリファレンス トピックを参照してください。

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Service Bus のパフォーマンスの向上について詳しくは、以下をご覧ください。 

* [Service Bus メッセージングを使用したパフォーマンス向上のためのベスト プラクティス](service-bus-performance-improvements.md)
* [パーティション分割されたメッセージング エンティティ][Partitioned messaging entities]。

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
