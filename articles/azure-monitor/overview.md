---
title: Azure Monitor の概要 | Microsoft Docs
description: Azure のサービスおよびアプリケーションの包括的な監視戦略に役立つ Microsoft のサービスと機能の概要。
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 544d6937e412e3e1cfc2cf4e520c02f3f804fc8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047164"
---
# <a name="azure-monitor-overview"></a>Azure Monitor の概要

Azure Monitor には、アプリケーションとサービスの可用性およびパフォーマンスを最大化する効果があります。 クラウドおよびオンプレミス環境のテレメトリを収集、分析し、対応する包括的なソリューションを提供します。 この情報を頼りにアプリケーションの実行状態を把握し、アプリケーションやその依存リソースに影響を及ぼす問題を事前に突き止めることができます。

Azure Monitor でできることの例を次に示します。

- [Application Insights](app/app-insights-overview.md) を使用して、アプリケーションと依存関係の問題を検出して診断します。
- インフラストラクチャの問題を [VM insights](vm/vminsights-overview.md) と [Container insights](containers/container-insights-overview.md) に関連付けます。
- トラブルシューティングや詳細な診断のために、[Log Analytics](logs/log-query-overview.md) を使用して監視データをドリルダウンします。
- [スマート アラート](alerts/alerts-smartgroups-overview.md)や[自動化されたアクション](alerts/alerts-action-rules.md)により、大規模な運用をサポートします。
- Azure の[ダッシュボード](visualize/tutorial-logs-dashboards.md)と[ブック](visualize/workbooks-overview.md)により視覚化を作成します。
- [Azure Monitor メトリック](./essentials/data-platform-metrics.md)を使用して、[監視対象のリソース](./monitor-reference.md)からデータを収集します。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>概要

次の図は、Azure Monitor の概要を示します。 図の中央にあるデータ ストアには、Azure Monitor で使用される 2 つの基本的なデータの種類であるメトリックとログを格納します。 左側には、これらの[データ ストア](data-platform.md)に投入される[監視データのソース](agents/data-sources.md)があります。 右側には、Azure Monitor が、収集したデータを使って実行するさまざまな機能が示されています。 分析、アラート、外部システムへのストリーム配信がその例です。

![Azure Monitor の概要](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>データ プラットフォームの監視

Azure Monitor が収集したすべてのデータは、2 つの基本的な型である[メトリックとログ](data-platform.md)のどちらかに該当します。 [メトリック](essentials/data-platform-metrics.md)は、特定の時点におけるシステムの何らかの側面を表す数値です。 メトリックは軽量であり、リアルタイムに近いシナリオをサポートできます。 [ログ](logs/data-platform-logs.md)には、種類ごとに異なるプロパティ セットを持つレコードに編成されたさまざまな種類のデータが含まれます。 イベントやトレースなどの利用統計情報は、組み合わせて分析できるように、パフォーマンス データとともにログとして格納されます。

Azure Monitor が収集した多くの Azure リソースのデータは、Azure portal の [Overview]\(概要\) ページで参照できます。 たとえば、任意の仮想マシンを見てみると、パフォーマンス メトリックが表示されたいくつかのグラフが表示されます。 いずれかのグラフをクリックすると、データが Azure portal の[メトリック エクスプローラー](essentials/metrics-charts.md)で開かれ、時系列で複数のメトリックの値を表示するグラフを確認できます。  グラフは、対話形式で表示したり、ダッシュボードにピン留めして他の視覚化と一緒に表示したりできます。

![視覚化で使用するためにメトリック エクスプローラーに送信されるメトリック データを示す図。](media/overview/metrics.png)

Azure Monitor が収集したログ データは、収集されたデータをすばやく検索、統合、分析する[クエリ](logs/log-query-overview.md)を使用して分析できます。  Azure portal から [Log Analytics](./logs/log-query-overview.md) を使用してクエリを作成し、テストすることができます。 その後、別のツールを使用してデータを直接分析するか、またはクエリを保存して[視覚化](visualizations.md)や[アラート ルール](alerts/alerts-overview.md)に利用することができます。

Azure Monitor では、[Kusto クエリ言語](/azure/kusto/query/)の 1 つのバージョンを使用します。この言語は、単純なログ検索に適している一方で、集計、結合、スマート分析など高度な機能も備えています。 [さまざまなレッスン](logs/get-started-queries.md)を利用すれば、クエリ言語はすぐに覚えることができます。  既に [SQL](/azure/data-explorer/kusto/query/sqlcheatsheet) や [Splunk](/azure/data-explorer/kusto/query/splunk-cheat-sheet) に習熟しているユーザーには、別途ガイダンスが用意されています。

![分析のために Log Analytics に送信されるログ データを示す図。](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Azure Monitor が収集するデータ

Azure Monitor は[さまざまなソース](monitor-reference.md)からデータを収集できます。 その範囲は、アプリケーションから、それが依存するオペレーティング システムとサービス、プラットフォーム自体にまで及びます。 Azure Monitor は、以下のそれぞれの層からデータを収集します。

- **アプリケーション監視データ:** プラットフォームを問わず、記述したコードのパフォーマンスと機能に関するデータ。
- **ゲスト OS 監視データ:** アプリケーションが実行されているオペレーティング システムに関するデータ。 これは Azure、別のクラウド、またはオンプレミスで実行できます。 
- **Azure リソース監視データ:** Azure リソースの操作に関するデータ。
- **Azure サブスクリプション監視データ:** Azure サブスクリプションの操作および管理に関するデータと、Azure 自体の正常性および操作に関するデータ。 
- **Azure テナントの監視データ:** Azure Active Directory など、テナント レベルの Azure サービスの操作に関するデータ。

Azure サブスクリプションを作成して仮想マシンや Web アプリなどのリソースを追加すると、Azure Monitor は即座にデータの収集を開始します。  リソースが作成または変更されると、[アクティビティ ログ](essentials/platform-logs-overview.md)が記録されます。 リソースの状況や消費しているリソースは、[メトリック](data-platform.md)からわかります。 

データの収集をリソースの内部動作にまで拡張するには[診断を有効](essentials/platform-logs-overview.md)にします。  ゲスト オペレーティング システムからテレメトリを収集したければ、コンピューティング リソースに[エージェントを追加](agents/agents-overview.md)します。 

ページ ビュー、アプリケーションの要求、例外など、詳細な情報を収集するには、Application Insights を使った[アプリケーション](app/app-insights-overview.md)の監視を有効します。 さらにアプリケーションの可用性を検証するには、ユーザー トラフィックをシミュレートする[可用性テスト](app/monitor-web-app-availability.md)を構成します。

### <a name="custom-sources"></a>カスタム ソース

Azure Monitor では、[Data Collector API](logs/data-collector-api.md) を使用して任意の REST クライアントからログ データを収集できます。 これにより、カスタム監視シナリオを作成し、他のソースを通じてテレメトリが公開されないリソースも監視対象に含めることができます。

## <a name="insights"></a>洞察
データの監視が役立つのは、コンピューティング環境の運用の可視性が高まる場合のみです。 [Insights](monitor-reference.md#insights-and-core-solutions) は、特定の Azure サービスを監視するためのカスタマイズされたエクスペリエンスを提供します。 最小限の構成さえ行えば、重要なリソースの運用の可視性を高めることができます。

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) は、クラウドとオンプレミスのどちらにホストされているかにかかわらず、Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。 Azure Monitor の強力なデータ分析プラットフォームを利用すれば、アプリケーションの運用に関する詳しい分析情報が得られます。 ユーザーからのレポートを待たずにエラーを診断することが可能です。 Application Insights にはさまざまな開発ツールへの接続ポイントが含まれており、DevOps プロセスをサポートする Visual Studio とも統合されています。

![App Insights](media/overview/app-insights.png)

### <a name="container-insights"></a>Container insights
[Container insights](containers/container-insights-overview.md) では、Azure Kubernetes Service (AKS) でホストされるマネージド Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスを監視します。 Kubernetes で使用可能なコントローラー、ノード、およびコンテナーから Metrics API 経由でメトリックを収集することにより、パフォーマンスを可視化します。 コンテナーのログも収集されます。  Kubernetes クラスターから監視を有効化すると、コンテナー化されたバージョンの Linux 向けの Log Analytics エージェントを使用してこれらのメトリックとログが自動的に収集されます。

![コンテナー正常性](media/overview/container-insights.png)

### <a name="vm-insights"></a>VM insights
[VM insights](vm/vminsights-overview.md) では、利用している Azure 仮想マシン (VM) の大規模な監視が行われます。 Windows VM と Linux VM のパフォーマンスと正常性が分析され、そのさまざまなプロセス、そして相互に関連し合う外部プロセスへの依存関係が特定されます。 このソリューションでは、オンプレミスまたは別のクラウド プロバイダーでホストされている VM について、パフォーマンスおよびアプリケーションの依存関係の監視もサポートされています。  


![VM Insights](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>重大な状況への応答
効果的な監視ソリューションは、対話形式で監視データを分析できるだけでなく、収集したデータから危機的な状況を識別して事前に応答できる必要があります。 たとえば、問題の調査を担当する管理者にテキストまたはメールを送信することが考えられます。 または、自動化されたプロセスを起動して、エラー状態の修正を試みることも考えられます。


### <a name="alerts"></a>警告
[Azure Monitor のアラート](alerts/alerts-overview.md)は、重大な状態を事前に通知し、可能であれば修正のためのアクションを実行しようとします。 メトリックに基づくアラート ルールにより、数値に基づいてほぼリアルタイムのアラートが提供されます。 ログに基づくルールでは、複数ソースのデータにまたがる複雑なロジックを実現できます。

Azure Monitor のアラート ルールは、受信者の一意なセットと複数のルール間で共有できるアクションを含む[アクション グループ](alerts/action-groups.md)を使用します。 アクション グループを使うと、Webhook を使用してアラートから外部アクションを起動したり、ITSM ツールと統合したりするなど、要件に基づいたアクションを実行することができます。

![重大度、アラートの合計、およびその他の情報を含む Azure Monitor のアラートを表示するスクリーンショット。](media/overview/alerts.png)

### <a name="autoscale"></a>自動スケール
自動スケールを使用すると、適切な量のリソースを実行して、アプリケーションに対する負荷を処理することができます。 Azure Monitor が収集したメトリックを使用し、負荷の増加に合わせて、いつ自動的にリソースを追加するかを判断するルールを作成しましょう。 アイドル状態のリソースを削除すればコストを削減することができます。 インスタンスの最小数および最大数と、リソースを増減するタイミングを示すロジックを指定します。

![「プロセッサ時間 > 80%」というラベルが付いたいくつかのサーバーが直線上にあり、2 台のサーバーが最小、3 台のサーバーが現在の容量、5 台のサーバーが最大とマークされている、自動スケーリングを示す図。](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>監視データの視覚化
グラフや表などによる[視覚化](visualizations.md)は、監視データをまとめてさまざまな対象ユーザーに提示する際に利用できる効果的なツールです。 Azure Monitor は、監視データを視覚化する独自の機能を備えており、他の Azure サービスを活用してさまざまな対象ユーザーに公開します。

### <a name="dashboards"></a>ダッシュボード
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)を使用すると、さまざまな種類のデータを組み合わせて、[Azure portal](https://portal.azure.com) 内の 1 つのペインに表示できます。 ダッシュボードは、他の Azure ユーザーと共有することもできます。 ログ クエリやメトリック グラフの出力を Azure ダッシュ ボードに追加できます。 たとえば、メトリックのグラフ、アクティビティ ログの表、Application Insights の使用状況グラフ、ログ クエリ結果を示すタイルを組み合わせて 1 つのダッシュボードを作成できます。

![アプリケーションとセキュリティのタイルと、その他のカスタマイズ可能な情報を含む、Azure ダッシュボードを示すスクリーンショット。](media/overview/dashboard.png)

### <a name="workbooks"></a>Workbooks
[ブック](visualize/workbooks-overview.md)は、Azure portal 内でデータを分析し、高度な視覚的レポートを作成するための柔軟なキャンバスを提供します。 Azure 全体から複数のデータ ソースを活用し、それらを結合して、統合された対話型エクスペリエンスにすることができます。 Insights に備わっているブックを使用するか、あらかじめ定義されているテンプレートから独自のブックを作成することができます。


![ブックの例](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) は、さまざまなデータ ソースにわたって対話的な視覚化を提供するビジネス分析サービスです。 組織内外の他のユーザーがデータを使用できるようにする効果的な手段です。 [Azure Monitor からログ データを自動的にインポート](./visualize/powerbi.md)するように Power BI を構成すると、そのデータも追加で視覚化することができます。


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>データの統合とエクスポート
多くの場合、監視データを使用するカスタム ソリューションを構築するために、Azure Monitor と他のシステムを統合したいという要件があります。 このような統合を実現するには、他の Azure サービスと Azure Monitor を連携させます。

### <a name="event-hub"></a>イベント ハブ
[Azure Event Hubs](../event-hubs/index.yml) は、ストリーミング プラットフォームとなるイベント インジェスト サービスです。 リアルタイム分析プロバイダーやバッチ処理アダプター、ストレージ アダプターを使用しながら、データを変換して格納することができます。 Event Hubs を使用すると、パートナー SIEM および監視ツールに対して [Azure Monitor データをストリーム配信](essentials/stream-monitoring-data-event-hubs.md)できます。


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) は、さまざまなシステムやサービスと統合されたワークフローを使用して、タスクやビジネス プロセスを自動化できるサービスです。 Azure Monitor でメトリックとログの読み取りおよび書き込みを行うアクティビティが用意されています。 他のさまざまなシステムと統合されたワークフローを構築することが可能です。


### <a name="api"></a>API
Azure Monitor のメトリックとログの読み取りや書き込み、生成されたアラートのアクセスには、複数の API が利用できます。 アラートの構成や取得も可能です。 したがって、Azure Monitor と統合されたカスタム ソリューションを構築するために、ほぼあらゆることを行うことができます。

## <a name="next-steps"></a>次のステップ
各項目の詳細情報

* [メトリックとログ](https://docs.microsoft.com/azure/azure-monitor/data-platform#metrics) Azure Monitor によって収集されたデータについては、こちらをご覧ください。
* [データ ソース](agents/data-sources.md) アプリケーションのさまざまなコンポーネントでテレメトリを送信する方法については、こちらをご覧ください。
* [ログ クエリ](logs/log-query-overview.md) 収集したデータを分析する場合は、こちらをご覧ください。
* クラウドのアプリケーションとサービスを監視するための[ベスト プラクティス](/azure/architecture/best-practices/monitoring)。
