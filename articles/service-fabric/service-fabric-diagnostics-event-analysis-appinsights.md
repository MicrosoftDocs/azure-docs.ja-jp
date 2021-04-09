---
title: Application Insights を使用した Azure Service Fabric イベントの分析
description: Azure Service Fabric クラスターの監視と診断に Application Insights を使用したイベントの視覚化と分析について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f4d77cc3b2f2182b02d47f047070b819daa5f3c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589016"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Application Insights を使用したイベント分析と視覚化

Azure Monitor の一部である Application Insights は、アプリケーションの監視と診断のための拡張可能なプラットフォームです。 このプラットフォームには、強力な分析ツールとクエリ ツール、カスタマイズ可能なダッシュボードや視覚化、さらには自動アラート通知を含むオプションが含まれています。 Application Insights と Service Fabric の統合には、Visual Studio と Azure portal のツール エクスペリエンスだけでなく、Service Fabric 固有のメトリックが含まれているため、すぐに使える包括的なロギング エクスペリエンスが実現します。 Application Insights を使用すると、多くのログが自動的に作成され、収集されますが、アプリケーションにさらにカスタム ログを追加して、診断エクスペリエンスをさらに豊かにすることをお勧めします。

この記事を読むと、次の一般的な問題に対処できます。

* アプリケーションとサービスの内部で何が行われているかを理解し、テレメトリを収集する方法
* 互いに通信しているアプリケーション (特にサービス) のトラブルシューティングを行う方法
* サービスのパフォーマンスに関するメトリック (たとえば、ページの読み込み時間や HTTP 要求など) を取得する方法

この記事の目的は、Application Insights 内から情報を取得し、トラブルシューティングを行う方法を示すことです。 Service Fabric で Application Insights を設定および構成する方法については、この[チュートリアル](service-fabric-tutorial-monitoring-aspnet.md)をご覧ください。

## <a name="monitoring-in-application-insights"></a>Application Insights での監視

Application Insights には、Service Fabric の使用時にすぐに利用できる豊富な機能が用意されています。 Application Insights の概要ページでは、サービスに関する重要な情報 (応答時間や処理された要求の数など) が提供されます。 上部にある [検索] をクリックすると、アプリケーション内での最近の要求の一覧を確認できます。 さらに、失敗した要求をここで確認し、発生したエラーを診断できます。

![Application Insights の概要](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

前の図の右側のパネルでは、リスト内に要求およびイベントという 2 つの主要な種類のエントリがあります。 要求は、HTTP 要求を通じてアプリの API に対して行われた呼び出しです。イベントはカスタム イベントで、コード内の任意の場所に追加できるテレメトリとして機能します。 アプリケーションのインストルメント化については、「[カスタムのイベントとメトリックのための Application Insights API](../azure-monitor/app/api-custom-events-metrics.md)」で詳しく調べることができます。 要求をクリックすると、次の図に示すような詳細が表示されます。これには、Application Insights の Service Fabric NuGet パッケージで収集される Service Fabric 固有のデータが含まれます。 この情報はアプリケーションのトラブルシューティングと状態の確認に役立ち、このすべての情報を Application Insights 内で検索できます

![詳細を示すスクリーンショット。これには、Application Insights の Service Fabric NuGet パッケージで収集される Server Fabric に固有のデータが含まれています。](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights には、取得したすべてのデータに対してクエリを実行するための指定ビューがあります。 [概要] ページの上部にある [メトリックス エクスプローラー] を選択して、Application Insights ポータルに移動します。 ここで、Kusto クエリ言語を使用して前述のカスタム イベント、要求、例外、パフォーマンス カウンター、その他のメトリックに対してクエリを実行できます。 次の例は、過去 1 時間のすべての要求を示しています。

![Application Insights の要求の詳細](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Application Insights ポータルの機能についてさらに調べるには、[Application Insights ポータルに関するドキュメント](../azure-monitor/app/overview-dashboard.md)をご覧ください。

### <a name="configuring-application-insights-with-eventflow"></a>EventFlow を使用した Application Insights の構成

EventFlow を使用してイベントを集計する場合は、必ず `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`NuGet パッケージをインポートしてください。 *eventFlowConfig.json* の *outputs* セクションには、次のコードが必要です。

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

フィルターに必要な変更を加えるだけでなく、他の入力を (それぞれの NuGet パッケージとともに) 含めるようにしてください。

## <a name="application-insights-sdk"></a>Application Insights SDK

EventFlow と WAD は集計ソリューションとして使用することをお勧めします。これは診断と監視を行うモジュール性の高いアプローチが可能になるためです。つまり、EventFlow からの出力を変更する場合、実際のインストルメンテーションを変更する必要はなく、構成ファイルを簡単に変更するだけで済みます。 Application Insights の利用に投資し、別のプラットフォームに変更する可能性が低い場合は、Application Insights の新しい SDK を使用してイベントを集計し、それを Application Insights に送信することを検討してください。 つまり、データを Application Insights に送信するように EventFlow を設定する必要はなくなり、代わりに ApplicationInsight の Service Fabric NuGet パッケージをインストールすることになります。 パッケージの詳細は、[こちら](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)をご覧ください。

[Application Insights のマイクロサービスとコンテナーのサポート](https://azure.microsoft.com/blog/app-insights-microservices/)により、開発中の新機能 (現在はまだベータ版) が表示され、Application Insights を使用した、より豊かで、細かい設定が不要なモニタリング オプションを利用できます。 これには、依存関係の追跡 (クラスター内すべてのサービスやアプリケーションおよびそれらの間の通信の AppMap 構築に使用されます)、サービスからのトレースの関連付けの向上 (アプリケーションやサービスのワークフローの問題点を特定するのに役立ちます) が含まれます。

.NET で開発中で、Service Fabric のプログラミング モデルの一部を使用する可能性があり、イベントとログ データを視覚化して分析するためのプラットフォームとして Application Insights を使用する場合は、Application Insights SDK ルートをたどる監視および診断ワークフローを作成することをお勧めします。 ログを収集して表示するために Application Insights の使用を開始するには、[Application Insights に関するドキュメント](../azure-monitor/azure-monitor-app-hub.yml)と[トレース ログに関するドキュメント](../azure-monitor/app/asp-net-trace-logs.md)を参照してください。

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Azure portal で Application Insights のリソースを参照する

イベントとログの出力として Application Insights を構成すると、数分後に Application Insights リソースに情報が表示されます。 Application Insights リソースに移動すると、Application Insights リソースのダッシュ ボードが表示されます。 Application Insights タスク バーの **[検索]** を選択すると、受信した最新のトレースが表示され、それらのトレースをフィルター処理できます。

*メトリックス エクスプローラー* は、アプリケーションやサービス、クラスターが報告するメトリックに基づいたカスタム ダッシュボードを作成する便利なツールです。 収集するデータに基づいたグラフを自分でいくつか設定するには、「[Application Insights を使用したメトリックの探索](../azure-monitor/essentials/metrics-charts.md)」を参照してください。

**[Analytics]** をクリックすると、Application Insights Analytics ポータルに移動します。このポータルでは、より多くのオプションを使用し、より広い範囲でイベントとトレースを照会できます。 この詳細については、「[Application Insights の Analytics](../azure-monitor/logs/log-query-overview.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [AI のアラートを設定して](../azure-monitor/alerts/alerts-log.md)、パフォーマンスまたは使用状況の変化について通知を受けます
* [Application Insights のスマート検出](../azure-monitor/app/proactive-diagnostics.md)は、Application Insights に送信されるテレメトリのプロアクティブ分析を実行し、潜在的なパフォーマンスの問題を警告します
