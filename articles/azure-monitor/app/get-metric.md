---
title: Azure Monitor Application Insights の Get-Metric
description: GetMetric() 呼び出しを効果的に使用し、Azure Monitor Application Insights で .NET および .NET Core アプリケーション用にローカルで事前集計されたメトリックをキャプチャする方法を学習する
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 0ce2651d5cfcb1578d78982af109a004aaac11f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719782"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>.NET および .NET Core でのカスタム メトリックの収集

Azure Monitor Application Insights の .NET および .NET Core SDK には、カスタム メトリックを収集する 2 つの異なるメソッド (`TrackMetric()` と `GetMetric()`) があります。 これら 2 つのメソッドの主な違いは、ローカル集計です。 `TrackMetric()` には事前集計はありませんが、`GetMetric()` には事前集計があります。 推奨される方法は集計を使用することであるため、`TrackMetric()` は、カスタム メトリックを収集するための推奨メソッドではなくなりました。 この記事では、GetMetric() メソッドの使用方法と、そのしくみの背後にある論理的根拠についていくつか説明します。

## <a name="trackmetric-versus-getmetric"></a>TrackMetric と GetMetric

`TrackMetric()` では、メトリックを示す未加工のテレメトリを送信します。 値ごとに単一のテレメトリ項目を送信することは、非効率的です。 また、すべての `TrackMetric(item)` がテレメトリ初期化子とプロセッサの完全な SDK パイプラインを通過するため、`TrackMetric()` はパフォーマンスの点でも非効率的です。 `TrackMetric()` とは異なり、`GetMetric()` ではローカル事前集計が自動的に処理され、1 分の一定間隔で集計されたサマリー メトリックのみが送信されます。 したがって、一部のカスタム メトリックを秒またはミリ秒単位で厳密に監視する必要がある場合、1 分ごとに監視するだけのストレージとネットワーク トラフィックのコストのみが発生している間にそのようにすることができます。 また、これにより、集計されたメトリックに対して送信する必要があるテレメトリ項目の合計数が大幅に減少するため、調整が発生するリスクが大幅に軽減されます。

Application Insights では、`TrackMetric()` と `GetMetric()` を介して収集されたカスタム メトリックは[サンプリング](./sampling.md)の対象にはなりません。 重要なメトリックをサンプリングすると、これらのメトリックに基づいて作成した可能性のあるアラートの信頼性が低下する場合があるシナリオにつながることがあります。 カスタム メトリックをサンプリングしないようにすることで、一般に、アラートのしきい値違反が発生した場合にアラートが起動するという確信を得ることができます。  しかし、カスタム メトリックはサンプリングされないため、潜在的な懸念事項がいくつかあります。

1 秒ごとに、あるいはさらに細かい間隔でメトリックの傾向を追跡する必要がある場合、次のようになることがあります。

- データ ストレージのコストが増加する。 Azure Monitor に送信するデータの量に関連したコストが発生します (送信するデータの量が多いほど、監視の全体的なコストが高くなります)。
- ネットワーク トラフィックやパフォーマンスのオーバーヘッドが増加する (一部のシナリオでは、金銭的およびアプリケーション パフォーマンスの両方のコストがかかる可能性があります)。
- インジェスト調整のリスク (アプリで短期間に非常に高いレートのテレメトリを送信すると、Azure Monitor サービスによってデータ ポイントが削除 ("調整") されます)。

調整は、サンプリングと同様に特に懸念されます。アラートをトリガーする条件がローカルで発生し、送信されるデータが多すぎるためにインジェスト エンドポイントで削除される可能性があるため、調整によりアラートが起動しなくなる場合があります。 このため、.NET and .NET Core では、独自のローカル集計ロジックを実装していない限り、`TrackMetric()` を使用しないことをお勧めします。 すべてのインスタンスを追跡しようとしたときに、特定の期間にわたってイベントが発生した場合は、[`TrackEvent()`](./api-custom-events-metrics.md#trackevent) の方が適していることがわかります。 しかし、カスタム メトリックとは異なり、カスタム イベントはサンプリングの対象となる場合があることに注意してください。 もちろん、独自のローカル事前集計を記述しなくても `TrackMetric()` を引き続き使用することはできますが、その場合は落とし穴に注意してください。

つまり、`GetMetric()` は、事前集計を行い、すべての Track() 呼び出しからの値を蓄積し、1 分ごとにサマリーや集計を送信するため、推奨される方法です。 これにより、すべての関連情報を引き続き収集しながら、送信するデータ ポイントを少なくすることによって、コストとパフォーマンスのオーバーヘッドを大幅に削減できます。

> [!NOTE]
> GetMetric() メソッドがあるのは、.NET および .NET Core SDK のみです。 Java を使用している場合は、[Micrometer メトリック](./micrometer-java.md)または `TrackMetric()` を利用できます。 JavaScript と Node.js の場合は、引き続き `TrackMetric()` を使用しますが、前のセクションで概説した注意事項に留意してください。 Python の場合は、[OpenCensus.stats](./opencensus-python.md#metrics) を使用してカスタム メトリックを送信できますが、メトリックの実装は異なります。

## <a name="getting-started-with-getmetric"></a>GetMetric の使用の開始

この例では、基本的な .NET Core 3.1 ワーカー サービス アプリケーションを使用します。 これらの例で使用されたテスト環境を正確にレプリケートする場合は、[ワーカー サービスの監視に関する記事](./worker-service.md#net-core-30-worker-service-application)の手順 1 から 6 に従って、基本的なワーカー サービス プロジェクト テンプレートに Application Insights を追加します。 これらの概念は、Web アプリやコンソール アプリを含め、SDK を使用できる一般的なアプリケーションに適用されます。

### <a name="sending-metrics"></a>メトリックの送信

`worker.cs` ファイルの内容を以下に置き換えます。

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

上記のコードを実行し、Visual Studio の出力ウィンドウ、または Telerik の Fiddler などのツールを使用して送信されるテレメトリを見ると、テレメトリが送信されずに while ループが繰り返し実行され、単一のテレメトリ項目が 60 秒マーク付近で送信されることがわかります。このテストの場合は、次のようになります。

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

この単一のテレメトリ項目は、41 の個別のメトリック測定の集計を表します。 同じ値を何度も送信していたため、"*標準偏差 (stDev)* " は 0 で、"*最大 (max)* " と "*最小 (min)* " の値は同一になっています。 *value* プロパティは、集計されたすべての個別の値の合計を表します。

> [!NOTE]
> GetMetric では、最後の値 ("gauge") の追跡も、ヒストグラムや分布の追跡もサポートしていません。

ログ (Analytics) エクスペリエンスで Application Insights リソースを調べると、この個別のテレメトリ項目は次のようになります。

![Log Analytics のクエリ ビュー](./media/get-metric/log-analytics.png)

> [!NOTE]
> 取り込み後に未加工のテレメトリ項目に明示的な sum プロパティやフィールドは含まれませんが、ここでは 1 つ作成します。 この場合、`value` と `valueSum` の両方のプロパティは同じことを表します。

また、ポータルの [ _[メトリック]_](../essentials/metrics-charts.md) セクションで、カスタム メトリック テレメトリにアクセスすることもできます。 [ログベース、およびカスタム メトリック](pre-aggregated-metrics-log-metrics.md)の両方があります (以下のスクリーンショットはログベースの例です)。![メトリックス エクスプローラー ビュー](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>高スループット使用率のメトリック参照のキャッシュ

場合によっては、メトリック値が非常に頻繁に観測されることがあります。 たとえば、500 要求/秒を処理する高スループット サービスでは、要求ごとに 20 個のテレメトリ メトリックの生成が必要になる場合があります。 これは、1 秒あたり 10,000 の値を追跡することを意味します。 このような高スループットのシナリオでは、ユーザーは、一部の参照を回避して SDK を補助することが必要になる場合があります。

たとえば、この例では、"ComputersSold" というメトリックのハンドルを参照し、観測値 42 を追跡しました。 代わりに、複数のトラック呼び出しに対してハンドルがキャッシュされる可能性があります。

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

上記の例では、メトリック ハンドルをキャッシュするだけでなく、`Task.Delay` も 50 ミリ秒に短縮され、ループがより頻繁に実行されるようになり、その結果、772 の `TrackValue()` 呼び出しが行われるようになりました。

## <a name="multi-dimensional-metrics"></a>多次元メトリック

前のセクションの例は、0 次元メトリックを示しています。 メトリックは多次元でもかまいません。 現在、最大で 10 個のディメンションがサポートされています。

 1 次元メトリックを作成する方法の例を次に示します。

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

このコードを少なくとも 60 秒実行すると、3 つの異なるテレメトリ項目が Azure に送信され、それぞれが 3 つのフォーム ファクターのいずれかの集計を表します。 先ほどと同じように、これらをログ (Analytics) ビューで確認することができます。

![多次元メトリックの Log Analytics ビュー](./media/get-metric/log-analytics-multi-dimensional.png)

メトリックス エクスプローラーのエクスペリエンスでも同様です。

![カスタム メトリック](./media/get-metric/custom-metrics.png)

しかし、新しいカスタム ディメンションでメトリックを分割したり、メトリック ビューでカスタム ディメンションを表示したりすることができないことがわかります。

![分割のサポート](./media/get-metric/splitting-support.png)

既定では、メトリック エクスプローラー エクスペリエンス内の多次元メトリックは、Application Insights リソースで有効になっていません。

### <a name="enable-multi-dimensional-metrics"></a>多次元メトリックを有効にする

Application Insights リソースに対して多次元メトリックを有効にするには、 **[使用量と推定コスト]**  >  **[カスタム メトリック]**  >  **[カスタム メトリック ディメンションに関するアラートを有効にします]**  >  **[OK]** の順に選択します。 この詳細については、[こちら](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)をご覧ください。

この変更を行って新しい多次元テレメトリを送信すると、**分割を適用** できるようになります。

> [!NOTE]
> ポータルで機能をオンにした後に新しく送信されたメトリックにのみ、ディメンションが格納されます。

![[Apply splitting]\(分割の適用\)](./media/get-metric/apply-splitting.png)

_FormFactor_ ディメンションごとにメトリック集計を表示します。

![フォーム ファクター](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>3 個を超えるディメンションがある場合に MetricIdentifier を使用する方法

現在、10 個のディメンションがサポートされていますが、3 個を超えるディメンションでは `MetricIdentifier` を使用する必要があります。

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>カスタム メトリックの構成

メトリックの構成を変更する場合は、メトリックが初期化されている場所でこれを行う必要があります。

### <a name="special-dimension-names"></a>特殊なディメンション名

メトリックでは、アクセスに使われる `TelemetryClient` のテレメトリ コンテキストは使用されません。`MetricDimensionNames` クラスの定数として使用できる特殊なディメンション名は、このような制限がある場合に最適な回避策です。

以下の "Special Operation Request Size" メトリックによって送信されたメトリック集計では、`Context.Operation.Name` が "Special Operation" に設定 **されません**。 一方、`TrackMetric()` やその他の TrackXXX() では、`OperationName` が "Special Operation" に正しく設定されます。

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

この場合、`TelemetryContext` 値を指定するために、`MetricDimensionNames` クラスに一覧表示されている特殊なディメンション名を使用します。

たとえば、次のステートメントの結果として得られるメトリック集計が Application Insights クラウド エンドポイントに送信されると、その `Context.Operation.Name` データ フィールドは "Special Operation" に設定されます。

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

この特殊なディメンションの値は `TelemetryContext` にコピーされ、'通常の' ディメンションとしては使用されません。 通常のメトリック探索のために操作ディメンションも保持する場合は、その目的のために別のディメンションを作成する必要があります。

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>ディメンションと時系列の上限

 テレメトリ サブシステムで誤ってリソースを使い切らないようにするために、メトリックごとのデータ系列の最大数を制御できます。 既定の制限は、メトリックごとのデータ系列の合計が 1000 を超えず、ディメンションごとの異なる値が 100 を超えないことです。

 ディメンションと時系列の上限のコンテキストでは、`Metric.TrackValue(..)` を使用して、制限が観測されていることを確認します。 制限に既に達している場合、`Metric.TrackValue(..)` により "False" が返され、値は追跡されません。 それ以外の場合は、"True" が返されます。 これは、メトリックのデータがユーザー入力によって生じる場合に便利です。

`MetricConfiguration` コンストラクターでは、各メトリック内のさまざまな系列を管理する方法に関するいくつかのオプションを使用します。また、メトリックの個々の系列に対して集計動作を指定する `IMetricSeriesConfiguration` を実装するクラスのオブジェクトを使用します。

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` は、メトリックに含めることができるデータ時系列の最大数です。 この上限に達すると、`TrackValue()` に対する呼び出しは追跡されなくなります。
* `valuesPerDimensionLimit` では、同じようにディメンションごとに個別の値の数を制限します。
* `restrictToUInt32Values` では、負でない整数値のみを追跡するかどうかを決定します。

上限を超えているかどうかを確認するためのメッセージを送信する方法の例を以下に示します。

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>次のステップ

* ワーカー サービス アプリケーションの監視の詳細については、[こちら](./worker-service.md)を参照してください。
* ログベースおよび事前集計メトリックの詳細については、[こちら](./pre-aggregated-metrics-log-metrics.md)を参照してください。
* [メトリックス エクスプローラー](../essentials/metrics-getting-started.md)
* [ASP.NET Core アプリケーション](asp-net-core.md)用の Application Insights を有効にする方法
* [ASP.NET アプリケーション](asp-net.md)用の Application Insights を有効にする方法
