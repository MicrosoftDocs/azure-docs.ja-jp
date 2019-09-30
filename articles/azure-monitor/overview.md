---
title: Azure Monitor の概要 | Microsoft Docs
description: Azure のサービスおよびアプリケーションの包括的な監視戦略に役立つ Microsoft のサービスと機能の概要。
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: bwren
ms.openlocfilehash: 1bc4a7634e864f0e3feb0d33cef3b967e5b6741f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262209"
---
# <a name="azure-monitor-overview"></a>Azure Monitor の概要

Azure Monitor は、クラウドおよびオンプレミス環境の利用統計情報を収集、分析し、それに対応する包括的なソリューションを提供することにより、アプリケーションの可用性とパフォーマンスを最大化します。 このツールは、ご利用のアプリケーションがどのように実行されているかを把握するのに役立ちます。さらに、このツールにより、そのアプリケーションに影響している問題点およびアプリケーションが依存しているリソースを事前に明らかにしておくことができます。

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>概要
次の図は、Azure Monitor の概要を示します。 図の中央には、Azure Monitor が使用するデータの 2 つの基本的な型であるメトリックとログを格納するデータ ストアがあります。 左側には、これらの[データ ストア](platform/data-platform.md)に投入される[監視データのソース](platform/data-sources.md)があります。 右側には、Azure Monitor が収集したデータを使って実行するさまざまな機能があります。たとえば、分析、アラート、および外部システムへのストリーミングなどです。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

![Azure Monitor の概要](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>データ プラットフォームの監視
Azure Monitor が収集したすべてのデータは、2 つの基本的な型である[メトリックとログ](platform/data-platform.md)のどちらかに該当します。 [メトリック](platform/data-platform-metrics.md)は、特定の時点におけるシステムの何らかの側面を表す数値です。 メトリックは軽量であり、リアルタイムに近いシナリオをサポートできます。 [ログ](platform/data-platform-logs.md)には、種類ごとに異なるプロパティ セットを持つレコードに編成されたさまざまな種類のデータが含まれます。 イベントやトレースなどの利用統計情報は、組み合わせて分析できるように、パフォーマンス データとともにログとして格納されます。

Azure Monitor が収集した多くの Azure リソースのデータは、Azure portal の [Overview]\(概要\) ページで参照できます。 たとえば、任意の仮想マシンを見てみると、パフォーマンス メトリックが表示されたいくつかのグラフが表示されます。 いずれかのグラフをクリックすると、データが Azure portal の[メトリック エクスプローラー](platform/metrics-charts.md)で開かれ、時系列で複数のメトリックの値を表示するグラフを確認できます。  グラフは、対話形式で表示したり、ダッシュボードにピン留めして他の視覚化と一緒に表示したりできます。

![メトリック](media/overview/metrics.png)

Azure Monitor が収集したログ データは、収集されたデータをすばやく検索、統合、分析する[クエリ](log-query/log-query-overview.md)を使用して分析できます。  Azure portal で [Log Analytics](log-query/portals.md) を使用してクエリを作成およびテストした後、これらのツールを使用してデータを直接分析できるほか、クエリを保存して[視覚化](visualizations.md)または[アラート ルール](platform/alerts-overview.md)に利用することができます。

Azure Monitor では、Azure Data Explorer で使用される [Kusto クエリ言語](/azure/kusto/query/)のバージョンを使用します。それは、単純なログ検索に適していますが、集計、結合、スマート分析などの高度な機能も備えています。 [さまざまなレッスン](log-query/get-started-queries.md)を利用すれば、クエリ言語はすぐに覚えることができます。  既に [SQL](log-query/sql-cheatsheet.md) や [Splunk](log-query/splunk-cheatsheet.md) に習熟しているユーザーには、別途ガイダンスが用意されています。

![ログ](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Azure Monitor が収集するデータ
Azure Monitor はさまざまなソースからデータを収集できます。 アプリケーションやそれが依存するオペレーティング システムやサービスから、プラットフォーム自体に至るまで、アプリケーションのさまざまな階層のデータ監視を検討することができます。 Azure Monitor は、以下のそれぞれの層からデータを収集します。

- **アプリケーション監視データ**:プラットフォームを問わず、記述したコードのパフォーマンスと機能に関するデータ。
- **ゲスト OS 監視データ**:アプリケーションが実行されているオペレーティング システムに関するデータ。 これは Azure、別のクラウド、またはオンプレミスで実行できます。 
- **Azure リソース監視データ**:Azure リソースの操作に関するデータ。
- **Azure サブスクリプション監視データ**:Azure サブスクリプションの操作および管理に関するデータと、Azure 自体の正常性および操作に関するデータ。 
- **Azure テナントの監視データ**:Azure Active Directory など、テナント レベルの Azure サービスの操作に関するデータ。

Azure サブスクリプションを作成して仮想マシンや Web アプリなどのリソースを追加すると、Azure Monitor は即座にデータの収集を開始します。  リソースが作成または変更されると、[アクティビティ ログ](platform/activity-logs-overview.md)が記録されます。 リソースの状況や消費しているリソースは、[メトリック](platform/data-platform.md)からわかります。 

データの収集をリソースの実運用にまで拡張するには、[診断を有効](platform/resource-logs-overview.md)にしてコンピューティング リソースに[エージェントを追加](platform/agent-windows.md)します。 これによって、リソースの内部操作に関する利用統計情報が収集されるようになり、さまざまな[データ ソース](platform/agent-data-sources.md)を構成して Windows および Linux ゲスト オペレーティング システムからログやメトリックを収集することができます。 

[App Services アプリケーション](app/azure-web-apps.md)または [VM および仮想マシン スケール セット アプリケーション](app/azure-vm-vmss-apps.md)の監視を有効にすると、Application Insights が有効になり、ページ ビュー、アプリケーションの要求、例外など、アプリケーションに関する詳細情報を収集できます。 さらにアプリケーションの可用性を検証するには、ユーザー トラフィックをシミュレートする[可用性テスト](app/monitor-web-app-availability.md)を構成します。

### <a name="custom-sources"></a>カスタム ソース
Azure Monitor では、[Data Collector API](platform/data-collector-api.md) を使用して任意の REST クライアントからログ データを収集できます。 これにより、カスタム監視シナリオを作成し、他のソースを通じてテレメトリが公開されないリソースも監視対象に含めることができます。



## <a name="insights"></a>洞察
データの監視が役立つのは、コンピューティング環境の運用の可視性が高まる場合のみです。 Azure Monitor には、アプリケーションやそれが依存するリソースに対する貴重な洞察を提供するいくつかの機能やツールが含まれています。 [Application Insights](app/app-insights-overview.md) や[コンテナーに対する Azure Monitor](insights/container-insights-overview.md) などの[監視ソリューション](insights/solutions.md)と機能では、アプリケーションや特定の Azure サービスのさまざまな側面に対する詳細な分析情報が提供されます。 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) は、クラウドとオンプレミスのどちらにホストされているかにかかわらず、Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。 Azure Monitor の強力なデータ分析プラットフォームを利用し、ユーザーの報告を待つことなく、アプリケーション運用やエラー診断に対する深い洞察を提供します。 Application Insights にはさまざまな開発ツールへの接続ポイントが含まれており、DevOps プロセスをサポートする Visual Studio とも統合されています。

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>コンテナーに対する Azure Monitor
[コンテナー向けの Azure Monitor](insights/container-insights-overview.md) は、Azure Kubernetes Service (AKS) でホストされるマネージド Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスを監視するために設計された機能です。 Kubernetes で使用可能なコントローラー、ノード、およびコンテナーから Metrics API 経由でメモリやプロセッサ メトリックを収集することにより、パフォーマンスを可視化します。 コンテナーのログも収集されます。  Kubernetes クラスターから監視を有効化すると、コンテナー化されたバージョンの Linux 向けの Log Analytics エージェントを使用してこれらのメトリックとログが自動的に収集されます。

![コンテナー正常性](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>VM に対する Azure Monitor
[Azure Monitor for VMs](insights/vminsights-overview.md) では、お使いの Azure 仮想マシン (VM) の大規模な監視が行われます。それを行うために、さまざまなプロセスや、その他のリソースおよび外部プロセスに対する相互接続された依存関係など、Windows および Linux VM のパフォーマンスと正常性が分析されます。 このソリューションでは、オンプレミスまたは別のクラウド プロバイダーでホストされている VM について、パフォーマンスおよびアプリケーションの依存関係の監視もサポートされています。  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>監視ソリューション
Azure Monitor の[監視ソリューション](insights/solutions.md)は、特定のアプリケーションまたはサービスに関する洞察を提供するロジックを 1 つにまとめたものです。 アプリケーションまたはサービスの監視データを収集するためのロジック、そのデータを分析するための[クエリ](log-query/log-query-overview.md)、視覚化のための[ビュー](../log-analytics/log-analytics-view-designer.md)が含まれています。 Azure サービスおよび他のアプリケーションを監視するさまざまな監視ソリューションが [Microsoft とパートナーから提供](insights/solutions-inventory.md)されています。

![監視ソリューション](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>重大な状況への応答
効果的な監視ソリューションは、対話形式で監視データを分析できるだけでなく、収集したデータから危機的な状況を識別して事前に応答できる必要があります。 たとえば、問題の調査を担当する管理者にテキストまたはメールを送信することが考えられます。 または、自動化されたプロセスを起動して、エラー状態の修正を試みることも考えられます。


### <a name="alerts"></a>アラート
[Azure Monitor のアラート](platform/alerts-overview.md)は、重大な状態を事前に通知し、可能であれば修正のためのアクションを実行しようとします。 メトリックに基づくアラート ルールは、数値に基づくリアルタイムに近いアラートを提供します。一方、ログに基づくルールを使うと、複数のソースのデータにまたがる複雑なロジックを実現できます。

Azure Monitor のアラート ルールは、受信者の一意なセットと複数のルール間で共有できるアクションを含む[アクション グループ](platform/action-groups.md)を使用します。 アクション グループを使うと、Webhook を使用してアラートから外部アクションを起動したり、ITSM ツールと統合したりするなど、要件に基づいたアクションを実行することができます。

![アラート](media/overview/alerts.png)

### <a name="autoscale"></a>自動スケール
自動スケールを使用すると、適切な量のリソースを実行して、アプリケーションに対する負荷を処理することができます。 Azure Monitor が収集したメトリックを使用して、負荷の増加に対応するために自動的にリソースを追加するタイミングや、アイドル状態にあるリソースを削除してコストを節約するタイミングを判別するルールを作成することができます。 インスタンスの最小数および最大数と、リソースを増減するタイミングを示すロジックを指定します。

![自動スケール](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>監視データの視覚化
グラフや表などによる[視覚化](visualizations.md)は、監視データをまとめてさまざまな対象ユーザーに提示する際に利用できる効果的なツールです。 Azure Monitor は、監視データを視覚化する独自の機能を備えており、他の Azure サービスを活用してさまざまな対象ユーザーに公開します。

### <a name="dashboards"></a>ダッシュボード
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)を使用すると、メトリックとログを含むさまざまな種類のデータを組み合わせて [Azure portal](https://portal.azure.com) 内の 1 つのウィンドウに表示できます。 ダッシュボードは、他の Azure ユーザーと共有することもできます。 Azure Monitor のすべての要素は、任意のログ クエリやメトリック グラフの出力と合わせて、Azure ダッシュ ボードに追加できます。 たとえば、メトリックのグラフ、アクティビティ ログの表、Application Insights の使用状況グラフ、ログ クエリ結果を示すタイルを組み合わせて 1 つのダッシュボードを作成できます。

![ダッシュボード](media/overview/dashboard.png)

### <a name="views"></a>ビュー
[ビュー](../log-analytics/log-analytics-view-designer.md)は Azure Monitor でログ データを視覚的に表現します。  それぞれのビューには単一のタイルが含まれており、ドリルダウンすることで重要なデータを集約したリストのほか、棒グラフや折れ線グラフなど、さまざまな視覚化要素を表示できます。  監視ソリューションには、特定の用途向けにデータを集計したビューが含まれているほか、ログ クエリから得たデータを表示する独自のビューを作成することもできます。 ビューは、Azure Monitor での他の要素と同様に、Azure ダッシュ ボードに追加できます。

![表示](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com)は、さまざまなデータ ソースを対話操作で視覚化するビジネス分析サービスで、データを組織内外のユーザーと効果的に共有することができます。 [Azure Monitor からログ データを自動的にインポート](../log-analytics/log-analytics-powerbi.md)するように Power BI を構成すると、そのデータも追加で視覚化することができます。


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>データの統合とエクスポート
多くの場合、監視データを使用するカスタム ソリューションを構築するために、Azure Monitor と他のシステムを統合したいという要件があります。 このような統合を実現するには、他の Azure サービスと Azure Monitor を連携させます。

### <a name="event-hub"></a>イベント ハブ
ストリーミング プラットフォームおよびイベント取り込みサービスである [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) は、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、データの変換と格納を行います。 Event Hubs を使用すると、パートナー SIEM および監視ツールに対して [Azure Monitor データをストリーム配信](platform/stream-monitoring-data-event-hubs.md)できます。


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) は、さまざまなシステムやサービスと統合されたワークフローを使用して、タスクやビジネス プロセスを自動化できるサービスです。 Azure Monitor でメトリックとログの読み取りおよび書き込みを行うアクティビティが利用できるので、他のさまざまなシステムと統合されたワークフローを構築することができます。


### <a name="api"></a>API
Azure Monitor のメトリックとログの読み取りや書き込み、生成されたアラートのアクセスには、複数の API が利用できます。 アラートの構成や取得も可能です。 したがって、Azure Monitor と統合されたカスタム ソリューションを構築するために、ほぼあらゆることを行うことができます。

## <a name="next-steps"></a>次の手順
各項目の詳細情報

* [メトリックとログ](platform/data-platform.md) Azure Monitor によって収集されたデータについては、こちらをご覧ください。
* [データ ソース](platform/data-sources.md) アプリケーションのさまざまなコンポーネントでテレメトリを送信する方法については、こちらをご覧ください。
* [ログ クエリ](log-query/log-query-overview.md) 収集したデータを分析する場合は、こちらをご覧ください。
* クラウドのアプリケーションとサービスを監視するための[ベスト プラクティス](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。
