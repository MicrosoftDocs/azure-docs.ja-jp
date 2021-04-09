---
title: Azure Cache for Redis Event Grid の概要
description: Azure Event Grid を使用して Azure Cache for Redis のイベントを公開します。
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99056972"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Azure Cache for Redis Event Grid の概要 

ファイルの部分置換、スケーリング、インポート/エクスポート (RDB) イベントなどの Azure Cache for Redis イベントは、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を使用して、Azure Functions、Azure Logic Apps などのサブスクライバー、または独自の HTTP リスナーにもプッシュされます。 Event Grid は、豊富な再試行ポリシーおよび配信不能を使用して、ご利用のアプリケーションに信頼性の高いイベント配信を提供します。

Azure Cache for Redis でサポートされているイベントの完全な一覧については、[Azure Cache for Redis のイベント スキーマ](../event-grid/event-schema-azure-cache.md)に関する記事を参照してください。

Azure Cache for Redis のイベントを試す場合は、次のクイックスタートのいずれかを参照してください。

|使うツール:    |参照するクイックスタート |
|--|-|
|Azure portal    |[クイックスタート:Azure portal を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[クイックスタート:PowerShell を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](cache-event-grid-quickstart-powershell.md) (英語)|
|Azure CLI    |[クイックスタート:Azure CLI を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](cache-event-grid-quickstart-cli.md) (英語)|

## <a name="the-event-model"></a>イベント モデル

Event Grid は、[イベント サブスクリプション](../event-grid/concepts.md#event-subscriptions)を使用して、イベント メッセージをサブスクライバーにルーティングします。 この図は、イベント発行元、イベント サブスクリプション、およびイベント ハンドラーの間の関係を示しています。

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Event Grid のモデル":::

最初に、イベントにエンドポイントをサブスクライブします。 その後、イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがエンドポイントに送信されます。

[Azure Cache for Redis のイベント スキーマ](../event-grid/event-schema-azure-cache.md)に関する記事で以下を確認してください。

> [!div class="checklist"]
> * Azure Cache for Redis のイベントの全一覧と各イベントがトリガーされる方法。
> * これらの各イベントに対して Event Grid が送信するデータの例。
> * データに表示される各キー値ペアの目的。


## <a name="best-practices-for-consuming-events"></a>イベントの使用に関するベスト プラクティス

Azure Cache for Redis のイベントを処理するアプリケーションでは、いくつかの推奨される手法に従う必要があります。
> [!div class="checklist"]
> * 複数のサブスクリプションが同じイベント ハンドラーにイベントをルーティングするように構成される場合があるため、イベントが特定のソースからのものであると見なすのではなく、メッセージのトピックをチェックして、予測している Azure Cache for Redis インスタンスから来ていることを確認することが重要です。
> * 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
> * Azure Cache for Redis のイベントにより、サブスクライバーへの少なくとも 1 回の配信が保証され、すべてのメッセージが確実に出力されます。 ただし、サブスクリプションの再試行または可用性のために、重複するメッセージが発生することがあります。 メッセージの配信と再試行の詳細については、「[Event Grid によるメッセージの配信と再試行](../event-grid/delivery-and-retry.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

Event Grid の詳細について理解し、Azure Cache for Redis のイベントを試してみてください。

- [Event Grid について](../event-grid/overview.md)
- [Azure Cache for Redis のイベント スキーマ](../event-grid/event-schema-azure-cache.md) (英語)
- [Azure CLI を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](cache-event-grid-quickstart-cli.md) (英語)
- [Azure portal を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](cache-event-grid-quickstart-portal.md)
- [PowerShell を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](cache-event-grid-quickstart-powershell.md) (英語)
