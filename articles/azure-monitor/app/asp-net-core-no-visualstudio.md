---
title: Azure Application Insights for ASP.NET Core、Visual Studio なし | Microsoft Docs
description: ASP.NET Core Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287544"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights for ASP.NET Core アプリケーション

この記事では、Visual Studio IDE を使用せずに Application Insights for a [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) アプリケーションを有効にする手順について説明します。 Visual Studio IDE をインストールしている場合、[この](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)ドキュメントに Visual Studio 固有の指示が含まれています。 この記事に記載されている指示を完了すると、Application Insights により ASP.NET Core アプリケーションから要求、依存関係、例外、パフォーマンス カウンター、ハートビート、ログの収集が開始されます。 サンプルで使用されているアプリケーションは `netcoreapp2.2` をターゲットにする [MVC Application](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) ですが、ここにある指示はすべての ASP.NET Core アプリケーションに当てはまります。 例外があれば表示されます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

Application Insights SDK (ソフトウェア開発キット) for ASP.NET Core により、アプリケーションを実行する場所と方法に関係なく、アプリケーションの監視が可能になります。 アプリケーションが実行中で、Application Insights サービスにネットワーク接続できる場合、テレメトリの収集が予想されます。 このサポートには、あらゆるオペレーティング システム (Windows、Linux、Mac)、ホスティング方法 (インプロセスまたはアウトオブプロセス)、展開方法 (フレームワーク依存または自己完結)、Web サーバー (IIS、Kestrel)、プラットフォーム (Azure Web アプリ、Azure VM、Docker、AKS など)、IDE (Visual Studio、VS Code、コマンド ライン) が含まれますが、それらに限定されません。

## <a name="prerequisites"></a>前提条件

- 機能している ASP.NET Core アプリケーション。 必要であれば、[こちら](https://docs.microsoft.com/aspnet/core/getting-started/)のガイドに従い、ASP.NET Core アプリケーションを作成してください。
- Application Insights の有効なインストルメンテーション キー。これは Application Insights サービスにテレメトリを送信するために必要です。 必要であれば、[こちら](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)の指示に従い、新しい Application Insights リソースを作成し、インストルメンテーション キーを取得してください。

## <a name="enabling-application-insights-server-side-telemetry"></a>Application Insights のサーバー側テレメトリを有効にする

1. Application Insights SDK for ASP.NET Core をインストールします。これは通常の NuGet パッケージです。 常に最新の安定したバージョンを使用することをお勧めします。 この記事で説明する機能の一部は、古いバージョンでは利用できない可能性があります。 SDK のリリース ノートは[こちら](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)ですべて見つかります。 

プロジェクトの .csproj ファイルに追加する変更は次のようになります。

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. `Startup` クラスで `ConfigureServices()` メソッドに `services.AddApplicationInsightsTelemetry();` を変更します。 完全なサンプルは下のようになります。

```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // The following line enables Application Insights telemetry collection.
        services.AddApplicationInsightsTelemetry();

        // code adding other services for your application
        services.AddMvc();
    }
```

3. インストルメンテーション キーを設定します。

   インストルメンテーション キーを引数として `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");` に与えることはできますが、インストルメンテーション キーは構成で指定することをお勧めします。 `appsettings.json` でインストルメンテーション キーを指定する方法は次のようになります。 公開時、`appsettings.json` がアプリケーションのルート フォルダーにコピーされるようにします。

```json
    {
      "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
      },
      "Logging": {
        "LogLevel": {
          "Default": "Warning"
        }
      }
    }
```

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` は通常、Azure Web アプリに展開されるアプリケーションのインストルメンテーション キーを指定するために使用されます。

> [!NOTE]
> コードに指定されたインストルメンテーション キーは、他のオプションより優先される環境変数 `APPINSIGHTS_INSTRUMENTATIONKEY` より優先されます。

4. アプリケーションを実行し、それに要求を行います。 これで Application Insights にテレメトリが送られるはずです。 次のテレメトリは Application Insights SDK によって自動的に収集されます。

    1. 要求 - アプリケーションに Web 要求が入ってきます。
    1. 依存関係
        1. Http/Https 呼び出し、これを実行するのは  `HttpClient`
        1. SQL 呼び出し、これを実行するのは  `SqlClient`
        1. [Azure Storage Client](https://www.nuget.org/packages/WindowsAzure.Storage/)* で実行される Azure Storage の呼び出し
        1. [EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) バージョン 1.1.0 以上
        1. [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) バージョン 3.0.0 以上

             *Azure Cosmos DB は、HTTP/HTTPS が使用されている場合にのみ、自動的に追跡されます。 TCP モードは、Application Insights ではキャプチャされません。


    1. [パフォーマンス カウンター](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. ASP.NET Core でのパフォーマンス カウンターのサポートは次に限定されています
            1. SDK バージョン 2.4.1 以上では、アプリケーションが Azure Web アプリ (Windows) で実行されている場合、パフォーマンス カウンターが収集されます。
            1. SDK バージョン 2.7.0-beta3 以上では、アプリケーションが Windows で実行されており、`NETSTANDARD2.0` 以降をターゲットにしている場合、パフォーマンス カウンターが収集されます。
            1. .NET Framework 全体を対象とするアプリケーションの場合、すべてのバージョンの SDK でパフォーマンス カウンターがサポートされます。

            Linux のパフォーマンス カウンター サポートが追加されるとき、このドキュメントは更新されます。
    1. [ライブ メトリック](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` ログは、重大度が `Warning` 以上のとき、SDK バージョン 2.7.0-beta3 以降から自動的に記録されます。 詳細については、[こちら](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)をご覧ください。

テレメトリがポータルに表示され始めるまで、数分かかることがあります。 すべてが動作しているかどうかを簡単に確認するには、実行中のアプリケーションに要求を行っているとき、[ライブ メトリック](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)を使用することをお勧めします。

## <a name="send-ilogger-logs-to-application-insights"></a>ILogger ログを Application Insights に送信する

Application Insights では、ILogger を介して送信されるログのキャプチャがサポートされます。 ドキュメントの完全版は[こちら](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)でお読みください。

## <a name="enable-client-side-telemetry-for-web-applications"></a>Web アプリケーションに対してクライアント側のテレメトリを有効にする

サーバー側テレメトリの収集を始めるには、上の手順で十分です。 お使いのアプリケーションにクライアント側コンポーネントがある場合、下の手順に沿ってクライアント側コンポーネントから[使用状況テレメトリ](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)の収集を始めます。

1. _ViewImports.cshtml で次のインジェクションを追加します。

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. _Layout.cshtml で `<head>` セクションの終わりに (ただし、他のスクリプトの前に) HtmlHelper を挿入します。 ページから報告するカスタム JavaScript テレメトリがあれば、このスニペットの後に挿入します。

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

ファイル名を実際のアプリケーションに基づいて変更します。 上で使用されている名前は既定の MVC アプリケーション テンプレートからのものです。

## <a name="configuring-application-insights-sdk"></a>Application Insights SDK の構成

Application Insights SDK for ASP.NET Core をカスタマイズし、既定の構成を変更できます。 Application Insights ASP.NET SDK のユーザーであれば、`ApplicationInsights.config` を使用する構成または `TelemetryConfiguration.Active` の変更に慣れている場合もあります。 ASP.NET Core の場合、構成は別の方法で行われます。 ASP.NET Core SDK は ASP.NET Core の組み込み [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) メカニズムを利用してアプリケーションに追加されます。その構成にも DependencyInjection が使用されます。 別途明記されていない限り、構成変更はほとんどすべて、`Startup.cs` クラスの `ConfigureServices()` メソッドで行われます。 下のセクションを進めると詳細についてご覧いただけます。

> [!NOTE]
> `TelemetryConfiguration.Active` を変更する方法で構成を変更することは、ASP.NET Core アプリケーションでは推奨されていない点に注意していただくことが重要です。

### <a name="configuring-using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions を使用して構成する

`ApplicationInsightsServiceOptions` を `services.AddApplicationInsightsTelemetry();` に渡す方法でいくつかの共通設定を変更できます。 次に例を示します。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

`ApplicationInsightsServiceOptions` で構成できる設定の一覧は[こちら](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)で確認できます。

### <a name="sampling"></a>サンプリング

Application Insights SDK for ASP.NET Core では FixedRate および Adaptive サンプリングがサポートされています。 アダプティブ サンプリングは、既定で有効になっています。 ASP.NET Core アプリケーションのサンプリングを構成する方法については、[こちら](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)のドキュメントをご覧ください。

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers の追加

新しい `TelemetryInitializer` を追加するには、下の画像のように DependencyInjection コンテナーにそれを追加します。 `TelemetryInitializer`は DependencyInjection コンテナーに追加されますが、SDK によって自動的に選択されます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers の削除

既定で存在するすべてまたは特定の TelemetryInitializers を削除するには、`AddApplicationInsightsTelemetry()` の呼び出し**後**に次のサンプル コードを使用します。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>TelemetryProcessors の追加

拡張メソッド `AddApplicationInsightsTelemetryProcessor` を `IServiceCollection` で使用することで、カスタム テレメトリ プロセッサを `TelemetryConfiguration` に追加できます。 次の例を使用してください。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>既定の TelemetryModules の構成または削除

次の自動収集モジュールは既定で有効になっており、テレメトリの自動収集を担います。 これらのモジュールは無効にしたり、既定の動作を変更したりできます。

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

既定の `TelemetryModule` を構成するには、下の画像のように、拡張メソッド `ConfigureTelemetryModule<T>` を `IServiceCollection` で使用します。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>テレメトリ チャネルの構成

使用される既定のチャネルは `ServerTelemetryChannel` です。 これは次のサンプルに従うことでオーバーライドできます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

*1.自動収集されたテレメトリ以外にいくつかのテレメトリを追加で追跡するには、 どうすればよいですか?*

* Constructor インジェクションを使用して `TelemetryClient` のインスタンスを取得し、そのインスタンスで必須の `TrackXXX()` メソッドを呼び出します。 ASP.NET Core アプリケーションで新しい `TelemetryClient` インスタンスを作成することは推奨されません。`TelemetryClient` のシングルトン インスタンスは、残りのテレメトリと `TelemetryConfiguration` を共有する DI コンテナーに既に登録されているためです。 新しい `TelemetryClient` インスタンスの作成は、残りのテレメトリとは別の構成を与える必要がある場合にのみ推奨されます。 次の例では、コントローラーから追加のテレメトリを追跡する方法を確認できます。

```csharp
public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Application Insights のカスタム データ レポートについては、[Application Insights カスタム メトリック API リファレンス](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)に関するページを参照してください。

*2.一部の Visual Studio テンプレートでは、Application Insights を有効にする目的で UseApplicationInsights() 拡張メソッドが IWebHostBuilder で使用されていました。 この使用方法は今でも有効ですか?*

* このメソッドで Application Insights を有効にすることは可能であり、Visual Studio オンボーディングや Azure Web アプリ拡張で使用されています。 ただし、`services.AddApplicationInsightsTelemery()` を使用することをお勧めします。一部の構成を制御するオーバーロードが与えられます。 いずれのメソッドでも内部的には同じことが行われます。そのため、カスタム構成を適用しない場合、どちらを呼び出しても構いません。

*手順 3.ASP.NET Core アプリケーションを Azure Web アプリにデプロイしています。 Application Insights 拡張を Web アプリから有効にできますか?*

* この記事で紹介しているように SDK がビルド時にインストールされる場合、Web Apps ポータルから Application Insights 拡張を有効にする必要はありません。 拡張がインストールされている場合でも、SDK がアプリケーションに既に追加されていることが検出されると、拡張はバックオフされます。 拡張から Application Insights を有効にすれば、SDK をアプリケーションにインストールしたり、更新したりする必要がありません。 ただし、下記の理由から、この記事のように Application Insights を有効にする方法のほうが自由度が高くなります。
    1. Application Insights は以下で引き続き作動します。
        1. すべてのオペレーティング システム - Windows、Linux、Mac。
        1. すべての発行モード - 自己完結またはフレームワーク依存。
        1. 完全 .NET Framework を含む、すべてのターゲット フレームワーク。
        1. すべてのホスティング オプション - Azure Web アプリ、VM、Linux、コンテナー、AKS、Azure 以外。
    1. Visual Studio からデバッグしているとき、テレメトリはローカルで表示できます。
    1. `TrackXXX()` API を使用し、追加のカスタム テレメトリを追跡できます。
    1. 構成を完全に制御できます。

*4.Status Monitor などのツールを利用して Application Insights 監視を有効にできますか?*

* いいえ。 [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) とそれに取って代わる予定の [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) は現在のところ、ASP.NET のみをサポートしています。 ASP.NET Core アプリケーションのサポートが利用できるようになると、ドキュメントが更新されます。

*5.ASP.NET Core 2.0 アプリケーションを持っています。 これに対して自分では何もしなくても Application Insights は自動的に有効になりますか?*

* `Microsoft.AspNetCore.All` 2.0 メタパッケージには、Application Insights SDK (バージョン 2.1.0) が含まれていました。アプリケーションを Visual Studio デバッガーの下で実行すると、Visual Studio は Application Insights を有効にし、IDE 自体でテレメトリをローカル表示します。 テレメトリは、インストルメンテーション キーが明示的に指定されない限り、Application Insights サービスに送信されませんでした。 2.0 アプリの場合でも、この記事の指示に従い、Application Insights を有効にすることをお勧めします。

*6.Linux でアプリケーションを実行しています。 Linux でもすべての機能がサポートされていますか?*

* はい。 SDK の機能サポートは、次の例外を除き、すべてのプラットフォームで同じです。
    1. Windows 以外ではパフォーマンス カウンターがまだサポートされていません。 Linux Support が追加されたとき、このドキュメントは更新されます。
    1. `ServerTelemetryChannel` が既定で有効になっていても、アプリケーションが Windows 以外で実行されている場合、ネットワークに問題があるときにテレメトリを一時的に保存する目的でローカル ストレージ フォルダーが自動的に作成されることは、チャネルではありません。 この制限に起因し、ネットワークやサーバーに一時的に問題が発生すると、テレメトリが失われます。 下の画像のように、ユーザーがチャネル用のローカル フォルダーを構成することがこの問題の回避策です。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```
