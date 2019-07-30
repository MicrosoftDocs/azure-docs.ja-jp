---
title: コンソール アプリケーション用の Azure Application Insights | Microsoft Docs
description: Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 0c2a28462633d47ad1d3f247793e3fcf6f4d40c0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795457"
---
# <a name="application-insights-for-net-console-applications"></a>.NET コンソール アプリケーション用の Application Insights
[Application Insights](../../azure-monitor/app/app-insights-overview.md) を使うと、Web アプリケーションの可用性、パフォーマンス、利用状況を監視できます。

[Microsoft Azure](https://azure.com) のサブスクリプションが必要になります。 Windows、Xbox Live、またはその他の Microsoft クラウド サービスの Microsoft アカウントでサインインします。 所属するチームが組織の Azure サブスクリプションを持っている場合は、自分の Microsoft アカウントを使用してサブスクリプションに追加してもらうよう所有者に依頼してください。

## <a name="getting-started"></a>使用の開始

* [Azure Portal](https://portal.azure.com) で、[Application Insights のリソースを作成します](../../azure-monitor/app/create-new-resource.md)。 アプリケーションの種類として **[一般]** を選びます。
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

[Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) パッケージの最新バージョンをインストールすることにより、構成ファイルの完全な例を入手できます。 ここでは、このコード例と同等の依存関係コレクションの**最小**構成を示します。

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
> .NET Core では、構成ファイルの読み取りはサポートされていません。 [Application Insights SDK for ASP.NET Core](../../azure-monitor/app/asp-net-core.md) の使用をご検討ください

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

* [こちら](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)の説明に従って、パフォーマンス カウンター コレクター モジュールをインストールし、初期化することもできます


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

            var telemetryClient = new TelemetryClient();
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

            // flush is not blocking so wait a bit
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
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>次の手順
* [依存関係の監視](../../azure-monitor/app/asp-net-dependencies.md): REST、SQL、その他の外部リソースの処理速度が低下しているかどうかを確認します。
* [API の使用](../../azure-monitor/app/api-custom-events-metrics.md) : アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。
