---
title: Azure SignalR Service のイベントに対応する
description: Azure SignalR Service のイベントをサブスクライブするには、Azure Event Grid を使います。 その他のダウンストリーム サービスは、これらのイベントによってトリガーできます。
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151115"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Azure SignalR Service のイベントに対応する

Azure SignalR Service のイベントは、最新のサーバーレス アーキテクチャを使用して、アプリケーションがクライアント接続の接続や接続解除に対応できるようにしています。 複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。  イベントは、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通して、[Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) などのサブスクライバー、またはユーザー独自のカスタム HTTP リスナーにプッシュされます。 Azure SignalR では、使用した分だけ支払います。

Azure SignalR Service のイベントは Event Grid Service に確実に送信されます。Event Grid Service では豊富な再試行ポリシーおよび配信不能メッセージ配信を使用してご利用のアプリケーションに信頼性の高い配信サービスが提供されます。 詳細については、「[Event Grid のメッセージの配信と再試行](../event-grid/delivery-and-retry.md)」を参照してください。

![Event Grid モデル](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>サーバーレスの状態
Azure SignalR Service のイベントは、クライアント接続がサーバーレスの状態の場合にのみアクティブになります。 クライアントでハブ サーバーにルーティングされない場合、サーバーレスの状態に入ります。 クラシック モードは、クライアント接続で接続しているハブにハブ サーバーがない場合にのみ動作します。 ベスト プラクティスとして、サーバーレス モードが推奨されます。 サービス モードの詳細については、[サービス モードの選択方法](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)に関する記述を参照してください。

## <a name="available-azure-signalr-service-events"></a>使用可能な Azure SignalR Service のイベント
Event Grid は、[イベント サブスクリプション](../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Azure SignalR Service のイベント サブスクリプションは、2 種類のイベントをサポートします。  

|イベント名|説明|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|クライアント接続が接続されたときに発生します。|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|クライアント接続が接続解除されたときに発生します。|

## <a name="event-schema"></a>イベント スキーマ
Azure SignalR Service のイベントには、データの変更に対応するために必要なすべての情報が含まれます。 Azure SignalR Service のイベントは、eventType プロパティが "Microsoft.SignalRService" で始まるものとして識別できます。 Event Grid イベントのプロパティの使用法について詳しくは、「[Event Grid イベント スキーマ](../event-grid/event-schema.md)」をご覧ください。  

クライアント接続が接続された場合のイベントの例を次に示します。
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

詳しくは、[SignalR Service のイベント スキーマに関する記事](../event-grid/event-schema-azure-signalr.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

Event Grid の詳細について理解し、Azure SignalR Service のイベントを試してみてください。

> [!div class="nextstepaction"]
> [Event Grid を Azure SignalR Service と統合するサンプルを試してみる](./signalr-howto-event-grid-integration.md)
> [Event Grid について](../event-grid/overview.md)