---
title: 'Application Insights: 言語、プラットフォーム、統合 | Microsoft Docs'
description: Application Insights で利用できる言語、プラットフォーム、統合
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 5a7fbeafe74926c7d89ac76dc2964756b3582d61
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671150"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>開発者分析: 言語、プラットフォーム、統合
以下は、これまでに把握している [Application Insights](../../azure-monitor/app/app-insights-overview.md) の実装です (サード パーティによるものも含む)。

## <a name="languages---officially-supported-by-application-insights-team"></a>Application Insights チームで正式にサポートされている言語
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript Web ページ](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>コミュニティでサポートされている言語
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [その他](#projects)

## <a name="platforms-and-frameworks"></a>プラットフォームとフレームワーク
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET - 既にライブとなっているアプリ用](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)&#151; Web ロールと worker ロールの両方を含む
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE スタック](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [ユニバーサル Windows アプリ](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows デスクトップのアプリケーションとサービス、および worker ロール](../../azure-monitor/app/windows-desktop.md)
* [その他](#projects)

## <a name="logging-frameworks"></a>ログ記録フレームワーク
* [Log4Net、NLog、または System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java、Log4J、または Logback](../../azure-monitor/app/java-trace-logs.md)
* [セマンティック ログ (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - [セマンティック ログ アプリケーション ブロック](https://msdn.microsoft.com/library/dn440729.aspx)と統合
* [クラウド ベースのロード テスト](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash プラグイン](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>コンテンツ管理システム
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>エクスポートとデータの分析
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> 独自の SDK をビルドする
使用する言語やプラットフォーム用の SDK がない場合は、自分で作成するという方法があります。 [GitHub の Application Insights SDK プロジェクト](https://github.com/Microsoft/AppInsights-Home)に示されている既存の SDK のコードをご覧ください。
