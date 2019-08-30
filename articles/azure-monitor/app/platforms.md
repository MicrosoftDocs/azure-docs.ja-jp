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
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: ab573d1d9ca9d9db39135ea76e555bb0720d7b68
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68878794"
---
# <a name="supported-languages"></a>サポートされている言語

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>サポートされているプラットフォームとフレームワーク

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>既にデプロイされているアプリケーションのインストルメンテーション (コード不要、エージェントベース)
* [Azure VM および Azure 仮想マシン スケール セット](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 既にライブとなっているアプリ用](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)、Web ロールと worker ロールの両方を含む
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>コードを使用したインストルメンテーション (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [ユニバーサル Windows アプリ](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows デスクトップのアプリケーションとサービス、および worker ロール](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>ログ記録フレームワーク
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net、NLog、または System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java、Log4J、または Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash プラグイン](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>エクスポートとデータ分析
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>サポートされていない SDK
Microsoft は、コミュニティでサポートされている他の SDK がいくつか存在することを認識しています。 ただし、Azure Monitor では、このページに記載されているサポート対象の SDK を使用した場合のサポートのみが提供されます。 Microsoft は、他の言語のサポートを拡大する機会を常に評価していますので、[GitHub のお知らせ](https://github.com/microsoft/ApplicationInsights-Announcements/issues)ページに従って、最新の SDK ニュースをぜひ受信してください。 
