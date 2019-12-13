---
title: Application Insights のイベント カウンター | Microsoft Docs
description: Application Insights でシステムとカスタムの .NET/.NET Core EventCounter を監視します。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2019
ms.openlocfilehash: 1719c917ee2a4c0a11e4a79953a8b67e946d5931
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889126"
---
# <a name="eventcounters-introduction"></a>EventCounter の概要

`EventCounter` は、カウンターまたは統計情報を発行および使用するための .NET/.NET Core メカニズムです。 `EventCounters` の概要と、それらを発行および使用する方法の例は、[こちらの](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)ドキュメントで説明しています。 EventCounter は、Windows、Linux、および macOS のすべての OS プラットフォームでサポートされています。 これは、Windows システムでのみサポートされている [PerformanceCounter](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) のクロスプラットフォームの同等のものと考えることができます。

ユーザーは必要に応じてカスタムの `EventCounters` を発行できますが、.NET Core 3.0 ランタイムによってこれらのカウンターのセットが既定で発行されます。 このドキュメントでは、Azure Application Insights での (システム定義またはユーザー定義の) `EventCounters` の収集および表示に必要な手順について説明します。

## <a name="using-application-insights-to-collect-eventcounters"></a>Application Insights を使用した EventCounter の収集

Application Insights では、その `EventCounterCollectionModule` (新しくリリースされた NuGet パッケージ [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector) の一部) を使用した `EventCounters` の収集をサポートしています。 `EventCounterCollectionModule` は、[AspNetCore](asp-net-core.md) または [WorkerService](worker-service.md) のいずれかを使用すると、自動的に有効になります。 `EventCounterCollectionModule` は、60 秒の収集頻度 (構成不可) でカウンターを収集します。 EventCounter を収集するために特別なアクセス許可は必要ありません。

## <a name="default-counters-collected"></a>収集される既定のカウンター

.NET Core 3.0 で実行されているアプリでは、次のカウンターが SDK によって自動的に収集されます。 カウンターの名前の形式は、"カテゴリ|カウンター" になります。

|カテゴリ | カウンター|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Microsoft.AspNetCore.Hosting カテゴリのカウンターは、Asp.Net Core アプリケーションにのみ追加されます。

## <a name="customizing-counters-to-be-collected"></a>収集されるカウンターのカスタマイズ

次の例は、カウンターを追加または削除する方法を示しています。 このカスタマイズは、`AddApplicationInsightsTelemetry()` または `AddApplicationInsightsWorkerService()` のいずれかを使用して Application Insights テレメトリの収集を有効にした後に、アプリケーションの `ConfigureServices` メソッドで行います。 ASP.NET Core アプリケーションのコード例を次に示します。 その他の種類のアプリケーションについては、[こちら](worker-service.md#configuring-or-removing-default-telemetrymodules)のドキュメントを参照してください。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>メトリック エクスプローラーのイベント カウンター

[メトリック エクスプローラー](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)で EventCounter メトリックを表示するには、Application Insights リソースを選択し、メトリック名前空間として [ログベースのメトリック] を選択します。 すると、EventCounter メトリックが PerformanceCounter カテゴリの下に表示されます。

> [!div class="mx-imgBorder"]
> ![Application Insights で報告されるイベント カウンター](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analytics のイベント カウンター

また、[Analytics](../../azure-monitor/app/analytics.md) 内で **performanceCounters** テーブルのイベント カウンター レポートを検索して表示することもできます。

たとえば、次のクエリを実行して、どのカウンターが収集され、クエリに使用できるかを確認します。

```Kusto
performanceCounters | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights で報告されるイベント カウンター](./media/event-counters/analytics-event-counters.png)

最近の期間おける特定のカウンター (例: `ThreadPool Completed Work Item Count`) のグラフを取得するには、次のクエリを実行します。

```Kusto
performanceCounters 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Application Insights 内の 1 つのカウンターのグラフ](./media/event-counters/analytics-completeditems-counters.png)

他のテレメトリと同様に、**performanceCounters** にも、アプリを実行しているホスト サーバー インスタンスの ID を示す列 `cloud_RoleInstance` があります。 上記のクエリは、インスタンスごとのカウンター値を示しており、さまざまなサーバー インスタンスのパフォーマンスを比較するために使用できます。

## <a name="alerts"></a>アラート
他のメトリックと同様に、指定した制限をイベント カウンターが超えた場合に警告する[アラートを設定](../../azure-monitor/app/alerts.md)できます。 [アラート] ウィンドウを開き、[アラートの追加] をクリックします。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Live Metrics で EventCounter を表示できますか。

現時点では、Live Metrics で EventCounter は表示されません。 テレメトリを確認するには、メトリック エクスプローラーまたは Analytics を使用してください。

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>どのプラットフォームで .NET Core 3.0 カウンターの既定の一覧を表示できますか。

EventCounter には特別なアクセス許可は必要なく、.NET Core 3.0 がサポートされているすべてのプラットフォームでサポートされています。 これには次のものが含まれます

* **[オペレーティング システム]** :Windows、Linux、または macOS。
* **ホスティング方法**: プロセス内、プロセス外。
* **デプロイ方法**: フレームワーク依存、自己完結型。
* **Web サーバー**: IIS (インターネット インフォメーション サーバー)、Kestrel。
* **ホスティング プラットフォーム**: Azure App Service、Azure VM、Docker、Azure Kubernetes Service (AKS) などの Web Apps 機能。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Azure Web アプリ ポータルから Application Insights を有効にしました。 それでも、EventCounter を表示できません。

 この機能は、ASP.NET Core 向けの [Application Insights 拡張機能](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) ではまだサポートされていません。 この機能がサポートされるようになりましたら、このドキュメントは更新されます。

## <a name="next"></a>次のステップ

* [依存関係の追跡](../../azure-monitor/app/asp-net-dependencies.md)