---
title: Azure Monitor Application Insights NuGet パッケージ
description: ASP.NET、ASP.NET Core、Python の Azure Monitor Application Insights NuGet パッケージ リスト
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 27a3d89b4a64de159535d346641c21616833e21b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87309955"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet パッケージ

Application Insights 用の安定版リリースの NuGet パッケージの最新リストを次に示します。

## <a name="common-packages-for-aspnet"></a>ASP.NET 用の一般的なパッケージ

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | すべての Application Insights Telemetry タイプの送信のためのコア機能を提供します。他のすべての Application Insights パッケージの依存パッケージです。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | メソッド呼び出しのインターセプションを有効にします。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | .NET アプリケーションの Application Insights 依存関係コレクター。 これは、Application Insights プラットフォーム固有のパッケージの依存パッケージであり、依存関係のテレメトリの自動収集を提供します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Application Insights パフォーマンス カウンター コレクターを使用すると、パフォーマンス カウンターで収集されたデータを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | .NET Web アプリケーション用の Application Insights | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Windows Server NuGet パッケージは、.NET アプリケーションのために Application Insights テレメトリの自動収集を提供します。 このパッケージは、Application Insights プラットフォーム固有のパッケージの依存パッケージとして使用できます。または、プラットフォーム固有のパッケージの対象にならない .NET アプリケーション (.NET worker ロールなど) のスタンドアロン パッケージとして使用できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | テレメトリをオフライン シナリオに保持する Application Insights Windows Server SDK にテレメトリ チャンネルを提供します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>ASP.NET Core 用の一般的なパッケージ

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights for ASP.NET Core Web アプリケーション。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | このパッケージは、すべての Application Insights Telemetry タイプの送信のためのコア機能を提供します。他のすべての Application Insights パッケージの依存パッケージです。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | .NET アプリケーションの Application Insights 依存関係コレクター。 これは、Application Insights プラットフォーム固有のパッケージの依存パッケージであり、依存関係のテレメトリの自動収集を提供します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Application Insights パフォーマンス カウンター コレクターを使用すると、パフォーマンス カウンターで収集されたデータを Application Insights に送信できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Windows Server NuGet パッケージは、.NET アプリケーションのために Application Insights テレメトリの自動収集を提供します。 このパッケージは、Application Insights プラットフォーム固有のパッケージの依存パッケージとして使用できます。または、プラットフォーム固有のパッケージの対象にならない .NET アプリケーション (.NET worker ロールなど) のスタンドアロン パッケージとして使用できます。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | テレメトリをオフライン シナリオに保持する Application Insights Windows Server SDK にテレメトリ チャンネルを提供します。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>OpenCensus を使用する Python の一般的なパッケージ
| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | OpenCensus での Azure Monitor 下の Python アプリケーション用 Application Insights。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | このパッケージは、Python [django](https://pypi.org/project/django/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-flask | 0.7.3 | このパッケージは、Python [flask](https://pypi.org/project/flask/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | このパッケージは、Python [http.client](https://docs.python.org/3/library/http.client.html) ライブラリ (Python3 用) および [httplib](https://docs.python.org/2/library/httplib.html) (Python2 用) との統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-logging | 0.1.0 | このパッケージは、トレース データでログ レコードをエンリッチします。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | このパッケージは、Python [mysql-connector](https://pypi.org/project/mysql-connector/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | このパッケージは、Python [psycopg2](https://pypi.org/project/psycopg2/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | このパッケージは、Python [pymongo](https://pypi.org/project/pymongo/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | このパッケージは、Python [PyMySQL](https://pypi.org/project/PyMySQL/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-pyramid | 0.7.1 | このパッケージは、Python [pyramid](https://pypi.org/project/pyramid/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-requests | 0.7.2 | このパッケージは、Python [requests](https://pypi.org/project/requests/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | このパッケージは、Python [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) ライブラリとの統合を実現します。 | [パッケージのダウンロード](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

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

これらのパッケージは、[Status Monitor](./monitor-performance-live-website-now.md) の実行時監視のコア機能を構成します。 これらのパッケージを直接ダウンロードする必要はありません。Status Monitor インストーラーを使用してください。 これらのパッケージの内部での動作について詳しく理解するには、開発者の 1 人によるこの[ブログ記事](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)をまずご覧ください。

## <a name="additional-packages"></a>その他のパッケージ

| パッケージ名 | 安定バージョン | 説明 | ダウンロード |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | この拡張機能を使用すると、Azure App Service での Application Insights の監視が有効になります。 SDK バージョン 2.6.1。 手順: 'APPINSIGHTS_INSTRUMENTATIONKEY' アプリケーション設定を ikey に追加し、有効にするために webapp を再起動します。| [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | このパッケージには、コード不要の Application Insights インジェクションのために必要なファイルが含まれています。 | [パッケージのダウンロード](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>次のステップ

- [ASP.NET Core](./asp-net-core.md) を監視します。
- ASP.NET Core [Azure Linux Web アプリ](./profiler-aspnetcore-linux.md)をプロファイルします。
- ASP.NET [スナップショット](./snapshot-debugger.md)をデバッグします。

