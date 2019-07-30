---
title: Azure Application Insights における依存関係の追跡 | Microsoft Docs
description: オンプレミスまたは Microsoft Azure Web アプリケーションからの依存関係呼び出しを Application Insights で監視します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: a233299b86100c7e43291ffb6448d576ef6072f0
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302562"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Azure Application Insights での依存関係の追跡 

*依存関係* は、アプリによって呼び出される外部コンポーネントです。 一般的には、HTTP を使用して呼び出されるサービス、またはデータベース、あるいはファイル システムです。 [Application Insights](../../azure-monitor/app/app-insights-overview.md) では、依存関係呼び出しの時間、その失敗と成功、追加情報 (依存関係の名前など) が計測されます。 特定の依存関係呼び出しを調査し、要求や例外に関連付けることができます。

## <a name="automatically-tracked-dependencies"></a>自動追跡された依存関係

.NET と .NET Core 向けの Application Insights SDK には `DependencyTrackingTelemetryModule` が付属しています。これは、依存関係を自動的に収集するテレメトリ モジュールです。 リンクされている公式ドキュメントに従って構成されているとき、[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) アプリケーションと [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) アプリケーションでは、この依存関係収集が自動的に有効になります。`DependencyTrackingTelemetryModule` は[この](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet パッケージとして出荷され、NuGet パッケージの `Microsoft.ApplicationInsights.Web` または `Microsoft.ApplicationInsights.AspNetCore` を使用するとき、自動的に取り込まれます。

 `DependencyTrackingTelemetryModule` では現在のところ、次の依存関係が自動的に追跡されます。

|依存関係 |詳細|
|---------------|-------|
|Http/Https | ローカルまたはリモートの http/https 呼び出し |
|WCF 呼び出し| Http ベースのバインディングを使用する場合にのみ自動追跡されます。|
|SQL | `SqlClient` で行われる呼び出し。 SQL クエリのキャプチャについては[こちら](#advanced-sql-tracking-to-get-full-sql-query)を参照してください。  |
|[Azure Storage (BLOB、テーブル、キュー)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage Client で行われる呼び出し。 |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | バージョン 1.1.0 以上。 |
|[ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| バージョン 3.0.0 以上。 |
|Azure Cosmos DB | HTTP/HTTPS が使用されている場合にのみ、自動的に追跡されます。 TCP モードは、Application Insights ではキャプチャされません。 |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>コンソール アプリで自動依存関係追跡を設定する

.NET/.NET Core コンソール アプリから依存関係を自動的に追跡するには、NuGet パッケージ `Microsoft.ApplicationInsights.DependencyCollector` をインストールし、次のように `DependencyTrackingTelemetryModule` を初期化します。

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>自動依存関係監視のしくみは?

依存関係は、次のいずれかの方法で自動的に収集されます。

* 一部のメソッドにバイト コード インストルメンテーションを使用する。 (StatusMonitor または Azure Web アプリ拡張機能の InstrumentationEngine)
* EventSource コールバック
* DiagnosticSource コールバック (最新の .NET/.NET Core SDK で)

## <a name="manually-tracking-dependencies"></a>依存関係を手動で追跡する

次の例では、依存関係が自動的に収集されず、手動追跡が必要になります。

* Azure Cosmos DB は、[HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) が使用されている場合にのみ、自動的に追跡されます。 TCP モードは、Application Insights ではキャプチャされません。
* Redis

SDK で自動追跡されない依存関係については、標準の自動収集モジュールで使用される [TrackDependency API](api-custom-events-metrics.md#trackdependency) を利用し、手動で追跡できます。

たとえば、自分で記述していないアセンブリを使ってコードを作成する場合、それに対するすべての呼び出しを測定し、何が応答時間に貢献するかを知ることができます。 このデータを Application Insights 内の依存関係グラフに表示するには、データを `TrackDependency`を使用して送信します。

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

あるいは、`TelemetryClient` から拡張メソッドの `StartOperation` と `StopOperation` が提供されます。[こちら](custom-operations-tracking.md#outgoing-dependencies-tracking)で確認できるように、それを利用して依存関係を手動追跡できます。

標準の依存関係追跡モジュールを無効にするには、ASP.NET アプリケーションの [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) にある DependencyTrackingTelemetryModule への参照を削除します。 ASP.NET Core アプリケーションの場合、[こちら](asp-net-core.md#configuring-or-removing-default-telemetrymodules)にある指示に従ってください。

## <a name="tracking-ajax-calls-from-web-pages"></a>Web ページから AJAX 呼び出しを追跡する

Web ページの場合、[こちら](javascript.md#ajax-performance)の説明にあるように、Application Insights JavaScript SDK によって AJAX 呼び出しが依存関係として自動収集されます。 このドキュメントでは、サーバー コンポーネントからの依存関係を主に取り上げています。

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>詳細な SQL 追跡で完全な SQL クエリを取得する

SQL 呼び出しの場合、サーバーとデータベースの名前が常に収集され、収集された `DependencyTelemetry` の名前として保存されます。 "データ" という名称の追加フィールドがあります。これに完全な SQL クエリ テキストを含めることができます。

ASP.NET Core アプリケーションの場合、完全な SQL クエリを取得するために必要な追加手順はありません。

ASP.NET アプリケーションの場合、インストルメンテーション エンジンを必要とするバイト コード インストルメンテーションの支援により完全な SQL クエリが収集されます。 以下の説明のように、プラットフォーム固有の追加手順が必要になります。

| プラットフォーム | 完全な SQL クエリを取得するために必要な手順 |
| --- | --- |
| Azure Web アプリ |Web アプリのコントロール パネルで [Application Insights ブレードを開き](../../azure-monitor/app/azure-web-apps.md)、SQL コマンドを .NET |
| IIS Server (Azure VM やオンプレミスなど) の下で有効にします。 | [アプリケーションが実行されているサーバーに Status Monitor をインストールし](../../azure-monitor/app/monitor-performance-live-website-now.md)、IIS を再起動します。
| Azure Cloud Services | [StatusMonitor をインストールするスタートアップ タスク](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional)を追加します <br> ビルド時に [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) または [ASP.NET Core アプリケーション](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)用の NuGet パッケージをインストールすることで、アプリを ApplicationInsights SDK にオンボードする必要があります。 |
| IIS Express | サポートされていません

上記の例では、インストルメンテーション エンジンが正しくインストールされていることを検証する適切な方法は、収集された `DependencyTelemetry` の SDK バージョンが "rddp" であることを確認することです。 "rdddsd" または "rddf" は、DiagnosticSource または EventSource コールバックを介して依存関係が収集されること、そのため、完全な SQL クエリはキャプチャされないことを示します。

## <a name="where-to-find-dependency-data"></a>依存関係データが見つかる場所

* [アプリケーション マップ](app-map.md)では、アプリと隣接コンポーネント間の依存関係が視覚化されます。
* [トランザクションの診断](transaction-diagnostics.md)には、統合され、関連付けられたサーバー データが表示されます。
* [[ブラウザー] タブ](javascript.md#ajax-performance)には、ユーザーのブラウザーからの AJAX 呼び出しが表示されます。
* 低速または失敗した要求からクリックしていき、依存関係呼び出しを確認します。
* 依存関係データのクエリを実行するには、[Analytics](#logs-analytics) を使用できます。

## <a name="diagnosis"></a> 低速なリクエストの診断

各要求イベントは、依存関係呼び出し、例外、およびアプリでの要求の処理中に追跡されるその他のイベントに関連しています。 そのため、いくつかのリクエストが正しく実行されない場合は、それが依存関係からの応答が遅いためかどうかを調べることができます。

### <a name="tracing-from-requests-to-dependencies"></a>リクエストから依存関係までのトレース

**[パフォーマンス]** タブを開き、上部の操作の横にある **[依存関係]** タブに移動します。

全体の下の **[依存関係名]** をクリックします。 依存関係を選択すると、その依存関係の期間の分布グラフが右側に表示されます。

![[パフォーマンス] タブで、上部の [依存関係] タブ、グラフ内の [依存関係名] の順にクリックします。](./media/asp-net-dependencies/2-perf-dependencies.png)

右下の青色の **[サンプル]** ボタン、サンプルの順にクリックし、エンドツーエンドのトランザクションの詳細を表示します。

![サンプルをクリックして、エンドツーエンドのトランザクション詳細を表示します。](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>ライブ サイトのプロファイリング

時間がどこに使われているのか見当がつかないでしょうか。 [Application Insights プロファイラー](../../azure-monitor/app/profiler.md)は、ライブ サイトへの HTTP 呼び出しをトレースし、コード内の関数のうち最も時間がかかったものを示します。

## <a name="failed-requests"></a>失敗した要求

失敗した要求も、依存関係への失敗した呼び出しに関連している可能性があります。

左側の **[失敗]** タブに移動し、上部の **[依存関係]** タブをクリックします。

![失敗した要求のグラフをクリックします。](./media/asp-net-dependencies/4-fail.png)

ここで、失敗した依存関係の数を確認できます。 失敗した回に関する詳細を確認するには、一番下の表の依存関係名をクリックします。 右下の青色の **[依存関係]** ボタンをクリックすると、エンドツーエンドのトランザクションの詳細を取得できます。

## <a name="logs-analytics"></a>ログ (Analytics)

依存関係は [Kusto クエリ言語](/azure/kusto/query/)で追跡できます。 次に例をいくつか示します。

* これは、失敗した依存関係呼び出しを見つけます。

``` Kusto

    dependencies | where success != "True" | take 10
```

* これは、AJAX 呼び出しを見つけます。

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* これは、要求に関連する依存関係呼び出しを見つけます。

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* これは、ページ ビューに関連する AJAX 呼び出しを見つけます。

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*依存関係の自動コレクターが呼び出しの失敗を依存関係に報告する方法は?*

* 依存関係の呼び出しに失敗すると、"成功" フィールドが False に設定されます。 `DependencyTrackingTelemetryModule` から `ExceptionTelemetry` が報告されることはありません。 依存関係の完全なデータ モデルは[こちら](data-model-dependency-telemetry.md)で説明されています。

## <a name="open-source-sdk"></a>オープンソース SDK
あらゆる Application Insights SDK と同様に、依存関係収集モジュールもオープンソースです。 コードの閲覧、投稿、問題報告は[公式の GitHub リポジトリ](https://github.com/Microsoft/ApplicationInsights-dotnet-server)で行います。

## <a name="next-steps"></a>次の手順

* [例外](../../azure-monitor/app/asp-net-exceptions.md)
* [ユーザーとページのデータ](../../azure-monitor/app/javascript.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
