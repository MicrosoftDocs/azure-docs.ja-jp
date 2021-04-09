---
title: Application Insights のイベント カウンター | Microsoft Docs
description: Application Insights でシステムとカスタムの .NET/.NET Core EventCounter を監視します。
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: d1ae0937c25a68798acd87fe8b2a0a54aa765b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579525"
---
# <a name="eventcounters-introduction"></a>EventCounter の概要

[`EventCounter`](/dotnet/core/diagnostics/event-counters) は、カウンターまたは統計情報を発行および使用するための .NET/.NET Core メカニズムです。 EventCounter は、Windows、Linux、および macOS のすべての OS プラットフォームでサポートされています。 これは、Windows システムでのみサポートされている [PerformanceCounter](/dotnet/api/system.diagnostics.performancecounter) のクロスプラットフォームの同等のものと考えることができます。

ユーザーは、ニーズに合わせてカスタムの `EventCounters` を発行できますが、.NET Core 3.0 以降では、ランタイムによってこれらのカウンターのセットが既定で発行されます。 このドキュメントでは、Azure Application Insights での (システム定義またはユーザー定義の) `EventCounters` の収集および表示に必要な手順について説明します。

## <a name="using-application-insights-to-collect-eventcounters"></a>Application Insights を使用した EventCounter の収集

Application Insights では、その `EventCounterCollectionModule` (新しくリリースされた NuGet パッケージである [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector) の一部) を使用した `EventCounters` の収集をサポートしています。 `EventCounterCollectionModule` は、[AspNetCore](asp-net-core.md) または [WorkerService](worker-service.md) のいずれかを使用すると、自動的に有効になります。 `EventCounterCollectionModule` は、60 秒の収集頻度 (構成不可) でカウンターを収集します。 EventCounter を収集するために特別なアクセス許可は必要ありません。

## <a name="default-counters-collected"></a>収集される既定のカウンター

[AspNetCore SDK](asp-net-core.md) または [WorkerService SDK](worker-service.md) の 2.15.0 バージョン以降では、カウンターの収集は既定で行われません。 モジュール自体が有効になっているので、ユーザーは、目的のカウンターを単に追加して収集することができます。

.NET Runtime で発行されている既知のカウンターの一覧を取得するには、[利用できるカウンター](/dotnet/core/diagnostics/event-counters#available-counters)のドキュメントを参照してください。

## <a name="customizing-counters-to-be-collected"></a>収集されるカウンターのカスタマイズ

次の例は、カウンターを追加または削除する方法を示しています。 このカスタマイズは、`AddApplicationInsightsTelemetry()` または `AddApplicationInsightsWorkerService()` のいずれかを使用して Application Insights テレメトリの収集を有効にした後に、アプリケーションの `ConfigureServices` メソッドで行います。 ASP.NET Core アプリケーションのコード例を次に示します。 その他の種類のアプリケーションについては、[こちら](worker-service.md#configuring-or-removing-default-telemetrymodules)のドキュメントを参照してください。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>EventCounter コレクション モジュールの無効化

`EventCounterCollectionModule` を無効にするには、`ApplicationInsightsServiceOptions` を使用します。 ASP.NET Core SDK を使用する場合の例を次に示します。

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

WorkerService SDK にも同様の方法を使用できますが、次の例に示すように、名前空間を変更する必要があります。

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>メトリック エクスプローラーのイベント カウンター

[メトリック エクスプローラー](../essentials/metrics-charts.md)で EventCounter メトリックを表示するには、Application Insights リソースを選択し、メトリック名前空間として [ログベースのメトリック] を選択します。 これにより、EventCounter メトリックがカスタム カテゴリの下に表示されます。

> [!div class="mx-imgBorder"]
> ![Application Insights メトリックス エクスプローラーで報告されるイベント カウンター](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analytics のイベント カウンター

また、[Analytics](../logs/log-query-overview.md) 内で **customMetrics** テーブルのイベント カウンター レポートを検索して表示することもできます。

たとえば、次のクエリを実行して、どのカウンターが収集され、クエリに使用できるかを確認します。

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights Analytics で報告されるイベント カウンター](./media/event-counters/analytics-event-counters.png)

最近の期間おける特定のカウンター (例: `ThreadPool Completed Work Item Count`) のグラフを取得するには、次のクエリを実行します。

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Application Insights 内の 1 つのカウンターのグラフ](./media/event-counters/analytics-completeditems-counters.png)

他のテレメトリと同様に、**customMetrics** にも、アプリを実行しているホスト サーバー インスタンスの ID を示す列 `cloud_RoleInstance` があります。 上記のクエリは、インスタンスごとのカウンター値を示しており、さまざまなサーバー インスタンスのパフォーマンスを比較するために使用できます。

## <a name="alerts"></a>警告
他のメトリックと同様に、指定した制限をイベント カウンターが超えた場合に警告する[アラートを設定](../alerts/alerts-log.md)できます。 [アラート] ウィンドウを開き、[アラートの追加] をクリックします。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Live Metrics で EventCounter を表示できますか。

現時点では、Live Metrics で EventCounter は表示されません。 テレメトリを確認するには、メトリック エクスプローラーまたは Analytics を使用してください。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Azure Web アプリ ポータルから Application Insights を有効にしました。 それでも、EventCounter を表示できません。

 この機能は、ASP.NET Core 向けの [Application Insights 拡張機能](./azure-web-apps.md) ではまだサポートされていません。 この機能がサポートされるようになりましたら、このドキュメントは更新されます。

## <a name="next-steps"></a><a name="next"></a>次のステップ

* [依存関係の追跡](./asp-net-dependencies.md)

