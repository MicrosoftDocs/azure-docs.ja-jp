---
title: ILogger を使用して .NET トレース ログを探索する - Azure Application Insights
description: ASP.NET Core とコンソール アプリケーションで Azure Application Insights の ILogger プロバイダーを使用するサンプルです。
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e7d0bd6f7d93eac944e0d53a8e898463bf9887f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592270"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>Microsoft.Extension.Logging の ApplicationInsightsLoggerProvider

この記事では、コンソール アプリケーションと ASP.NET Core アプリケーションで、`ApplicationInsightsLoggerProvider` を使用して `ILogger` ログをキャプチャする方法を示します。
ログについて詳しくは、「[ASP.NET Core でのログ記録](/aspnet/core/fundamentals/logging)」をご覧ください。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

ApplicationInsights が[コード](./asp-net-core.md)または[コードレス](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) アプローチを使用して構成されている場合に、`ApplicationInsightsLoggerProvider` は、ASP.NET Core アプリケーションで既定で有効になります。

既定で、(すべての [カテゴリ](/aspnet/core/fundamentals/logging/#log-category)から) *警告* 以上の `ILogger` ログのみが既定で Application Insights に送信されます。 ただし、[この動作はカスタマイズする](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection)ことができます。 **Program.cs** または **Startup.cs** から ILogger ログをキャプチャするには、追加の手順が必要です。 (「[ASP.NET Core アプリの Startup.cs と Program.cs から ILogger ログをキャプチャする](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)」をご覧ください。)

Application Insights のほかの監視を使用せずに `ApplicationInsightsLoggerProvider` のみを使用する場合は、次の手順に従います。

1. NuGet パッケージをインストールします。

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. `Program.cs` を次のように変更します。

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
                   builder.AddApplicationInsights("put-actual-ikey-here");

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
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>ASP.NET Core アプリの Startup.cs と Program.cs から ILogger ログをキャプチャする

> [!NOTE]
> ASP.NET Core 3.0 以降では、Program.cs および Startup.cs 内に `ILogger` を挿入できなくなりました。 詳細については、 https://github.com/aspnet/Announcements/issues/353 をご覧ください。

`ApplicationInsightsLoggerProvider` では、アプリケーション起動の早期からログをキャプチャできます。 ApplicationInsightsLoggerProvider は Application Insights (バージョン 2.7.1 以降) で自動的に有効になりますが、インストルメンテーション キーはパイプライン内の後の方まで設定されません。 そのため、**Controller**/その他クラスからのログのみがキャプチャされます。 **Program.cs** および **Startup.cs** 自体以降のすべてのログをキャプチャするには、ApplicationInsightsLoggerProvider のインストルメンテーション キーを明示的に有効にする必要があります。 また、**Program.cs** または **Startup.cs** 自体からログを記録するときは、*TelemetryConfiguration* は完全にはセットアップされません。 したがって、これらのログの構成は [InMemoryChannel](./telemetry-channels.md) を使用する最小限のものであり、[サンプリング](./sampling.md)は行われず、標準の[テレメトリ初期化子またはプロセッサ](./api-filtering-sampling.md)はありません。

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
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>古い ApplicationInsightsLoggerProvider から移行する

2\.7.1 より前の Microsoft.ApplicationInsights.AspNet SDK バージョンは、現在は廃止されているログ プロバイダーをサポートしていました。 このプロバイダーは、ILoggerFactory の **AddApplicationInsights()** 拡張メソッドによって有効にされました。 次の 2 つのステップで、新しいプロバイダーに移行することをお勧めします。

1. ログ記録が二重に行われないよう、*ILoggerFactory.AddApplicationInsights()* の呼び出しを **Startup.Configure()** メソッドから削除します。
2. 新しいプロバイダーでは考慮されないため、コード内のすべてのフィルタリング規則を再適用します。 *ILoggerFactory.AddApplicationInsights()* のオーバーロードでは、最小限の LogLevel またはフィルター関数が取得されました。 新しいプロバイダーでは、フィルタリングはログ記録フレームワーク自体の一部です。 Application Insights プロバイダーでは行われません。 そのため、*ILoggerFactory.AddApplicationInsights()* のオーバーロードによって提供されるすべてのフィルターを削除する必要があります。 そして、「[ログ記録レベルの制御](#control-logging-level)」の説明に従ってフィルタリング規則を提供する必要があります。 *appsettings.json* を使用してログをフィルター処理している場合は、新しいプロバイダーでも引き続き動作します。どちらでも、同じプロバイダー別名 *ApplicationInsights* が使用されています。

まだ古いプロバイダーを使用できます。 (メジャー バージョンが 3.*xx* に変更されるときにだけ削除されます。)ただし、次の理由で、新しいプロバイダーに移行することをお勧めします。

- 以前のプロバイダーには[ログ スコープ](/aspnet/core/fundamentals/logging#log-scopes)のサポートがありません。 新しいプロバイダーでは、スコープからのプロパティが、収集されるテレメトリにカスタム プロパティとして自動的に追加されます。
- アプリケーション スタートアップ パイプラインのはるかに早い段階で、ログをキャプチャできます。 **Program** クラスと **Startup** クラスからのログをキャプチャできるようになっています。
- 新しいプロバイダーでは、フィルタリングがフレームワーク レベル自体で行われます。 コンソールやデバッグといった組み込みプロバイダーなどの他のプロバイダーと同じ方法で、Application Insights プロバイダーへのログのフィルタリングを行うことができます。 また、複数のプロバイダーに同じフィルターを適用することもできます。
- ASP.NET Core (2.0 以降) で [ログ記録プロバイダーを有効にする](https://github.com/aspnet/Announcements/issues/255)推奨される方法は、**Program.cs** 自体の ILoggingBuilder で拡張メソッドを使用することです。

> [!Note]
> 新しいプロバイダーは、NETSTANDARD2.0 以降を対象とするアプリケーションで使用できます。 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.14.0 以降では、新しいプロバイダーは .NET Framework NET461 以降を対象とするアプリケーションでも使用できます。 アプリケーションが .NET Core 1.1 のような古いバージョンの .NET Core を対象としている場合、または NET46 より前の .NET Framework を対象としている場合は、引き続き古いプロバイダーを使用してください。

## <a name="console-application"></a>コンソール アプリケーション

> [!NOTE]
> 任意のコンソール アプリケーションに対して Application Insights (ILogger やその他の Application Insights テレメトリ) を有効にするために使用できる、[Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) と呼ばれる新しい Application Insights SDK が存在します。 このパッケージおよび関連する手順は、[ここ](./worker-service.md)から使用することをお勧めします。

Application Insights のほかの監視を使用せずに ApplicationInsightsLoggerProvider のみを使用する場合は、次の手順に従います。

インストール済みパッケージ:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
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

        logger.LogInformation("Logger is working");

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

次のセクションでは、**services.Configure\<TelemetryConfiguration>()** メソッドを使用して既定の TelemetryConfiguration をオーバーライドする方法を示します。 この例では、`ServerTelemetryChannel` とサンプリングを設定します。 カスタム ITelemetryInitializer を TelemetryConfiguration に追加します。

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
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>ログ記録レベルの制御

`ILogger` には、[ログのフィルタリング](/aspnet/core/fundamentals/logging#log-filtering)を適用するための組み込みメカニズムがあります。 これにより、Application Insights プロバイダーなどの、各登録済みプロバイダーに送信されるログを制御できます。 フィルター処理は、構成 (通常は *appsettings.json* ファイルを使用) またはコードで行うことができます。

次の例は、`ApplicationInsightsLoggerProvider` にフィルター ルールを適用する方法を示しています。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>appsettings.json を使用して構成でフィルター規則を作成する

ApplicationInsightsLoggerProvider では、プロバイダーの別名は `ApplicationInsights` です。 *appsettings.json* の次のセクションでは、ログ プロバイダーに対して、通常、"*警告*" 以上のレベルでログ記録するように指示しています。 次に、`ApplicationInsightsLoggerProvider` をオーバーライドして、"Microsoft" で始まるカテゴリを "*エラー*" 以上のレベルでログ記録します。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
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

## <a name="logging-scopes"></a>ログのスコープ

`ApplicationInsightsLoggingProvider` では、[ログのスコープ](/aspnet/core/fundamentals/logging#log-scopes)がサポートされており、スコープは既定で有効にされています。

スコープの型が `IReadOnlyCollection<KeyValuePair<string,object>>` の場合、コレクション内の各キーと値のペアが、カスタム プロパティとして、アプリケーション分析情報テレメトリに追加されます。 次の例で、ログは `TraceTelemetry` としてキャプチャされ、プロパティに ("MyKey", "MyValue") が含まれます。

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

他の型がスコープとして使用されている場合は、それらがアプリケーション情報分析テレメトリのプロパティ "Scope" の下に格納されます。 次の例で、`TraceTelemetry` は、スコープを格納する "Scope" というプロパティがあります。

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider の古いバージョンと新しいバージョンとは何ですか?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) には組み込みの ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider) が含まれており、**ILoggerFactory** 拡張メソッドを使用して有効にしました。 このプロバイダーは、バージョン 2.7.1 からは廃止とマークされています。 次のメジャー バージョンの変更で完全に削除されます。 [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) パッケージ自体は古くありません。 要求や依存関係などの監視を有効にするために必要です。

推奨される代替手段は、改善された ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) と、それを有効にするための ILoggerBuilder の拡張メソッドが含まれる、新しいスタンドアロン パッケージ [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) です。

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.1 は新しいパッケージへの依存関係を取得し、ILogger のキャプチャを自動的に有効にします。

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

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.7.1 に更新しており、ILogger からのログが自動的にキャプチャされます。 この機能を完全にオフにするにはどうすればよいですか?

ログ記録をフィルター処理する一般的な方法については、「[ログ記録レベルの制御](#control-logging-level)」セクションをご覧ください。 ApplicationInsightsLoggerProvider をオフにするには、`LogLevel.None` を使用します。

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
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Microsoft.ApplicationInsights.AspNetCore パッケージを使用して Application Insights を有効にする場合は、このコードを変更して、コンストラクター内で直接 `TelemetryClient` を取得します。 例については、[こちらの FAQ](./asp-net-core.md#frequently-asked-questions) をご覧ください。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger ログからはどのような種類の Application Insights テレメトリが生成されますか? または、Application Insights ではどこで ILogger ログを見ることができますか?

ApplicationInsightsLoggerProvider では、ILogger ログがキャプチャされて、それから TraceTelemetry が作成されます。 `ILogger` で `Log` メソッドに Exception オブジェクトを渡した場合、TraceTelemetry の代わりに *ExceptionTelemetry* が作成されます。 これらのテレメトリ項目は、ポータル、分析、Visual Studio ローカル デバッガーなど、Application Insights の他の TraceTelemetry または ExceptionTelemetry と同じ場所で見ることができます。

TraceTelemetry を常に送信したい場合は、次のスニペットを使用します。 ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK をインストールせず、Azure Web Apps 拡張機能を使用して ASP.NET Core アプリケーションの Application Insights を有効にしています。 新しいプロバイダーを使用するにはどうすればよいですか? 

Azure Web Apps の Application Insights 拡張機能は、新しいプロバイダーを使用します。 フィルタリング規則は、アプリケーションの *appsettings.json* ファイルで変更することができます。

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

このコードは、スタンドアロンのログ記録プロバイダーを使用する場合にのみ必要です。 Application Insights の通常の監視では、インストルメンテーション キーは構成パス *ApplicationInsights: InstrumentationKey* から自動的に読み込まれます。 Appsettings.json は次のようになります。

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>次のステップ

各項目の詳細情報

* [ASP.NET Core でのログ記録](/aspnet/core/fundamentals/logging)
* [Application Insights での .NET トレース ログ](./asp-net-trace-logs.md)

