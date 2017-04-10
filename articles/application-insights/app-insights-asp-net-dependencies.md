---
title: "Azure Application Insights における依存関係の追跡 | Microsoft Docs"
description: "オンプレミスまたは Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 35817adde713995ec82eead033f058ee109bf900
ms.lasthandoff: 03/16/2017


---
# <a name="set-up-application-insights-dependency-tracking"></a>Application Insights の設定: 依存関係の追跡
*依存関係* は、アプリによって呼び出される外部コンポーネントです。 一般的には、HTTP を使用して呼び出されるサービス、またはデータベース、あるいはファイル システムです。 [Application Insights](app-insights-overview.md) では、アプリケーションが依存関係を待機する期間や、依存関係の呼び出しが失敗する頻度が測定されます。 特定の呼び出しを調査し、要求や例外に関連付けることができます。

![サンプルのグラフ](./media/app-insights-asp-net-dependencies/10-intro.png)

すぐに使用できる依存関係モニターは、現在、次の種類の依存関係の呼び出しを報告します。

* サーバー
  * SQL データベース
  * HTTP ベースのバインドを使用する ASP.NET Web および WCF サービス
  * ローカルまたはリモートの HTTP 呼び出し
  * Azure DocumentDb、テーブル、Blob Storage、およびキュー
* Web ページ
  * AJAX 呼び出し

監視は、選択したメソッドに関する[バイト コードのインストルメンテーション](https://msdn.microsoft.com/library/z9z62c29.aspx)を使用することにより動作します。 パフォーマンスのオーバーヘッドは最小限になります。

[TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) を使用して他の依存関係を監視するために、クライアントとサーバーの両方のコードで、独自の SDK 呼び出しを記述することもできます。

## <a name="set-up-dependency-monitoring"></a>依存関係の監視の設定
[Application Insights SDK](app-insights-asp-net.md) では、部分的な依存関係情報が自動的に収集されます。 完全なデータを取得するには、ホスト サーバー用の適切なエージェントをインストールします。

| Platform | インストール |
| --- | --- |
| IIS サーバー |[サーバーに Status Monitor をインストール](app-insights-monitor-performance-live-website-now.md)するか、[アプリを .NET Framework 4.6 以降にアップグレード](http://go.microsoft.com/fwlink/?LinkId=528259)して [Application Insights SDK](app-insights-asp-net.md) をアプリにインストールします。 |
| Azure Web アプリ |Web アプリのコントロール パネルで [[Application Insights] ブレードを開き](app-insights-azure-web-apps.md)、メッセージが表示された場合は [Install (インストール)] を選択します。 |
| Azure Cloud Services |[スタートアップ タスクを使用](app-insights-cloudservices.md)するか、[.NET Framework 4.6 以降をインストール](../cloud-services/cloud-services-dotnet-install-dotnet.md)します。 |

## <a name="where-to-find-dependency-data"></a>依存関係データが見つかる場所
* [アプリケーション マップ](#application-map)では、アプリと隣接コンポーネント間の依存関係が視覚化されます。
* [[パフォーマンス]、[ブラウザー]、および [障害] ブレード](#performance-and-blades)では、サーバー依存関係データが示されます。
* [[ブラウザー] ブレード](#ajax-calls)では、ユーザーのブラウザーからの AJAX 呼び出しが示されます。
* [低速または失敗した要求からクリックしていき](#diagnose-slow-requests)、依存関係呼び出しを確認します。
* 依存関係データのクエリを実行するには、[Analytics](#analytics) を使用できます。

## <a name="application-map"></a>アプリケーション マップ
アプリケーション マップを見ると、アプリケーションのコンポーネント間の依存関係を発見できます。 これは、アプリから送信されたテレメトリから自動的に生成されます。 この例では、ブラウザー スクリプトからの AJAX 呼び出しとサーバー アプリから 2 つの外部サービスへの REST 呼び出しが示されています。

![アプリケーション マップ](./media/app-insights-asp-net-dependencies/08.png)

* ボックスから、関連する依存関係および他のグラフに**移動できます**。
* マップは[ダッシュボード](app-insights-dashboards.md)に**ピン留め**し、すべての機能を利用することができます。

[詳細情報](app-insights-app-map.md)。

## <a name="performance-and-failure-blades"></a>[パフォーマンス] および [障害] ブレード
[パフォーマンス] ブレードは、サーバー アプリから行われた依存関係呼び出しの期間を示します。 概要グラフと、呼び出しによって分類されたテーブルがあります。

![[パフォーマンス] ブレードの依存関係グラフ](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

概要グラフまたはテーブル項目をクリックしていき、これらの呼び出しの生の発生を検索します。

![依存関係呼び出しインスタンス](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

**[障害]** ブレードには**エラーの数**が表示されます。 エラーとは、範囲 200 ～ 399 にない、または不明なリターン コードのことです。

> [!NOTE]
> **100% のエラーとは何ですか。** - これはおそらく、部分的な依存関係データのみが得られていることを示しています。 [プラットフォームに適した依存関係監視を設定](#set-up-dependency-monitoring)する必要があります。
>
>

## <a name="ajax-calls"></a>AJAX 呼び出し
[ブラウザー] ブレードには、[Web ページ内の JavaScript](app-insights-javascript.md) からの AJAX 呼び出しの期間とエラー率が示されます。 これらは依存関係として示されます。

## <a name="diagnosis"></a> 低速なリクエストの診断
各要求イベントは、依存関係呼び出し、例外、およびアプリでの要求の処理中に追跡されるその他のイベントに関連しています。 そのため、いくつかのリクエストが正しく実行されない場合は、それが依存関係からの応答が遅いためかどうかを調べることができます。

ひとつの例を見てみましょう。

### <a name="tracing-from-requests-to-dependencies"></a>リクエストから依存関係までのトレース
[パフォーマンス] ブレードを開き、要求のグリッドを参照します。

![平均およびカウントを持つ要求の一覧](./media/app-insights-asp-net-dependencies/02-reqs.png)

上部にあるものは、非常に長い時間がかかります。 どこに時間がかかるか見つけられるか見てみましょう。

個々の要求イベントを表示するには、その行をクリックします。

![要求回数の一覧](./media/app-insights-asp-net-dependencies/03-instances.png)

実行時間の長いインスタンスをクリックしてさらに検査し、この要求に関連したリモート依存関係呼び出しまで下にスクロールします。

![リモートの依存関係への呼び出しを見つけ、異常な期間を特定します](./media/app-insights-asp-net-dependencies/04-dependencies.png)

この要求に使われた時間のほとんどが、ローカル サービスへの呼び出しに費やされたように見えます。

さらに情報を得るには、その行をクリックします。

![そのリモートの依存関係をクリックし、問題の原因を特定します](./media/app-insights-asp-net-dependencies/05-detail.png)

ここに問題があるようです。 問題を特定できたので、あとは呼び出しに長い時間がかかっている理由を確認するだけです。

### <a name="request-timeline"></a>要求のタイムライン
別のケースでは、呼び出しが長い依存関係はありませんが、 タイムライン ビューに切り替えることで、内部処理に遅延が発生した箇所を確認できます。

![リモートの依存関係への呼び出しを見つけ、異常な期間を特定します](./media/app-insights-asp-net-dependencies/04-1.png)

最初の依存関係呼び出しの後に大きな隔たりがあるようです。コードを参照してその理由を調べる必要があります。

### <a name="profiling-your-live-site"></a>ライブ サイトのプロファイリング

時間がどこに使われているのか見当がつかないでしょうか。 Application Insights プロファイラーは、ライブ サイトへの HTTP 呼び出しをトレースし、コード内の関数のうち最も時間がかかったものを示します。 現在、このプロファイラーは限定プレビュー中です。[サインアップして試す](https://aka.ms/AIProfilerPreview)ことができます。

## <a name="failed-requests"></a>失敗した要求
失敗した要求も、依存関係への失敗した呼び出しに関連している可能性があります。 この場合も、クリック操作で問題を追跡することができます。

![失敗した要求のグラフをクリックします。](./media/app-insights-asp-net-dependencies/06-fail.png)

失敗した要求の発生場所までクリックしていき、関連するイベントを参照します。

![要求の種類をクリックし、インスタンスをクリックして同じインスタンスの異なるビューを取得し、それをクリックして例外の詳細を取得します。](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>[分析]
依存関係は [Analytics クエリ言語](app-insights-analytics.md)によって追跡できます。 次に例をいくつか示します。

* これは、失敗した依存関係呼び出しを見つけます。

```

    dependencies | where success != "True" | take 10
```

* これは、AJAX 呼び出しを見つけます。

```

    dependencies | where client_Type == "Browser" | take 10
```

* これは、要求に関連する依存関係呼び出しを見つけます。

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* これは、ページ ビューに関連する AJAX 呼び出しを見つけます。

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>カスタム依存関係の追跡
標準の依存関係追跡モジュールは、外部の依存関係 (データベース、REST API など) を自動的に検出しますが、 同じように扱える追加のコンポーネントが必要になる可能性もあります。

標準のモジュールで使用するのと同じ [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) を使用して、依存関係情報を送信するコードを記述できます。

たとえば、自分で記述していないアセンブリを使ってコードを作成する場合、それに対するすべての呼び出しを測定し、何が応答時間に貢献するかを知ることができます。 このデータを Application Insights 内の依存関係グラフに表示するには、データを `TrackDependency`を使用して送信します。

```C#

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

標準の依存関係追跡モジュールを無効にするには、 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)の DependencyTrackingTelemetryModule への参照を削除します。

## <a name="troubleshooting"></a>トラブルシューティング
*依存関係の成功を示すフラグは、常に true と false のいずれかを示します。*

*SQL クエリが完全に表示されません。*

* 最新バージョンの SDK にアップグレードしてください。 .NET バージョンが 4.6 未満の場合は、次のようにします。
  * IIS ホスト: [Application Insights エージェント](app-insights-monitor-performance-live-website-now.md)をホスト サーバーにインストールします。
  * Azure Web アプリ: Web アプリのコントロール パネルで [Application Insights] タブを開き、Application Insights をインストールします。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>次のステップ
* [例外](app-insights-asp-net-exceptions.md)
* [ユーザーとページのデータ](app-insights-javascript.md)
* [可用性](app-insights-monitor-web-app-availability.md)

