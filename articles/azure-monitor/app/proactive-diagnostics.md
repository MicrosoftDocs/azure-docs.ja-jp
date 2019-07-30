---
title: Azure Application Insights のスマート検出 | Microsoft Docs
description: Application Insights は、アプリのテレメトリの詳細な分析を自動的に実行し、潜在的なパフォーマンスの問題について警告します。
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: 8ee2dea364253d871d5624242d15d8a81ab6f08f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465896"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights のスマート検出
 スマート検出により、Web アプリケーションの潜在的なパフォーマンスの問題と失敗の異常について警告を自動的に受け取ることができます。 スマート検出では、アプリから [Application Insights](../../azure-monitor/app/app-insights-overview.md) に送信されるテレメトリがプロアクティブに分析されます。 障害発生率が急激に上昇したり、クライアントまたはサーバーのパフォーマンスに異常なパターンが発生したりした場合に、アラートが表示されます。 この機能には構成は不要です。 アプリケーションから適切なテレメトリが送信されていれば動作します。

スマート検出によって発行される検出には、受信した電子メールと、[スマート検出] ブレードの両方からアクセスできます。

## <a name="review-your-smart-detections"></a>スマート検出数を確認する
検出結果は 2 種類の方法で確認できます。

* **電子メール** を Application Insights から受信します。 典型的な例を次に示します。
  
    ![電子メール アラート](./media/proactive-diagnostics/03.png)
  
    ポータルで詳細を表示するには、大きなボタンをクリックします。
* Application Insights の **[スマート検出] ブレード**。 **[調査]** メニューの **[スマート検出]** を選択すると、最近の検出の一覧が表示されます。

![最近の検出結果の表示](./media/proactive-diagnostics/04.png)

検出を選択すると、その詳細が表示されます。

## <a name="what-problems-are-detected"></a>検出される問題
スマート検出では、次のようなさまざまな問題が検出され、通知が行われます。

* [スマート検出 - 失敗の異常](../../azure-monitor/app/proactive-failure-diagnostics.md)。 機械学習を使用して、負荷などの要因と関連付けて、アプリの要求が失敗すると予想される率を設定します。 障害発生率が、予想の包絡線を超えた場合、アラートが送信されます。
* [スマート検出 - パフォーマンスの異常](../../azure-monitor/app/proactive-performance-diagnostics.md)。 操作の応答時間または依存関係の継続期間が過去の基準と比較して低下している場合や、応答時間またはページの読み込み時間に異常なパターンが特定された場合は、通知を受信します。   
* [トレースの低下](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity)、[メモリ リーク](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak)、[例外数の異常な上昇](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume)、[セキュリティ アンチ パターン](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack)など、一般的な低下および問題。

(各通知のヘルプ リンクをクリックすると関連する記事が表示されます)。

## <a name="smart-detection-email-notifications"></a>スマート検出の電子メール通知

_プレビュー_とマークされているルールを除く、すべてのスマート検出ルールは、検出の発見時に既定で電子メール通知を送信するように構成されます。

特定のスマート検出ルールの電子メール通知を構成するには、スマート検出の **[設定]** ブレードを開き、ルールを選択すると、 **[ルールの編集]** ブレードが開きます。

または、Azure Resource Manager テンプレートを使用して、構成を変更できます。 [詳細については、「Azure Resource Manager テンプレートを使用して Application Insights スマート検出ルールを管理する」](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) を参照してください。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>次の手順
これらの診断ツールを使用すると、アプリからテレメトリを調査できます。

* [メトリックス エクスプローラー](../../azure-monitor/app/metrics-explorer.md)
* [Search エクスプローラー](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - 強力なクエリ言語](../../azure-monitor/log-query/get-started-portal.md)

スマート検出は、すべて自動化されています。 ただし、アラートを追加で設定する機能が用意されています。

* [手動で構成するメトリックのアラート](../../azure-monitor/app/alerts.md)
* [可用性 Web テスト](../../azure-monitor/app/monitor-web-app-availability.md) 

