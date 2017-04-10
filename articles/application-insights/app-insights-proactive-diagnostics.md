---
title: "Azure Application Insights のスマート検出 | Microsoft Docs"
description: "Application Insights は、アプリのテレメトリの詳細な分析を自動的に実行し、潜在的なパフォーマンスの問題について警告します。"
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: fe2596a7fed52118425c612ebf50c01134928bf6
ms.lasthandoff: 03/16/2017


---
# <a name="smart-detection-in-application-insights"></a>Application Insights のスマート検出
 スマート検出により、Web アプリケーションの潜在的なパフォーマンスの問題について警告を自動的に受け取ることができます。 スマート検出では、アプリから [Application Insights](app-insights-overview.md) に送信されるテレメトリがプロアクティブに分析されます。 障害発生率が急激に上昇したり、クライアントまたはサーバーのパフォーマンスに異常なパターンが発生したりした場合に、アラートが表示されます。 この機能には構成は不要です。 アプリケーションから適切なテレメトリが送信されていれば動作します。

スマート検出アラートには、受信した電子メールと、[スマート検出] ブレードの両方からアクセスできます。

## <a name="review-your-smart-detections"></a>スマート検出数を確認する
検出結果は 2 種類の方法で確認できます。

* **電子メール** を Application Insights から受信します。 典型的な例を次に示します。
  
    ![電子メール アラート](./media/app-insights-proactive-diagnostics/03.png)
  
    ポータルで詳細を表示するには、大きなボタンをクリックします。
* **スマート検出タイル**に、最近のアラートの数が表示されます。 最近のアラートの一覧を確認するには、タイルをクリックします。

![最近の検出結果の表示](./media/app-insights-proactive-diagnostics/04.png)

詳細を表示するには、対象のアラートを選択します。

## <a name="what-problems-are-detected"></a>検出される問題
検出には 3 種類あります。

* [スマート検出 - 失敗の異常](app-insights-proactive-failure-diagnostics.md)。 機械学習を使用して、負荷などの要因と関連付けて、アプリの要求が失敗すると予想される率を設定します。 障害発生率が、予想の包絡線を超えた場合、アラートが送信されます。
* [スマート検出 - パフォーマンスの異常](app-insights-proactive-performance-diagnostics.md)。 応答時間と障害発生率の中から異常なパターンを毎日検索します。 これらの問題を、場所、ブラウザー、クライアント OS、サーバー インスタンス、時刻などのプロパティに関する問題と関連付けます。
* [スマート検出 - Azure クラウド サービスの問題](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/)。 アプリを Azure Cloud Services でホストしている場合に、起動エラー、頻繁なリサイクル、ランタイム クラッシュがロール インスタンスで発生すると、アラートを受け取ります

(各通知のヘルプ リンクをクリックすると関連する記事が表示されます)。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>次のステップ
これらの診断ツールを使用すると、アプリからテレメトリを調査できます。

* [メトリックス エクスプローラー](app-insights-metrics-explorer.md)
* [Search エクスプローラー](app-insights-diagnostic-search.md)
* [Analytics - 強力なクエリ言語](app-insights-analytics-tour.md)

スマート検出は、すべて自動化されています。 ただし、アラートを追加で設定する機能が用意されています。

* [手動で構成するメトリックのアラート](app-insights-alerts.md)
* [可用性 Web テスト](app-insights-monitor-web-app-availability.md) 


