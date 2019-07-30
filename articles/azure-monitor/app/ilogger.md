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
ms.openlocfilehash: 0691c35661a6d185a6aa5ed3383ad600653359d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058603"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider for .NET Core の ILogger ログ

ASP.NET Core では、さまざまな種類の組み込みおよびサードパーティ製のログ記録プロバイダーで動作するログ記録 API がサポートされています。 ログ記録は、**Log()** またはそのバリエーションを *ILogger* インスタンスで呼び出すことにより行われます。 この記事では、*ApplicationInsightsLoggerProvider* を使用して、コンソールと ASP.NET Core アプリケーションで ILogger ログをキャプチャする方法を示します。 また、この記事では、ApplicationInsightsLoggerProvider を他の Application Insights テレメトリと統合する方法についても説明します。
詳しくは、「[ASP.NET Core でのログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging)」をご覧ください。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

次のいずれかの標準的な方法で通常の Application Insights の監視を有効にすると、[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.0-beta3 (それ以降) では ApplicationInsightsLoggerProvider が既定で有効になります。
- IWebHostBuilder で **UseApplicationInsights** 拡張メソッドを呼び出す 
- IServiceCollection で **AddApplicationInsightsTelemetry** 拡張メソッドを呼び出す

ApplicationInsightsLoggerProvider によってキャプチャされる ILogger ログには、収集される他のテレメトリと同じ構成が適用されます。 他のテレメトリと同じ TelemetryInitializer と TelemetryProcessor のセットを持ち、同じ TelemetryChannel を使用し、同じ方法で相関およびサンプリングされます。 バージョン 2.7.0-beta3 以降を使用している場合は、ILogger ログをキャプチャするために何も行う必要はありません。

既定では、(すべてのカテゴリから) "*警告*" 以上の ILogger ログのみが Application Insights に送信されます。 ただし、[フィルターを適用してこの動作を変更する](#control-logging-level)ことができます。 **Program.cs** または **Startup.cs** から ILogger ログをキャプチャするには、追加の手順が必要です。 (「[ASP.NET Core アプリの Startup.cs と Program.cs から ILogger ログをキャプチャする](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)」をご覧ください。)

以前のバージョンの Microsoft.ApplicationInsights.AspNet SDK を使用している場合、または Application Insights の他の監視を行わずに ApplicationInsightsLoggerProvider だけを使いたい場合は、以下の手順のようにします。

1. NuGet パッケージをインストールします。

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. **Program.cs** を次のように変更します。

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
                   // Providing an instrumentation key here is required if you're using
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

ステップ 2 のコードでは、`ApplicationInsightsLoggerProvider` が構成されます。 次のコードでは、`ILogger` を使用してログを送信する Controller クラスの例を示します。 ログは Application Insights によってキャプチャされます。

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ASP.NET Core アプリの Startup.cs と Program.cs から ILogger ログをキャプチャする

> [!NOTE]
> ASP.NET Core 3.0 以降では、Program.cs および Startup.cs 内に `ILogger` を挿入できなくなりました。 詳細については、 https://github.com/aspnet/Announcements/issues/353 をご覧ください。

新しい ApplicationInsightsLoggerProvider では、アプリケーション スタートアップ パイプラインの早い段階からログをキャプチャできます。 ApplicationInsightsLoggerProvider は Application Insights (バージョン 2.7.0-beta3 以降) で自動的に有効になりますが、パイプラインの後半までインストルメンテーション キーは設定されません。 そのため、**Controller**/その他クラスからのログのみがキャプチャされます。 **Program.cs** および **Startup.cs** 自体以降のすべてのログをキャプチャするには、ApplicationInsightsLoggerProvider のインストルメンテーション キーを明示的に有効にする必要があります。 また、**Program.cs** または **Startup.cs** 自体からログを記録するときは、*TelemetryConfiguration* は完全にはセットアップされません。 したがって、これらのログの構成は InMemoryChannel を使用する最小限のものであり、サンプリングは行われず、標準のテレメトリ初期化子またはプロセッサはありません。

次の例では、**Program.cs** および **Startup.cs** でのこの機能を示します。

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
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>古い ApplicationInsightsLoggerProvider から移行する

2\.7.0-beta2 より前のバージョンの Microsoft.ApplicationInsights.AspNet SDK では、現在は古い形式になっているログ記録プロバイダーがサポートされていました。 このプロバイダーは、ILoggerFactory の **AddApplicationInsights()** 拡張メソッドによって有効にされました。 次の 2 つのステップで、新しいプロバイダーに移行することをお勧めします。

1. ログ記録が二重に行われないよう、*ILoggerFactory.AddApplicationInsights()* の呼び出しを **Startup.Configure()** メソッドから削除します。
2. 新しいプロバイダーでは考慮されないため、コード内のすべてのフィルタリング規則を再適用します。 *ILoggerFactory.AddApplicationInsights()* のオーバーロードでは、最小限の LogLevel またはフィルター関数が取得されました。 新しいプロバイダーでは、フィルタリングはログ記録フレームワーク自体の一部です。 Application Insights プロバイダーでは行われません。 そのため、*ILoggerFactory.AddApplicationInsights()* のオーバーロードによって提供されるすべてのフィルターを削除する必要があります。 そして、「[ログ記録レベルの制御](#control-logging-level)」の説明に従ってフィルタリング規則を提供する必要があります。 *appsettings.json* を使用してログをフィルター処理している場合は、新しいプロバイダーでも引き続き動作します。どちらでも、同じプロバイダー別名 *ApplicationInsights* が使用されています。

まだ古いプロバイダーを使用できます。 (メジャー バージョンが 3.*xx* に変更されるときにだけ削除されます。)ただし、次の理由で、新しいプロバイダーに移行することをお勧めします。

- 以前のプロバイダーには[ログ スコープ](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)のサポートがありません。 新しいプロバイダーでは、スコープからのプロパティが、収集されるテレメトリにカスタム プロパティとして自動的に追加されます。
- アプリケーション スタートアップ パイプラインのはるかに早い段階で、ログをキャプチャできます。 **Program** クラスと **Startup** クラスからのログをキャプチャできるようになっています。
- 新しいプロバイダーでは、フィルタリングがフレームワーク レベル自体で行われます。 コンソールやデバッグといった組み込みプロバイダーなどの他のプロバイダーと同じ方法で、Application Insights プロバイダーへのログのフィルタリングを行うことができます。 また、複数のプロバイダーに同じフィルターを適用することもできます。
- ASP.NET Core (2.0 以降) で[ログ記録プロバイダーを有効にする](https://github.com/aspnet/Announcements/issues/255)推奨される方法は、**Program.cs** 自体の ILoggingBuilder で拡張メソッドを使用することです。

> [!Note]
> 新しいプロバイダーは、NETSTANDARD2.0 以降を対象とするアプリケーションで使用できます。 アプリケーションが .NET Core 1.1 のような古いバージョンの .NET Core または .NET Framework を対象にしている場合は、引き続き古いプロバイダーを使用してください。

## <a name="console-application"></a>コンソール アプリケーション

ILogger のトレースを Application Insights に送信するように構成されたコンソール アプリケーションのコードの例を次に示します。

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
        // Create the DI container.
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

この例では、スタンドアロン パッケージ `Microsoft.Extensions.Logging.ApplicationInsights` を使用しています。 既定では、この構成では、Application Insights にデータを送信するために、"必要最小限" の TelemetryConfiguration が使用されます。 必要最小限とは、InMemoryChannel が使用されるチャネルであることを示します。 サンプリングも標準の TelemetryInitializer もありません。 この動作は、コンソール アプリケーションでは、次の例に示すようにオーバーライドできます。

次の追加パッケージをインストールします。

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

次のセクションでは、**services.Configure<TelemetryConfiguration>()** メソッドを使用して既定の TelemetryConfiguration をオーバーライドする方法を示します。 この例では、`ServerTelemetryChannel` とサンプリングを設定します。 カスタム ITelemetryInitializer を TelemetryConfiguration に追加します。

```csharp
    // Create the DI container.
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

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>ログ記録レベルの制御

ASP.NET Core の *ILogger* インフラには、[ログのフィルタリング](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)を適用するための組み込みメカニズムがあります。 これにより、Application Insights プロバイダーなどの、各登録済みプロバイダーに送信されるログを制御できます。 フィルター処理は、構成 (通常は *appsettings.json* ファイルを使用) またはコードで行うことができます。 この機能は、フレームワーク自体によって提供されます。 Application Insights プロバイダーに固有ではありません。

次の例では、ApplicationInsightsLoggerProvider にフィルター規則を適用します。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>appsettings.json を使用して構成でフィルター規則を作成する

ApplicationInsightsLoggerProvider では、プロバイダーの別名は `ApplicationInsights` です。 *appsettings.json* の次のセクションでは、すべてのカテゴリからの "*警告*" 以上と、"Microsoft" で始まるカテゴリからの "*エラー*" 以上が、`ApplicationInsightsLoggerProvider` に送信されるようにログが構成されます。

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

次のコード スニペットでは、すべてのカテゴリからの "*警告*" 以上と、"Microsoft" で始まるカテゴリからの "*エラー*" 以上が、`ApplicationInsightsLoggerProvider` に送信されるようにログが構成されます。 この構成は、*appsettings.json* の前のセクションと同じです。

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider の古いバージョンと新しいバージョンとは何ですか?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) には組み込みの ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider) が含まれており、**ILoggerFactory** 拡張メソッドを使用して有効にしました。 このプロバイダーは、バージョン 2.7.0-beta2 からは古いものとマークされています。 次のメジャー バージョンの変更で完全に削除されます。 [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) パッケージ自体は古くありません。 要求や依存関係などの監視を有効にするために必要です。

推奨される代替手段は、改善された ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) と、それを有効にするための ILoggerBuilder の拡張メソッドが含まれる、新しいスタンドアロン パッケージ [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) です。

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.0-beta3 では、新しいパッケージに依存関係が取り込まれて、ILogger のキャプチャが自動的に有効になります。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>一部の ILogger ログが Application Insights に 2 回表示されるのはなぜですか?

`ILoggerFactory` で `AddApplicationInsights` を呼び出すことによって、古い (廃止されている) バージョンの ApplicationInsightsLoggerProvider を有効にした場合、重複が発生する可能性があります。 **Configure** メソッドに次のような行があるかどうかを確認し、ある場合はそれを削除してください。

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio からデバッグすると二重のログ記録が発生する場合は、次のように、Application Insights を有効にするコードで `EnableDebugLogger` を *false* に設定します。 この重複と修正は、アプリケーションをデバッグするときにのみ関係します。

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.0-beta3 に更新し、ILogger からのログが自動的にキャプチャされるようになっています。 この機能を完全にオフにするにはどうすればよいですか?

ログ記録をフィルター処理する一般的な方法については、「[ログ記録レベルの制御](../../azure-monitor/app/ilogger.md#control-logging-level)」セクションをご覧ください。 ApplicationInsightsLoggerProvider をオフにするには、`LogLevel.None` を使用します。

**コードの場合:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**構成の場合:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>一部の ILogger ログのプロパティが他と同じではないのはなぜですか?

Application Insights では、他のすべてのテレメトリに使用されるのと同じ TelemetryConfiguration を使用して、ILogger ログのキャプチャと送信が行われます。 ただし、例外があります。 既定では、**Program.cs** または **Startup.cs** からログを記録するときは、TelemetryConfiguration は完全にはセットアップされません。 これらの場所からのログには既定の構成がないため、すべての TelemetryInitializers と TelemetryProcessors は実行されません。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>スタンドアロン パッケージ Microsoft.Extensions.Logging.ApplicationInsights を使用しており、いくつかの追加カスタム テレメトリを手動でログに記録しようと考えています。 どうすればよいですか?

スタンドアロン パッケージを使用すると、`TelemetryClient` が DI コンテナーに挿入されないので、次に示すように、`TelemetryClient` の新しいインスタンスを作成し、ロガー プロバイダーで使われるものと同じ構成を使用する必要があります。 これにより、カスタム テレメトリと、ILogger からのテレメトリのすべてに、同じ構成が使われることが保証されます。

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
> Microsoft.ApplicationInsights.AspNetCore パッケージを使用して Application Insights を有効にする場合は、このコードを変更して、コンストラクター内で直接 `TelemetryClient` を取得します。 例については、[こちらの FAQ](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions) をご覧ください。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger ログからはどのような種類の Application Insights テレメトリが生成されますか? または、Application Insights ではどこで ILogger ログを見ることができますか?

ApplicationInsightsLoggerProvider では、ILogger ログがキャプチャされて、それから TraceTelemetry が作成されます。 ILogger で **Log()** メソッドに Exception オブジェクトを渡した場合、TraceTelemetry の代わりに *ExceptionTelemetry* が作成されます。 これらのテレメトリ項目は、ポータル、分析、Visual Studio ローカル デバッガーなど、Application Insights の他の TraceTelemetry または ExceptionTelemetry と同じ場所で見ることができます。

TraceTelemetry を常に送信したい場合は、次のスニペットを使用します。 ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK をインストールせず、Azure Web Apps 拡張機能を使用して ASP.NET Core アプリケーションの Application Insights を有効にしています。 新しいプロバイダーを使用するにはどうすればよいですか? 

Azure Web Apps の Application Insights 拡張機能では、古いプロバイダーが使用されています。 フィルタリング規則は、アプリケーションの *appsettings.json* ファイルで変更することができます。 新しいプロバイダーを利用するには、SDK で NuGet の依存関係を取得することで、ビルド時のインストルメンテーションを使用します。 拡張機能が新しいプロバイダーを使用するように切り替わったら、この記事は更新されます。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>スタンドアロン パッケージの Microsoft.Extensions.Logging.ApplicationInsights を使用し、**builder.AddApplicationInsights("ikey")** を呼び出すことによって Application Insights プロバイダーを有効にしています。 構成からインストルメンテーション キーを取得するオプションはありますか?


Program.cs と appsettings.json を次のように変更します。

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

   `appsettings.json` からの関連するセクション:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

このコードは、スタンドアロンのログ記録プロバイダーを使用する場合にのみ必要です。 Application Insights の通常の監視では、インストルメンテーション キーは構成パス *ApplicationInsights:Instrumentationkey* から自動的に読み込まれます。 Appsettings.json は次のようになります。

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
