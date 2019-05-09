---
title: ILogger を使用して Azure Application Insights 内の .NET トレース ログを探索する | Microsoft Docs
description: ASP.NET Core とコンソール アプリケーションで Azure Application Insights の ILogger プロバイダーを使用するサンプルです。
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608287"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider for .NET Core の ILogger ログ

ASP.NET Core では、さまざまな種類の組み込みおよびサードパーティ製のログ記録プロバイダーで動作するログ記録 API がサポートされています。 ログ記録は、Log() またはそのバリエーションを `ILogger` インスタンスで呼び出すことにより行われます。 この記事では、コンソール アプリケーションと ASP.NET Core アプリケーションの両方で、`ApplicationInsightsLoggerProvider` を使用して `ILogger` ログをキャプチャする方法を示します。 また、この記事では、`ApplicationInsightsLoggerProvider` を他の Application Insights テレメトリと統合する方法についても説明します。
ASP.NET Core でのログ記録について詳しくは、[こちらの記事](https://docs.microsoft.com/aspnet/core/fundamentals/logging)をご覧ください。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.0-beta3 以降では、IWebHostBuilder で `UseApplicationInsights` 拡張メソッドを呼び出すか、IServiceCollection で `AddApplicationInsightsTelemetry` 拡張メソッドを呼び出す、いずれかの標準的な方法を使用して、Application Insights の通常の監視を有効にすると、`ApplicationInsightsLoggerProvider` が既定で有効になります。 `ApplicationInsightsLoggerProvider` によってキャプチャされる `ILogger` ログは、収集される他のテレメトリと同じ構成の対象になります。 つまり  他のすべてのテレメトリと同じ `TelemetryInitializer` および `TelemetryProcessor` のセットを持ち、同じ `TelemetryChannel` を使用し、同じ方法で関連付けおよびサンプリングされます。  このバージョン以降の SDK を使用している場合は、`ILogger` ログをキャプチャするために何も行う必要はありません。

既定では、(すべてのカテゴリの) `Warning` 以上の `ILogger` ログだけが、Application Insights に送信されます。 [こちら](#control-logging-level)で示すように、フィルターを適用することでこの動作を変更できます。 また、[こちら](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications)で示されているように、`Program.cs` または `Startup.cs` から `ILogger` ログをキャプチャする場合は、追加の手順が必要です。

以前のバージョンの Microsoft.ApplicationInsights.AspNet SDK を使用している場合、または Application Insights の他の監視を行わずに ApplicationInsightsLoggerProvider だけを使いたい場合は、以下の手順のようにします。

1. NuGet パッケージをインストールします。

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. `Program.cs` を次のように変更します

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
            {
                // Providing an instrumentation key here is required if you are using
                // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                // or if you want to capture logs from early in the application startup
                // pipeline from Startup.cs or Program.cs itself.
                builder.AddApplicationInsights("ikey");

                // Optional: Apply filters to control what logs are sent to Application Insights.
                // The following configures LogLevel Information or above to be sent to
                // Application Insights for all categories.
                builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                 ("", LogLevel.Information);
            }
        );
}
```

上記のコードでは、`ApplicationInsightsLoggerProvider` が構成されます。 `ILogger` を使用してログを送信する Controller クラスの例を次に示します。ログは、Application Insights によってキャプチャされます。

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>ASP.NET Core アプリケーションの Program.cs および Startup.cs からの ILogger ログをキャプチャする

新しい ApplicationInsightsLoggerProvider では、アプリケーション スタートアップ パイプラインの早い段階からのログをキャプチャできます。 Application Insights (2.7.0-beta3 以降) では ApplicationInsightsLoggerProvider が自動的に有効になりますが、パイプラインの終盤になるまでインストルメンテーション キーが設定されないため、Controller/その他のクラスからのログだけがキャプチャされます。 `Program.cs` および `Startup.cs` 自体から始めてすべてのログをキャプチャするには、インストルメンテーション キーで ApplicationInsightsLoggerProvider を明示的に有効にする必要があります。 また、注意すべき重要な点とし、`Program.cs` または `Startup.cs` 自体から何かをログ記録するときは、`TelemetryConfiguration` は完全には設定されないので、それらのログでは必要最小限の構成が使用されます。つまり、InMemoryChannel が使用され、サンプリングは行われず、標準のテレメトリ初期化子またはプロセッサは使用されません。

この機能を使用する `Program.cs` と `Startup.cs` の例を次に示します。

#### <a name="example-programcs"></a>Program.cs の例

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Startup.cs の例

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>古い ApplicationInsightsLoggerProvider から移行する

2.7.0-beta2 より前のバージョンの Microsoft.ApplicationInsights.AspNet SDK では、現在は古い形式になっているログ記録プロバイダーがサポートされていました。 このプロバイダーは、`ILoggerFactory` の `AddApplicationInsights()` 拡張メソッドで有効にされました。 このプロバイダーは古いものなので、新しいプロバイダーに移行することをお勧めします。 移行には 2 つのステップが含まれます。

1. ログ記録が二重に行われないよう、ILoggerFactory.AddApplicationInsights() の呼び出しを `Startup.Configure()` から削除します。
2. 新しいプロバイダーでは考慮されないため、コード内のすべてのフィルタリング規則を再適用します。 ILoggerFactory.AddApplicationInsights() のオーバーロードでは、最小限の LogLevel またはフィルター関数が取得されました。 新しいプロバイダーでは、フィルタリングはログ記録フレームワーク自体の一部であり、Application Insights プロバイダーでは行われません。 そのため、`ILoggerFactory.AddApplicationInsights()` のオーバーロードで提供されていたフィルターは削除する必要があり、[こちら](#control-logging-level)の説明に従ってフィルタリング規則を提供する必要があります。 `appsettings.json` を使用してログをフィルター処理している場合は、新しいプロバイダーでも引き続き動作します。どちらでも、同じプロバイダー別名 **ApplicationInsights** が使用されています。

古いプロバイダーもまだ使用できますが (現在は古くなっており、3.xx へのメジャー バージョンの変更だけで削除されます)、以下の理由から新しいプロバイダーへの移行を強くお勧めします。

1. 以前のプロバイダーでは、[スコープ](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)のサポートが不足していました。 新しいプロバイダーでは、スコープからのプロパティが、収集されるテレメトリにカスタム プロパティとして自動的に追加されます。
2. アプリケーション スタートアップ パイプラインのはるかに早い段階で、ログをキャプチャできます。 つまり  Program クラスと Startup クラスからのログをキャプチャできるようになっています。
3. 新しいプロバイダーでは、フィルタリングがフレームワーク レベル自体で行われます。 コンソールやデバッグといった組み込みプロバイダーなどの他のプロバイダーとまったく同じ方法で、Application Insights プロバイダーへのログのフィルタリングを行うことができます。 また、複数のプロバイダーに同じフィルターを適用することもできます。
4. ASP.NET Core (2.0 以降) でログ記録プロバイダーを有効にする[推奨される](https://github.com/aspnet/Announcements/issues/255)方法は、`Program.cs` 自体の ILoggingBuilder で拡張メソッドを使用することです。

> [!Note]
> 新しいプロバイダーは、`NETSTANDARD2.0` 以降を対象とするアプリケーションで使用できます。 アプリケーションが .NET Core 1.1 のような古いバージョンの .NET Core または .NET Framework を対象にしている場合は、引き続き古いプロバイダーを使用してください。

## <a name="console-application"></a>コンソール アプリケーション

次のコードでは、`ILogger` トレースを Application Insights に送信するように構成されたコンソール アプリケーションの例を示します。

インストール済みパッケージ:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

上の例では、スタンドアロン パッケージ `Microsoft.Extensions.Logging.ApplicationInsights` が使用されています。 既定では、この構成では、Application Insights にデータを送信するために、必要最小限の `TelemetryConfiguration` が使用されます。 必要最小限とは、使用されるチャネルは `InMemoryChannel` であり、サンプリングも標準 TelemetryInitializer も実行されないことを意味します。 この動作は、コンソール アプリケーションでは、次の例に示すようにオーバーライドできます。

次の追加パッケージをインストールします。

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

次のセクションでは、`services.Configure<TelemetryConfiguration>()` メソッドを使用して既定の `TelemetryConfiguration` をオーバーライドする方法を示します。 この例では、`ServerTelemetryChannel` のサンプリングを設定し、カスタム `ITelemetryInitializer` を `TelemetryConfiguration` に追加します。

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>ログ記録レベルの制御

ASP.NET Core の `ILogger` インフラには、ログの[フィルター処理](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)を適用する組み込みのメカニズムがあります。これにより、ユーザーは Application Insights プロバイダーなどの各登録済みプロバイダーに送信されるログを制御することができます。 このフィルター処理は、構成 (通常は `appsettings.json` ファイルを使用) またはコードで行うことができます。 この機能はフレームワーク自体によって提供されるもので、Application Insights プロバイダーに固有ではありません。

ApplicationInsightsLoggerProvider にフィルター規則を適用する例を次に示します。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>appsettings.json を使用して構成でフィルター規則を作成する

ApplicationInsightsLoggerProvider では、プロバイダーの別名は `ApplicationInsights` です。 次に示す `appsettings.json` のセクションでは、すべてのカテゴリからからのログ `Warning` 以上、および "Microsoft" で始まるカテゴリからの `Error` 以上を、`ApplicationInsightsLoggerProvider` に送信するように構成されています。

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>コードでフィルター規則を作成する

次のコード スニペットでは、すべてのカテゴリからからのログ `Warning` 以上、および "Microsoft" で始まるカテゴリからの `Error` 以上を、`ApplicationInsightsLoggerProvider` に送信するように構成されています。 この構成は、上の `appsettings.json` で行った構成と同じです。

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

*1.ApplicationInsightsLoggerProvider の古いものと、新しいものとは何ですか?*

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) には組み込みの ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider) が付属しており、ILoggerFactory 拡張メソッドを使用して有効にしました。 このプロバイダーは、2.7.0-beta2 以降では古いものとマークされており、次のメジャー バージョン変更で完全に削除されます。 [こちら](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)のパッケージ自体は古くなく、要求や依存関係などの監視を有効にするために必要です。

* 推奨される代替手段は、改善された ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) と、それを有効にするための ILoggerBuilder の拡張メソッドが含まれる、新しいスタンドアロン パッケージ [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) です。

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.0-beta3 以降では、上記のパッケージに依存関係が取り込まれて、`ILogger` のキャプチャが自動的に有効になります。

*2.一部の `ILogger` ログが Application Insights に 2 回表示されます。*

* `ILoggerFactory` で `AddApplicationInsights` を呼び出して `ApplicationInsightsLoggerProvider` の以前の (古い) バージョンを有効にしている場合、このような重複が発生する可能性があります。 `Configure` メソッドに次のような行があるかどうかを確認し、ある場合はそれを削除してください。

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Visual Studio からデバッグしているときに二重のログ記録が発生する場合は、Application Insights を有効にするために使用されているコードを、次のように `EnableDebugLogger` を false に設定するよう変更します。 この重複の問題と修正は、アプリケーションをデバッグするときにのみ関係します。

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3.[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.0-beta3 に更新し、`ILogger` からのログが自動的にキャプチャされるようになっています。どうすればこの機能を完全にオフにできますか?*

* ログの一般的なフィルター方法については、[こちら](../../azure-monitor/app/ilogger.md#control-logging-level)のセクションをご覧ください。 ApplicationInsightsLoggerProvider をオフにするには、その `LogLevel.None` を使用します。

  コードの場合

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  構成の場合

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4.`ILogger` の一部のログのプロパティが他とは異なります。*

* Application Insights では、他のすべてのテレメトリに使用されているものと同じ `TelemetryConfiguration` を使用して、`ILogger` のログがキャプチャされて送信されます。 この規則には例外があります。 `Program.cs` または `Startup.cs` 自体から何らかのログを記録するときは、既定の `TelemetryConfiguration` は完全には設定されないため、これらの場所からのログには既定の構成が含まれず、したがってすべての `TelemetryInitializer` と `TelemetryProcessor` は実行されません。

*5.スタンドアロン パッケージ Microsoft.Extensions.Logging.ApplicationInsights を使用しており、いくつかの追加カスタム テレメトリを手動でログに記録しようと考えています。どうすればよいですか?*

* スタンドアロン パッケージを使用すると、`TelemetryClient` が DI コンテナーに挿入されないので、ユーザーは、次に示すように、ロガー プロバイダーで使われるものと同じ構成を使って、`TelemetryClient` の新しいインスタンスを作成する必要があります。 これにより、カスタム テレメトリと、ILogger からキャプチャされるテレメトリのすべてに、同じ構成が使われることが保証されます。

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> なお、パッケージ Microsoft.ApplicationInsights.AspNetCore を使って Application Insights を有効にする場合は、上記の例を、コンストラクターで直接 `TelemetryClient` を取得するように変更する必要があることに注意してください。 完全な例については、[こちら](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions)をご覧ください。


*6.`ILogger` ログからはどのような Application Insights テレメトリの種類が生成されますか? または、Application Insights のどこで `ILogger` ログを表示できますか?*

* ApplicationInsightsLoggerProvider では、`ILogger` ログがキャプチャされて、それから `TraceTelemetry` が作成されます。 ILogger で Log() メソッドに Exception オブジェクトを渡した場合、`TraceTelemetry` の代わりに `ExceptionTelemetry` が作成されます。 これらのテレメトリ項目は、ポータル、分析、Visual Studio ローカル デバッガーなど、Application Insights の他の `TraceTelemetry` または `ExceptionTelemetry` と同じ場所で見ることができます。
常に `TraceTelemetry` を送信する場合は、スニペット ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);``` を使用します。

*7.SDK をインストールせず、Azure Web アプリ拡張機能を使用して ASP.NET Core アプリケーションの Application Insights を有効にしています。新しいプロバイダーを使用するにはどうすればよいですか?*

* Azure Web アプリの Application Insights 拡張機能では、古いプロバイダーが使用されています。 フィルタリング規則は、アプリケーションの `appsettings.json` で変更できます。 新しいプロバイダーを利用したい場合は、SDK で NuGet の依存関係を取得することで、ビルド時のインストルメンテーションを使用します。 拡張機能が新しいプロバイダーを使用するように切り替わったら、このドキュメントは更新されます。

*8.スタンドアロン パッケージの Microsoft.Extensions.Logging.ApplicationInsights を使用し、builder.AddApplicationInsights("ikey") を呼び出すことによって Application Insights プロバイダーを有効にしています。構成からインストルメンテーション キーを取得するオプションはありますか?*


* 次に示すように、`Program.cs` と `appsettings.json` を変更します。

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging((hostingContext, logging) =>
            {
                // hostingContext.HostingEnvironment can be used to determine environments as well.
                var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                logging.AddApplicationInsights(appInsightKey);
            });
}
```

`appsettings.json` からの関連するセクション

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

上記のコードは、スタンドアロンのログ記録プロバイダーを使用する場合にのみ必要です。 Application Insights の通常の監視では、インストルメンテーション キーは構成パス `ApplicationInsights:Instrumentationkey` から自動的に読み込まれ、`appsettings.json` は以下のようになります。

```json
{
  "ApplicationInsights":
    {
        "Instrumentationkey":"putrealikeyhere"
    }
}
```

## <a name="next-steps"></a>次の手順

各項目の詳細情報

* [ASP.NET Core でのログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Application Insights での .NET トレース ログ](../../azure-monitor/app/asp-net-trace-logs.md)
