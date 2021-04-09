---
title: 'Application Insights: 言語、プラットフォーム、統合 | Microsoft Docs'
description: Application Insights で利用できる言語、プラットフォーム、統合
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 399e57377a779622aa3073dfd3313cee1db345f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583860"
---
# <a name="supported-languages"></a>サポートされている言語

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>サポートされているプラットフォームとフレームワーク

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>既にデプロイされているアプリケーションのインストルメンテーション (コード不要、エージェントベース)
* [Azure VM および Azure 仮想マシン スケール セット](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [ASP.NET - 既にライブとなっているアプリ用](./monitor-performance-live-website-now.md)
* [Azure Cloud Services](./cloudservices.md)、Web ロールと worker ロールの両方を含む
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>コードを使用したインストルメンテーション (SDK)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [ユニバーサル Windows アプリ](../app/mobile-center-quickstart.md) (App Center)
* [Windows デスクトップのアプリケーションとサービス、および worker ロール](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>ログ記録フレームワーク
* [ILogger](./ilogger.md)
* [Log4Net、NLog、または System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java、Log4J、または Logback](./java-trace-logs.md)
* [LogStash プラグイン](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>エクスポートとデータ分析
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>サポートされていない SDK
Microsoft は、コミュニティでサポートされている他の SDK がいくつか存在することを認識しています。 ただし、Azure Monitor では、このページに記載されているサポート対象の SDK を使用した場合のサポートのみが提供されます。 Microsoft は、他の言語のサポートを拡大する機会を常に評価していますので、[GitHub のお知らせ](https://github.com/microsoft/ApplicationInsights-Announcements/issues)ページに従って、最新の SDK ニュースをぜひ受信してください。 

