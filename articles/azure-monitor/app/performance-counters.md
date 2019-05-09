---
title: Application Insights のパフォーマンス カウンター | Microsoft Docs
description: Application Insights でシステムとカスタムの .NET パフォーマンス カウンターを監視します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: d38a575af54f044d64efc67b5483a67ffcd2fcd6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837611"
---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights のシステム パフォーマンス カウンター

Windows には、CPU 占有率や、メモリ、ディスク、ネットワークの使用率など、広範な[パフォーマンス カウンター](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters)が用意されています。 ユーザーが独自のパフォーマンス カウンターを定義することもできます。 アプリケーションが、オンプレミスのホスト、ユーザーが管理アクセス権を持っている仮想マシンの IIS で実行されている場合に限ります。

## <a name="view-counters"></a>カウンターを表示する

[メトリック] ウィンドウには、既定のパフォーマンス カウンターのセットが表示されます。

![Application Insights で表示されるパフォーマンス カウンター](./media/performance-counters/performance-counters.png)

.NET Web アプリケーション用に収集された現在の既定のカウンターは、次のとおりです。

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

最も役に立つすべてのグラフをまとめて表示するには、[ダッシュボード](../../azure-monitor/app/app-insights-dashboards.md)を作成してグラフを固定します。

## <a name="add-counters"></a>カウンターを追加する

目的のパフォーマンス カウンターがメトリックの一覧に含まれていない場合は、追加することができます。

1. サーバーで使えるカウンターを確認するには、ローカル サーバーで次の PowerShell コマンドを実行します。
   
    `Get-Counter -ListSet *`
   
    (「[`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx)」をご覧ください)
2. ApplicationInsights.config を開きます。
   
   * 開発中にアプリに Application Insights を追加した場合は、プロジェクトで ApplicationInsights.config を編集して、サーバーに再デプロイします。
   * Status Monitor を使って実行時に Web アプリケーションをインストルメント化した場合は、IIS のアプリのルート ディレクトリで ApplicationInsights.config を探します。 各サーバー インスタンスでファイルを更新します。
3. パフォーマンス コレクター ディレクティブを編集します。
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

標準カウンターとカスタム カウンターの両方をキャプチャできます。 `\Objects\Processes` は、すべての Windows システムで使える標準カウンターの例です。 `\Sales(photo)\# Items Sold` は、Web サービスに実装されているカスタム カウンターの例です。 

形式は `\Category(instance)\Counter"` です。インスタンスが存在しないカテゴリの場合は、単に `\Category\Counter` です。

`[a-zA-Z()/-_ \.]+` に一致しないカウンター名の場合、つまり英字、丸かっこ、スラッシュ、ハイフン、アンダースコア、スペース、ドット (.) 以外の文字が含まれる場合は、`ReportAs` が必要です。

インスタンスを指定した場合は、報告されるメトリックの "CounterInstanceName" ディメンションとして収集されます。

### <a name="collecting-performance-counters-in-code"></a>コードによるパフォーマンス カウンターの収集
システム パフォーマンス カウンターを収集し、それらを Application Insights に送信する場合は、次のスニペットを使用できます。


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
作成したカスタム メトリックの場合も同様です。

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Analytics のパフォーマンス カウンター
[Analytics](../../azure-monitor/app/analytics.md) でパフォーマンス カウンター レポートを検索して表示できます。

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

* *例外* は、グラフのサンプリング時間間隔中にポータルが受信した TrackException レポートの数です。 これには、コード内で TrackException 呼び出しが記述されている処理済みの例外のみが含まれ、 [未処理の例外](../../azure-monitor/app/asp-net-exceptions.md)はいずれも含められません。 

## <a name="performance-counters-in-aspnet-core-applications"></a>ASP.NET Core アプリケーションのパフォーマンス カウンター
パフォーマンス カウンターは、アプリケーションが .NET Framework 全体を対象とする場合のみサポートされます。 .NET Core アプリケーションのパフォーマンス カウンターを収集する機能はありません。

## <a name="alerts"></a>アラート
他のメトリックと同様に、パフォーマンス カウンターが指定した制限を超えた場合に警告する[アラートを設定](../../azure-monitor/app/alerts.md)できます。 [アラート] ウィンドウを開き、[アラートの追加] をクリックします。

## <a name="next"></a>次のステップ
* [依存関係の追跡](../../azure-monitor/app/asp-net-dependencies.md)
* [例外の追跡](../../azure-monitor/app/asp-net-exceptions.md)

