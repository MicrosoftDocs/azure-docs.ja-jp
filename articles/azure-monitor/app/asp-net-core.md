---
title: ASP.NET Core アプリケーション用の Azure Application Insights | Microsoft Docs
description: ASP.NET Core Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: be4cbe5f95fa6901ae4299662750c232d4700a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711503"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights for ASP.NET Core アプリケーション

この記事では、[ASP.NET Core](/aspnet/core) アプリケーションで Application Insights を有効にする方法について説明します。 この記事の手順を完了すると、Application Insights で、ASP.NET Core アプリケーションから要求、依存関係、例外、パフォーマンス カウンター、ハートビート、ログが収集されます。

ここで使用する例は、`netcoreapp3.0` を対象とする [MVC アプリケーション](/aspnet/core/tutorials/first-mvc-app)です。 これらの手順は、すべての ASP.NET Core アプリケーションに適用できます。 [ワーカー サービス](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)を使っている場合は、[こちら](./worker-service.md)の手順を使用してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

[Application Insights SDK for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) では、実行されている場所や方法に関係なく、アプリケーションを監視できます。 アプリケーションが実行されていて、Azure へのネットワーク接続がある場合は、テレメトリを収集することができます。 Application Insights の監視は、.NET Core がサポートされているすべての場所でサポートされます。 以下がサポートされます。
* **[オペレーティング システム]** :Windows、Linux、Mac。
* **ホスティング方法**: プロセス内、プロセス外。
* **デプロイ方法**: フレームワーク依存、自己完結型。
* **Web サーバー**: IIS (インターネット インフォメーション サーバー)、Kestrel。
* **ホスティング プラットフォーム**: Azure App Service、Azure VM、Docker、Azure Kubernetes Service (AKS) などの Web Apps 機能。
* **.NET Core バージョン**:正式に [サポートされている](https://dotnet.microsoft.com/download/dotnet-core)すべての .NET Core バージョン。
* **IDE**: Visual Studio、VS Code、コマンド ライン。

> [!NOTE]
> ASP.NET Core 3.1 では [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) 以降が必要です。

## <a name="prerequisites"></a>前提条件

- 機能している ASP.NET Core アプリケーション。 ASP.NET Core アプリケーションを作成する必要がある場合は、こちらの [ASP.NET Core チュートリアル](/aspnet/core/getting-started/)に従ってください。
- 有効な Application Insights インストルメンテーション キー。 Application Insights にテレメトリを送信するには、このキーが必要です。 インストルメンテーション キーを取得するために新しい Application Insights リソースを作成する必要がある場合は、「[Application Insights リソースの作成](./create-new-resource.md)」をご覧ください。

> [!IMPORTANT]
> 新しい Azure リージョンでは、インストルメンテーション キーの代わりに接続文字列を使用する **必要** があります。 [接続文字列](./sdk-connection-string.md?tabs=net)により、利用統計情報と関連付けるリソースが識別されます。 また、リソースでテレメトリの宛先として使用するエンドポイントを変更することもできます。 接続文字列をコピーし、アプリケーションのコードまたは環境変数に追加する必要があります。


## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights のサーバー側テレメトリを有効にする (Visual Studio)

Visual Studio for Mac の場合は、[手動のガイダンス](#enable-application-insights-server-side-telemetry-no-visual-studio)を使用します。 この手順は、Visual Studio の Windows バージョンでのみサポートされています。

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

1. [ASP.NET Core 用の Application Insights SDK NuGet パッケージ](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)をインストールします。 常に最新の安定バージョンを使用することをお勧めします。 [オープンソースの GitHub リポジトリ](https://github.com/Microsoft/ApplicationInsights-dotnet/releases)で、SDK の完全なリリース ノートを探します。

    次のコード サンプルでは、プロジェクトの `.csproj` ファイルに追加される変更が示されています。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.16.0" />
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

    次に例を示します。

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` は通常、[Azure Web Apps](./azure-web-apps.md?tabs=net) で使用されますが、この SDK がサポートされているすべての場所で使用することもできます (コード不要の Web アプリの監視を行う際に接続文字列を使用しない場合はこの形式が必要です)。

    インストルメンテーション キーを設定する代わりに、[接続文字列](./sdk-connection-string.md?tabs=net)も使用できるようになりました。

    > [!NOTE]
    > コードで指定されたインストルメンテーション キーは、他のオプションより優先される環境変数 `APPINSIGHTS_INSTRUMENTATIONKEY` より優先されます。

### <a name="user-secrets-and-other-configuration-providers"></a>ユーザー シークレットとその他の構成プロバイダー

インストルメンテーション キーを ASP.NET Core ユーザー シークレットに格納するか、別の構成プロバイダーから取得する場合は、`Microsoft.Extensions.Configuration.IConfiguration` パラメーターでオーバーロードを使用できます。 たとえば、「 `services.AddApplicationInsightsTelemetry(Configuration);` 」のように入力します。
Microsoft.ApplicationInsights.AspNetCore バージョン [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 以降、`services.AddApplicationInsightsTelemetry()` の呼び出しによって、アプリケーションの `Microsoft.Extensions.Configuration.IConfiguration` からインストルメンテーション キーが自動的に読み取られます。 `IConfiguration` を明示的に指定する必要はありません。

## <a name="run-your-application"></a>アプリケーションを実行する

アプリケーションを実行し、それに対して要求を行います。 これで Application Insights にテレメトリが送られるはずです。 Application Insights SDK では、次のテレメトリと共に、アプリケーションへの受信 Web 要求を自動的に収集します。

### <a name="live-metrics"></a>ライブ メトリック

[Live Metrics](./live-stream.md) を使用すると、Application Insights の監視が正しく構成されているかどうかをすばやく確認できます。 ポータルおよび分析でテレメトリの表示が始まるまで数分かかることがありますが、Live Metrics では、実行中のプロセスの CPU 使用率がほぼリアルタイムで示されます。 また、要求、依存関係、トレースなどの他のテレメトリも表示できます。

### <a name="ilogger-logs"></a>ILogger ログ

既定の構成で `ILogger` 重大度`Warning`以上のログを収集します。 この構成は[カスタマイズできます](#how-do-i-customize-ilogger-logs-collection)。

### <a name="dependencies"></a>依存関係

依存関係の収集は既定で有効になっています。 [こちら](asp-net-dependencies.md#automatically-tracked-dependencies)の記事では、自動収集される依存関係について説明されており、手動で追跡するための手順も含まれます。

### <a name="performance-counters"></a>パフォーマンス カウンター

ASP.NET Core での[パフォーマンス カウンター](./performance-counters.md)のサポートは制限されています。

* SDK バージョン 2.4.1 以降では、アプリケーションが Azure Web Apps (Windows) で実行されている場合、パフォーマンス カウンターが収集されます。
* SDK バージョン 2.7.1 以降では、アプリケーションが Windows で実行されていて、`NETSTANDARD2.0` 以降を対象とする場合、パフォーマンス カウンターが収集されます。
* .NET Framework を対象とするアプリケーションの場合、すべてのバージョンの SDK でパフォーマンス カウンターがサポートされます。
* SDK バージョン 2.8.0 以降では、Linux の CPU/メモリ カウンターがサポートされます。 Linux では、その他のカウンターはサポートされません。 Linux (およびその他の非 Windows 環境) でシステム カウンターを取得するには、[EventCounter](#eventcounter) を使用することをお勧めします。

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` は既定で有効になっています。 [EventCounter](eventcounters.md) チュートリアルには、回収するカウンターの一覧を構成する方法に関する指示があります。

## <a name="enable-client-side-telemetry-for-web-applications"></a>Web アプリケーションに対してクライアント側のテレメトリを有効にする

サーバー側テレメトリの収集を始めるためなら、上記の手順で十分です。 お使いのアプリケーションにクライアント側コンポーネントがある場合、[使用状況テレメトリ](./usage-overview.md)の収集を始めるには次の手順のようにします。

1. `_ViewImports.cshtml` で、インジェクションを追加します。

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. `_Layout.cshtml` で、`<head>` セクションの終わりに (ただし、他のスクリプトの前に) `HtmlHelper` を挿入します。 ページからカスタム JavaScript テレメトリを報告する場合は、このスニペットの後に挿入します。

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

または、`FullScript` を使用するには、SDK v2.14 で `ScriptBody` を使用します。 コンテンツ セキュリティ ポリシーを設定するために `<script>` タグをコントロールする必要がある場合は、次のようにします。

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

上で参照されている `.cshtml` ファイル名は、既定の MVC アプリケーション テンプレートからのものです。 最終的に、アプリケーションに対するクライアント側の監視を正しく有効にするためには、JavaScript スニペットが、監視するアプリケーションの各ページの `<head>` セクションにある必要があります。 このアプリケーション テンプレートでは、Javascript スニペットを `_Layout.cshtml` に追加することで、実質的にこの目標を達成できます。 

プロジェクトに `_Layout.cshtml` が含まれない場合でも、[クライアント側の監視](./website-monitoring.md)を追加することができます。 アプリ内のすべてのページの `<head>` を制御する同等のファイルに JavaScript のスニペットを追加することで、これを行うことができます。 または、複数のページにスニペットを追加することもできますが、この方法では保守が困難になるので、一般にはお勧めしません。

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK を構成する

Application Insights SDK for ASP.NET Core をカスタマイズして、既定の構成を変更できます。 Application Insights ASP.NET SDK のユーザーであれば、`ApplicationInsights.config` を使用する構成または `TelemetryConfiguration.Active` の変更に慣れている場合もあります。 ASP.NET Core の場合、他の方法が指定されていない限り、ほとんどすべての構成の変更は、`Startup.cs` クラスの `ConfigureServices()` メソッドで行われます。 以下のセクションではさらに詳しく説明します。

> [!NOTE]
> ASP.NET Core アプリケーションでは、`TelemetryConfiguration.Active` を変更することによる構成の変更はサポートされていません。

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

`ApplicationInsightsServiceOptions` の設定の完全な一覧

|設定 | 説明 | Default
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | `PerformanceCounterCollectionModule` を有効または無効にします | true
|EnableRequestTrackingTelemetryModule   | `RequestTrackingTelemetryModule` を有効または無効にします | true
|EnableEventCounterCollectionModule   | `EventCounterCollectionModule` を有効または無効にします | true
|EnableDependencyTrackingTelemetryModule   | `DependencyTrackingTelemetryModule` を有効または無効にします | true
|EnableAppServicesHeartbeatTelemetryModule  |  `AppServicesHeartbeatTelemetryModule` を有効または無効にします | true
|EnableAzureInstanceMetadataTelemetryModule   |  `AzureInstanceMetadataTelemetryModule` を有効または無効にします | true
|EnableQuickPulseMetricStream | LiveMetrics 機能を有効または無効にします | true
|EnableAdaptiveSampling | アダプティブ サンプリングを有効または無効にします | true
|EnableHeartbeat | ハートビート機能を有効または無効にします。この機能は、"HeartbeatState" という名前のカスタム メトリックを、.NET バージョン、Azure 環境情報 (該当する場合) などのランタイムに関する情報と共に定期的に (既定では 15 分) 送信します。 | true
|AddAutoCollectedMetricExtractor | AutoCollectedMetrics エクストラクターを有効または無効にします。これは、サンプリングが行われる前に要求/依存関係に関する事前に集計されたメトリックを送信する TelemetryProcessor です。 | true
|RequestCollectionOptions.TrackExceptions | 要求収集モジュールによる未処理の例外の追跡についてのレポートを有効または無効にします。 | NETSTANDARD 2.0 では false (例外は ApplicationInsightsLoggerProvider で追跡されるため) で、それ以外の場合は true です。
|EnableDiagnosticsTelemetryModule | `DiagnosticsTelemetryModule` を有効または無効にします。 これを無効にすると、次の設定が無視されます: `EnableHeartbeat`、`EnableAzureInstanceMetadataTelemetryModule`、`EnableAppServicesHeartbeatTelemetryModule` | true

最新の一覧については、[`ApplicationInsightsServiceOptions` の構成可能な設定](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)を参照してください。

### <a name="configuration-recommendation-for-microsoftapplicationinsightsaspnetcore-sdk-2150--above"></a>Microsoft.ApplicationInsights.AspNetCore SDK 2.15.0 以降の構成に関する推奨事項

Microsoft.ApplicationInsights.AspNetCore SDK バージョン [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) 以降では、アプリケーションの `IConfiguration` インスタンスを使用する instrumentationkey を含む、`ApplicationInsightsServiceOptions` で使用可能なすべての設定を構成することをお勧めします。 次の例に示すように、設定は "ApplicationInsights" セクションの下に記載されている必要があります。 appsettings.json の次のセクションは、インストルメンテーション キーを構成し、アダプティブ サンプリングとパフォーマンス カウンターの収集も無効にします。

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

`services.AddApplicationInsightsTelemetry(aiOptions)` を使用した場合、`Microsoft.Extensions.Configuration.IConfiguration` の設定がオーバーライドされます。

### <a name="sampling"></a>サンプリング

Application Insights SDK for ASP.NET Core では、固定レートとアダプティブ サンプリングの両方がサポートされています。 アダプティブ サンプリングは、既定で有効になっています。

詳しくは、「[ASP.NET Core アプリケーションのためのアダプティブ サンプリングの構成](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)」をご覧ください。

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers の追加

追加の情報でテレメトリをエンリッチするには、[TelemetryInitializers](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) を使用します。

次のコードで示すように、新しい `TelemetryInitializer` を `DependencyInjection` コンテナーに追加します。 `DependencyInjection` コンテナーに追加した `TelemetryInitializer` は、SDK によって自動的に取得されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();` は単純な初期化子に対して機能します。 それ以外には、次のものが必要です: `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`
    
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

拡張メソッド `AddApplicationInsightsTelemetryProcessor` を `IServiceCollection` で使用することで、カスタム テレメトリ プロセッサを `TelemetryConfiguration` に追加できます。 テレメトリ プロセッサは、[高度なフィルター処理のシナリオ](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer)で使用します。 次の例を使用してください。

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

Application Insights では、テレメトリ モジュールを使用して、ユーザーが手動で追跡することなく特定のワークロードに関する有用なテレメトリが自動収集されます。

次の自動収集モジュールが既定で有効になります。 これらのモジュールでは、自動的にテレメトリが収集されます。 それらを無効にするか構成して、既定の動作を変更できます。

* `RequestTrackingTelemetryModule` - 受信 Web 要求から RequestTelemetry を収集します。
* `DependencyTrackingTelemetryModule` - 送信 HTTP 呼び出しと SQL 呼び出しから [DependencyTelemetry](./asp-net-dependencies.md) を収集します。
* `PerformanceCollectorModule` - Windows PerformanceCounters を収集します。
* `QuickPulseTelemetryModule` - Live Metrics ポータルで表示するテレメトリを収集します。
* `AppServicesHeartbeatTelemetryModule` - アプリケーションがホストされる Azure App Service 環境について、(カスタム メトリックとして送信される) ハート ビートを収集します。
* `AzureInstanceMetadataTelemetryModule` - アプリケーションがホストされる Azure VM 環境について、(カスタム メトリックとして送信される) ハート ビートを収集します。
* `EventCounterCollectionModule` - [EventCounter](eventcounters.md) を収集します。 このモジュールは新機能であり、SDK バージョン 2.8.0 以降で使用できます。

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

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

2\.12.2 バージョン以降、[`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) には、任意の既定のモジュールを無効にする簡単なオプションが含まれています。

### <a name="configuring-a-telemetry-channel"></a>テレメトリ チャネルを構成する

既定の[テレメトリ チャネル](./telemetry-channels.md)は `ServerTelemetryChannel` です。 次の例のようにしてオーバーライドできます。

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

### <a name="disable-telemetry-dynamically"></a>テレメトリを動的に無効にする

テレメトリを条件に基づき、動的に無効にする場合、コードの任意の場所に ASP.NET Core 依存関係挿入コンテナーを持つ `TelemetryConfiguration` インスタンスを解決し、それに `DisableTelemetry` フラグを設定できます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

上記は、自動収集モジュールでテレメトリが収集されないようにするものではありません。 上記の方法では、Application Insights へのテレメトリの送信のみが無効になります。 特定の自動収集モジュールが必要でない場合は、[テレメトリ モジュールを削除する](#configuring-or-removing-default-telemetrymodules)ことをお勧めします

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights で ASP.NET Core 3.X はサポートされますか?

はい。 [Application Insights SDK for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.8.0 以降に更新してください。 これより古いバージョンの SDK では、ASP.NET Core 3.X はサポートされていません。

また、[こちら](#enable-application-insights-server-side-telemetry-visual-studio)の Visual Studio ベースの手順を使用している場合は、最新バージョンの Visual Studio 2019 (16.3.0) に更新してオンボードしてください。 以前のバージョンの Visual Studio では、ASP.NET Core 3.X アプリの自動オンボードはサポートされていません。

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>自動的に収集されないテレメトリを追跡するにはどうすればよいですか?

コンストラクター インジェクションを使用して `TelemetryClient` のインスタンスを取得し、そのインスタンスで必須の `TrackXXX()` メソッドを呼び出します。 ASP.NET Core アプリケーションで新しい `TelemetryClient` または `TelemetryConfiguration` のインスタンスを作成することはお勧めしません。 `TelemetryClient` のシングルトン インスタンスが `DependencyInjection` コンテナーに既に登録されており、それによって `TelemetryConfiguration` がテレメトリの残りの部分と共有されます。 新しい `TelemetryClient` インスタンスの作成は、残りのテレメトリとは別の構成が必要な場合にのみ推奨されます。

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

Application Insights でのカスタム データ レポートについては、[Application Insights カスタム メトリック API リファレンス](./api-custom-events-metrics.md)に関するページを参照してください。 同様の方法を使用して、[GetMetric API](./get-metric.md) を使用して Application Insights にカスタム メトリックを送信することもできます。

### <a name="how-do-i-customize-ilogger-logs-collection"></a>ILogger logs コレクションをカスタマイズする方法

既定では、重大度 `Warning` 以上のログのみが自動的にキャプチャされます。 この動作を変更するには、次に示すように、プロバイダー `ApplicationInsights` のログ構成を明示的にオーバーライドします。
次の構成では、ApplicationInsights は重大度 `Information` 以上のすべてのログをキャプチャできます。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

次のような場合は、ApplicationInsights プロバイダーによって `Information` ログがキャプチャされないことに注意してください。 これは、SDK によって既定のログフィルターが追加され、`Warning` 以降のみをキャプチャするように `ApplicationInsights` を指示するためです。 このため、ApplicationInsights には明示的なオーバーライドが必要です。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

詳細については「[ILogger configuration](ilogger.md#control-logging-level)」を参照してください。

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>一部の Visual Studio テンプレートでは、Application Insights を有効にする目的で UseApplicationInsights() 拡張メソッドが IWebHostBuilder で使用されていました。 この使用方法は今でも有効ですか?

拡張メソッド `UseApplicationInsights()` はまだサポートされていますが、Application Insights SDK バージョン 2.8.0 以降では古いものとしてマークされています。 次のメジャー バージョンの SDK で削除されます。 Application Insights テレメトリを有効にする方法としては、いくつかの構成を制御するためのオーバーロードが用意されているため、`AddApplicationInsightsTelemetry()` を使用することをお勧めします。 また、ASP.NET Core 3.X アプリでは、`services.AddApplicationInsightsTelemetry()` が Application Insights を有効にする唯一の方法です。

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>ASP.NET Core アプリケーションを Web Apps にデプロイしています。 Application Insights 拡張を Web アプリから有効にできますか?

この記事に示されているように、SDK がビルド時にインストールされる場合は、App Service ポータルから [Application Insights の拡張機能](./azure-web-apps.md)を有効にする必要はありません。 拡張機能はインストールされていても、既に SDK がアプリケーションに追加されていることが検出されると、バックオフします。 拡張機能から Application Insights を有効にする場合、SDK をインストールして更新する必要はありません。 ただし、この記事の手順に従って Application Insights を有効にする場合は、次のような理由で柔軟性が増します。

   * Application Insights テレメトリは以下で引き続き機能します。
       * Windows、Linux、Mac を含む、すべてのオペレーティング システム。
       * 自己完結型やフレームワーク依存型など、すべての発行モード。
       * 完全 .NET Framework を含む、すべてのターゲット フレームワーク。
       * Web Apps、VM、Linux、コンテナー、Azure Kubernetes Service、Azure 以外のホスティングなど、すべてのホスティング オプション。
       * プレビュー バージョンを含むすべての .NET Core バージョン。
   * Visual Studio からデバッグしているとき、テレメトリをローカル環境で見ることができます。
   * `TrackXXX()` API を使って、追加のカスタム テレメトリを追跡できます。
   * 構成を完全に制御できます。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Status Monitor などのツールを利用して Application Insights 監視を有効にできますか?

いいえ。 現在、[Status Monitor](./monitor-performance-live-website-now.md) と [Status Monitor v2](./status-monitor-v2-overview.md) では、ASP.NET 4.x のみがサポートされます。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux でアプリケーションを実行する場合、すべての機能がサポートされますか?

はい。 SDK の機能サポートは、次の例外を除き、すべてのプラットフォームで同じです。

* [パフォーマンス カウンター](./performance-counters.md)は Windows でのみサポートされているため、この SDK を使って Linux 上の[イベント カウンター](./eventcounters.md)を収集します。 ほとんどのメトリックは同じです。
* `ServerTelemetryChannel` が既定で有効になっていても、アプリケーションが Linux または macOS で実行されているときは、ネットワークに問題がある場合に、チャネルによってテレメトリを一時的に保持するためのローカル ストレージ フォルダーが自動的に作成されることはありません。 この制限のため、ネットワークやサーバーに一時的に問題が発生すると、テレメトリが失われます。 この問題を回避するには、チャネル用のローカル フォルダーを構成します。

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

この制限は、[2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) 以降のバージョンには適用されません。

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>この SDK は、新しい .NET Core 3.X ワーカー サービス テンプレート アプリケーションでサポートされていますか?

この SDK には `HttpContext` が必要であるため、.NET Core 3.X ワーカー サービス アプリケーションを含め、HTTP 以外のアプリケーションでは機能しません。 新しくリリースされた Microsoft.ApplicationInsights.WorkerService SDK を使用して、このようなアプリケーションで Application Insights を有効にする方法については、[こちら](worker-service.md)を参照してください。

## <a name="open-source-sdk"></a>オープンソース SDK

* [コードを読んで協力してください。](https://github.com/microsoft/ApplicationInsights-dotnet)

最新の更新プログラムとバグ修正については、[リリース ノートを参照してください](./release-notes.md)。

## <a name="next-steps"></a>次のステップ

* [ユーザー フローの探索](./usage-flows.md): ユーザーがアプリ内をどのように移動しているかを把握します。
* [スナップショット コレクションを構成](./snapshot-debugger.md)して、例外がスローされたときのソース コードと変数の状態を確認します。
* [API を使用](./api-custom-events-metrics.md)して、アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。
* [可用性テスト](./monitor-web-app-availability.md)の使用: 世界中からアプリを常にチェックします。
* [ASP.NET Core での依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)
