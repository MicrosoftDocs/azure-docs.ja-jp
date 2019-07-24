---
title: Azure Service Bus のメッセージ数 | Microsoft Docs
description: Azure Service Bus のメッセージ数の取得
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: adfd8c5849cfee69805715378a3f56ec9f685b00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050749"
---
# <a name="message-counters"></a>メッセージ カウンター

.NET Framework SDK の Azure Resource Manager および Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API を使用して、キュー、およびサブスクリプションに保持されているメッセージの数を取得できます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用して、次のように、メッセージ数を取得できます。

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>メッセージ数の詳細

現在展開されているものより、処理により多くのリソースを必要とするバックログをキューが生成するかどうか判断するために、アクティブなメッセージの数を把握することが有効です。 次のカウンターの詳細は、[MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) クラスにあります。

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): キューまたはサブスクリプションにある、アクティブな状態および配信の準備ができているメッセージの数。
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): 配信不能キュー内のメッセージの数。
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): スケジュールされた状態にあるメッセージの数。
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): 別のキューまたはトピックへの転送に失敗し、転送配信不能キューに移動されたメッセージの数。
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): 別のキューまたはトピックへの転送が保留されたメッセージの数。

アプリケーションで、キューの長さに基づいてリソースを拡張する必要がある場合は、正確に測定された間隔で実行する必要があります。 メッセージ カウンターの取得は、メッセージ ブローカー内で多くのリソースを必要とする操作であり、頻繁に実行すると、エンティティ パフォーマンスに直接的な悪影響を及ぼします。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
