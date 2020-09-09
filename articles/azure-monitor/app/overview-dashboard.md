---
title: Azure Application Insights の概要ダッシュボード | Microsoft Docs
description: Azure の Application Insights と概要ダッシュボードの機能を使用してアプリケーションを監視します。
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8c8aa4460a545f032eaa60560de00efff27ae1ef
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322535"
---
# <a name="application-insights-overview-dashboard"></a>Application Insights の概要ダッシュボード

Application Insights では常に、アプリケーションの正常性とパフォーマンスを一目で評価できる概要ウィンドウが提供されてきました。 新しい概要ダッシュボードでは、より高速で柔軟なエクスペリエンスが提供されます。

## <a name="how-do-i-test-out-the-new-experience"></a>新しいエクスペリエンスをテストする方法

新しい概要ダッシュボードは、既定で起動されるようになりました。

![概要プレビュー ウィンドウ](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>パフォーマンスの向上

時間範囲の選択が、簡単な 1 クリックのインターフェイスに簡素化されました。

![時間の範囲](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

全体的なパフォーマンスが大幅に向上しました。 **検索**や **Analytics** のような人気のある機能に 1 回のクリックでアクセスできます。 既定の動的に更新される各 KPI タイルでは、Application Insights の対応する機能について把握できます。 失敗した要求の詳細については、 **[調査]** ヘッダーの下の **[失敗]** を選択します。

![エラー](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>アプリケーション ダッシュボード

アプリケーション ダッシュボードは、Azure が備える既存のダッシュボード テクノロジを利用して、アプリケーションの正常性とパフォーマンスに関する完全にカスタマイズ可能な単一のウィンドウ ビューを提供します。

既定のダッシュボードにアクセスするには、左上隅の _[アプリケーション ダッシュボード]_ を選びます。

![ダッシュボード ビュー](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

初めてダッシュボードにアクセスした場合は、既定のビューが表示されます。

![ダッシュボード ビュー](./media/overview-dashboard/0001-dashboard.png)

気に入った場合は、既定のビューをそのままにしておけます。 または、ダッシュボードの要素を追加および削除して、チームのニーズに合わせることもできます。

> [!NOTE]
> Application Insights のリソースにアクセスできるすべてのユーザーが、同じアプリケーション ダッシュボードのエクスペリエンスを共有します。 1 人のユーザーによって行われた変更が、すべてのユーザーのビューを変更します。

選択するだけで、概要エクスペリエンスに戻ります。

![[概要] ボタン](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>トラブルシューティング

**[タイル設定の構成]** を選択し、31 日を超えるカスタムの時間範囲を設定した場合、既定のデータ保有期間が 90 日であっても、ダッシュボードには 31 日を超えるデータは表示されません。 現時点では、この動作の回避策はありません。

## <a name="next-steps"></a>次のステップ

- [ファネル](./usage-funnels.md)
- [保持](./usage-retention.md)
- [ユーザー フロー](./usage-flows.md)

