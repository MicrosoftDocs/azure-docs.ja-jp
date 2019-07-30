---
title: Azure Service Bus の重複メッセージの検出 | Microsoft Docs
description: Service Bus の重複メッセージの検出
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
ms.openlocfilehash: 286c5850400242224e710a7883d3d3dc175cef12
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273206"
---
# <a name="duplicate-detection"></a>重複検出

メッセージの送信直後に致命的なエラーが原因でアプリケーションが終了し、再起動したアプリケーション インスタンスがメッセージが送信されていなかったと誤認識した場合、メッセージが再送信され、同じメッセージがシステム内で 2 回表示されます。

また、送信直前にクライアントまたはネットワーク レベルでエラーが発生し、送信されたメッセージがキューにコミットされて、クライアントに確認が返信されない場合もあります。 このシナリオでは、送信操作の結果についてクライアントは確認が取れないままになります。

重複メッセージの検出機能は、送信側が同じメッセージを再送信する一方でキューまたはトピックで重複メッセージを削除できるようにすることで、このような状況から不確実性を排除します。

重複メッセージ検出を有効にすることにより、アプリケーションによって制御され、指定した時間枠内のキューまたはトピックに送信されたすべてのメッセージの *MessageId* を追跡できます。 その時間枠内にログに記録された *MessageId* が付いた新しいメッセージが送信されると、メッセージは受信済み (送信操作成功) としてレポートされ、新しく送信されたメッセージは瞬時に無視され破棄されます。 メッセージの *MessageId* 以外の部分は考慮されません。

ID のアプリケーションによる制御は、アプリケーションが *MessageId* をエラー発生時に予想可能な方法で再構築できるビジネス プロセス コンテキストに紐付けることができる唯一の方法であるため、不可欠です。

アプリケーション コンテキストの処理過程で複数のメッセージが送信されるビジネス プロセスの場合、*MessageId* は、注文書番号などのアプリケーション レベルのコンテキスト識別子と、メッセージの内容を組み合わせたもの、たとえば、**12345.2017/支払い**のようになります。

*MessageId* には常になんらかの GUID を使用でき、重複検出機能を効果的に活用するには、ビジネス プロセスと ID を紐付けることで予想可能な再現性を確立する必要があります。

> [!NOTE]
> 重複の検出が有効になっていて、セッション ID またはパーティション キーが設定されていない場合、メッセージ ID がパーティション キーとして使用されます。 メッセージ ID も設定されていない場合は、.NET と AMQP のライブラリによってメッセージのメッセージ ID が自動的に生成されます。 詳細については、「[パーティション キーの使用](service-bus-partitioning.md#use-of-partition-keys)」を参照してください。

## <a name="enable-duplicate-detection"></a>重複検出の有効化

この機能は、ポータルでエンティティの作成する際に、既定ではオフになっている、 **[重複データ検出を有効にする]** チェック ボックスをオンにすることで有効にできます。 新しいトピックを作成するための設定と同じです。

![][1]

> [!IMPORTANT]
> キューが作成された後に重複の検出を有効または無効にすることはできません。 それができるのはキューの作成時点のみです。 

プログラム上では、フル フレームワーク .NET API の [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) プロパティにフラグを設定します。 Azure Resource Manager API では、値は [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) プロパティで設定されます。

重複検出時間の履歴は、既定では、キューおよびトピックで 30 秒に設定され、最大値は 7 日間に設定されています。 この設定は、Azure ポータルの [キューおよびトピックのプロパティ] ウィンドウで変更することができます。

![][2]

プログラム上は、フル .NET Framework API の [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) プロパティを使用して、メッセージ が保持される重複検出時間枠を設定できます。 Azure Resource Manager API では、値は [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) プロパティで設定します。

すべての記録されたメッセージ ID を新しく送信されたメッセージ ID と照合する必要があるため、重複の検出機能を有効化し、時間枠を設定すると、キュー (およびトピック) のスループットに直接影響します。

時間枠を小さく設定すると、保持し照合する必要があるメッセージ ID 数が減り、スループットへの影響は小さくなります。 スループットの高いエンティティで重複検出が必要な場合は、時間枠を小さく設定してください。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
