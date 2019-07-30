---
title: ASP.NET Core アプリケーション用の Azure Application Insights | Microsoft Docs
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
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: 7fe5a4f5a5d1d254918f1b4f997acfb9cf67a75b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272443"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights for ASP.NET Core アプリケーション

この記事では、[ASP.NET Core](https://docs.microsoft.com/aspnet/core) アプリケーションで Application Insights を有効にする方法について説明します。 この記事の手順を完了すると、Application Insights で、ASP.NET Core アプリケーションから要求、依存関係、例外、パフォーマンス カウンター、ハートビート、ログが収集されます。 

ここで使用する例は、`netcoreapp2.2` を対象とする [MVC アプリケーション](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app)です。 これらの手順は、すべての ASP.NET Core アプリケーションに適用できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

[Application Insights SDK for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) では、実行されている場所や方法に関係なく、アプリケーションを監視できます。 アプリケーションが実行されていて、Azure へのネットワーク接続がある場合は、テレメトリを収集することができます。 Application Insights の監視は、.NET Core がサポートされているすべての場所でサポートされます。 以下がサポートされます。
* **[オペレーティング システム]** :Windows、Linux、Mac。
* **ホスティング方法**: プロセス内、プロセス外。 
* **デプロイ方法**: フレームワーク依存、自己完結型。
* **Web サーバー**: IIS (インターネット インフォメーション サーバー)、Kestrel。 
* **ホスティング プラットフォーム**: Azure App Service、Azure VM、Docker、Azure Kubernetes Service (AKS) などの Web Apps 機能。
* **IDE**: Visual Studio、VS Code、コマンド ライン。

## <a name="prerequisites"></a>前提条件

- 機能している ASP.NET Core アプリケーション。 ASP.NET Core アプリケーションを作成する必要がある場合は、こちらの [ASP.NET Core チュートリアル](https://docs.microsoft.com/aspnet/core/getting-started/)に従ってください。
- 有効な Application Insights インストルメンテーション キー。 Application Insights にテレメトリを送信するには、このキーが必要です。 インストルメンテーション キーを取得するために新しい Application Insights リソースを作成する必要がある場合は、「[Application Insights リソースの作成](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)」をご覧ください。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights のサーバー側テレメトリを有効にする (Visual Studio)

1. Visual Studio でプロジェクトを開きます。

    > [!TIP]
    > 必要な場合は、プロジェクトのソース管理を設定すると、Application Insights によって行われたすべての変更を追跡することができます。 ソース管理を有効にするには、 **[ファイル]**  >  **[ソース管理に追加]** を選択します。

2. **[プロジェクト]**  >  **[Application Insights Telemetry の追加]** を選択します

3. **[開始]** を選択します。 この選択のテキストは、Visual Studio のバージョンによって多少異なる場合があります。 以前のバージョンの一部では、代わりに **[開始 (無料)]** ボタンが使われています。

4. サブスクリプションを選択します。 次に、 **[リソース]**  >  **[登録]** を選択します。

5. Application Insights をプロジェクトに追加したら、SDK の最新の安定リリースを使用していることを確認します。 **[プロジェクト]**  >  **[NuGet パッケージの管理]**  >  **[Microsoft.ApplicationInsights.AspNetCore]** に移動します。 必要であれば、 **[更新]** を選択します。

     ![更新用の Application Insights パッケージを選択する場所を示すスクリーンショット](./media/asp-net-core/update-nuget-package.png)

6. オプションのヒントに従い、プロジェクトをソース管理に追加した場合は、 **[表示]**  >  **[チーム エクスプローラー]**  >  **[変更]** に移動します。 次に、各ファイルを選択して、Application Insights のテレメトリによって行われた変更の差分ビューを表示します。

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Application Insights のサーバー側テレメトリを有効にする (Visual Studio なし)

1. [ASP.NET Core 用の Application Insights SDK NuGet パッケージ](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)をインストールします。 常に最新の安定バージョンを使用することをお勧めします。 [オープンソースの GitHub リポジトリ](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)で、SDK の完全なリリース ノートを探します。

    次のコード サンプルでは、プロジェクトの `.csproj` ファイルに追加される変更が示されています。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. この例のように、`Startup` クラスで `ConfigureServices()` メソッドに `services.AddApplicationInsightsTelemetry();` を追加します。

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. インストルメンテーション キーを設定します。

    インストルメンテーション キーを `AddApplicationInsightsTelemetry` に引数として指定することはできますが、インストルメンテーション キーは構成に指定することをお勧めします。 `appsettings.json` でインストルメンテーション キーを指定する方法は、次のコード サンプルのようになります。 発行の間に、`appsettings.json` がアプリケーションのルート フォルダーにコピーされるようにします。

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

    または、次のいずれかの環境変数にインストルメンテーション キーを指定します。

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    例:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    通常、`APPINSIGHTS_INSTRUMENTATIONKEY` では Web Apps にデプロイされるアプリケーションのインストルメンテーション キーを指定します。

    > [!NOTE]
    > コードで指定されたインストルメンテーション キーは、他のオプションより優先される環境変数 `APPINSIGHTS_INSTRUMENTATIONKEY` より優先されます。

## <a name="run-your-application"></a>アプリケーションを実行する

アプリケーションを実行し、それに対して要求を行います。 これで Application Insights にテレメトリが送られるはずです。 Application Insights SDK では次のテレメトリが自動的に収集されます。

|要求/依存関係 |詳細|
|---------------|-------|
|Requests | アプリケーションへの受信 Web 要求。 |
|HTTP または HTTPS | `HttpClient` で行われる呼び出し。 |
|SQL | `SqlClient` で行われる呼び出し。 |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage クライアントで行われた呼び出し。 |
|[EventHubs クライアント SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | バージョン 1.1.0 以降。 |
|[ServiceBus クライアント SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| バージョン 3.0.0 以降。 |
|Azure Cosmos DB | HTTP/HTTPS が使用されている場合にのみ、自動的に追跡されます。 Application Insights では、TCP モードはキャプチャされません。 |

### <a name="performance-counters"></a>パフォーマンス カウンター

ASP.NET Core での[パフォーマンス カウンター](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)のサポートは制限されています。

   * SDK バージョン 2.4.1 以降では、アプリケーションが Web Apps (Windows) で実行されている場合、パフォーマンス カウンターが収集されます。
   * SDK バージョン 2.7.0-beta3 以降では、アプリケーションが Windows で実行されていて、`NETSTANDARD2.0` 以降を対象とする場合、パフォーマンス カウンターが収集されます。
   * .NET Framework を対象とするアプリケーションの場合、すべてのバージョンの SDK でパフォーマンス カウンターがサポートされます。
 
この記事は、Linux でパフォーマンス カウンター サポートが追加されるときに更新されます。

### <a name="ilogger-logs"></a>ILogger ログ

SDK バージョン 2.7.0-beta3 以降では、重大度が `Warning` 以上の [ILogger ログ](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)は自動的にキャプチャされます。

### <a name="live-metrics"></a>ライブ メトリック

ポータルでテレメトリの表示が始まるまで、数分かかる場合があります。 すべてが動作していることを簡単に確認するには、実行中のアプリケーションに要求を行うとき、[ライブ メトリック](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)を使用することをお勧めします。

## <a name="enable-client-side-telemetry-for-web-applications"></a>Web アプリケーションに対してクライアント側のテレメトリを有効にする

サーバー側テレメトリの収集を始めるためなら、上記の手順で十分です。 お使いのアプリケーションにクライアント側コンポーネントがある場合、[使用状況テレメトリ](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)の収集を始めるには次の手順のようにします。

1. `_ViewImports.cshtml` で、インジェクションを追加します。

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. `_Layout.cshtml` で、`<head>` セクションの終わりに (ただし、他のスクリプトの前に) `HtmlHelper` を挿入します。 ページからカスタム JavaScript テレメトリを報告する場合は、このスニペットの後に挿入します。

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

上で参照されている `.cshtml` ファイル名は、既定の MVC アプリケーション テンプレートからのものです。 最終的に、アプリケーションに対するクライアント側の監視を正しく有効にするためには、JavaScript スニペットが、監視するアプリケーションの各ページの `<head>` セクションにある必要があります。 このアプリケーション テンプレートでは、Javascript スニペットを `_Layout.cshtml` に追加することで、実質的にこの目標を達成できます。 

プロジェクトに `_Layout.cshtml` が含まれない場合でも、[クライアント側の監視](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)を追加することができます。 アプリ内のすべてのページの `<head>` を制御する同等のファイルに JavaScript のスニペットを追加することで、これを行うことができます。 または、複数のページにスニペットを追加することもできますが、この方法では保守が困難になるので、一般にはお勧めしません。

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK を構成する

Application Insights SDK for ASP.NET Core をカスタマイズして、既定の構成を変更できます。 Application Insights ASP.NET SDK のユーザーであれば、`ApplicationInsights.config` を使用する構成または `TelemetryConfiguration.Active` の変更に慣れている場合もあります。 ASP.NET Core の場合、構成は別の方法で変更します。 ASP.NET Core SDK をアプリケーションに追加し、ASP.NET Core の組み込み[依存関係インジェクション](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を使用して構成します。 他の方法が指示されていない限り、ほとんどすべての構成の変更は、`Startup.cs` クラスの `ConfigureServices()` メソッドで行います。 以下のセクションではさらに詳しく説明します。

> [!NOTE]
> ASP.NET Core アプリケーションでは、`TelemetryConfiguration.Active` を変更することによる構成の変更は推奨されません。

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions を使用する

次の例のように `ApplicationInsightsServiceOptions` を `AddApplicationInsightsTelemetry` に渡すことで、いくつかの一般的な設定を変更できます。

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

詳細については、[`ApplicationInsightsServiceOptions` での構成可能な設定](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)を参照してください。

### <a name="sampling"></a>サンプリング

Application Insights SDK for ASP.NET Core では、固定レートとアダプティブ サンプリングの両方がサポートされています。 アダプティブ サンプリングは、既定で有効になっています。 

詳しくは、「[ASP.NET Core アプリケーションのためのアダプティブ サンプリングの構成](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)」をご覧ください。

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers の追加

すべてのテレメトリで送信されるグローバル プロパティを定義するときは、[テレメトリ初期化子](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)を使います。

次のコードで示すように、新しい `TelemetryInitializer` を `DependencyInjection` コンテナーに追加します。 `DependencyInjection` コンテナーに追加した `TelemetryInitializer` は、SDK によって自動的に取得されます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers の削除

テレメトリ初期化子は既定で存在します。 すべてまたは特定のテレメトリ初期化子を削除するには、`AddApplicationInsightsTelemetry()` を呼び出した "*後*" で、次のサンプル コードを使用します。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

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

拡張メソッド `AddApplicationInsightsTelemetryProcessor` を `IServiceCollection` で使用することで、カスタム テレメトリ プロセッサを `TelemetryConfiguration` に追加できます。 [高度なフィルター処理シナリオ](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)でテレメトリ プロセッサを使用して、Application Insights サービスに送信するテレメトリに何を含めて何を除外するかを、より直接的に制御できるようにします。 次の例を使用してください。

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

Application Insights では、追加構成を必要とせずに特定のワークロードに関する[有用な情報を自動的に収集する](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies)ために、テレメトリ モジュールが使われます。

次の自動収集モジュールが既定で有効になります。 これらのモジュールでは、自動的にテレメトリが収集されます。 それらを無効にするか構成して、既定の動作を変更できます。

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

既定の `TelemetryModule` を構成するには、下の画像のように、拡張メソッド `ConfigureTelemetryModule<T>` を `IServiceCollection` で使用します。

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

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

### <a name="configuring-a-telemetry-channel"></a>テレメトリ チャネルを構成する

既定のチャネルは `ServerTelemetryChannel` です。 次の例のようにしてオーバーライドできます。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>自動的に収集されないテレメトリを追跡するにはどうすればよいですか?

コンストラクター インジェクションを使用して `TelemetryClient` のインスタンスを取得し、そのインスタンスで必須の `TrackXXX()` メソッドを呼び出します。 ASP.NET Core アプリケーションで新しい `TelemetryClient` インスタンスを作成することはお勧めしません。 `TelemetryClient` のシングルトン インスタンスが `DependencyInjection` コンテナーに既に登録されており、それによって `TelemetryConfiguration` がテレメトリの残りの部分と共有されます。 新しい `TelemetryClient` インスタンスの作成は、残りのテレメトリとは別の構成が必要な場合にのみ推奨されます。 

次の例では、コントローラーから追加のテレメトリを追跡する方法を確認できます。

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Application Insights でのカスタム データ レポートについては、[Application Insights カスタム メトリック API リファレンス](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)に関するページを参照してください。

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>一部の Visual Studio テンプレートでは、Application Insights を有効にする目的で UseApplicationInsights() 拡張メソッドが IWebHostBuilder で使用されていました。 この使用方法は今でも有効ですか?

はい、このメソッドで Application Insights を有効にできます。 この手法は、Visual Studio のオンボードと、Web Apps 拡張機能で使用されます。 ただし、一部の構成を制御するためのオーバーロードが提供されるため、`services.AddApplicationInsightsTelemetry()` を使うことをお勧めします。 どちらのメソッドも内部的には同じことが行われるので、カスタム構成を適用する必要がない場合は、どちらのメソッドを呼び出してもかまいません。

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET Core アプリケーションを Web Apps にデプロイしています。 Application Insights 拡張を Web アプリから有効にできますか?

この記事に示されているように、SDK がビルド時にインストールされる場合は、App Service ポータルから Application Insights の拡張機能を有効にする必要はありません。 拡張機能はインストールされていても、既に SDK がアプリケーションに追加されていることが検出されると、バックオフします。 拡張機能から Application Insights を有効にする場合、SDK をインストールして更新する必要はありません。 ただし、この記事の手順に従って Application Insights を有効にする場合は、次のような理由で柔軟性が増します。

   * Application Insights テレメトリは以下で引き続き機能します。
       * Windows、Linux、Mac を含む、すべてのオペレーティング システム。
       * 自己完結型やフレームワーク依存型など、すべての発行モード。
       * 完全 .NET Framework を含む、すべてのターゲット フレームワーク。
       * Web Apps、VM、Linux、コンテナー、Azure Kubernetes Service、Azure 以外のホスティングなど、すべてのホスティング オプション。
   * Visual Studio からデバッグしているとき、テレメトリをローカル環境で見ることができます。
   * `TrackXXX()` API を使って、追加のカスタム テレメトリを追跡できます。
   * 構成を完全に制御できます。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Status Monitor などのツールを利用して Application Insights 監視を有効にできますか?

いいえ。 現在、[Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) と [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) では、ASP.NET 4.x のみがサポートされます。

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>ASP.NET Core 2.0 アプリケーションでは、Application Insights が自動的に有効になりますか?

`Microsoft.AspNetCore.All` 2.0 メタパッケージには、Application Insights SDK (バージョン 2.1.0) が含まれていました。 アプリケーションを Visual Studio デバッガーの下で実行すると、Visual Studio によって Application Insights が有効にされ、テレメトリが IDE 自体にローカルに表示されます。 テレメトリは、インストルメンテーション キーが指定されない限り、Application Insights サービスに送信されませんでした。 2\.0 アプリについても、この記事の指示に従って Application Insights を有効にすることをお勧めします。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux でアプリケーションを実行する場合、すべての機能がサポートされますか?

はい。 SDK の機能サポートは、次の例外を除き、すべてのプラットフォームで同じです。

* パフォーマンス カウンターは、Windows でのみサポートされます。
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
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>オープンソース SDK

[コードを読んで協力してください。](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>ビデオ

- 最初から [.NET Core と Visual Studio を使って Application Insights を構成する](https://www.youtube.com/watch?v=NoS9UhcR4gA&t)には、この手順に関するステップ バイ ステップの外部ビデオをご覧ください。
- 最初から [.NET Core と Visual Studio Code を使って Application Insights を構成する](https://youtu.be/ygGt84GDync)には、この手順に関するステップ バイ ステップの外部ビデオをご覧ください。

## <a name="next-steps"></a>次の手順

* [ユーザー フローの探索](../../azure-monitor/app/usage-flows.md): ユーザーがアプリ内をどのように移動しているかを把握します。
* [スナップショット コレクションを構成](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)して、例外がスローされたときのソース コードと変数の状態を確認します。
* [API を使用](../../azure-monitor/app/api-custom-events-metrics.md)して、アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。
* [可用性テスト](../../azure-monitor/app/monitor-web-app-availability.md)の使用: 世界中からアプリを常にチェックします。
