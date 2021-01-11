---
title: Azure Application Insights におけるテレメトリ サンプリング | Microsoft Docs
description: テレメトリのボリュームを抑制する方法。
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: bb6793bc1e3d5bb55426c1f344520ae19a22a9f9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549567"
---
# <a name="sampling-in-application-insights"></a>Application Insights におけるサンプリング

サンプリングは [Azure Application Insights](./app-insights-overview.md) の機能です。 アプリケーション データの分析に関して統計的な正しさを保ちながら、テレメトリのトラフィック、データ コスト、およびストレージ コストを削減する方法として推奨されます。 サンプリングを使用すると、Application Insights によるテレメトリのスロットルを回避することもできます。 サンプリング フィルターによって関連のある項目が選択されるため、診断調査を行うときに項目間を移動できるようになります。

ポータルにメトリック カウントが表示される場合、それらはサンプリングを考慮するように再正規化されます。 それにより、統計への影響が最小限に抑えられます。

## <a name="brief-summary"></a>簡単な概要

* サンプリングには、アダプティブ サンプリング、固定レート サンプリング、インジェスト サンプリングの 3 種類があります。
* アダプティブ サンプリングは、Application Insights ASP.NET および ASP.NET Core ソフトウェア開発キット (SDK) のすべての最新バージョンで既定で有効になっています。 また、[Azure Functions](../../azure-functions/functions-overview.md) でも使用されます。
* 固定レート サンプリングは、ASP.NET、ASP.NET Core、Java (エージェントと SDK の両方)、および Python 用の Application Insights SDK の最近のバージョンで使用できます。
* インジェスト サンプリングは、Application Insights サービス エンドポイントで機能します。 これは、他のサンプリングが有効になっていない場合にのみ適用されます。 SDK でテレメトリがサンプリングされると、インジェスト サンプリングは無効になります。
* Web アプリケーションの場合、カスタム イベントを記録しており、一連のイベントが確実にまとめて保持または破棄されるようにする必要がある場合は、それらのイベントに同じ `OperationId` 値を割り当てる必要があります。
* Analytics クエリを作成する場合は、 [サンプリングを考慮する](../log-query/aggregations.md)必要があります。 具体的には、単純にレコードをカウントするのではなく、 `summarize sum(itemCount)`を使用する必要があります。
* パフォーマンス メトリックやカスタム メトリックなど、一部のテレメトリの種類は、サンプリングが有効かどうかに関係なく、常に保持されます。

次の表は、各 SDK およびアプリケーションの種類で使用できるサンプリングの種類をまとめたものです。

| Application Insights SDK | アダプティブ サンプリングのサポート | 固定レート サンプリングのサポート | インジェスト サンプリングのサポート |
|-|-|-|-|
| ASP.NET | [はい (既定でオン)](#configuring-adaptive-sampling-for-aspnet-applications) | [はい](#configuring-fixed-rate-sampling-for-aspnet-applications) | 他のサンプリングが有効になっていない場合のみ |
| ASP.NET Core | [はい (既定でオン)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [はい](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | 他のサンプリングが有効になっていない場合のみ |
| Azure Functions | [はい (既定でオン)](#configuring-adaptive-sampling-for-azure-functions) | いいえ | 他のサンプリングが有効になっていない場合のみ |
| Java | いいえ | [はい](#configuring-fixed-rate-sampling-for-java-applications) | 他のサンプリングが有効になっていない場合のみ |
| Node.JS | いいえ | [はい](./nodejs.md#sampling) | 他のサンプリングが有効になっていない場合のみ
| Python | いいえ | [はい](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | 他のサンプリングが有効になっていない場合のみ |
| その他すべて | いいえ | いいえ | [はい](#ingestion-sampling) |

> [!NOTE]
> このページのほとんどの情報は、Application Insights SDK の現在のバージョンに適用されます。 SDK の以前のバージョンについては、[以下のセクションを参照](#older-sdk-versions)してください。

## <a name="types-of-sampling"></a>サンプリングの種類

3 つの異なるサンプリング方法があります。

* **アダプティブ サンプリング**では、ASP.NET/ASP.NET Core アプリの SDK および Azure Functions から送信されるテレメトリの量が自動的に調整されます。 これは、ASP.NET または ASP.NET Core SDK を使用する場合の既定のサンプリングです。 アダプティブ サンプリングは、現在、ASP.NET サーバー側テレメトリおよび Azure Functions でのみ利用できます。

* **固定レート サンプリング**は、ASP.NET、ASP.NET Core、または Java サーバーとユーザーのブラウザーの両方から送信されるテレメトリの量を削減します。 管理者がレートを設定します。 クライアントとサーバーはサンプリングを同期するので、検索では関連のあるページ ビューと要求の間を移動できます。

* **インジェスト サンプリング**は、Application Insights サービス エンドポイントで動作します。 アプリケーションから受信したテレメトリの一部を、設定したサンプリング レートで破棄します。 インジェスト サンプリングはアプリから送信されるテレメトリのトラフィックを削減しませんが、トラフィックを月間のクォータ (上限) 以内で維持するのに役立ちます。 インジェスト サンプリングの主な利点は、アプリを再デプロイすることなくサンプリング レートを設定できることです。 インジェスト サンプリングは、すべてのサーバーとクライアントで一様に動作しますが、他の種類のサンプリングが動作している場合は適用されません。

> [!IMPORTANT]
> アダプティブ サンプリングまたは固定レート サンプリング方法の実行中は、インジェスト サンプリングは無効になります。

## <a name="adaptive-sampling"></a>アダプティブ サンプリング

アダプティブ サンプリングは、Web サーバー アプリから Application Insights サービス エンドポイントに送信されるテレメトリの量に適用されます。

> [!TIP]
> アダプティブ サンプリングは、ASP.NET SDK または ASP.NET Core SDK を使用する場合は既定で有効になり、Azure Functions でも既定で有効になります。

この量は、指定されたトラフィックの最大レート内に維持されるように自動的に調整され、設定 `MaxTelemetryItemsPerSecond` を使用して制御されます。 アプリケーションが少量のテレメトリを生成している場合 (デバッグ時や使用量が少ない場合など)、量が `MaxTelemetryItemsPerSecond` を下回っている限り、サンプリング プロセッサによって項目がドロップされることはありません。 テレメトリの量が増えてくると、目的の量を達成するようにサンプリング レートが調整されます。 調整は、定期的に、送信レートの移動平均に基づいて再計算されます。

目標の量を達成するため、生成されたテレメトリの一部は破棄されます。 他のサンプリング種類と同様に、このアルゴリズムは関連するテレメトリ項目を維持します。 たとえば、検索でテレメトリを調べているときは、特定の例外に関連する要求を検索できます。

要求レートや例外レートなどのメトリック カウントはサンプリング レートを補正するように調整され、メトリックス エクスプローラーにはほぼ正しい値が表示されます。

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>ASP.NET アプリケーション用のアダプティブ サンプリングの構成

> [!NOTE]
> このセクションは、ASP.NET Core アプリケーションではなく、ASP.NET アプリケーションに適用されます。 [ASP.NET Core アプリケーション用のアダプティブ サンプリングの構成については、このドキュメントで後述します。](#configuring-adaptive-sampling-for-aspnet-core-applications)

[`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) で、`AdaptiveSamplingTelemetryProcessor` ノードのいくつかのパラメーターを調整できます。 次の図は既定値です。

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    アダプティブ アルゴリズムが**各サーバー ホストで**収集しようとする[論理演算](./correlation.md#data-model-for-telemetry-correlation)の目標レート。 Web アプリが多数のホストで実行される場合は、Application Insights ポータルのトラフィックの目標レート内に収まるようにこの値を減らします。

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    テレメトリの現在のレートを再評価する間隔。 評価は移動平均として実行されます。 急変しやすいテレメトリの場合は、この間隔を短くすることもできます。

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    サンプリング率が変化してから、どのくらいの時間が経過すると、サンプリング率を下げてキャプチャ データ量を減らすことができるようになるかを指定します。

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    サンプリング率が変化してから、どのくらいの時間が経過すると、サンプリング率を上げてキャプチャ データ量を増やすことができるようになるかを指定します。

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    サンプリング率はさまざまであるため、設定できる最小値を指定します。

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    サンプリング率はさまざまであるため、設定できる最大値を指定します。

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    移動平均の計算で最新値に割り当てられる重みを指定します。 1 以下の値を使用します。 小さい値にすると、急変に対する反応が低いアルゴリズムになります。

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    アプリの起動直後にサンプリングするテレメトリの量。 デバッグ中はこの値を減らさないでください。

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    サンプリング対象にしない種類のセミコロン区切りのリスト。 認識される種類は、`Dependency`、`Event`、`Exception`、`PageView`、`Request`、`Trace` です。 指定した種類はすべてのテレメトリが送信されます。指定されていない種類はサンプリングされます。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    サンプリング対象にしない種類のセミコロン区切りのリスト。 認識される種類は、`Dependency`、`Event`、`Exception`、`PageView`、`Request`、`Trace` です。 指定した種類はサンプリングされます。他のすべての種類のテレメトリは常に送信されます。

アダプティブ サンプリングを**オフにする**には、`ApplicationInsights.config` から `AdaptiveSamplingTelemetryProcessor` ノードを削除します。

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>代替手段:コードでアダプティブ サンプリングを構成する

`.config` ファイルでサンプリング パラメーターを設定する代わりに、プログラムでこれらの値を設定できます。

1. `.config` ファイルからすべての `AdaptiveSamplingTelemetryProcessor` ノードを削除します。
2. 次のスニペットを使用してアダプティブ サンプリングを構成します。

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([テレメトリ プロセッサについてはこちらをご覧ください](./api-filtering-sampling.md#filtering)。)

テレメトリの種類ごとにサンプリング レートを個別に調整したり、特定の種類をまったくサンプリングされないように除外したりすることもできます。

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>ASP.NET Core アプリケーション用のアダプティブ サンプリングの構成

ASP.NET Core アプリケーションには `ApplicationInsights.config` が存在しないため、コードを使用してすべての構成を行います。
アダプティブ サンプリングは、すべての ASP.NET Core アプリケーションで既定で有効にされています。 サンプリング動作は無効にしたり、カスタマイズしたりできます。

#### <a name="turning-off-adaptive-sampling"></a>アダプティブ サンプリングを無効にする

Application Insights サービスを追加するときに、メソッド `ConfigureServices` で `Startup.cs` ファイル内の `ApplicationInsightsServiceOptions` を使用して、既定のサンプリング機能を無効にできます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

上記のコードによって、アダプティブ サンプリングが無効になります。 その他のカスタマイズ オプションを使用したサンプリングを追加するには、次の手順に従います。

#### <a name="configure-sampling-settings"></a>サンプリング設定を構成する

サンプリング動作をカスタマイズするには、次に示すように、`TelemetryProcessorChainBuilder` の拡張メソッドを使用します。

> [!IMPORTANT]
> このメソッドを使用してサンプリングを構成する場合は、`AddApplicationInsightsTelemetry()`を呼び出すときに `aiOptions.EnableAdaptiveSampling` プロパティを `false` に設定してください。

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Azure Functions 用のアダプティブ サンプリングの構成

Azure Functions で実行されているアプリに対してアダプティブ サンプリングを構成するには、[こちらのページ](../../azure-functions/functions-monitoring.md#configure-sampling)の手順に従います。

## <a name="fixed-rate-sampling"></a>固定レート サンプリング

固定レート サンプリングは、Web サーバーおよび Web ブラウザーから送信されるトラフィックを削減します。 アダプティブ サンプリングとは異なり、管理者によって決定された固定レートでテレメトリを削減します。 固定レート サンプリングは、ASP.NET、ASP.NET Core、Java、および Python アプリケーションで使用できます。

他のサンプリング手法と同様に、ここでも関連する項目が保持されます。 また、クライアントとサーバーのサンプリングが同期されて、関連する項目が維持されます。たとえば、検索でページ ビューを見ると、それに関連するサーバー要求を見つけることができます。 

メトリックス エクスプローラーでは、要求や例外の数などのレートに係数が乗算されて、サンプリング レートに対してほぼ正しくなるように補正されます。

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>ASP.NET アプリケーション用の固定レート サンプリングの構成

1. **アダプティブ サンプリングを無効にする**:[`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) で、`AdaptiveSamplingTelemetryProcessor` ノードを削除またはコメント アウトします。

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **固定レート サンプリング モジュールを有効にします。** 次のスニペットを [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) に追加します。
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      または、`ApplicationInsights.config` ファイルでサンプリング パラメーターを設定する代わりに、プログラムでこれらの値を設定できます。

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([テレメトリ プロセッサについてはこちらをご覧ください](./api-filtering-sampling.md#filtering)。)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>ASP.NET Core アプリケーション用の固定レート サンプリングの構成

1. **アダプティブ サンプリングを無効にする**:`ApplicationInsightsServiceOptions` を使用して、`ConfigureServices` メソッドで変更を行うことができます。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **固定レート サンプリング モジュールを有効にします。** 次のスニペットに示すように、`Configure` メソッドで変更を行うことができます。

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Java アプリケーション用の固定レート サンプリングの構成

Java エージェントおよび SDK では、既定ではどのサンプリングも有効になっていません。 現在、固定レート サンプリングのみがサポートされています。 Java では、アダプティブ サンプリングはサポートされていません。

#### <a name="configuring-java-agent"></a>Java エージェントの構成

1. [applicationinsights-agent-3.0.0-PREVIEW.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar) をダウンロードします

1. サンプリングを有効にするには、`ApplicationInsights.json` ファイルに以下を追加します。

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>Java SDK の構成

1. 最新の [Application Insights Java SDK](./java-get-started.md) を使用して Web アプリケーションをダウンロードして構成します。

2. `ApplicationInsights.xml` ファイルに次のスニペットを追加することによって、**固定レート サンプリング モジュールを有効にします**。

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. `Processor` タグの `FixedRateSamplingTelemetryProcessor` 内の次のタグを使用して、特定の種類のテレメトリをサンプリングに含めたり、サンプリングから除外したりできます。
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

サンプリングに含めたり、サンプリングから除外したりできるテレメトリの種類は、`Dependency`、`Event`、`Exception`、`PageView`、`Request`、および `Trace` です。

> [!NOTE]
> サンプリング率には、N を整数として 100/N に近い割合を選択します。  サンプリングでは現在、その他の値はサポートされていません。

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>OpenCensus Python アプリケーション用の固定レート サンプリングの構成

最新の [OpenCensus Azure Monitor エクスポーター](./opencensus-python.md)を使用してアプリケーションをインストルメント化します。

> [!NOTE]
> メトリック エクスポーターには固定レート サンプリングを利用できません。 つまり、カスタム メトリックは、サンプリングを構成できない唯一のテレメトリということになります。 メトリック エクスポーターからは、それが追跡するあらゆるテレメトリが送信されます。

#### <a name="fixed-rate-sampling-for-tracing"></a>追跡の固定レート サンプリング ####
`Tracer` 構成の一部として `sampler` を指定できます。 明示的なサンプラーが指定されていない場合は、既定で `ProbabilitySampler` が使用されます。 `ProbabilitySampler` では、既定で 1/10,000 のレートが使用されます。これは、10,000 の要求のうちの 1 つが Application Insights に送信されることを意味します。 サンプリング レートを指定する場合は、以下を参照してください。

サンプリング レートを指定する場合は、サンプリング レートが 0.0 から 1.0 の範囲であるサンプラーを `Tracer` に指定していることを確認します。 サンプリング レート 1.0 は 100% を表します。つまり、すべての要求は Application Insights にテレメトリとして送信されます。

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>ログの固定レート サンプリング ####
オプションの引数 `logging_sampling_rate` を変更することで、`AzureLogHandler` の固定レート サンプリングを構成できます。 引数を指定しない場合、1.0 のサンプリング レートが使用されます。 サンプリング レート 1.0 は 100% を表します。つまり、すべての要求は Application Insights にテレメトリとして送信されます。

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>JavaScript を使用した Web ページ用の固定レート サンプリングの構成

JavaScript ベースの Web ページは、Application Insights を使用するように構成できます。 テレメトリは、ユーザーのブラウザー内で実行中のクライアント アプリケーションから送信され、ページは任意のサーバーからホストできます。

[Application Insights 用に JavaScript ベースの Web ページを構成する](javascript.md)場合は、Application Insights ポータルから JavaScript スニペットを入手して、それを変更します。

> [!TIP]
> JavaScript が含まれている ASP.NET アプリでは、スニペットは通常 `_Layout.cshtml` にあります。

`samplingPercentage: 10,` のような行をインストルメンテーション キーの前に挿入します。

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

サンプリング率には、N を整数として 100/N に近い割合を選択します。 サンプリングでは現在、その他の値はサポートされていません。

#### <a name="coordinating-server-side-and-client-side-sampling"></a>サーバー側とクライアント側のサンプリングの調整

クライアント側 JavaScript SDK では、サーバー側 SDK と連携して固定レート サンプリングが行われます。 インストルメント化されたページでは、サーバー側 SDK でサンプリング対象にすると判断したのと同じユーザーからのクライアント側テレメトリのみが送信されます。 このロジックは、クライアント側とサーバー側のアプリケーション間でユーザー セッションの整合性を維持することを目的としています。 その結果、Application Insights 内の特定のテレメトリ項目から、このユーザーまたはセッションに関するその他すべてのテレメトリ項目を見つけることができ、検索では関連のあるページ ビューと要求の間を移動できます。

クライアント側とサーバー側のテレメトリに、調整済みのサンプルが表示されない場合:

* サーバーとクライアントの両方でサンプリングを有効にしていることを確認してください。
* クライアントとサーバーの両方で同じサンプリング率を設定していることを確認してください。
* SDK のバージョンが 2.0 以降であることを確認してください。

## <a name="ingestion-sampling"></a>インジェスト サンプリング

インジェスト サンプリングは、Web サーバー、ブラウザー、デバイスからのテレメトリが Application Insights サービス エンドポイントに到達した時点で動作します。 アプリから送信されるテレメトリ トラフィックを削減することはありませんが、Application Insights によって処理および維持 (そして課金) される量を削減します。

アプリが頻繁に月間クォータを超えて、SDK ベースのいずれかのサンプリング種類を使用するオプションがない場合は、この種類のサンプリングを使用します。 

[使用量と推定コスト] ページで、サンプリング レートを設定します。

![アプリケーションの [概要] ブレードで、[設定]、[クォータ]、[サンプル] の順にクリックし、サンプリング レートを選択して、[更新] をクリックします。](./media/sampling/data-sampling.png)

他のサンプリング種類と同様に、このアルゴリズムは関連するテレメトリ項目を維持します。 たとえば、検索でテレメトリを調べているときは、特定の例外に関連する要求を検索できます。 メトリックはそのような要求のレートをカウントし、例外レートを正しく維持します。

サンプリングによって破棄されたデータ ポイントは、 [連続エクスポート](./export-telemetry.md)などの Application Insights の機能では使用できません。

インジェスト サンプリングは、アダプティブ サンプリングまたは固定レート サンプリングの実行中は動作しません。 アダプティブ サンプリングは、ASP.NET SDK または ASP.NET Core SDK が使用されている場合に、または [Azure App Service](azure-web-apps.md) あるいは Status Monitor を使用して Application Insights が有効になっている場合に、既定で有効になります。 Application Insights サービス エンドポイントによってテレメトリが受信されると、そのテレメトリが調べられ、サンプリング レートが100% 未満であると報告された (つまり、テレメトリがサンプリングされている) 場合、設定したインジェスト サンプリング レートは無視されます。

> [!WARNING]
> ポータル タイルに表示される値は、インジェスト サンプリングに設定した値を示します。 いずれかの種類の SDK サンプリング (アダプティブまたは固定レート サンプリング) が動作している場合、この値は実際のサンプリング レートを表しません。

## <a name="when-to-use-sampling"></a>サンプリングを使用する場合

一般に、ほとんどの中小規模アプリケーションでは、サンプリングは不要です。 最も役立つ診断情報や最も正確な統計は、すべてのユーザー アクティビティからデータを収集することで入手します。 

サンプリングの主な利点:

* アプリが短期間に非常に高いレートのテレメトリを送信すると、Application Insights サービスによってデータ ポイントがドロップ ("スロットル") される。 サンプリングによって、アプリケーションでスロットルが発生する可能性が減少する。
* データ ポイントを、価格レベルの [クォータ](pricing.md) 以内に抑える。 
* テレメトリの収集によるネットワーク トラフィックを削減する。 

### <a name="which-type-of-sampling-should-i-use"></a>使用する必要があるサンプリングの種類

**次の場合はインジェスト サンプリングを使用します。**

* テレメトリの月間クォータを消費することが多い場合。
* ユーザーの Web ブラウザーから取得するテレメトリが多すぎる場合。
* サンプリングをサポートしていない SDK のバージョン (たとえば、2 より前の ASP.NET バージョン) を使用している場合。

**次の場合は固定レート サンプリングを使用します。**

* クライアントとサーバーのサンプリングを同期する場合。[検索](./diagnostic-search.md)でイベントを調査するときに、クライアントとサーバーの関連するイベント間を移動できます (ページ ビュー、HTTP 要求など)。
* アプリに適したサンプリング率を確保する場合。 正確なメトリックを取得できる程度に高く、価格クォータとスロットル制限を超えないレートにする必要があります。

**アダプティブ サンプリングを使用する場合。**

他の形式のサンプリングを使う条件に当てはまらない場合は、アダプティブ サンプリングをお勧めします。 この設定は、ASP.NET/ASP.NET Core SDK で既定で有効になっています。 一定の最小レートに達するまでトラフィックは減らないので、使用頻度の低いサイトは一切サンプリングされない可能性があります。

## <a name="knowing-whether-sampling-is-in-operation"></a>サンプリングが動作しているかどうかを把握する

適用されている場所に関係なく、実際のサンプリング レートを検出するには、次のように [Analytics クエリ](../log-query/log-query-overview.md) を使用します。

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

いずれかの種類の `RetainedPercentage` が 100 未満である場合、その種類のテレメトリはサンプリングされています。

> [!IMPORTANT]
> Application Insights は、上で説明したどのサンプリング手法でも、セッション、メトリック (カスタム メトリックを含む)、またはパフォーマンス カウンターのテレメトリの種類をサンプリングしません。 これらのテレメトリの種類では精度の低下がきわめて望ましくないため、これらの種類は常にサンプリングから除外されます。

## <a name="how-sampling-works"></a>サンプリングのしくみ

サンプリング アルゴリズムによって、ドロップするテレメトリ項目と保持する項目が決定されます。 これは、サンプリングが SDK または Application Insights サービスのどちらで実行されるかに関係なく当てはまります。 サンプリングはいくつかのルールに基づいて決定されますが、このルールのねらいは、削減されたデータ セットを使用する場合でも、Application Insights の診断機能を実用的で信頼性の高いものに維持しながら、すべての相互に関連するデータ ポイントはそのままの状態で保持することです。 たとえば、アプリのサンプルに失敗した要求が含まれている場合、追加のテレメトリ項目 (この要求に対してログに記録された例外やトレースなど) は保持されます。 サンプリングでは、すべてをまとめて維持するか、ドロップするかのどちらかです。 そのため、Application Insights で要求の詳細を表示する場合は、その要求に加えて、関連付けられているテレメトリ項目も常に表示されます。

サンプリングの判定は、要求の操作 ID に基づいています。つまり、特定の操作に属するすべてのテレメトリ項目が保持されるか、または破棄されるかのどちらかです。 操作 ID が設定されていないテレメトリ項目 (HTTP コンテキストのない非同期スレッドから報告されたテレメトリ項目など) の場合、サンプリングでは単純に各種類のテレメトリ項目を一定の割合ずつキャプチャします。

テレメトリを表示する場合、Application Insights サービスは、不足しているデータ ポイントを補正するために、収集時に使用したものと同じサンプリング率でメトリックを調整します。 そのため、Application Insights でテレメトリを表示する場合、ユーザーは統計的に正しく、実際の数値に非常に近い近似値を見ていることになります。

近似精度は、構成したサンプリング率に大きく左右されます。 また、多数のユーザーからの同じような要求を大量に処理するアプリケーションの場合は、近似精度が高くなります。 一方で、処理量が膨大ではないアプリケーションの場合、通常はクォータの範囲内ですべてのテレメトリを送信でき、スロットルによるデータの損失もないため、サンプリングは必要ありません。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

*ASP.NET および ASP.NET Core SDK での既定のサンプリング動作はどのようなものですか。*

* 上の SDK の最新バージョンのいずれかを使用している場合は、1 秒あたり 5 つのテレメトリ項目のアダプティブ サンプリングが既定で有効になります。
  既定では 2 つの `AdaptiveSamplingTelemetryProcessor` ノードが追加され、一方はサンプリングに `Event` の種類を含め、もう一方は `Event` の種類をサンプリングから除外します。 この構成は、SDK がテレメトリ項目を `Event` の種類の 5 つのテレメトリ項目と、その他のすべての種類の組み合わせの 5 つのテレメトリ項目に制限することにより、`Events` が確実に他のテレメトリの種類とは別にサンプリングされるようにすることを示します。 イベントは通常、ビジネス テレメトリに使用されるため、多くの場合、診断テレメトリの量によって影響を受けないようにする必要があります。
  
  生成される既定の `ApplicationInsights.config` ファイルを以下に示します。 ASP.NET Core では、同じ既定の動作がコードで有効になります。 この既定の動作を変更するには、[このページの前のセクションの例](#configuring-adaptive-sampling-for-aspnet-core-applications)を使用します。

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*テレメトリを複数回サンプリングできますか*

* いいえ。 ある項目が既にサンプリングされている場合、SamplingTelemetryProcessor はサンプリングの検討からその項目を無視します。 同じことがインジェスト サンプリングにも当てはまります。これにより、SDK 自体で既にサンプリングされている項目にはサンプリングが適用されません。

*サンプリングを、"各テレメトリ タイプの X% を収集" という単純な方法にしないのはなぜですか。*

* このサンプリング方法では、メトリックの近似精度は非常に高レベルになりますが、一方でユーザーごと、セッションごと、要求ごとに診断データを関連付けるという、診断には欠かせない機能が利用できなくなります。 そのため、"アプリ ユーザーの X% に関するすべてのテレメトリ項目を収集"、または "アプリ要求の X% に関するすべてのテレメトリを収集" のようなポリシーを使用すると、サンプリングがより適切に実行されます。 要求に関連付けられていないテレメトリ項目 (バックグラウンドの非同期処理など) の場合、フォールバックは "テレメトリの種類ごとにすべての項目の X % を収集する" ことです。 

*サンプリング率は、時間経過に伴い変化する可能性がありますか。*

* はい。アダプティブ サンプリングの場合、現在監視されているテレメトリのデータ量に基づいて、サンプリング率が徐々に変化します。

*固定レート サンプリング率を使用している場合、自分のアプリに最適なサンプリング率は、どのようにして確認できますか。*

* 1 つの方法として、アダプティブ サンプリングから始め、決定されたレートを確認したら (前の質問を参照)、そのレートを使って固定レート サンプリングに切り替えます。 
  
    それ以外では、推測するしかありません。 Application Insights での現在のテレメトリ使用状況を分析し、発生しているスロットルを観察して、収集されるテレメトリの量を見積もります。 この 3 つの情報に、選択した価格レベルを合わせて考えると、収集されるテレメトリの量をどれくらい削減すればよいかがわかります。 ただし、ユーザー数の増加やテレメトリ量の何らかの変化によって、見積りが無効になることがあります。

*サンプリング率を低く構成しすぎると、どうなりますか。*

* Application Insights がデータ量の減少に関してデータの視覚化を補正しようとしている場合、サンプリング率を極端に低くすると過度にアグレッシブなサンプリングが行われ、近似精度の低下につながります。 また、頻度の低い失敗や遅延要求の一部はサンプリングから除外される場合があるため、診断機能に悪影響を及ぼす可能性もあります。

*サンプリング率を高く構成しすぎると、どうなりますか。*

* サンプリング率を高く構成しすぎた (十分にアグレッシブでない) 場合は、収集されるテレメトリの量が十分に減少しないことになります。 スロットルに関連するテレメトリ データが失われる可能性が残り、超過料金によって Application Insights の使用コストが予定額を超える場合もあります。

*サンプリングはどのようなプラットフォームで使用できますか。*

* インジェスト サンプリングは、SDK がサンプリングを実行していない場合に任意のテレメトリが特定の量を超えると、自動的に実行される場合があります。 この構成は、たとえば、ASP.NET SDK や Java SDK の古いバージョンを使用している場合に機能します。
* 最新の ASP.NET または ASP.NET Core SDK (Azure または独自のサーバーのいずれかでホストされる) を使用している場合、既定ではアダプティブ サンプリングが行われますが、前述の方法で固定レートに切り替えることができます。 固定レート サンプリングの場合、ブラウザー SDK は自動的にサンプル関連のイベントに同期します。 
* 最新の Java エージェントを使用している場合は、`ApplicationInsights.json` を構成して (Java SDK の場合は `ApplicationInsights.xml` を構成する) 固定レート サンプリングを有効にできます。 サンプリングは、既定で無効になっています。 固定レート サンプリングでは、ブラウザー SDK とサーバーは自動的にサンプル関連のイベントに同期されます。

*常に確認したい頻度の低いイベントがあります。サンプリング モジュールを使わずに確認するにはどうすればよいですか。*

* これを実現する最善の方法は、次に示すように、保持したいテレメトリ項目で `SamplingPercentage` を 100 に設定するカスタムの [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) を記述することです。 初期化子はテレメトリ プロセッサ (サンプリングを含む) の前に実行されることが保証されるため、これにより、すべてのサンプリング手法ではサンプリングに関する考慮事項からこの項目が確実に無視されます。 ASP.NET SDK、ASP.NET Core SDK、JavaScript SDK、Java SDK では、カスタムのテレメトリ初期化子が利用できます。 たとえば、ASP.NET SDK を使用してテレメトリ初期化子を構成できます。

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>以前の SDK バージョン

アダプティブ サンプリングは ASP.NET v2.0.0-beta3 以降の Application Insights SDK、Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 以降で使用でき、既定で有効になっています。

固定レート サンプリングは、ASP.NET バージョン 2.0.0 以降および Java SDK バージョン 2.0.1 以降の SDK の機能です。

ASP.NET SDK の v2.5.0-beta2 および ASP.NET Core SDK の v2.2.0-beta3 より前では、サンプリングの判定は、"ユーザー" を定義するアプリケーション (つまり、最も一般的な Web アプリケーション) のユーザー ID のハッシュに基づいていました。 ユーザーを定義しない種類のアプリケーション (Web サービスなど) の場合、サンプリングの判定は要求の操作 ID に基づいていました。 ASP.NET および ASP.NET Core SDK の最近のバージョンでは、サンプリングの判定に操作 ID が使用されます。

## <a name="next-steps"></a>次のステップ

* [フィルター](./api-filtering-sampling.md) を使用して、SDK から送信される情報についてさらに厳密に制御できます。
* [Application Insights によるテレメトリの最適化](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights)に関する Developer Network の記事を読みます。

