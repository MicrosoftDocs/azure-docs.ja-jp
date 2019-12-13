---
title: ワーカー サービス アプリ (非 HTTP アプリ) 向け Application Insights | Microsoft Docs
description: Application Insights を使用した .NET Core/.NET Framework (非 HTTP アプリ) の監視。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2019
ms.openlocfilehash: 386c171e4785fac2c7fa6da39f249e211f4c660c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893300"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>ワーカー サービス アプリケーション (非 HTTP アプリケーション) 向け Application Insights

Application Insights では、`Microsoft.ApplicationInsights.WorkerService` と呼ばれる新しい SDK がリリースされます。これは、メッセージング、バックグラウンド タスク、コンソール アプリケーションなどの非 HTTP ワークロードに最適です。これらの種類のアプリケーションには、従来の ASP.NET/ASP.NET Core Web アプリケーションのような受信 HTTP 要求の概念がないため、[ASP.NET](asp-net.md) または [ASP.NET Core](asp-net-core.md) アプリケーションの Application Insights パッケージの使用はサポートされていません。

新しい SDK では、単独でテレメトリは収集されません。 代わりに、[DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)、[PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/)、[ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) など、他の既知の Application Insights 自動コレクターを利用できます。この SDK では `IServiceCollection` の拡張メソッドを公開し、テレメトリの収集を有効にして構成します。

## <a name="supported-scenarios"></a>サポートされるシナリオ

[ワーカー サービス向け Application Insights SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) は、実行されている場所や方法に関係なく、非 HTTP アプリケーションに最適です。 アプリケーションが実行されていて、Azure へのネットワーク接続がある場合は、テレメトリを収集することができます。 Application Insights の監視は、.NET Core がサポートされているすべての場所でサポートされます。 このパッケージは、新しく導入された [.NET Core 3.0 ワーカー サービス](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)、[ Asp.Net Core 2.1/2.2 のバックグラウンド タスク](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)、コンソール アプリ (.NET Core/.NET Framework) などで使用できます。

## <a name="prerequisites"></a>前提条件

有効な Application Insights インストルメンテーション キー。 Application Insights にテレメトリを送信するには、このキーが必要です。 インストルメンテーション キーを取得するために新しい Application Insights リソースを作成する必要がある場合は、「[Application Insights リソースの作成](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)」をご覧ください。

## <a name="using-application-insights-sdk-for-worker-services"></a>ワーカー サービス向け Application Insights SDK の使用

1. アプリケーションに [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) パッケージをインストールします。
   次のスニペットは、プロジェクトの `.csproj` ファイルに追加する必要がある変更を示しています。

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.8.2" />
    </ItemGroup>
```

1. インストルメンテーション キーを指定して、`IServiceCollection` で `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` 拡張メソッドを呼び出します。 このメソッドは、アプリケーションの先頭で呼び出す必要があります。 正確な場所は、アプリケーションの種類によって異なります。

1. `serviceProvider.GetRequiredService<TelemetryClient>();` を呼び出すか、コンストラクターの挿入を使用して、依存関係の挿入 (DI) コンテナーから `ILogger` インスタンスまたは `TelemetryClient` インスタンスを取得します。 この手順により、`TelemetryConfiguration` および自動収集モジュールの設定がトリガーされます。

各種類のアプリケーションの具体的な手順については、次のセクションで説明します。

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 ワーカー サービス アプリケーション

完全な例は、[こちら](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)で共有されています

1. [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) をダウンロードしてインストールします
2. Visual Studio の新しいプロジェクト テンプレートまたはコマンド ライン `dotnet new worker` を使用して、新しいワーカー サービス プロジェクトを作成します
3. アプリケーションに [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) パッケージをインストールします。

4. この例のように、`Program.cs` クラスで `CreateHostBuilder()` メソッドに `services.AddApplicationInsightsTelemetryWorkerService();` を追加します。

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. 以下の例に従って、`Worker.cs` を変更します。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. インストルメンテーション キーを設定します。

    インストルメンテーション キーを `AddApplicationInsightsTelemetryWorkerService` に引数として指定することはできますが、インストルメンテーション キーは構成に指定することをお勧めします。 `appsettings.json` でインストルメンテーション キーを指定する方法は、次のコード サンプルのようになります。 発行の間に、`appsettings.json` がアプリケーションのルート フォルダーにコピーされるようにします。

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

または、次のいずれかの環境変数にインストルメンテーション キーを指定します。
`APPINSIGHTS_INSTRUMENTATIONKEY` または `ApplicationInsights:InstrumentationKey`

次に例を示します。`SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
または `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

通常、`APPINSIGHTS_INSTRUMENTATIONKEY` では Web ジョブとして Web Apps にデプロイされるアプリケーションのインストルメンテーション キーを指定します。

> [!NOTE]
> コードで指定されたインストルメンテーション キーは、他のオプションより優先される環境変数 `APPINSIGHTS_INSTRUMENTATIONKEY` より優先されます。

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ホステッド サービスを使用した ASP.NET Core のバックグラウンド タスク

ASP.NET Core 2.1/2.2 アプリケーションでのバックグラウンド タスクの作成方法については、[こちら](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio)のドキュメントで説明されています。

完全な例は、[こちら](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)で共有されています

1. アプリケーションに Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) パッケージをインストールします。
2. この例のように、`ConfigureServices()` メソッドに `services.AddApplicationInsightsTelemetryWorkerService();` を追加します。

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

バックグラウンド タスク ロジックが存在する `TimedHostedService` のコードを次に示します。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. インストルメンテーション キーを設定します。
   前述の .NET Core 3.0 ワーカー サービスの例と同じ `appsettings.json` を使用します。

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework コンソール アプリケーション

この記事の冒頭で説明したように、新しいパッケージを使用して、通常のコンソール アプリケーションからでも Application Insights Telemetry を有効にすることができます。 このパッケージは [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard) を対象としているため、.NET Core 2.0 以上と、.NET Framework 4.7.2 以上のコンソール アプリで使用できます。

完全な例は、[こちら](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)で共有されています

1. アプリケーションに Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) パッケージをインストールします。

2. 以下の例のように Program.cs を変更します。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key must be specified here.
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

このコンソール アプリケーションでも、同じ既定の `TelemetryConfiguration` が使用され、前のセクションの例と同じ方法でカスタマイズできます。

## <a name="run-your-application"></a>アプリケーションを実行する

アプリケーションを実行します。 上記のすべてのワーカー例では、bing.com に対して 1 秒ごとに http 呼び出しを行い、また、ILogger を使用していくつかのログを出力します。 これらの行は、操作を作成するために使用される、`TelemetryClient` の `StartOperation` 呼び出し内にラップされます (この例では "operation" という名前の `RequestTelemetry`)。 Application Insights では、これらの ILogger ログ (既定では警告以上) と依存関係が収集され、親子関係を持つ `RequestTelemetry` に関連付けられます。 プロセス/ネットワーク境界を越える関連付けも機能します。 たとえば、別の監視対象コンポーネントに対して呼び出しが行われた場合、この親にも関連付けられます。

`RequestTelemetry` のこのカスタム操作は、一般的な Web アプリケーションでの受信 Web 要求に相当するものと考えることができます。 操作を使用する必要はありませんが、[Application Insights の関連付けデータ モデル](https://docs.microsoft.com/azure/azure-monitor/app/correlation)の場合、つまり、`RequestTelemetry` が親操作として機能し、また、ワーカー イテレーション内で生成されるすべてのテレメトリが論理的に同じ操作に属しているものとして扱われる場合に最適です。 また、この方法では、(自動および手動で) 生成されるすべてのテレメトリの `operation_id` が確実に同じになります。 サンプリングは `operation_id` に基づいているため、サンプリング アルゴリズムでは、単一のイテレーションからのすべてのテレメトリが保持または削除されます。

Application Insights によって自動的に収集されるすべてのテレメトリを以下にリストします。

### <a name="live-metrics"></a>ライブ メトリック

[Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) を使用すると、Application Insights の監視が正しく構成されているかどうかをすばやく確認できます。 ポータルおよび分析でテレメトリの表示が始まるまで数分かかることがありますが、Live Metrics では、実行中のプロセスの CPU 使用率がほぼリアルタイムで示されます。 また、要求、依存関係、トレースなどの他のテレメトリも表示できます。

### <a name="ilogger-logs"></a>ILogger ログ

重大度が `Warning` 以上の `ILogger` で出力されたログは、自動的にキャプチャされます。 Application Insights によってキャプチャされるログ レベルをカスタマイズする場合は、[ILogger のドキュメント](ilogger.md#control-logging-level)に従ってください。

### <a name="dependencies"></a>依存関係

依存関係の収集は既定で有効になっています。 [こちら](asp-net-dependencies.md#automatically-tracked-dependencies)の記事では、自動収集される依存関係について説明されており、手動で追跡するための手順も含まれます。

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` は既定で有効になっており、.NET Core 3.0 アプリから既定のカウンター セットが収集されます。 [EventCounter](eventcounters.md) チュートリアルには、収集される既定のカウンター セットがリスト表示されています。 また、リストのカスタマイズについても説明されています。

### <a name="manually-tracking-additional-telemetry"></a>追加のテレメトリの手動による追跡

SDK では、上記の説明のとおり、テレメトリが自動的に収集されますが、ほとんどの場合、ユーザーが Application Insights サービスに追加のテレメトリを送信する必要があります。 追加のテレメトリを追跡する場合は、依存関係の挿入から `TelemetryClient` のインスタンスを取得し、サポートされている `TrackXXX()` [API ](api-custom-events-metrics.md) メソッドのいずれかを呼び出すことをお勧めします。 もう 1 つの一般的なユース ケースは、[操作のカスタム追跡](custom-operations-tracking.md)です。 この方法は、上記のワーカーの例に示されています。

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK を構成する

ワーカー サービス SDK で使用される既定の `TelemetryConfiguration` は、ASP.NET または ASP.NET Core アプリケーションで使用される自動構成に似ています (`HttpContext` からのテレメトリを強化するために使用される TelemetryInitializer を除く)。

ワーカー サービス向け Application Insights SDK をカスタマイズして、既定の構成を変更することができます。 Application Insights ASP.NET Core SDK のユーザーであれば、ASP.NET Core の組み込みの[依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を使用する構成の変更に慣れている場合があります。 また、WorkerService SDK は同様の原則に基づいています。 以下の詳しい説明のとおり、`IServiceCollection` で適切なメソッドを呼び出して、`ConfigureServices()` セクションのほとんどすべての構成変更を行います。

> [!NOTE]
> この SDK を使用するときは、`TelemetryConfiguration.Active` の変更による構成の変更がサポートされず、変更は反映されません。

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions を使用する

次の例のように `ApplicationInsightsServiceOptions` を `AddApplicationInsightsTelemetryWorkerService` に渡すことで、いくつかの一般的な設定を変更できます。

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

この SDK の `ApplicationInsightsServiceOptions` は、ASP.NET Core SDK の `Microsoft.ApplicationInsights.AspNetCore.Extensions` ではなく、名前空間 `Microsoft.ApplicationInsights.WorkerService` にあることに注意してください。

`ApplicationInsightsServiceOptions` でよく使用される設定

|Setting | 説明 | Default
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics 機能を有効または無効にします | true
|EnableAdaptiveSampling | アダプティブ サンプリングを有効または無効にします | true
|EnableHeartbeat | ハートビート機能を有効または無効にします。この機能は、"HeartBeatState" という名前のカスタム メトリックを、.NET バージョン、Azure 環境情報 (該当する場合) などのランタイムに関する情報と共に定期的に (既定では 15 分) 送信します。 | true
|AddAutoCollectedMetricExtractor | AutoCollectedMetrics エクストラクターを有効または無効にします。これは、サンプリングが行われる前に要求/依存関係に関する事前に集計されたメトリックを送信する TelemetryProcessor です。 | true

最新の一覧については、[`ApplicationInsightsServiceOptions` の構成可能な設定](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)を参照してください。

### <a name="sampling"></a>サンプリング

ワーカー サービス向け Application Insights SDK では、固定レートとアダプティブ サンプリングの両方がサポートされます。 アダプティブ サンプリングは、既定で有効になっています。 ワーカー サービス用のサンプリングの構成は、[ASP.NET Core アプリケーション](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)の場合と同じように行われます。

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers の追加

すべてのテレメトリと共に送信されるプロパティを定義する場合は、[テレメトリ初期化子](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer)を使用します。

新しい `TelemetryInitializer` を `DependencyInjection` コンテナーに追加すると、SDK によって、それらが `TelemetryConfiguration` に自動的に追加されます。

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers の削除

テレメトリ初期化子は既定で存在します。 すべてまたは特定のテレメトリ初期化子を削除するには、`AddApplicationInsightsTelemetryWorkerService()` を呼び出した "*後*" で、次のサンプル コードを使用します。

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>テレメトリ プロセッサを追加する

拡張メソッド `AddApplicationInsightsTelemetryProcessor` を `IServiceCollection` で使用することで、カスタム テレメトリ プロセッサを `TelemetryConfiguration` に追加できます。 [高度なフィルター処理シナリオ](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)でテレメトリ プロセッサを使用して、Application Insights サービスに送信するテレメトリに何を含めて何を除外するかを、より直接的に制御できるようにします。 次の例を使用してください。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>既定の TelemetryModules の構成または削除

Application Insights では、テレメトリ モジュールを使用して、手動の追跡を必要とせずに特定のワークロードに関するテレメトリが自動収集されます。

次の自動収集モジュールが既定で有効になります。 これらのモジュールでは、自動的にテレメトリが収集されます。 それらを無効にするか構成して、既定の動作を変更できます。

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

既定の `TelemetryModule` を構成するには、下の画像のように、拡張メソッド `ConfigureTelemetryModule<T>` を `IServiceCollection` で使用します。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>テレメトリ チャネルの構成

既定のチャネルは `ServerTelemetryChannel` です。 次の例のようにしてオーバーライドできます。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>テレメトリを動的に無効にする

テレメトリを条件に基づき、動的に無効にする場合、コードの任意の場所に ASP.NET Core 依存関係挿入コンテナーを持つ `TelemetryConfiguration` インスタンスを解決し、それに `DisableTelemetry` フラグを設定できます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>自動的に収集されないテレメトリを追跡するにはどうすればよいですか?

コンストラクター インジェクションを使用して `TelemetryClient` のインスタンスを取得し、そのインスタンスで必須の `TrackXXX()` メソッドを呼び出します。 新しい `TelemetryClient` インスタンスを作成することはお勧めできません。 `TelemetryClient` のシングルトン インスタンスが `DependencyInjection` コンテナーに既に登録されており、それによって `TelemetryConfiguration` がテレメトリの残りの部分と共有されます。 新しい `TelemetryClient` インスタンスの作成は、残りのテレメトリとは別の構成が必要な場合にのみ推奨されます。

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Visual Studio IDE を使用して、ワーカー サービス プロジェクトに Application Insights をオンボードすることはできますか?

現在、Visual Studio IDE のオンボードは ASP.NET/ASP.NET Core アプリケーションでのみサポートされています。 このドキュメントは、Visual Studio のリリースでワーカー サービス アプリケーションのオンボードがサポートされるようになったときに更新されます。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Status Monitor などのツールを利用して Application Insights 監視を有効にできますか?

No. 現在、[Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) と [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) では、ASP.NET 4.x のみがサポートされます。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux でアプリケーションを実行する場合、すべての機能がサポートされますか?

はい。 この SDK の機能サポートは、次の例外を除き、すべてのプラットフォームで同じです。

* パフォーマンス カウンターは、Windows でのみサポートされます。ただし、Live Metrics に表示されるプロセス CPU/メモリは例外です。
* `ServerTelemetryChannel` が既定で有効になっていても、アプリケーションが Linux または MacOS で実行されているときは、ネットワークに問題がある場合に、チャネルによってテレメトリを一時的に保持するためのローカル ストレージ フォルダーが自動的に作成されることはありません。 この制限のため、ネットワークやサーバーに一時的に問題が発生すると、テレメトリが失われます。 この問題を回避するには、チャネル用のローカル フォルダーを構成します。

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>サンプル アプリケーション

[.NET Core コンソール アプリケーション](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) .NET Core (2.0 以上) または .NET Framework (4.7.2 以上) で記述されたコンソール アプリケーションを使用している場合は、このサンプルを使用します

[HostedService を使用した ASP .NET Core バックグラウンド タスク](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Asp.Net Core 2.1/2.2 を使用していて、[こちら](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)の公式のガイダンスに従ってバックグラウンド タスクを作成する場合は、このサンプルを使用します

[.NET Core 3.0 ワーカー サービス](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) [こちら](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)の公式のガイダンスに従って .NET Core 3.0 ワーカー サービス アプリケーションを作成した場合は、このサンプルを使用します

## <a name="open-source-sdk"></a>オープンソース SDK

[コードを読んで協力してください。](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="next-steps"></a>次の手順

* [API を使用](../../azure-monitor/app/api-custom-events-metrics.md)して、アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。
* [自動的に追跡されない追加の依存関係を追跡します](../../azure-monitor/app/auto-collect-dependencies.md)。
* [自動収集されたテレメトリを強化またはフィルター処理します](../../azure-monitor/app/api-filtering-sampling.md)。
* [ASP.NET Core での依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。
