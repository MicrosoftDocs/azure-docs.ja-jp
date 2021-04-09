---
title: コンソール アプリケーション用の Azure Application Insights | Microsoft Docs
description: Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: aa39a1eca04621fc4db75f755402d3679403e814
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920594"
---
# <a name="application-insights-for-net-console-applications"></a>.NET コンソール アプリケーション用の Application Insights

[Application Insights](./app-insights-overview.md) を使うと、Web アプリケーションの可用性、パフォーマンス、利用状況を監視できます。

[Microsoft Azure](https://azure.com) のサブスクリプションが必要になります。 Windows、Xbox Live、またはその他の Microsoft クラウド サービスの Microsoft アカウントでサインインします。 所属するチームが組織の Azure サブスクリプションを持っている場合は、自分の Microsoft アカウントを使用してサブスクリプションに追加してもらうよう所有者に依頼してください。

> [!NOTE]
> コンソール アプリケーションについては、[ここ](./worker-service.md)から [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) パッケージおよび関連付けられた手順を使用することを *強くお勧めします*。 このパッケージは [`NetStandard2.0`](/dotnet/standard/net-standard) を対象としているため、.NET Core 2.1 以上と .NET Framework 4.7.2 以上で使用できます。

## <a name="getting-started"></a>作業の開始

> [!IMPORTANT]
> 新しい Azure リージョンでは、インストルメンテーション キーの代わりに接続文字列を使用する **必要** があります。 [接続文字列](./sdk-connection-string.md?tabs=net)により、利用統計情報と関連付けるリソースが識別されます。 また、リソースでテレメトリの宛先として使用するエンドポイントを変更することもできます。 接続文字列をコピーし、アプリケーションのコードまたは環境変数に追加する必要があります。

* [Azure Portal](https://portal.azure.com) で、[Application Insights のリソースを作成します](./create-new-resource.md)。 アプリケーションの種類として **[一般]** を選びます。
* インストルメンテーション キーをコピーします。 先ほど作成した新しいリソースの **[要点]** ドロップダウン リストで、キーを探します。
* 最新の [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) パッケージをインストールします。
* テレメトリを追跡する前に、コードにインストルメンテーション キーを設定します (または APPINSIGHTS_INSTRUMENTATIONKEY 環境変数を設定します)。 その後、テレメトリを手動で追跡して、Azure Portal に表示します。

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> テレメトリはすぐには送信されません。 テレメトリ項目はバッチ処理され、ApplicationInsights SDK によって送信されます。 `Track()` メソッドを呼び出した直後に終了するコンソール アプリでは、この記事で後述する[完全な例](#full-example)に示すように、アプリが終了する前に `Flush()` と `Sleep`/`Delay` が完了しない限り、テレメトリが送信されない場合があります。 `InMemoryChannel` を使用する場合、`Sleep` は必要ありません。 `Sleep` の必要性に関してはアクティブな問題があります。参照先:[ApplicationInsights-dotnet/issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) パッケージの最新バージョンをインストールします。このパッケージにより、HTTP、SQL、またはその他の外部の依存関係呼び出しが自動的に追跡されます。

コードから、または `ApplicationInsights.config` ファイルを使用して、Application Insights を初期化し、構成できます。 初期化はできるだけ早期に実行するようにします。 

> [!NOTE]
> **ApplicationInsights.config** を参照している説明は、.NET Framework を対象とするアプリに対してのみ適用され、.NET Core アプリケーションには適用されません。

### <a name="using-config-file"></a>構成ファイルを使用する

既定では、`TelemetryConfiguration` が作成されると、Application Insights SDK は作業ディレクトリで `ApplicationInsights.config` ファイルを検索します。

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

構成ファイルのパスを指定することもできます。

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

詳細については、[構成ファイル リファレンス](configuration-with-applicationinsights-config.md)に関するページをご覧ください。

[Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) パッケージの最新バージョンをインストールすることにより、構成ファイルの完全な例を入手できます。 ここでは、このコード例と同等の依存関係コレクションの **最小** 構成を示します。

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>コードからテレメトリ コレクションを構成する
> [!NOTE]
> .NET Core では、構成ファイルの読み取りはサポートされていません。 [Application Insights SDK for ASP.NET Core](./asp-net-core.md) の使用をご検討ください

* アプリケーションの起動中に、`DependencyTrackingTelemetryModule` インスタンスを作成し、構成します。このインスタンスは単一にする必要があり、また、アプリケーションの有効期間中は保持する必要があります。

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* 共通のテレメトリ初期化子を追加します。

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

プレーンな `TelemetryConfiguration()` コンストラクターを使用して構成を作成した場合は、さらに相関関係のサポートを有効にする必要があります。 ファイルから構成を読み取り、`TelemetryConfiguration.CreateDefault()` または `TelemetryConfiguration.Active` を使用した場合は、**不要です**。

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* [こちら](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/)の説明に従って、パフォーマンス カウンター コレクター モジュールをインストールし、初期化することもできます


#### <a name="full-example"></a>完全な例

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>次のステップ
* [依存関係の監視](./asp-net-dependencies.md): REST、SQL、その他の外部リソースの処理速度が低下しているかどうかを確認します。
* [API の使用](./api-custom-events-metrics.md) : アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。

