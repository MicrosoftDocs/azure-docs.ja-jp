---
title: Application Insights のパフォーマンス カウンター | Microsoft Docs
description: Application Insights でシステムとカスタムの .NET パフォーマンス カウンターを監視します。
ms.topic: conceptual
ms.date: 12/13/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 423e17ef2b44286c28b464836075284929d8644c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031363"
---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights のシステム パフォーマンス カウンター

Windows には、CPU 占有率や、メモリ、ディスク、ネットワークの使用率など、広範な[パフォーマンス カウンター](/windows/desktop/perfctrs/about-performance-counters)が用意されています。 ユーザーが独自のパフォーマンス カウンターを定義することもできます。 アプリケーションがオンプレミス ホスト上、または管理権限を持っている仮想マシン上の IIS で実行されている限り、パフォーマンス カウンターの収集はサポートされます。 Azure Web Apps として実行されているアプリケーションはパフォーマンス カウンターには直接アクセスできませんが、使用可能なカウンターのサブセットが Application Insights によって収集されます。

## <a name="view-counters"></a>カウンターを表示する

[メトリック] ウィンドウには、既定のパフォーマンス カウンターのセットが表示されます。

![Application Insights で表示されるパフォーマンス カウンター](./media/performance-counters/performance-counters.png)

ASP.NET Web アプリケーションについて収集するように構成されている現在の既定カウンターは次のとおりです。
- % Process\\Processor Time
- % Process\\Processor Time Normalized
- Memory\\Available Bytes
- ASP.NET Requests/Sec
- .NET CLR Exceptions Thrown / sec
- ASP.NET ApplicationsRequest Execution Time
- Process\\Private Bytes
- Process\\IO Data Bytes/sec
- ASP.NET Applications\\Requests In Application Queue
- Processor(_Total)\\% Processor Time

ASP.NET Core Web アプリケーションについて収集するように構成されている現在の既定カウンターは次のとおりです。
- % Process\\Processor Time
- % Process\\Processor Time Normalized
- Memory\\Available Bytes
- Process\\Private Bytes
- Process\\IO Data Bytes/sec
- Processor(_Total)\\% Processor Time

## <a name="add-counters"></a>カウンターを追加する

目的のパフォーマンス カウンターがメトリックの一覧に含まれていない場合は、追加することができます。

1. サーバーで使えるカウンターを確認するには、ローカル サーバーで次の PowerShell コマンドを実行します。

    `Get-Counter -ListSet *`

    (「[`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter)」をご覧ください)
2. ApplicationInsights.config を開きます。

   * 開発中にアプリに Application Insights を追加した場合は、プロジェクトで ApplicationInsights.config を編集して、サーバーに再デプロイします。
3. パフォーマンス コレクター ディレクティブを編集します。

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core アプリケーションには `ApplicationInsights.config` がないため、上記の方法は ASP.NET Core アプリケーションには無効です。

標準カウンターとカスタム カウンターの両方をキャプチャできます。 `\Objects\Processes` は、すべての Windows システムで使える標準カウンターの例です。 `\Sales(photo)\# Items Sold` は、Web サービスに実装されているカスタム カウンターの例です。

形式は `\Category(instance)\Counter"` です。インスタンスが存在しないカテゴリの場合は、単に `\Category\Counter` です。

`[a-zA-Z()/-_ \.]+` に一致しないカウンター名の場合、つまり英字、丸かっこ、スラッシュ、ハイフン、アンダースコア、スペース、ドット (.) 以外の文字が含まれる場合は、`ReportAs` が必要です。

インスタンスを指定した場合は、報告されるメトリックの "CounterInstanceName" ディメンションとして収集されます。

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>ASP.NET Web アプリケーションまたは .NET/.NET Core Console アプリケーションについてコードでパフォーマンス カウンターを収集する
システム パフォーマンス カウンターを収集し、それらを Application Insights に送信する場合は、次のスニペットを使用できます。


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

作成したカスタム メトリックの場合も同様です。

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>ASP.NET Core Web アプリケーションについてコードでパフォーマンス カウンターを収集する

以下の `Startup.cs` クラスの `ConfigureServices` メソッドを変更します。

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Analytics のパフォーマンス カウンター
[Analytics](../logs/log-query-overview.md) でパフォーマンス カウンター レポートを検索して表示できます。

**performanceCounters** スキーマは、各パフォーマンス カウンターの `category`、`counter` 名、および `instance` 名を表示します。  各アプリケーションのテレメトリでは、そのアプリケーションのカウンターのみが確認できます。 たとえば、使用できるカウンターを表示するには次のようにします。 

![Application Insights Analytics のパフォーマンス カウンター](./media/performance-counters/analytics-performance-counters.png)

(ここで、"instance" は役割またはサーバー マシンのインスタンスではなくパフォーマンス カウンターのインスタンスを示します。 パフォーマンス カウンター インスタンス名は通常、プロセスまたはアプリケーションの名前によって、プロセッサ時間などのカウンターをセグメントに分割します。)

最近の利用可能なメモリのグラフを取得するには、次のようにします。 

![Application Insights Analytics のメモリ タイムチャート](./media/performance-counters/analytics-available-memory.png)

他のテレメトリと同様に、**performanceCounters** にも、アプリを実行しているホスト サーバー インスタンスの ID を示す列 `cloud_RoleInstance` があります。 たとえば、異なるコンピューター上でのアプリのパフォーマンスを比較するには、次のようにします。 

![Application Insights Analytics でロール インスタンス別にセグメント化されたパフォーマンス](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET と Application Insights のカウント

*例外レートと例外のメトリックの違いは何ですか*

* *例外レート* はシステム パフォーマンス カウンターです。 CLR ではスローされた処理済みおよび未処理の例外をすべてカウントし、特定のサンプリング時間間隔での合計をその時間間隔の長さで除算します。 Application Insights SDK では、この結果を収集し、ポータルに送信します。

* *例外* は、グラフのサンプリング時間間隔中にポータルが受信した TrackException レポートの数です。 これには、コード内で TrackException 呼び出しが記述されている処理済みの例外のみが含まれ、 [未処理の例外](./asp-net-exceptions.md)はいずれも含められません。 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Azure Web Apps で実行されているアプリケーションのパフォーマンス カウンター

Azure Web Apps にデプロイされた ASP.NET アプリケーションと ASP.NET Core アプリケーションは、特別なサンド ボックス環境で実行されます。 この環境では、システム パフォーマンス カウンターへの直接アクセスは許可されません。 ただし、[ここ](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)に記載されているように、限定されたカウンターのサブセットが、環境変数として公開されています。 ASP.NET および ASP.NET Core 用 Application Insights SDK は、これらの環境変数から、Azure Web Apps からのパフォーマンス カウンターを収集します。 この環境ではカウンターのサブセットのみが使用可能で、完全な一覧は[ここ](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)にあります。

## <a name="performance-counters-in-aspnet-core-applications"></a>ASP.NET Core アプリケーションのパフォーマンス カウンター

ASP.NET Core でのパフォーマンス カウンターのサポートは制限されています。

* [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) バージョン 2.4.1 以降では、アプリケーションが Azure Web Apps (Windows) で実行されている場合、パフォーマンス カウンターが収集されます。
* SDK バージョン 2.7.1 以降では、アプリケーションが Windows で実行されていて、`NETSTANDARD2.0` 以降を対象とする場合、パフォーマンス カウンターが収集されます。
* .NET Framework を対象とするアプリケーションの場合、すべてのバージョンの SDK でパフォーマンス カウンターがサポートされます。
* SDK バージョン 2.8.0 以降では、Linux の CPU/メモリ カウンターがサポートされます。 Linux では、その他のカウンターはサポートされません。 Linux (およびその他の非 Windows 環境) でシステム カウンターを取得するには、[EventCounter](eventcounters.md) を使用することをお勧めします。

## <a name="alerts"></a>警告
他のメトリックと同様に、パフォーマンス カウンターが指定した制限を超えた場合に警告する[アラートを設定](../alerts/alerts-log.md)できます。 [アラート] ウィンドウを開き、[アラートの追加] をクリックします。

## <a name="next-steps"></a><a name="next"></a>次のステップ

* [依存関係の追跡](./asp-net-dependencies.md)
* [例外の追跡](./asp-net-exceptions.md)

