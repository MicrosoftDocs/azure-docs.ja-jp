---
title: Azure API Management の [問題の診断と解決]
description: Azure portal の診断と解決ツールを使用して Azure API Management での API の問題をトラブルシューティングする方法について説明します。
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652404"
---
# <a name="azure-api-management-diagnostics-overview"></a>Azure API Management 診断の概要

Azure API Management で API を構築して管理する際には、404 見つかりませんのエラーから 502 無効なゲートウェイのエラーまで、発生する可能性のある問題に備える必要があります。 API Management 診断は、APIM で公開された API のトラブルシューティングを支援するインテリジェントな対話型エクスペリエンスで、構成は不要です。 公開した API で問題が発生すると、API Management 診断によって問題点が指摘され、その問題を迅速にトラブルシューティングして解決する適切な情報がユーザーに提供されます。

このエクスペリエンスは API の問題発生時刻が過去 24 時間以内である場合に最も役立ちますが、すべての診断グラフをいつでも分析に利用できます。

## <a name="open-api-management-diagnostics"></a>API Management 診断を開く

API Management 診断にアクセスするには、[Azure portal](https://portal.azure.com) で API Management サービス インスタンスに移動します。 左側のナビゲーションで、 **[問題の診断と解決]** を選択します。

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="診断に移動する方法を示すスクリーンショット。":::



## <a name="intelligent-search"></a>インテリジェント検索

ページの上部にある検索バーで、問題を検索できます。 検索は、問題のトラブルシューティングや解決に役立つツールを見つけるのにも役立ちます。 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="インテリジェント検索のスクリーンショット。":::


## <a name="troubleshooting-categories"></a>カテゴリのトラブルシューティング

カテゴリの下で問題をトラブルシューティングできます。 API のパフォーマンス、ゲートウェイ、API ポリシー、サービス レベルに関連する一般的な問題はすべて、各カテゴリ内で分析できます。 各カテゴリには、より具体的な診断チェックも用意されています。 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="カテゴリの概要のスクリーンショット。":::


### <a name="availability-and-performance"></a>可用性とパフォーマンス

API の可用性とパフォーマンスの問題については、こちらのカテゴリの下でチェックします。 このカテゴリのタイルを選択すると、対話型インターフェイスでいくつかの一般的なチェックが推奨されていることがわかります。 各問題の詳細を確認するには、各チェックをクリックします。 このチェックによって、API のパフォーマンスとパフォーマンスの問題の概要を示すグラフも表示されます。 たとえば、API サービスで 5xx エラーが発生し、バックエンドで過去 1 時間にタイムアウトが発生した可能性があります。 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="対話型インターフェイス チェックのスクリーンショット 1。":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="対話型インターフェイス チェックのスクリーンショット 2。":::

### <a name="api-policies"></a>API ポリシー

このカテゴリでは、ポリシーの問題に関するエラーを検出し、ユーザーに通知します。 

類似の対話型インターフェイスにより、API ポリシーの構成のトラブルシューティングに役立つデータ メトリックが提供されます。

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="API ポリシー カテゴリ タイルのスクリーンショット。":::

### <a name="gateway-performance"></a>ゲートウェイ パフォーマンス 

ゲートウェイの要求や応答、ゲートウェイでの 4xx エラーや 5xx エラーについては、このカテゴリを使用して監視とトラブルシューティングを行います。 同様に、対話型インターフェイスを活用して、API ゲートウェイのパフォーマンスをチェックする必要がある特定の領域を深く掘り下げます。 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="ゲートウェイ パフォーマンス カテゴリ タイルのスクリーンショット。":::

### <a name="service-upgrade"></a>サービス アップグレード

このカテゴリでは、現在どのサービス レベル (SKU) をユーザーが使用しているかをチェックし、そのレベルに関連する可能性のある問題を回避するためにアップグレードするようユーザーに通知します。 同じ対話型インターフェイスにより、さまざまなグラフィックスやチェック結果の概要を確認して、深く掘り下げることができます。 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="サービス アップグレード カテゴリ タイルのスクリーンショット。":::

## <a name="search-documentation"></a>ドキュメントの検索

問題の診断と解決ツールに加えて、その問題に関連するトラブルシューティングのドキュメントを検索することもできます。 サービスの診断を実行した後、対話型インターフェイスで **[ドキュメントの検索]** を選択します。 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="[ドキュメントの検索] 機能の使用方法のスクリーンショット 1。":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="[ドキュメントの検索] 機能の使用方法のスクリーンショット 2。":::


## <a name="next-steps"></a>次のステップ

* さらに [API 分析](howto-use-analytics.md)を使用して、API の使用状況とパフォーマンスを分析します。 
* 診断を使用して Web アプリの問題をトラブルシューティングする場合は、 [こちら](../app-service/overview-diagnostics.md)を参照
* 診断を活用して、Azure Kubernetes Services の問題をチェックします。 [AKS での診断](../aks/concepts-diagnostics.md)に関するページを参照
* 質問またはフィードバックは、[UserVoice](https://feedback.azure.com/forums/248703-api-management) でタイトルに "[Diag]" を付けて投稿してください。
