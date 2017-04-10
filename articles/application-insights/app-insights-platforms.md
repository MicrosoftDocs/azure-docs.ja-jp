---
title: "Application Insights: 言語、プラットフォーム、統合 | Microsoft Docs"
description: "Application Insights で利用できる言語、プラットフォーム、統合"
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
manager: douge
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 46e6ab8a9c5d4e6a00d61ee4e5bbfd42307bfc69
ms.openlocfilehash: 5ec35fb0171a4a62b915579b5a079060132998dd


---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>開発者分析: 言語、プラットフォーム、統合
以下は、これまでに把握している [Application Insights](app-insights-overview.md) の実装です (サード パーティによるものも含む)。

## <a name="languages"></a>言語
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [JavaScript Web ページ](app-insights-web-track-usage.md)
* [Objective-C](https://github.com/Microsoft/ApplicationInsights-iOS)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [その他](#projects)

## <a name="platforms-and-frameworks"></a>プラットフォームとフレームワーク
* [Angular](https://www.npmjs.com/package/angular-applicationinsights)
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET - 既にライブとなっているアプリ用](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET 5](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (HockeyApp)
* [Azure Web Apps](app-insights-azure-web-apps.md)
* [Azure Cloud Services](app-insights-cloudservices.md)&#151; Web ロールと worker ロールの両方を含む
* [Microsoft Dynamics からの CRM オンライン](app-insights-sample-mscrm.md)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE - 既にライブとなっているアプリ用](app-insights-java-live.md)
* [Mac OS X アプリ](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [ユニバーサル Windows アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows Phone 8 および 8.1 アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Windows Presentation Foundation アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Windows デスクトップのアプリケーションとサービス、および worker ロール](app-insights-windows-desktop.md)
* [その他](#projects)

## <a name="logging-frameworks"></a>ログ記録フレームワーク
* [Log4Net、NLog、または System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java、Log4J、または Logback](app-insights-java-trace-logs.md)
* [セマンティック ログ (SLAB)](https://github.com/fidmor89/SLAB_AppInsights)&#151; [セマンティック ログ アプリケーション ブロック](https://msdn.microsoft.com/library/dn440729.aspx)と統合
* [クラウド ベースのロード テスト](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash プラグイン](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)

## <a name="content-management-systems"></a>コンテンツ管理システム
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://orchardazureappinsights.codeplex.com)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>エクスポートとデータの分析
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="a-nameprojectsa-build-your-own-sdk"></a><a name="projects"></a> 独自の SDK をビルドする
使用する言語やプラットフォーム用の SDK がない場合は、自分で作成するという方法があります。 [GitHub の Application Insights SDK プロジェクト](https://github.com/Microsoft/AppInsights-Home)に示されている既存の SDK のコードをご覧ください。



<!--HONumber=Jan17_HO4-->


