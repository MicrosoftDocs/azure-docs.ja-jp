---
title: Azure Service Fabric、クラウド サービス、および Virtual Machines で .NET アプリのスナップショット デバッガーを有効にする | Microsoft Docs
description: Azure Service Fabric、クラウド サービス、および Virtual Machines で .NET アプリのスナップショット デバッガーを有効にする
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: ac937ddb1bcaed6813a0de4d631f820eff01e26f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877740"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Azure Service Fabric、クラウド サービス、および Virtual Machines で .NET アプリのスナップショット デバッガーを有効にする

ASP.NET または ASP.NET Core のアプリケーションを Azure App Service で実行する場合は、以下の手順を使用することもできます。 カスタマイズされたスナップショット デバッガー構成がアプリケーションで必要でなければ、[Application Insights ポータル ページからスナップショット デバッガーを有効にする](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)ことを強くお勧めします。 アプリケーションを Azure Service Fabric、クラウド サービス、Virtual Machines、またはオンプレミスのマシンで実行する場合、次の手順を使用する必要があります。 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>ASP.NET アプリケーションのスナップショット コレクションの構成

1. まだ有効にしていない場合は、[Web アプリで Application Insights を有効](../../azure-monitor/app/asp-net.md)にします。

2. [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに含めます。

3. 必要に応じて、[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) に追加されたスナップショット デバッガー構成をカスタマイズします。既定のスナップショット デバッガー構成はほぼ空であり、すべての設定が省略可能です。 既定の構成と同じ構成を示す例を次に示します。

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. スナップショットは、Application Insights にレポートされる例外についてのみ収集されます。 場合によっては (たとえば、.NET プラットフォームの古いバージョン)、[例外コレクションを構成](../../azure-monitor/app/asp-net-exceptions.md#exceptions)して、ポータルでスナップショット付きの例外を表示する必要があります。


## <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>ASP.NET Core 2.0 アプリケーションのスナップショット コレクションの構成

1. まだ有効にしていない場合は、[ASP.NET Core Web アプリで Application Insights を有効](../../azure-monitor/app/asp-net-core.md)にします。

    > [!NOTE]
    > お使いのアプリケーションが、2.1.1 以降のバージョンの Microsoft.ApplicationInsights.AspNetCore パッケージを参照していることを確認します。

2. [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに含めます。

3. アプリケーションの `Startup` クラスを変更し、スナップショット コレクターのテレメトリ プロセッサを追加および構成します。

    次の using ステートメントを `Startup.cs` に追加します。

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   次の `SnapshotCollectorTelemetryProcessorFactory`クラスを `Startup` クラスに追加します。

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    `SnapshotCollectorConfiguration` と `SnapshotCollectorTelemetryProcessorFactory` サービスをスタートアップ パイプラインに追加します。

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. 必要に応じて、appsettings.json に SnapshotCollectorConfiguration セクションを追加して、スナップショット デバッガー構成をカスタマイズします。 スナップショット デバッガー構成のすべての設定は省略可能です。 既定の構成と同じ構成を示す例を次に示します。

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>その他の .NET アプリケーションのスナップショット コレクションの構成

1. アプリケーションがまだ Application Insights を使用してインストルメント化されていない場合、まず[Application Insights を有効にし、インストルメンテーション キーを設定](../../azure-monitor/app/windows-desktop.md)します。

2. [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに追加します。

3. スナップショットは、Application Insights にレポートされる例外についてのみ収集されます。 例外をレポートするようにコードを変更する必要があります。 例外処理コードは、アプリケーションの構造によって異なります。次に例を示します。
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>次の手順

- 例外をトリガーできるアプリケーションへのトラフィックを生成します。 その後、Application Insights インスタンスにスナップショットが送信がされるまで 10 ～ 15 分待機します。
- Azure portal で[スナップショット](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json)を確認します。
- Profiler の問題のトラブルシューティングについては、[スナップショット デバッガーのトラブルシューティング](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)に関する記事をご覧ください。
