---
title: "モバイル アプリのパフォーマンス監視 - Azure Application Insights | Microsoft Docs"
description: "モバイル アプリ開発者向けアプリケーションのパフォーマンスと使用状況の監視。 HockeyApp と Application Insights による DevOps。"
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: c4077aa88a401a1c75c0d3fbcd58186f7cb137c2


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>HockeyApp と Application Insights によるモバイル分析
[HockeyApp](https://hockeyapp.net/) により、ベータテストおよびデプロイ済みのモバイルおよびデスクトップ アプリのパフォーマンスと使用状況を監視できます。 [Azure Application Insights](app-insights-overview.md) により、サポートする Web サービスとバックエンド アプリを監視できます。 Analytics でクライアントとサーバーの両方のアプリからのデータを分析し、Azure ダッシュボードでグラフを並べて表示できます。

Microsoft 開発者分析では、アプリケーション パフォーマンス監視用に次の&2; つのソリューションを提供しています。

* モバイルおよびデスクトップ アプリ用の **HockeyApp**。
  * 選択したユーザーへのテスト バージョンの配布。
  * クラッシュ分析。
  * 使用状況分析用のカスタム テレメトリ。
* Web サイト、Web サービス、およびバックエンド アプリケーション用の **Application Insights**。
  * パフォーマンスと使用状況に関するメトリックとアラート。
  * 例外レポートと診断トレース。
  * フィルター処理および関連するテレメトリ リンクを備えた診断検索。

いずれのソリューションも次の機能を備えています。

* 診断および分析用の強力な**[分析クエリ言語](app-insights-analytics.md)**。
* 独自ストレージへの**[データのエクスポート](app-insights-export-telemetry.md)**。
* 分析用のグラフとテーブルからなる**統合ダッシュ ボード**表示。

## <a name="monitor-your-app-components"></a>アプリ コンポーネントを監視する
SDK をコードにインストールしてアプリの追跡を開始するには、下のページの指示に従ってください。

### <a name="web-apps---application-insights"></a>Web アプリ - Application Insights
* [ASP.NET Web アプリ](app-insights-asp-net.md) 
* [Java Web アプリ](app-insights-java-get-started.md)
* [Node.js Web アプリ](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Azure Cloud Services](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>モバイル アプリ - HockeyApp
* [iOS アプリ](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X アプリ](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android アプリ](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [ユニバーサル Windows アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 および 8.1 アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Windows デスクトップ アプリ用には HockeyApp をお勧めしています。 ただし、[Windows アプリから Application Insights テレメトリを送信する](app-insights-windows-desktop.md)こともできます。 これは、Application Insights を実験する目的で行うこともできます。

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>HockeyApp テレメトリの分析とエクスポート
[ブリッジを設定](app-insights-hockeyapp-bridge-app.md)すると、Application Insights の Analytics 機能と連続エクスポート機能を使用して HockeyApp のカスタムおよびログ テレメトリを調査することができます。




<!--HONumber=Jan17_HO4-->


