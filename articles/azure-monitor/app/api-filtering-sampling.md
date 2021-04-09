---
title: Application Insights SDK におけるフィルター処理および前処理 | Microsoft Docs
description: テレメトリが Application Insights ポータルに送信される前に、SDK でフィルター処理またはデータへのプロパティの追加を行うためのテレメトリ プロセッサおよびテレメトリ初期化子を記述します。
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cb9159b98b219c6fb04beb7bbbaade64fc72a30b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98927754"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK におけるフィルター処理および前処理

Application Insights SDK のプラグインを作成および構成して、Application Insights サービスに送信される前のテレメトリのエンリッチと処理の方法をカスタマイズできます。

* [サンプリング](sampling.md) は、統計に影響を与えることなくテレメトリの量を削減します。 関連するデータ ポイントが一緒に保持されるので、問題の診断時にそれらのデータ ポイント間を移動することができます。 ポータルでは、サンプリングを補正するために合計数が乗算されます。
* テレメトリ プロセッサを使ったフィルター処理により、サーバーに送信される前のテレメトリから、不要な情報を SDK で取り除くことができます。 たとえば、ロボットからの要求を除外することでテレメトリの量を削減することができます。 フィルター処理は、トラフィックを削減するうえでは、サンプリングよりも基本的な方法です。 送信内容をより細かく制御できますが、統計に影響します。 たとえば、すべての成功した要求をフィルターで除外することができます。
* アプリから送信されるテレメトリ (標準モジュールからのテレメトリも含む) に対し、[テレメトリ初期化子でプロパティを追加したりプロパティに変更を加えたり](#add-properties)することができます。 たとえば、算出値や、ポータルでデータをフィルター処理するのに使用できるバージョン番号を追加することが可能です。
* [SDK API](./api-custom-events-metrics.md) は、カスタム イベントとメトリックの送信に使用します。

開始する前に次の操作を実行してください。

* アプリケーションに適した SDK をインストールします。[ASP.NET](asp-net.md)、[ASP.NET Core](asp-net-core.md)、[Non HTTP/Worker for .NET/.NET Core](worker-service.md)、または [JavaScript](javascript.md)。

<a name="filtering"></a>

## <a name="filtering"></a>Filtering

この手法では、テレメトリ ストリームに含める内容またはテレメトリ ストリームから除外する内容を直接制御できます。 フィルター処理を使用すると、Application Insights への送信対象からテレメトリ項目を除外することができます。 フィルター処理は、サンプリングと組み合わせて使用することも、個別に使用することもできます。

テレメトリのフィルター処理を行うには、テレメトリ プロセッサを記述し、それを `TelemetryConfiguration` に登録します。 すべてのテレメトリはプロセッサを経由します。 ストリームから削除するか、チェーン内の次のプロセッサに渡すかを選択できます。 HTTP 要求コレクターや依存関係コレクターなどの標準的なモジュールのテレメトリに加えて、自身で追跡したテレメトリも含まれます。 たとえば、ロボットからの要求や成功した依存関係の呼び出しについてのテレメトリをフィルターで除外できます。

> [!WARNING]
> プロセッサを使用して SDK から送信されるテレメトリをフィルター処理すると、ポータルに表示される統計にゆがみが生じ、関連項目を追跡するのが困難になる可能性があります。
>
> 代わりに、 [サンプリング](./sampling.md)の使用を検討します。
>
>

### <a name="create-a-telemetry-processor-c"></a>テレメトリ プロセッサを作成する (C#)

1. フィルターを作成するには、`ITelemetryProcessor` を実装します。

    テレメトリ プロセッサによって処理のチェーンが構築されます。 テレメトリ プロセッサをインスタンス化するときは、チェーン内の次のプロセッサへの参照が与えられます。 テレメトリ データ ポイントが process メソッドに渡されると、作業が実行され、そのチェーンの次のテレメトリ プロセッサが呼び出されます (呼び出されないこともあります)。

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. プロセッサを追加します。

ASP.NET **アプリ**

次のスニペットを ApplicationInsights.config に挿入します。

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

名前付きのパブリック プロパティをクラス内に指定することにより、.config ファイルから文字列値を渡すことができます。

> [!WARNING]
> .config ファイル内の型名とプロパティ名をコード内のクラスおよびプロパティ名と慎重に照合してください。 存在しない型またはプロパティが .config ファイルによって参照されていると、SDK は何も通知せずにテレメトリの送信に失敗する場合があります。
>

または、コード内でフィルターを初期化することもできます。 適切な初期化クラス (たとえば `Global.asax.cs` の AppStart) で、プロセッサをチェーンに挿入します。

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

この時点より後に作成されたテレメトリ クライアントではプロセッサが使用されます。

ASP.NET **Core または Worker サービス アプリ**

> [!NOTE]
> `ApplicationInsights.config` または `TelemetryConfiguration.Active` を使用してプロセッサを追加することは、ASP.NET Core アプリケーションの場合、または Microsoft.ApplicationInsights.WorkerService SDK を使用している場合は無効です。

[ASP.NET Core](asp-net-core.md#adding-telemetry-processors) または [WorkerService](worker-service.md#adding-telemetry-processors) を使用して作成されたアプリの場合、新しいテレメトリ プロセッサを追加するには、次に示すように、`IServiceCollection` に対して `AddApplicationInsightsTelemetryProcessor` 拡張メソッドを使用します。 このメソッドは、`Startup.cs` クラスの `ConfigureServices` メソッドの中で呼び出されます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>フィルターの例

#### <a name="synthetic-requests"></a>人工的な要求

ボットと Web テストを除外します。 メトリックス エクスプローラーで人工的なソースを除外することもできますが、SDK 自体でそのソースをフィルター処理することにより、トラフィックとインジェストのサイズが削減されます。

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>失敗した認証

"401" 応答が返された要求を除外します。

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>リモートの依存関係の高速呼び出しを除外する

低速な呼び出しのみの診断を実行する場合は、高速呼び出しを除外します。

> [!NOTE]
> このフィルター処理によって、ポータルに表示される統計にゆがみが生じます。
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>依存関係の問題の診断

[このブログ](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) では、依存関係に対して定期的な ping を自動送信することによって依存関係の問題を診断するプロジェクトについて説明します。

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript Web アプリケーション

**ITelemetryInitializer を使用したフィルター処理**

1. テレメトリ初期化子のコールバック関数を作成します。 コールバック関数は、パラメーターとして `ITelemetryItem` を受け取ります。これは、処理されるイベントを指します。 このコールバックから `false` が返されると、テレメトリ項目がフィルターで除外されます。

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. 次のテレメトリ初期化子のコールバックを追加します。

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>プロパティの追加/変更:ITelemetryInitializer

テレメトリを追加情報でエンリッチしたり、標準のテレメトリ モジュールによって設定されたテレメトリのプロパティをオーバーライドしたりするには、テレメトリ初期化子を使用します。

たとえば、Web 向けの Application Insights パッケージでは HTTP 要求に関するテレメトリが収集されます。 既定では、応答コードが 400 以上の要求はすべて失敗としてフラグが設定されます。 これに対して 400 を成功として処理する場合は、"成功" プロパティを設定するテレメトリ初期化子を指定できます。

テレメトリ初期化子を指定すると、Track*() メソッドのいずれかが呼び出されるたびに、テレメトリ初期化子も呼び出されます。 これには、標準のテレメトリ モジュールによって呼び出される `Track()` メソッドも含まれます。 規約により、これらのモジュールでは、初期化子によって既に設定されているプロパティは設定されません。 テレメトリ初期化子は、テレメトリ プロセッサを呼び出す前に呼び出されます。 そのため、初期化子によって実行されたエンリッチメントはすべて、プロセッサから参照することができます。

**初期化子を定義する**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

ASP.NET **アプリ:初期化子を読み込む**

ApplicationInsights.config で:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

または、Global.aspx.cs などのコード内で初期化子をインスタンス化することもできます。

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

詳細については、[このサンプル](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)を参照してください。

ASP.NET **Core または Worker サービス アプリ:初期化子を読み込む**

> [!NOTE]
> `ApplicationInsights.config` または `TelemetryConfiguration.Active` を使用して初期化子を追加することは、ASP.NET Core アプリケーションの場合、または Microsoft.ApplicationInsights.WorkerService SDK を使用している場合は無効です。

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) または [WorkerService](worker-service.md#adding-telemetryinitializers) を使用して作成されたアプリの場合、次に示すように、新しいテレメトリ初期化子を追加するには、依存関係挿入コンテナーに追加します。 これは、`Startup.ConfigureServices` メソッドで実行します。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript テレメトリ初期化子
*JavaScript*

ポータルから取得した初期化コードの直後にテレメトリ初期化子を挿入します。

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.addTelemetryInitializer(function (envelope) {
        var telemetryItem = envelope.data.baseData;

        // To check the telemetry items type - for example PageView:
        if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
            // this statement removes url from all page view documents
            telemetryItem.url = "URL CENSORED";
        }

        // To set custom properties:
        telemetryItem.properties = telemetryItem.properties || {};
        telemetryItem.properties["globalProperty"] = "boo";
        
        // To set cloud role name / instance
        envelope.tags["ai.cloud.role"] = "your role name";
        envelope.tags["ai.cloud.roleInstance"] = "your role instance";
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

telemetryItem で使用できる非カスタム プロパティの概要については、[Application Insights エクスポート データ モデル](./export-data-model.md)に関するページを参照してください。

任意の数の初期化子を追加できます。 これらは、追加された順序で呼び出されます。

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python テレメトリ プロセッサ

OpenCensus Python のテレメトリ プロセッサは、エクスポート前にテレメトリを処理するために呼び出される単なるコールバック関数です。 コールバック関数は、パラメーターとして [エンベロープ](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) データ型を受け入れる必要があります。 テレメトリをエクスポートから除外するには、コールバック関数が `False` を返すようにします。 エンベロープの Azure Monitor データ型のスキーマについては、[GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py) を参照してください。

> [!NOTE]
> `cloud_RoleName` を変更するには、`tags` フィールドの `ai.cloud.role` 属性を変更します。

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
必要な数だけプロセッサを追加できます。 これらは、追加された順序で呼び出されます。 1 つのプロセッサから例外がスローされた場合、次のプロセッサには影響しません。

### <a name="example-telemetryinitializers"></a>TelemetryInitializers の例

#### <a name="add-a-custom-property"></a>カスタム プロパティを追加する

次のサンプル初期化子は、追跡されたすべてのテレメトリにカスタム プロパティを追加します。

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>クラウド ロール名を追加する

次のサンプル初期化子によって、追跡されたすべてのテレメトリにクラウド ロール名が設定されます。

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>HttpContext から情報を追加する

次のサンプル初期化子では、[`HttpContext`](/aspnet/core/fundamentals/http-context) からデータが読み取られ、`RequestTelemetry` インスタンスに追加されます。 `IHttpContextAccessor` は、コンストラクターの依存関係のインジェクションを通じて自動的に提供されます。

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor と ITelemetryInitializer

テレメトリ プロセッサとテレメトリ初期化子は何が違うのでしょうか。

* それらを使って実行できることにはいくつかの重複があります。 どちらもテレメトリのプロパティを追加または変更するために使用できますが、その用途には初期化子を使用することをお勧めします。
* テレメトリ初期化子は、常にテレメトリ プロセッサの前に実行されます。
* テレメトリ初期化子は、複数回呼び出すことができます。 規約により、既に設定されているプロパティは設定されません。
* テレメトリ プロセッサを使用すると、テレメトリ項目を完全に置換または破棄できます。
* 登録されているテレメトリ初期化子はすべて、テレメトリ項目ごとに呼び出されることが保証されます。 テレメトリ プロセッサの場合、SDK で呼び出しが保証されるのは、最初のテレメトリ プロセッサのみです。 その他のプロセッサが呼び出されるかどうかは、先行するテレメトリ プロセッサによって決まります。
* 追加のプロパティでテレメトリをエンリッチしたり、既存のプロパティをオーバーライドしたりするには、TelemetryInitializers を使用します。 テレメトリ プロセッサを使用してテレメトリを除外します。

## <a name="troubleshoot-applicationinsightsconfig"></a>ApplicationInsights.config のトラブルシューティング

* 完全修飾された型名とアセンブリ名が正しいことを確認します。
* applicationinsights.config ファイルが出力ディレクトリ内に存在し、最近の変更がすべて含まれていることを確認します。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [API の概要](./api-custom-events-metrics.md)
* [ASP.NET リファレンス](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>SDK コード

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>次のステップ
* [イベントおよびログを検索する](./diagnostic-search.md)
* [サンプリング](./sampling.md)
* [トラブルシューティング](../faq.md)

