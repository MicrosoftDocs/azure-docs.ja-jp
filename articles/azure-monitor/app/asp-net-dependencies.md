---
title: Azure Application Insights における依存関係の追跡 | Microsoft Docs
description: オンプレミスまたは Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: c77b5810164aef7508f717a0f75d90cf6cba2089
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273109"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Application Insights の設定:依存関係の追跡
*依存関係* は、アプリによって呼び出される外部コンポーネントです。 一般的には、HTTP を使用して呼び出されるサービス、またはデータベース、あるいはファイル システムです。 [Application Insights](../../azure-monitor/app/app-insights-overview.md) では、アプリケーションが依存関係を待機する期間や、依存関係の呼び出しが失敗する頻度が測定されます。 特定の呼び出しを調査し、要求や例外に関連付けることができます。

すぐに使用できる依存関係モニターは、現在、次の種類の依存関係の呼び出しを報告します。

* サーバー
  * SQL データベース
  * HTTP ベースのバインドを使用する ASP.NET Web および WCF サービス
  * ローカルまたはリモートの HTTP 呼び出し
  * Azure Cosmos DB、テーブル、Blob Storage、およびキュー 
* Web ページ
  * AJAX 呼び出し

監視は、選択したメソッドに関する[バイト コードのインストルメンテーション](https://msdn.microsoft.com/library/z9z62c29.aspx)を使用することにより、または .NET Framework の DiagnosticSource コールバック (最新の .NET SDK) に基づいて、動作します。 パフォーマンスのオーバーヘッドは最小限になります。

[TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) を使用して他の依存関係を監視するために、クライアントとサーバーの両方のコードで、独自の SDK 呼び出しを記述することもできます。

> [!NOTE]
> Azure Cosmos DB は、[HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) が使用されている場合にのみ、自動的に追跡されます。 TCP モードは、Application Insights ではキャプチャされません。

## <a name="set-up-dependency-monitoring"></a>依存関係の監視の設定
[Application Insights SDK](asp-net.md) では、部分的な依存関係情報が自動的に収集されます。 完全なデータを取得するには、ホスト サーバー用の適切なエージェントをインストールします。

| プラットフォーム | Install |
| --- | --- |
| IIS サーバー |[サーバーに Status Monitor をインストール](../../azure-monitor/app/monitor-performance-live-website-now.md)するか、[アプリを .NET Framework 4.6 以降にアップグレード](https://go.microsoft.com/fwlink/?LinkId=528259)して [Application Insights SDK](asp-net.md) をアプリにインストールします。 |
| Azure Web アプリ |Web アプリのコントロール パネルで [[Application Insights] ブレードを開き](../../azure-monitor/app/azure-web-apps.md)、メッセージが表示された場合は [Install (インストール)] を選択します。 |
| Azure Cloud Services |[スタートアップ タスクを使用](../../azure-monitor/app/cloudservices.md)するか、[.NET Framework 4.6 以降をインストール](../../cloud-services/cloud-services-dotnet-install-dotnet.md)します。 |

## <a name="where-to-find-dependency-data"></a>依存関係データが見つかる場所
* [アプリケーション マップ](#application-map)では、アプリと隣接コンポーネント間の依存関係が視覚化されます。
* [[パフォーマンス]、[ブラウザー]、および [障害] ブレード](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance)では、サーバー依存関係データが示されます。
* [[ブラウザー] ブレード](#ajax-calls)では、ユーザーのブラウザーからの AJAX 呼び出しが示されます。
* 低速または失敗した要求からクリックしていき、依存関係呼び出しを確認します。
* 依存関係データのクエリを実行するには、[Analytics](#analytics) を使用できます。

## <a name="application-map"></a>アプリケーション マップ
アプリケーション マップを見ると、アプリケーションのコンポーネント間の依存関係を発見できます。 これは、アプリから送信されたテレメトリから自動的に生成されます。 この例では、ブラウザー スクリプトからの AJAX 呼び出しとサーバー アプリから 2 つの外部サービスへの REST 呼び出しが示されています。

![アプリケーション マップ](./media/asp-net-dependencies/cloud-rolename.png)

* ボックスから、関連する依存関係および他のグラフに**移動できます**。
* マップは[ダッシュボード](../../azure-monitor/app/app-insights-dashboards.md)に**ピン留め**し、すべての機能を利用することができます。

[詳細情報](../../azure-monitor/app/app-map.md)。

## <a name="performance-and-failure-blades"></a>[パフォーマンス] および [障害] ブレード
[パフォーマンス] ブレードは、サーバー アプリから行われた依存関係呼び出しの期間を示します。

**[障害]** ブレードには**エラーの数**が表示されます。 エラーとは、範囲 200 ～ 399 にない、または不明なリターン コードのことです。

> [!NOTE]
> **100% のエラーとは何ですか。** - これはおそらく、部分的な依存関係データのみが得られていることを示しています。 [プラットフォームに適した依存関係監視を設定](#set-up-dependency-monitoring)する必要があります。
>
>

## <a name="ajax-calls"></a>AJAX 呼び出し
[ブラウザー] ブレードには、[Web ページ内の JavaScript](../../azure-monitor/app/javascript.md) からの AJAX 呼び出しの期間とエラー率が示されます。 これらは依存関係として示されます。

## <a name="diagnosis"></a> 低速なリクエストの診断
各要求イベントは、依存関係呼び出し、例外、およびアプリでの要求の処理中に追跡されるその他のイベントに関連しています。 そのため、いくつかのリクエストが正しく実行されない場合は、それが依存関係からの応答が遅いためかどうかを調べることができます。

### <a name="profile-your-live-site"></a>ライブ サイトのプロファイリング

時間がどこに使われているのか見当がつかないでしょうか。 [Application Insights プロファイラー](../../azure-monitor/app/profiler.md)は、ライブ サイトへの HTTP 呼び出しをトレースし、コード内の関数のうち最も時間がかかったものを示します。

## <a name="analytics"></a>Analytics
依存関係は [Kusto クエリ言語](/azure/kusto/query/)で追跡できます。 次に例をいくつか示します。

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

標準のモジュールで使用するのと同じ [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) を使用して、依存関係情報を送信するコードを記述できます。

たとえば、自分で記述していないアセンブリを使ってコードを作成する場合、それに対するすべての呼び出しを測定し、何が応答時間に貢献するかを知ることができます。 このデータを Application Insights 内の依存関係グラフに表示するには、データを `TrackDependency`を使用して送信します。

```csharp

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
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

標準の依存関係追跡モジュールを無効にするには、 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)の DependencyTrackingTelemetryModule への参照を削除します。

## <a name="troubleshooting"></a>トラブルシューティング
*依存関係の成功を示すフラグは、常に true と false のいずれかを示します。*

*SQL クエリが完全に表示されません。*

次の表を参照して、アプリケーションの依存関係の監視が可能なように正しい構成を選択したことを確認します。

| プラットフォーム | Install |
| --- | --- |
| IIS サーバー |[Status Monitor をサーバーにインストールします](../../azure-monitor/app/monitor-performance-live-website-now.md)。 または、[アプリを .NET Framework 4.6 以降にアップグレード](https://go.microsoft.com/fwlink/?LinkId=528259)して、[Application Insights SDK](asp-net.md) をアプリにインストールします。 |
| IIS Express |代わりに IIS サーバーを使用します。 |
| Azure Web アプリ |Web アプリのコントロール パネルで [[Application Insights] ブレードを開き](../../azure-monitor/app/azure-web-apps.md)、メッセージが表示された場合は [Install (インストール)] を選択します。 |
| Azure Cloud Services |[スタートアップ タスクを使用](../../azure-monitor/app/cloudservices.md)するか、[.NET Framework 4.6 以降をインストール](../../cloud-services/cloud-services-dotnet-install-dotnet.md)します。 |

## <a name="next-steps"></a>次の手順
* [例外](../../azure-monitor/app/asp-net-exceptions.md)
* [ユーザーとページのデータ](../../azure-monitor/app/javascript.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
