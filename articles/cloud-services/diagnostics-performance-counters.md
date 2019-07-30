---
title: Azure Cloud Services でパフォーマンス カウンターを収集する | Microsoft Docs
description: Azure Diagnostics および Application Insights を使用して、Cloud Services でパフォーマンス カウンターを検出、使用、作成する方法について説明します。
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 02/02/2018
ms.author: gwallace
ms.openlocfilehash: d6b16b859b29ef835bca75c5fca0ea1a9d35a306
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358937"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Azure Cloud Services のパフォーマンス カウンターの収集

パフォーマンス カウンターを使用すると、アプリケーションとホストがどの程度動作しているかを追跡できます。 Windows Server には、ハードウェア、アプリケーション、オペレーティング システムなどに関連したさまざまなパフォーマンス カウンターが多数用意されています。 パフォーマンス カウンターを収集して Azure に送信することにより、この情報を分析してより適切な決定を下すことができます。 

## <a name="discover-available-counters"></a>使用可能なカウンターの検出

パフォーマンス カウンターは、セット名 (カテゴリとも呼ばれます) と 1 つ以上のカウンターという 2 つの部分で構成されています。 PowerShell を使用して、使用可能なパフォーマンス カウンターの一覧を取得できます。

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

`CounterSetName` プロパティはセット (またはカテゴリ) を表し、パフォーマンス カウンターが何に関連しているかを適切に示しています。 `Paths` プロパティは、セットのカウンターのコレクションを表します。 また、カウンター セットの詳細については、`Description` プロパティを取得することもできます。

セットのすべてのカウンターを取得するには、`CounterSetName` 値を使用し、`Paths` コレクションを展開します。 各パス項目は、照会できるカウンターです。 たとえば、`Processor` セットに関連した使用可能なカウンターを取得するには、`Paths` コレクションを展開します。

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

このような個々のカウンター パスは、クラウド サービスで使用される診断フレームワークに追加できます。 パフォーマンス カウンター パスの構築方法の詳細については、「[Specifying a Counter Path (カウンター パスの指定)](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))」を参照してください。

## <a name="collect-a-performance-counter"></a>パフォーマンス カウンターの収集

パフォーマンス カウンターは、Azure Diagnostics または Application Insights のクラウド サービスに追加できます。

### <a name="application-insights"></a>Application Insights

Cloud Services 用の Azure Application Insights では、収集するパフォーマンス カウンターを指定できます。 [プロジェクトに Application Insights を追加する](../azure-monitor/app/cloudservices.md#sdk)と、**ApplicationInsights.config** という名前の構成ファイルが Visual Studio プロジェクトに追加されます。 この構成ファイルでは、Application Insights で収集して Azure に送信する情報の種類を定義します。

**ApplicationInsights.config** ファイルを開き、**ApplicationInsights** > **TelemetryModules** 要素を見つけます。 各 `<Add>` 子要素では、収集するテレメトリの種類をその構成と共に定義します。 パフォーマンス カウンター テレメトリ モジュールの種類は `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector` です。 この要素が既に定義されている場合は、もう一度追加しないでください。 収集する各パフォーマンス カウンターは、`<Counters>` という名前のノードの下で定義されます。 ドライブ パフォーマンス カウンターを収集する例を次に示します。

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

各パフォーマンス カウンターは、`<Counters>` の下にある `<Add>` 要素として表されます。 `PerformanceCounter` 属性では、収集するパフォーマンス カウンターを定義します。 `ReportAs` 属性は、Azure Portal に表示されるパフォーマンス カウンターのタイトルです。 収集したパフォーマンス カウンターは、ポータルの **[カスタム]** というカテゴリに分類されます。 Azure Diagnostics とは異なり、これらのパフォーマンス カウンターを収集して Azure に送信する間隔を設定することはできません。 Application Insights では、1 分ごとにパフォーマンス カウンターが収集され、送信されます。 

Application Insights は自動的に次のパフォーマンス カウンターを収集します。

* \Process(??APP_WIN32_PROC??)\%Processor Time
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

詳細については、「[Application Insights のシステム パフォーマンス カウンター](../azure-monitor/app/performance-counters.md)」および「[Azure Cloud Services 向けの Application Insights](../azure-monitor/app/cloudservices.md#performance-counters)」を参照してください。

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> これらすべてのデータがストレージ アカウントに集計されますが、データのグラフを作成するポータル固有の方法は用意されて**いません**。 Application Insights などの他の診断サービスをアプリケーションに統合することを強くお勧めします。

Cloud Services 用の Azure Diagnostics 拡張機能では、収集するパフォーマンス カウンターを指定できます。 Azure Diagnostics を設定するには、[クラウド サービスの監視の概要](cloud-services-how-to-monitor.md#setup-diagnostics-extension)に関するページを参照してください。

収集するパフォーマンス カウンターは、**diagnostics.wadcfgx** ファイルで定義されています。 このファイル (ロールごとに定義されています) を Visual Studio で開き、**DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters** 要素を探します。 新しい **PerformanceCounterConfiguration** 要素を子として追加します。 この要素には、`counterSpecifier` と `sampleRate` の 2 つの属性があります。 `counterSpecifier` 属性では、収集するシステム パフォーマンス カウンター セット (前のセクションで説明) を定義します。 `sampleRate` 値は、その値がポーリングされる頻度を示します。 全体として、すべてのパフォーマンス カウンターは、親 `PerformanceCounters` 要素の `scheduledTransferPeriod` 属性値に従って Azure に転送されます。

`PerformanceCounters` スキーマ要素の詳細については、[Azure Diagnostics スキーマ](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)に関するページを参照してください。

`sampleRate` 属性で定義された期間は、XML 期間データ型を使用して、パフォーマンス カウンターのポーリング頻度を示します。 下の例では、頻度が `PT3M` に設定されています。これは、`[P]eriod[T]ime[3][M]inutes` (3 分ごと) を意味します。

`sampleRate` と `scheduledTransferPeriod` の定義方法の詳細については、[W3 の XML の日付データ型と時刻データ型](https://www.w3schools.com/XML/schema_dtypes_date.asp)に関するチュートリアルの**期間データ型**に関するセクションを参照してください。

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>新しいパーフォーマンス カウンターの作成

新しいパフォーマンス カウンターは、コードで作成して使用することができます。 新しいパフォーマンス カウンターを作成するコードは、管理者特権で実行されている必要があります。そうでなければ、失敗します。 クラウド サービスの `OnStart` のスタートアップ コードではパフォーマンス カウンターを作成できるため、管理者特権のコンテキストでロールを実行することが必要になります。 また、管理者特権で実行され、パフォーマンス カウンターを作成するスタートアップ タスクを作成することもできます。 スタートアップ タスクの詳細については、「[クラウド サービスのスタートアップ タスクを構成して実行する方法](cloud-services-startup-tasks.md)」を参照してください。

管理者特権で実行するようにロールを構成するには、[.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) ファイルに `<Runtime>` 要素を追加します。

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

数行のコードで新しいパフォーマンス カウンターを作成して登録することができます。 カテゴリとカウンターの両方を作成する `System.Diagnostics.PerformanceCounterCategory.Create` メソッド オーバーロードを使用します。 次のコードでは、まずカテゴリが存在するかどうかを確認し、存在しない場合にカテゴリとカウンターの両方を作成します。

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

カウンターを使用する場合は、`Increment` メソッドまたは `IncrementBy` メソッドを呼び出します。

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

アプリケーションでカスタム カウンターが使用されているため、カウンターを追跡するように Azure Diagnostics または Application Insights を構成する必要があります。


### <a name="application-insights"></a>Application Insights

既に説明したとおり、Application Insights のパフォーマンス カウンターは **ApplicationInsights.config** ファイルで定義されています。 **ApplicationInsights.config** を開き、**ApplicationInsights** > **TelemetryModules** > **Add** > **Counters** 要素を見つけます。 `<Add>` 子要素を作成し、`PerformanceCounter` 属性を、コードで作成したパフォーマンス カウンターのカテゴリと名前に設定します。 `ReportAs` 属性を、ポータルに表示するわかりやすい名前に設定します。

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

既に説明したとおり、収集するパフォーマンス カウンターは、**diagnostics.wadcfgx** ファイルで定義されています。 このファイル (ロールごとに定義されています) を Visual Studio で開き、**DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters** 要素を探します。 新しい **PerformanceCounterConfiguration** 要素を子として追加します。 `counterSpecifier` 属性を、コードで作成したパフォーマンス カウンターのカテゴリと名前に設定します。 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>詳細情報

- [Azure Cloud Services 向けの Application Insights](../azure-monitor/app/cloudservices.md#performance-counters)
- [Application Insights のシステム パフォーマンス カウンター](../azure-monitor/app/performance-counters.md)
- [カウンター パスの指定](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure Diagnostics のスキーマ - パフォーマンス カウンター](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)
