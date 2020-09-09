---
title: Azure Event Grid パートナー トピック
description: Azure Event Grid を使用して、サードパーティの Event Grid SaaS および PaaS パートナーから直接 Azure サービスにイベントを送信します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e56055c55a3b30c5d13736b9838257f3c0bbec10
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831908"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Azure Event Grid でのパートナー トピック (プレビュー)
パートナー トピックを使用することで、サードパーティのイベント ソースを Azure Event Grid に直接接続できます。 この統合により、Azure サービスからのイベントをサブスクライブするのと同じ方法で、パートナーからのイベントをサブスクライブすることができます。 

## <a name="available-partners"></a>使用可能なパートナー
Event Grid パートナー トピックを介して使用できる最初のパートナーは、Auth0 です。 [Auth0 パートナー トピック](auth0-overview.md)を使用して、Auth0 アカウントと Azure アカウントを接続できます。 この統合によって、リアルタイムで Auth0 イベントに反応し、ログを記録し、監視することができます。

[試してみる](auth0-how-to.md)には、Auth0 アカウントにサインインし、Event Grid 統合を作成します。 Auth0 で **[作成]** をクリックすると、Azure アカウントに保留中の Auth0 トピックが表示されます。 **[アクティブ化]** を選択すると、他のイベント ソースの場合と同様に、ルーティング、フィルター処理、およびイベントの配信を行うための Event Grid イベント サブスクリプションを作成できます。

## <a name="pricing"></a>価格
パートナー トピックは、システム トピックと同じ稼働率で課金されます。

## <a name="limits"></a>制限
パートナー トピックは、パブリック プレビュー段階にあります。 パブリック プレビューの段階では、システム トピックおよびカスタム トピックと[同じ制限](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)がパートナー トピックにも適用されます。

## <a name="how-do-i-become-an-event-grid-partner"></a>Event Grid パートナーになるには
開始のサポートのために作成されたインフラストラクチャを使用すると、新しいパートナーはそのイベント機能を Event Grid と短時間で簡単に統合できます。 詳細については、[パートナーのオンボードに関するドキュメント](partner-onboarding-overview.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Auth0 パートナー トピック](auth0-overview.md)
- [Auth0 パートナー トピックの使用方法](auth0-how-to.md)
- [Event Grid パートナーになる](partner-onboarding-overview.md)