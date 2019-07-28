---
title: Azure Media Services イベントへの対応 | Microsoft Docs
description: Media Services イベントをサブスクライブするには、Azure Event Grid を使います。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996164"
---
# <a name="handling-event-grid-events"></a>Event Grid イベントの処理

Media Services イベントは、アプリケーションがモダンなサーバーレス アーキテクチャを使用してさまざまなイベント (ジョブ状態変更イベントなど) に対応できるようにします。 複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。 代わりに、イベントが [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通してイベント ハンドラー ([Azure Functions](https://azure.microsoft.com/services/functions/) や [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) の他にユーザー独自の Webhook も可) にプッシュされるため、支払いは使用した分だけで済みます。 価格の詳細については、[Event Grid の価格](https://azure.microsoft.com/pricing/details/event-grid/)に関する記事をご覧ください。

Media Services イベントの可用性は、Event Grid の[可用性](../../event-grid/overview.md)に関連付けられており、Event Grid の場合と同様に、他のリージョンで使用可能になります。  

## <a name="media-services-events-and-schemas"></a>Media Services のイベントとスキーマ

Event Grid は、[イベント サブスクリプション](../../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Media Services イベントには、データの変更に対応するために必要なすべての情報が含まれます。 Media Services イベントは、eventType プロパティが "Microsoft.Media" で始まっていることで識別できます。

詳細については、「[Media Services イベントのスキーマ](media-services-event-schemas.md)」を参照してください。

## <a name="practices-for-consuming-events"></a>イベントの使用に関する手法

Media Services イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションが構成されている可能性があるので、イベントが特定のソースからのものであると思い込まず、メッセージのトピックを調べて、想定しているストレージ アカウントからのものであることを確認することが重要です。
* 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
* わからないフィールドは無視します。  この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。
* イベントを特定のイベントに制限するには、"subject" プレフィックスとサフィックスの一致を使用します。

## <a name="next-steps"></a>次の手順

[ジョブ状態イベントを取得する](job-state-events-cli-how-to.md)
