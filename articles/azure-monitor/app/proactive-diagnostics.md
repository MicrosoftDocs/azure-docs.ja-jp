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
ms.openlocfilehash: edbd7000001ae6927078e2f1bb9e348cc78f9efa
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962113"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights のスマート検出
 スマート検出により、Web アプリケーションの潜在的なパフォーマンスの問題について警告を自動的に受け取ることができます。 スマート検出では、アプリから [Application Insights](../../azure-monitor/app/app-insights-overview.md) に送信されるテレメトリがプロアクティブに分析されます。 障害発生率が急激に上昇したり、クライアントまたはサーバーのパフォーマンスに異常なパターンが発生したりした場合に、アラートが表示されます。 この機能には構成は不要です。 アプリケーションから適切なテレメトリが送信されていれば動作します。

スマート検出アラートには、受信した電子メールと、[スマート検出] ブレードの両方からアクセスできます。

## <a name="review-your-smart-detections"></a>スマート検出数を確認する
検出結果は 2 種類の方法で確認できます。

* **電子メール** を Application Insights から受信します。 典型的な例を次に示します。
  
    ![電子メール アラート](./media/proactive-diagnostics/03.png)
  
    ポータルで詳細を表示するには、大きなボタンをクリックします。
* **スマート検出タイル**に、最近のアラートの数が表示されます。 最近のアラートの一覧を確認するには、タイルをクリックします。

![最近の検出結果の表示](./media/proactive-diagnostics/04.png)

詳細を表示するには、対象のアラートを選択します。

## <a name="what-problems-are-detected"></a>検出される問題
検出には 3 種類あります。

* [スマート検出 - 失敗の異常](../../azure-monitor/app/proactive-failure-diagnostics.md)。 機械学習を使用して、負荷などの要因と関連付けて、アプリの要求が失敗すると予想される率を設定します。 障害発生率が、予想の包絡線を超えた場合、アラートが送信されます。
* [スマート検出 - パフォーマンスの異常](../../azure-monitor/app/proactive-performance-diagnostics.md)。 操作の応答時間または依存関係の継続期間が過去の基準と比較して低下している場合や、応答時間またはページの読み込み時間に異常なパターンが特定された場合は、通知を受信します。   
* [スマート検出 - Azure クラウド サービスの問題](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/)。 アプリを Azure Cloud Services でホストしている場合に、起動エラー、頻繁なリサイクル、ランタイム クラッシュがロール インスタンスで発生すると、アラートを受け取ります

(各通知のヘルプ リンクをクリックすると関連する記事が表示されます)。

## <a name="smart-detection-email-notifications"></a>スマート検出の電子メール通知

プレビューとマークされているルールを除く、すべてのスマート検出ルールは、検出の発見時に既定で電子メール通知を送信するように構成されます。

特定のスマート検出ルールの電子メール通知を構成するには、スマート検出の **[設定]** ブレードを開き、ルールを選択すると、**[ルールの編集]** ブレードが開きます。

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

