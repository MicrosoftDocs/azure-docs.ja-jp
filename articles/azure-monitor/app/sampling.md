---
title: Azure Application Insights におけるテレメトリ サンプリング | Microsoft Docs
description: テレメトリのボリュームを抑制する方法。
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: 4da91150999864c64ead28b74242e85d23a51ead
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310446"
---
# <a name="sampling-in-application-insights"></a>Application Insights におけるサンプリング

サンプリングは [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) の機能です。 サンプリングは、アプリケーション データの分析に関して統計的な正しさを保ちながら、テレメトリのトラフィックと保存スペースを減らす方法として推奨されます。 フィルターによって関連のある項目が選択されるため、診断調査を行うときに項目間を移動できるようになります。
ポータルにメトリック カウントが表示される場合、それらはサンプリングを考慮するように再正規化されます。 それにより、統計への影響が最小限に抑えられます。

サンプリングによってトラフィックとデータ コストが減り、スロットルを回避できます。

## <a name="in-brief"></a>概要:

* サンプリングでは、 *n* 個のレコードのうちの 1 個が保持されて、残りは破棄されます。 たとえば、5 つのイベントのうちの 1 つが保持された場合は 20% のサンプリング レートです。 
* アダプティブ サンプリングは、ASP.NET および ASP.NET Core ソフトウェア開発キット (SDK) のすべての最新バージョンで既定で有効になります。
* サンプリングはまた、手動で設定することもできます。 これは、ポータルの *[使用量と推定コスト] ページ*、ASP.NET SDK の ApplicationInsights.config ファイル、ASP.NET Core SDK でのコード、または Java SDK の ApplicationInsights.xml ファイルで構成できます。
* カスタム イベントを記録しており、一連のイベントが確実にまとめて保持または破棄されるようにする必要がある場合は、それらのイベントに同じ OperationId 値を割り当てる必要があります。
* サンプリング除数 *n* は、`itemCount` プロパティでレコードごとに報告されます。この値は、"要求カウント" または "イベント数" というわかりやすい名前で検索結果に表示されます。 サンプリング操作が行われていない場合は、`itemCount==1` になります。
* Analytics クエリを作成する場合は、 [サンプリングを考慮する](../../azure-monitor/log-query/aggregations.md)必要があります。 具体的には、単純にレコードをカウントするのではなく、 `summarize sum(itemCount)`を使用する必要があります。

## <a name="types-of-sampling"></a>サンプリングの種類

現在は 3 つのサンプリング メソッドがあります。

* **アダプティブ サンプリング**は、ASP.NET/ASP.NET Core アプリの SDK から送信されるテレメトリの量を自動的に調整します。 これは、ASP.NET Web SDK v 2.0.0-beta3 以降、および Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 以降の既定のサンプリングです。  アダプティブ サンプリングは、現在、ASP.NET サーバー側テレメトリでのみ利用できます。

* **固定レート サンプリング**は、ASP.NET、ASP.NET Core、または Java サーバーとユーザーのブラウザーの両方から送信されるテレメトリの量を削減します。 管理者がレートを設定します。 クライアントとサーバーはサンプリングを同期するので、検索では関連のあるページ ビューと要求の間を移動できます。

* **インジェスト サンプリング**は Azure Portal で動作し、 アプリケーションから受信したテレメトリの一部を、設定したサンプリング レートで破棄します。 インジェスト サンプリングはアプリから送信されるテレメトリのトラフィックを削減しませんが、トラフィックを月間のクォータ (上限) 以内で維持するのに役立ちます。 インジェスト サンプリングの主な利点は、アプリを再デプロイすることなくサンプリング レートを設定できることです。 インジェスト サンプリングは、すべてのサーバーおよびクライアントに対して均一に動作します。

アダプティブ サンプリングまたは固定レート サンプリングの実行中は、インジェスト サンプリングが無効になります。


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>ASP.NET/ASP.NET Core Web アプリケーションでのアダプティブ サンプリング

アダプティブ サンプリングは ASP.NET v 2.0.0-beta3 以降の Application Insights SDK、Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 以降で使用でき、既定で有効になります。

アダプティブ サンプリングは、Web サーバー アプリから Application Insights サービス エンドポイントに送信されるテレメトリの量に適用されます。 この量は、指定されたトラフィックの最大レート内に維持されるように自動的に調整され、設定 `MaxTelemetryItemsPerSecond` を使用して制御されます。 アプリケーションが少量のテレメトリを生成している場合 (デバッグ時や使用量が少ない場合など)、量が `MaxTelemetryItemsPerSecond` を下回っている限り、サンプリング プロセッサによって項目がドロップされることはありません。 テレメトリの量が増えてくると、目的の量を達成するようにサンプリング レートが調整されます。

目標の量を達成するため、生成されたテレメトリの一部は破棄されます。 他のサンプリング種類と同様に、このアルゴリズムは関連するテレメトリ項目を維持します。 たとえば、検索でテレメトリを調べているときは、特定の例外に関連する要求を検索できます。

要求レートや例外レートなどのメトリック カウントはサンプリング レートを補正するように調整され、メトリックス エクスプローラーにはほぼ正しい値が表示されます。

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>ASP.NET アプリケーションのためのアダプティブ サンプリングの構成

ASP.NET Core アプリケーションのためのアダプティブ サンプリングの構成については、[こちら](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)を参照してください。 

[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) で、`AdaptiveSamplingTelemetryProcessor` ノードのいくつかのパラメーターを調整できます。 次の図は既定値です。

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    アダプティブ アルゴリズムが **各サーバー ホスト**で目標とするレート。 Web アプリが多数のホストで実行される場合は、Application Insights ポータルのトラフィックの目標レート内に収まるようにこの値を減らします。
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    テレメトリの現在のレートを再評価する間隔。 評価は移動平均として実行されます。 急変しやすいテレメトリの場合は、この間隔を短くすることもできます。
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    サンプリング率が変化してから、どのくらいの時間が経過すると、サンプリング率を下げてキャプチャ データ量を減らすことができるようになるかを指定します。
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    サンプリング率が変化してから、どのくらいの時間が経過すると、サンプリング率を上げてキャプチャ データ量を増やすことができるようになるかを指定します。
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    サンプリング率の変化に合わせて、設定できる最小値。
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    サンプリング率の変化に合わせて、設定できる最大値。
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    移動平均の計算で最新値に割り当てられる重み。 1 以下の値を使用します。 小さい値にすると、急変に対する反応が低いアルゴリズムになります。
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    アプリの起動直後に割り当てられる値。 デバッグ中はこの値を減らさないでください。

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    サンプリング対象にしない型のセミコロン区切りのリスト。 認識される型は、Dependency、Event、Exception、PageView、Request、Trace です。 指定した型はすべてのインスタンスが転送されます。指定されていない型はサンプリングされます。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    サンプリング対象にする型のセミコロン区切りのリスト。 認識される型は、Dependency、Event、Exception、PageView、Request、Trace です。 指定した型はサンプリングされます。他の型のすべてのインスタンスは常に転送されます。


アダプティブ サンプリングを**オフにする**には、applicationinsights-config から AdaptiveSamplingTelemetryProcessor ノードを削除します。

### <a name="alternative-configure-adaptive-sampling-in-code"></a>代替方法: コードでのアダプティブ サンプリングの構成

.config ファイルでサンプリング パラメーターを設定する代わりに、プログラムでこれらの値を設定できます。

1. .config ファイルからすべての `AdaptiveSamplingTelemetryProcessor` ノードを削除します。
2. 次のスニペットを使用してアダプティブ サンプリングを構成します。

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([テレメトリ プロセッサについてはこちらをご覧ください](../../azure-monitor/app/api-filtering-sampling.md#filtering)。)

テレメトリの種類ごとにサンプリング レートを個別に調整したり、特定の種類をまったくサンプリングされないように除外したりすることもできます。 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>ASP.NET Core アプリケーションのためのアダプティブ サンプリングの構成。

ASP.NET Core アプリケーションには `ApplicationInsights.Config` が存在しないため、すべての構成がコードを使用して実行されます。
アダプティブ サンプリングは、すべての ASP.NET Core アプリケーションで既定で有効にされています。 サンプリング動作は無効にしたり、カスタマイズしたりできます。

### <a name="turning-off-adaptive-sampling"></a>アダプティブ サンプリングを無効にする

Application Insights サービスを追加するときに、メソッド ```ConfigureServices``` で `Startup.cs` ファイル内の ```ApplicationInsightsServiceOptions``` を使用して、既定のサンプリング機能を無効にできます。

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

上記のコードによって、サンプリング機能が無効になります。 その他のカスタマイズ オプションを使用したサンプリングを追加するには、次の手順に従います。

### <a name="configure-sampling-settings"></a>サンプリング設定を構成する

サンプリング動作をカスタマイズするには、次に示すように、```TelemetryProcessorChainBuilder``` の拡張メソッドを使用します。

> [!IMPORTANT]
> このメソッドを使用して、サンプリングを構成する場合、AddApplicationInsightsTelemetry() で aiOptions.EnableAdaptiveSampling = false; 設定を使用してください。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**上のメソッドを使用してサンプリングを構成する場合は、AddApplicationInsightsTelemetry() で ```aiOptions.EnableAdaptiveSampling = false;``` 設定を使用するようにしてください。**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>ASP.NET、ASP.NET Core、および Java Web サイトのための固定レート サンプリング

固定レート サンプリングは、Web サーバーおよび Web ブラウザーから送信されるトラフィックを削減します。 アダプティブ サンプリングとは異なり、管理者によって決定された固定レートでテレメトリを削減します。 また、クライアントとサーバーのサンプリングが同期されて、関連する項目が維持されます。たとえば、検索でページ ビューを見るとき、それに関連する要求を検索できます。

他のサンプリング手法と同様に、ここでも関連する項目が保持されます。 HTTP 要求イベントごとに、要求とそれに関連するイベントがまとめて破棄または転送されます。

メトリックス エクスプローラーでは、要求や例外の数などのレートに係数が乗算されて、サンプリング レートに対してほぼ正しくなるように補正されます。

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>ASP.NET での固定レート サンプリングの構成

1. **アダプティブ サンプリングを無効にする**:[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) で、`AdaptiveSamplingTelemetryProcessor` ノードを削除するか、コメントにします。

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **固定レート サンプリング モジュールを有効にします。** 次のスニペットを [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)に追加します。
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>代替方法: サーバー コードでの固定レート サンプリングの有効化
    
    .config ファイルでサンプリング パラメーターを設定する代わりに、プログラムでこれらの値を設定できます。 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([テレメトリ プロセッサについてはこちらをご覧ください](../../azure-monitor/app/api-filtering-sampling.md#filtering)。)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>ASP.NET Core での固定レート サンプリングの構成

1. **アダプティブ サンプリングを無効にする**:```ApplicationInsightsServiceOptions``` を使用して、メソッド ```ConfigureServices``` で変更を行うことができます。

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

2. **固定レート サンプリング モジュールを有効にします。** 次のスニペットに示すように、メソッド ```Configure``` で変更を行うことができます。

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Java での固定レート サンプリングの構成 ###

1. 最新の [Application Insights Java SDK](../../azure-monitor/app/java-get-started.md) を使用して Web アプリケーションをダウンロードして構成する

2. ApplicationInsights.xml ファイルに次のスニペットを追加することによって、**固定レート サンプリング モジュールを有効にします**。

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        </TelemetryProcessors>
    ```

3. プロセッサ タグ "FixedRateSamplingTelemetryProcessor" 内の次のタグを使用して、特定の種類のテレメトリをサンプリングに含めたり、サンプリングから除外したりできます。
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

サンプリングに含めたり、サンプリングから除外したりできるテレメトリの種類は、依存関係、イベント、例外、ページビュー、要求、およびトレースです。

> [!NOTE]
> サンプリング率には、N を整数として 100/N に近い割合を選択します。  サンプリングでは現在、その他の値はサポートされていません。
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>インジェスト サンプリング

この形式のサンプリングは、Web サーバー、ブラウザー、デバイスからのテレメトリが Application Insights サービス エンドポイントに到達した場所で動作します。 アプリから送信されるテレメトリ トラフィックを削減することはありませんが、Application Insights によって処理および維持 (そして課金) される量を削減します。

アプリが頻繁に月間クォータを超えて、SDK ベースのいずれかのサンプリング種類を使用するオプションがない場合は、この種類のサンプリングを使用します。 

[使用量と推定コスト] ページで、サンプリング レートを設定します。

![アプリケーションの [概要] ブレードで、[設定]、[クォータ]、[サンプル] の順にクリックし、サンプリング レートを選択して、[更新] をクリックします。](./media/sampling/04.png)

他のサンプリング種類と同様に、このアルゴリズムは関連するテレメトリ項目を維持します。 たとえば、検索でテレメトリを調べているときは、特定の例外に関連する要求を検索できます。 メトリックはそのような要求のレートをカウントし、例外レートを正しく維持します。

サンプリングによって破棄されたデータ ポイントは、 [連続エクスポート](../../azure-monitor/app/export-telemetry.md)などの Application Insights の機能では使用できません。

SDK ベースのアダプティブ サンプリングまたは固定レート サンプリングの実行中は、インジェスト サンプリングは動作しません。 アダプティブ サンプリングは、Visual Studio で ASP.NET/ASP.NET Core SDK が有効になったときに既定で有効になるか、あるいは Azure Web アプリ拡張機能で、または Status Monitor を使用して有効になります。そのとき、インジェスト サンプリングは無効になります。 SDK でのサンプリング レートが 100% 未満である (つまり、 項目がサンプリングされている) 場合、設定したインジェスト サンプリング レートは無視されます。

> [!WARNING]
> タイルに表示される値は、インジェスト サンプリングに設定した値を示します。 SDK サンプリングの実行中は、この値は実際のサンプリング レートを表しません。
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>JavaScript を使用した Web ページのサンプリング
任意のサーバーから固定レートのサンプリング用に Web ページを構成できます。 

[Application Insights 用に Web ページを構成する](../../azure-monitor/app/javascript.md)場合は、Application Insights ポータルから JavaScript スニペットを入手して、それを変更します (通常、ASP.NET アプリでは _Layout.cshtml を利用できます)。`samplingPercentage: 10,` のような行をインストルメンテーション キーの前に挿入します。

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

サンプリング率には、N を整数として 100/N に近い割合を選択します。  サンプリングでは現在、その他の値はサポートされていません。

サーバーでも固定レート サンプリングを有効にしている場合は、クライアントとサーバーはサンプリングを同期するので、検索で関連のあるページ ビューと要求の間を移動できます。

## <a name="when-to-use-sampling"></a>サンプリングを使用する場合

最新の .NET および .NET Core SDK では、アダプティブ サンプリングが自動的に有効になります。 使っている SDK のバージョンにかかわらず、インジェスト サンプリングを有効にして、収集されたデータを Application Insights でサンプリングできます。

Java SDK では、既定ではどのサンプリングも有効になっていません。 現在、固定レート サンプリングのみがサポートされています。 Java SDK では、アダプティブ サンプリングはサポートされていません。

一般に、ほとんどの中小規模アプリケーションでは、サンプリングは不要です。 最も役立つ診断情報や最も正確な統計は、すべてのユーザー アクティビティからデータを収集することで入手します。 

サンプリングの主な利点:

* アプリが短期間に非常に高いレートのテレメトリを送信すると、Application Insights サービスがデータ ポイントを削減 ("スロットル") する。 
* データ ポイントを、価格レベルの [クォータ](../../azure-monitor/app/pricing.md) 以内に抑える。 
* テレメトリの収集によるネットワーク トラフィックを削減する。 

### <a name="which-type-of-sampling-should-i-use"></a>使用する必要があるサンプリングの種類

**次の場合はインジェスト サンプリングを使用します。**

* テレメトリの月間クォータを超えることが多い場合。
* サンプリングをサポートしていない SDK のバージョン (たとえば、2 より前の ASP.NET バージョン) を使用している場合。
* ユーザーの Web ブラウザーから取得するテレメトリが多すぎる場合。

**次の場合は固定レート サンプリングを使用します。**

* ASP.NET Web サービス バージョン 2.0.0 以降または Java SDK v2.0.1 以降の Application Insights SDK を使用している場合。
* クライアントとサーバーのサンプリングを同期する場合。[検索](../../azure-monitor/app/diagnostic-search.md)でイベントを調査するときに、クライアントとサーバーの関連するイベント単位 (ページ ビュー、HTTP 要求など) で操作できます。
* アプリに適したサンプリング率を確保する場合。 正確なメトリックを取得できる程度に高く、価格クォータとスロットル制限を超えないレートにする必要があります。 

**アダプティブ サンプリングを使用する場合。**

他の形式のサンプリングを使う条件に当てはまらない場合は、アダプティブ サンプリングをお勧めします。 この設定は、ASP.NET/ASP.NET Core サーバー SDK で既定で有効になります。 一定の最小レートに達するまでトラフィックは減らないので、使用頻度の低いサイトは影響を受けません。

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>サンプリングが実行中かどうかを知る方法

適用されている場所に関係なく、実際のサンプリング レートを検出するには、次のように [Analytics クエリ](../../azure-monitor/app/analytics.md) を使用します。

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

いずれかの種類の RetainedPercentage が 100 未満である場合、その項目はサンプリングされています。

**Application Insights は、上で説明したどのサンプリング手法でも、セッション、メトリック、およびパフォーマンス カウンターのテレメトリの種類をサンプリングしません。これらのテレメトリの種類では精度の低下が非常に好ましくないため、これらの種類は常にサンプリングから除外されます**

## <a name="how-does-sampling-work"></a>サンプリングのしくみ

ASP.NET バージョン 2.0.0 以降および Java SDK バージョン 2.0.1 以降の SDK の固定レート サンプリング機能。 アダプティブ サンプリングは、ASP.NET バージョン 2.0.0 以降の SDK の機能です。 インジェスト サンプリングは Application Insights サービスの機能であり、SDK がサンプリングを実行していない場合に有効になります。

サンプリング アルゴリズムでは、削除するテレメトリ項目と、保持するテレメトリ項目を決定します (SDK または Application Insights サービスのいずれで動作しているかに関係なく)。 サンプリングはいくつかのルールに基づいて決定されますが、このルールのねらいは、削減されたデータ セットを使用する場合でも、Application Insights の診断機能を実用的で信頼性の高いものに維持しながら、すべての相互に関連するデータ ポイントはそのままの状態で保持することです。 たとえば、失敗した要求に対してアプリが追加のテレメトリ項目 (この要求からログに記録された例外やトレースなど) を送信した場合、サンプリングでは、この要求とその他のテレメトリを分割することはありません。 すべてをまとめて維持するか、削除するかのどちらかです。 そのため、Application Insights で要求の詳細を表示する場合は、その要求に加えて、関連付けられているテレメトリ項目も常に表示されます。 

サンプリングの判定は、要求の操作 ID に基づいています。つまり、特定の操作に属するすべてのテレメトリ項目が保持されるか、または破棄されるかのどちらかです。 操作 ID が設定されていないテレメトリ項目 (たとえば、HTTP コンテキストのない非同期スレッドから報告されたテレメトリ項目) の場合、サンプリングでは単純に各種類のテレメトリ項目の一定の割合をキャプチャします。 .NET SDK の 2.5.0-beta2 および ASP.NET Core SDK の 2.2.0-beta3 より前では、サンプリングの判定は、"ユーザー" を定義するアプリケーション (つまり、最も一般的な Web アプリケーション) のユーザー ID のハッシュに基づいていました。 ユーザーを定義しない種類のアプリケーション (Web サービスなど) の場合、サンプリングの判定は要求の操作 ID に基づいていました。

テレメトリを表示する場合、Application Insights サービスは、不足しているデータ ポイントを補正するために、収集時に使用したものと同じサンプリング率でメトリックを調整します。 そのため、Application Insights でテレメトリを表示する場合、ユーザーは統計的に正しく、実際の数値に非常に近い近似値を見ていることになります。

近似精度は、構成したサンプリング率に大きく左右されます。 また、多数のユーザーからの同じような要求を大量に処理するアプリケーションの場合は、近似精度が高くなります。 一方で、処理量が膨大ではないアプリケーションの場合、通常はクォータの範囲内ですべてのテレメトリを送信でき、スロットルによるデータの損失もないため、サンプリングは必要ありません。 

> [!WARNING]
> Application Insights は、メトリックとセッションのテレメトリの種類をサンプリングしません。 このようなテレメトリの種類では、精度の低下は望ましくありません。
> 

### <a name="adaptive-sampling"></a>アダプティブ サンプリング

アダプティブ サンプリングの場合、SDK からの現在の転送速度を監視し、目標最大レート以下になるようにサンプリング率を調整するコンポーネントが追加されます。 調整は、定期的に、送信レートの移動平均に基づいて再計算されます。

## <a name="sampling-and-the-javascript-sdk"></a>サンプリングと JavaScript SDK

固定レート サンプリングでは、クライアント側 (JavaScript) SDK とサーバー側 SDK を組み合わせて使用します。 SDK が追加されたページは、サーバー側が "サンプリングに入れる" と判断したユーザーと同じユーザーからのクライアント側テレメトリのみを送信します。 これは、クライアント側とサーバー側の間でユーザー セッションの整合性を維持するためのロジックです。 その結果、Application Insights 内の特定のテレメトリ項目から、このユーザーまたはセッションに関するその他すべてのテレメトリ項目を見つけることができます。 

*"クライアント側とサーバー側のテレメトリに、前述されているような調整済みのサンプルが表示されない場合。"*

* サーバーとクライアントの両方で固定レート サンプリングを有効にしていることを確認してください。
* SDK のバージョンが 2.0 以降であることを確認してください。
* クライアントとサーバーの両方で同じサンプリング率を設定していることを確認してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

*ASP.NET および ASP.NET Core SDK での既定のサンプリング動作はどのようなものですか*

* 上の SDK の最新バージョンのいずれかを使用している場合は、1 秒あたり 5 つのテレメトリ項目のアダプティブ サンプリングが既定で有効になります。
  既定では 2 つの AdaptiveSamplingTelemetryProcessor が追加され、その 1 つがサンプリングにイベントの種類を含め、もう一方がサンプリングからイベントの種類を除外します。 この構成は、SDK がテレメトリ項目をイベントの種類の 5 つのテレメトリ項目と、その他のすべての種類の組み合わせの 5 つのテレメトリ項目に制限することにより、イベントが確実に他のテレメトリの種類とは別にサンプリングされるようにしようと試みることを示します。 イベントは通常、ビジネス テレメトリに使用されるため、多くの場合、診断テレメトリの量によって影響を受けないようにする必要があります。
  
  生成される既定の ApplicationInsights.Config ファイルを次に示します。 前に説明したように、2 つの個別の AdaptiveSamplingTelemetryProcessor ノードが追加され、その 1 つがイベントの種類を除外し、もう一方がそれを含めます。 ASP.NET Core では、まったく同じ既定の動作がコードで有効になります。 この既定の動作を変更するには、このドキュメントの前のセクションの例を使用します。

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

* このサンプリング方法ではメトリックの近似での高レベルの精度が提供されますが、診断にとって重要な、ユーザー、セッション、および要求ごとに診断データを関連付ける機能が使用できなくなります。 そのため、サンプリングには "アプリ ユーザーの X% に関するすべてのテレメトリ項目を収集"、または "アプリ要求の X% に関するすべてのテレメトリを収集" というロジックが適しています。 要求に関連付けられていないテレメトリ項目 (バックグラウンドの非同期処理など) の場合、フォールバックは "テレメトリの種類ごとにすべての項目の X % を収集する" ことです。 

*サンプリング率は、時間経過に伴い変化する可能性がありますか。*

* はい。アダプティブ サンプリングの場合、現在監視されているテレメトリのデータ量に基づいて、サンプリング率が徐々に変化します。

*固定レート サンプリング率を使用している場合、自分のアプリに最適なサンプリング率は、どのようにして確認できますか。*

* 1 つの方法として、アダプティブ サンプリングから始め、決定されたレートを確認したら (前の質問を参照)、そのレートを使って固定レート サンプリングに切り替えます。 
  
    それ以外では、推測するしかありません。 Application Insights での現在のテレメトリ使用状況を分析し、発生しているスロットルを観察して、収集されるテレメトリの量を見積もります。 この 3 つの情報に、選択した価格レベルを合わせて考えると、収集されるテレメトリの量をどれくらい削減すればよいかがわかります。 ただし、ユーザー数の増加やテレメトリ量の何らかの変化によって、見積りが無効になることがあります。

*サンプリング率を低く構成しすぎると、どうなりますか。*

* Application Insights がデータ量の減少に関してデータの視覚化を補正しようとしている場合、極端に低いサンプリング率 (過度にアグレッシブなサンプリング) は、近似精度の低下につながります。 また、低頻度の失敗や遅い要求の一部はサンプリングから除外される場合があるため、診断機能に悪影響を及ぼす可能性もあります。

*サンプリング率を高く構成しすぎると、どうなりますか。*

* サンプリング率を高く構成しすぎた (十分にアグレッシブでない) 場合は、収集されるテレメトリの量が十分に減少しないことになります。 スロットルに関連するテレメトリ データが失われる可能性が残り、超過料金によって Application Insights の使用コストが予定額を超える場合もあります。

*サンプリングはどのようなプラットフォームで使用できますか。*

* インジェスト サンプリングは、SDK がサンプリングを実行していない場合に任意のテレメトリが特定の量を超えると、自動的に実行される場合があります。 この構成は、たとえば、古いバージョンの ASP.NET SDK または以前のバージョンの Java SDK (1.0.10 以前) を使用している場合に機能します。
* ASP.NET SDK バージョン 2.0.0 以上または ASP.NET Core SDK バージョン 2.2.0 以上 (Azure または独自のサーバーのどちらかでホストされている) を使用している場合、既定ではアダプティブ サンプリングが得られますが、上で説明されているように固定レートに切り替えることができます。 固定レート サンプリングの場合、ブラウザー SDK は自動的にサンプル関連のイベントに同期します。 
* Java SDK バージョン 2.0.1 以降を使用している場合は、固定レート サンプリングを有効にするように ApplicationInsights.xml を構成できます。 サンプリングは、既定で無効になっています。 固定レート サンプリングの場合、ブラウザー SDK は自動的にサンプル関連のイベントに同期します。

*常に確認したい頻度の低いイベントがあります。サンプリング モジュールを使わずに確認するにはどうすればよいですか。*

* これを実現する最善の方法は、次に示すように、保持したいテレメトリ項目で `SamplingPercentage` を 100 に設定するカスタムの [TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering) を記述することです。 これにより、すべてのサンプリング手法で、任意のサンプリングに関する考慮事項からこの項目が無視されるようになります。

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>次の手順

* [フィルター](../../azure-monitor/app/api-filtering-sampling.md) を使用して、SDK から送信される情報についてさらに厳密に制御できます。
* [Application Insights によるテレメトリの最適化](https://msdn.microsoft.com/magazine/mt808502.aspx)に関する Developer Network の記事を読みます。
