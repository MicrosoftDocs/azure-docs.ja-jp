---
title: Azure Application Insights - 依存関係の自動収集 | Microsoft Docs
description: Application Insights では、依存関係が自動的に収集されて視覚化されます。
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 3ad2f4788a765366066023724772f5432d0d56eb
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109427"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet パッケージ

Application Insights 用の安定版リリースの NuGet パッケージの最新リストを次に示します。

## <a name="common-packages-for-aspnet"></a>ASP.NET 用の一般的なパッケージ

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | すべての Application Insights Telemetry タイプの送信のためのコア機能を提供します。他のすべての Application Insights パッケージの依存パッケージです。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | メソッド呼び出しのインターセプションを有効にします。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | .NET アプリケーションの Application Insights 依存関係コレクター。 これは、Application Insights プラットフォーム固有のパッケージの依存パッケージであり、依存関係のテレメトリの自動収集を提供します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights パフォーマンス カウンター コレクターを使用すると、パフォーマンス カウンターで収集されたデータを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | .NET Web アプリケーション用の Application Insights | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet パッケージは、.NET アプリケーションのために Application Insights テレメトリの自動収集を提供します。 このパッケージは、Application Insights プラットフォーム固有のパッケージの依存パッケージとして使用できます。または、プラットフォーム固有のパッケージの対象にならない .NET アプリケーション (.NET worker ロールなど) のスタンドアロン パッケージとして使用できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | テレメトリをオフライン シナリオに保持する Application Insights Windows Server SDK にテレメトリ チャンネルを提供します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>ASP.NET Core 用の一般的なパッケージ

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights for ASP.NET Core Web アプリケーション。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | このパッケージは、すべての Application Insights Telemetry タイプの送信のためのコア機能を提供します。他のすべての Application Insights パッケージの依存パッケージです。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | .NET アプリケーションの Application Insights 依存関係コレクター。 これは、Application Insights プラットフォーム固有のパッケージの依存パッケージであり、依存関係のテレメトリの自動収集を提供します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights パフォーマンス カウンター コレクターを使用すると、パフォーマンス カウンターで収集されたデータを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet パッケージは、.NET アプリケーションのために Application Insights テレメトリの自動収集を提供します。 このパッケージは、Application Insights プラットフォーム固有のパッケージの依存パッケージとして使用できます。または、プラットフォーム固有のパッケージの対象にならない .NET アプリケーション (.NET worker ロールなど) のスタンドアロン パッケージとして使用できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | テレメトリをオフライン シナリオに保持する Application Insights Windows Server SDK にテレメトリ チャンネルを提供します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>リスナー/コレクター/アペンダー

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  DiagnosticSource から Application Insights にイベントを転送できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener を使用すると、EventSource イベントのデータを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector を使用すると、Windows イベント トレーシング (ETW) のデータを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | カスタム TraceListener を使用すると、トレース ログ メッセージを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | カスタム アペンダーを使用すると、Log4Net ログ メッセージを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  カスタム ターゲットを使用すると、NLog ログ メッセージを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | アプリケーションの例外を監視し、オフライン分析用にスナップショットを自動的に収集します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | このパッケージは、アプリケーションが実行しているサービス ファブリック コンテキストでテレメトリを自動的に修飾します。 ネイティブの Service Fabric アプリケーションでは、この NuGet を使用しないでください。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Service Fabric アプリケーション用の Application Insights モジュール。 この NuGet はネイティブの Service Fabric アプリケーションのみで使用します。 コンテナーで実行しているアプリケーションの場合は、Microsoft.ApplicationInsights.ServiceFabric パッケージを使用します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Status Monitor

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  x64 アプリケーションの実行時データ収集を有効化 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  x86 アプリケーションの実行時データ収集を有効化。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

これらのパッケージは、[Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md) の実行時監視のコア機能を構成します。 これらのパッケージを直接ダウンロードする必要はありません。Status Monitor インストーラーを使用してください。 これらのパッケージの内部での動作について詳しく理解するには、開発者の 1 人によるこの[ブログ記事](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)をまずご覧ください。

## <a name="additional-packages"></a>その他のパッケージ

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | この拡張機能を使用すると、Azure App Service での Application Insights の監視が有効になります。 SDK バージョン 2.6.1。 手順: 'APPINSIGHTS_INSTRUMENTATIONKEY' アプリケーション設定を ikey に追加し、有効にするために webapp を再起動します。| [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | このパッケージには、コード不要の Application Insights インジェクションのために必要なファイルが含まれています。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>次の手順

- [ASP.NET Core](../../azure-monitor/app/asp-net-core.md) を監視します。
- ASP.NET Core [Azure Linux Web アプリ](../../azure-monitor/app/profiler-aspnetcore-linux.md)をプロファイルします。
- ASP.NET [スナップショット](../../azure-monitor/app/snapshot-debugger.md)をデバッグします。