---
title: Azure Service Bus の重複メッセージの検出 | Microsoft Docs
description: この記事では、Azure Service Bus メッセージの重複を検出する方法について説明します。 重複したメッセージは無視して、破棄することができます。
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 527c2dea34b02733907372b6e75a40a5ef5fc289
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711927"
---
# <a name="duplicate-detection"></a>重複検出

メッセージの送信直後に致命的なエラーが原因でアプリケーションが終了し、再起動したアプリケーション インスタンスがメッセージが送信されていなかったと誤認識した場合、メッセージが再送信され、同じメッセージがシステム内で 2 回表示されます。

また、直前にクライアントまたはネットワーク レベルでエラーが発生し、送信されたメッセージがキューにコミットされ、クライアントに受信確認が正常に返されない場合もあります。 このシナリオでは、送信操作の結果についてクライアントは確認が取れないままになります。

重複メッセージの検出機能は、送信側が同じメッセージを再送信する一方でキューまたはトピックで重複メッセージを削除できるようにすることで、このような状況から不確実性を排除します。

> [!NOTE]
> Service Bus の Basic レベルでは、重複検出はサポートされません。 Standard と Premium レベルでは、重複検出はサポートされます。 これらのレベルの違いについては、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。

## <a name="how-it-works"></a>動作のしくみ 
重複メッセージ検出を有効にすることにより、アプリケーションによって制御され、指定した時間枠内のキューまたはトピックに送信されたすべてのメッセージの *MessageId* を追跡できます。 その時間枠内にログに記録された *MessageId* が付いた新しいメッセージが送信されると、メッセージは受信済み (送信操作成功) としてレポートされ、新しく送信されたメッセージは瞬時に無視され破棄されます。 メッセージの *MessageId* 以外の部分は考慮されません。

ID のアプリケーションによる制御は、アプリケーションが *MessageId* をエラー発生時に予想可能な方法で再構築できるビジネス プロセス コンテキストに紐付けることができる唯一の方法であるため、不可欠です。

アプリケーション コンテキストの処理過程で複数のメッセージが送信されるビジネス プロセスの場合、*MessageId* は、注文書番号などのアプリケーション レベルのコンテキスト識別子と、メッセージの内容を組み合わせたもの、たとえば、**12345.2017/支払い** のようになります。

*MessageId* は、常に何らかの GUID にすることができますが、ID をビジネス プロセスに固定すると、重複検出機能を効果的に使用するために必要とされる予測可能な再現性が生み出されます。

> [!IMPORTANT]
>- **パーティション分割** が **有効** になっている場合は、`MessageId+PartitionKey` を使用して一意性が判断されます。 セッションが有効になっている場合、パーティション キーとセッション ID は同じである必要があります。 
>- **パーティション分割** が **無効** (既定) になっている場合は、`MessageId` のみを使用して一意性が判断されます。
>- SessionId、PartitionKey、および MessageId の詳細については、「[パーティション キーの使用](service-bus-partitioning.md#use-of-partition-keys)」を参照してください。
>- [Premium レベル](service-bus-premium-messaging.md)ではパーティション分割はサポートされていないため、アプリケーションでは一意のメッセージ ID を使用し、重複検出に関してパーティション キーに依存しないことをお勧めします。 


## <a name="enable-duplicate-detection"></a>重複検出の有効化

この機能は、ポータルでエンティティの作成する際に、既定ではオフになっている、 **[重複データ検出を有効にする]** チェック ボックスをオンにすることで有効にできます。 新しいトピックを作成するための設定と同じです。

![[キューの作成] ダイアログ ボックスのスクリーンショット。[重複データ検出の有効化] オプションが選択され、赤の枠線で囲まれています。][1]

> [!IMPORTANT]
> キューが作成された後に重複の検出を有効または無効にすることはできません。 それができるのはキューの作成時点のみです。 

プログラム上では、フル フレームワーク .NET API の [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) プロパティにフラグを設定します。 Azure Resource Manager API では、値は [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) プロパティで設定されます。

重複検出時間の履歴は、既定では、キューとトピックで 10 分に設定され、最小値は 20 秒、最大値は 7 日です。 この設定は、Azure ポータルの [キューおよびトピックのプロパティ] ウィンドウで変更することができます。

![Service Bus 機能のスクリーンショット。[プロパティ] 設定が強調表示され、[重複データ検出の履歴] オプションが赤の枠線で囲まれています。][2]

プログラム上は、フル .NET Framework API の [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) プロパティを使用して、メッセージ が保持される重複検出時間枠を設定できます。 Azure Resource Manager API では、値は [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) プロパティで設定します。

すべての記録されたメッセージ ID を新しく送信されたメッセージ ID と照合する必要があるため、重複の検出機能を有効化し、時間枠を設定すると、キュー (およびトピック) のスループットに直接影響します。

時間枠を小さく設定すると、保持し照合する必要があるメッセージ ID 数が減り、スループットへの影響は小さくなります。 スループットの高いエンティティで重複検出が必要な場合は、時間枠を小さく設定してください。

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

クライアント コードで、以前と同じ *MessageId* を使用してメッセージを再送信できない場合は、安全に再処理できるメッセージを設計することが重要です。 このためのさまざまな手法について詳しくは、[冪等性に関するブログ投稿](https://particular.net/blog/what-does-idempotent-mean)をご覧ください。

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
