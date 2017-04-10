---
title: "Application Insights のパフォーマンス カウンター | Microsoft Docs"
description: "Application Insights でシステムとカスタムの .NET パフォーマンス カウンターを監視します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 94a4d2b85c845b135201fd919e8eac64a5e1cb37


---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights のシステム パフォーマンス カウンター
Windows には、CPU 占有率や、メモリ、ディスク、ネットワークの使用率など、広範な[パフォーマンス カウンター](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters)が用意されています。 ユーザーが独自のパフォーマンス カウンターを定義することもできます。 アプリケーションを実行している IIS が存在するオンプレミスのホストまたは仮想マシンに対し、ユーザーが管理アクセス権を持っている場合、[Application Insights](app-insights-overview.md) を使ってこれらのパフォーマンス カウンターを表示することができます。 グラフにはライブ アプリケーションで使用できるリソースが示され、サーバー インスタンス間での負荷の不均衡を識別するのに役立ちます。

パフォーマンス カウンターは [サーバー] ブレードに表示され、サーバー インスタンスごとに分かれた表が含まれます。

![Application Insights で表示されるパフォーマンス カウンター](./media/app-insights-performance-counters/counters-by-server-instance.png)

(パフォーマンス カウンターは Azure Web Apps には使用できません。 ただし、[Azure 診断を Application Insights に送信する](app-insights-azure-diagnostics.md)ことはできます。)

## <a name="configure"></a>構成
Application Insights Status Monitor がサーバー コンピューターにまだインストールされていない場合、パフォーマンス カウンターを表示するにはインストールする必要があります。

[Status Monitor のインストーラー](http://go.microsoft.com/fwlink/?LinkId=506648)を各サーバー インスタンスにダウンロードして実行します。 既にインストールされている場合は、再度インストールする必要はありません。

* *開発の間に[アプリに Application Insights SDK をインストール](app-insights-asp-net.md)しました。それでも Status Monitor が必要ですか?*
  
    はい、ASP.NET Web アプリのパフォーマンス カウンターを収集するには Status Monitor が必要です。 既にご存じかもしれませんが、Status Monitor を使うと、開発時に SDK をインストールしなくても、[既にライブ状態の Web アプリを監視する](app-insights-monitor-performance-live-website-now.md)ことができます。

## <a name="view-counters"></a>カウンターを表示する
[サーバー] ブレードには、既定のパフォーマンス カウンターのセットが表示されます。 

他のカウンターを表示するには、[サーバー] ブレードでグラフを編集するか、または新しい [[メトリックス エクスプローラー](app-insights-metrics-explorer.md)] ブレードを開いて新しいグラフを追加します。 

グラフを編集するときに、使用可能なカウンターがメトリックとして一覧表示されます。

![Application Insights で表示されるパフォーマンス カウンター](./media/app-insights-performance-counters/choose-performance-counters.png)

最も役に立つすべてのグラフをまとめて表示するには、[ダッシュボード](app-insights-dashboards.md)を作成してグラフを固定します。

## <a name="add-counters"></a>カウンターを追加する
必要なパフォーマンス カウンターがメトリックの一覧に表示されない場合は、Application Insights SDK が Web サーバーでその情報を収集していないためです。 メトリックスが収集されるように構成できます。

1. サーバーで使えるカウンターを確認するには、サーバーで次の PowerShell コマンドを実行します。
   
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
[Analytics](app-insights-analytics.md) でパフォーマンス カウンター レポートを検索して表示できます。

**performanceCounters** スキーマは、各パフォーマンス カウンターの `category`、`counter` 名、および `instance` 名を表示します。  各アプリケーションのテレメトリでは、そのアプリケーションのカウンターのみが確認できます。 たとえば、使用できるカウンターを表示するには次のようにします。 

![Application Insights Analytics のパフォーマンス カウンター](./media/app-insights-performance-counters/analytics-performance-counters.png)

(ここで、"instance" は役割またはサーバー マシンのインスタンスではなくパフォーマンス カウンターのインスタンスを示します。 パフォーマンス カウンター インスタンス名は通常、プロセスまたはアプリケーションの名前によって、プロセッサ時間などのカウンターをセグメントに分割します。)

最近の利用可能なメモリのグラフを取得するには、次のようにします。 

![Application Insights Analytics のメモリ タイムチャート](./media/app-insights-performance-counters/analytics-available-memory.png)

他のテレメトリと同様に、**performanceCounters** にも、アプリを実行しているホスト サーバー インスタンスの ID を示す列 `cloud_RoleInstance` があります。 たとえば、異なるコンピューター上でのアプリのパフォーマンスを比較するには、次のようにします。 

![Application Insights Analytics でロール インスタンス別にセグメント化されたパフォーマンス](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET と Application Insights のカウント
*例外レートと例外のメトリックの違いは何ですか*

* *例外レート* はシステム パフォーマンス カウンターです。 CLR ではスローされた処理済みおよび未処理の例外をすべてカウントし、特定のサンプリング時間間隔での合計をその時間間隔の長さで除算します。 Application Insights SDK では、この結果を収集し、ポータルに送信します。
* *例外* は、グラフのサンプリング時間間隔中にポータルが受信した TrackException レポートの数です。 これには、コード内で TrackException 呼び出しが記述されている処理済みの例外のみが含まれ、 [未処理の例外](app-insights-asp-net-exceptions.md)はいずれも含められません。 

## <a name="alerts"></a>アラート
他のメトリックと同様に、パフォーマンス カウンターが指定した制限を超えた場合に警告する[アラートを設定](app-insights-alerts.md)できます。 [アラート] ブレードを開き、[アラートの追加] をクリックします。

## <a name="a-namenextanext-steps"></a><a name="next"></a>次のステップ
* [依存関係の追跡](app-insights-asp-net-dependencies.md)
* [例外の追跡](app-insights-asp-net-exceptions.md)




<!--HONumber=Nov16_HO3-->


