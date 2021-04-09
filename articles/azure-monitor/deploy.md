---
title: Azure Monitor をデプロイする
description: Azure Monitor を完全に実装して Azure サブスクリプション内のすべてのリソースを監視するために必要なさまざまなステップについて説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 7656efeb26a8a8b3c752ea996c8e644c68a48626
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044002"
---
# <a name="deploy-azure-monitor"></a>Azure Monitor をデプロイする
Azure Monitor ですべての Azure リソースを監視できるようにするには、Azure Monitor コンポーネントの構成と、Azure Monitor で収集される監視データを生成するための Azure リソースの構成を、組み合わせて行います。 この記事では、一般的な構成を使用して Azure Monitor を完全に実装して Azure サブスクリプション内のすべてのリソースを監視するために必要なさまざまなステップについて説明します。 各ステップの基本的な説明は、詳細な構成要件に関する他のドキュメントへのリンクで提供されます。

> [!IMPORTANT]
> Azure Monitor の機能とその構成は、ビジネス要件と、有効にする機能のコストとのバランスによって異なります。 以下の各ステップでは、潜在的なコストがあるかどうかが示されており、そのステップを進める前にこれらのコストを評価する必要があります。 価格の詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="configuration-goals"></a>構成の目標
Azure Monitor の完全な実装の目標は、すべてのクラウド リソースとアプリケーションから使用可能なすべてのデータを収集し、そのデータに基づいてできるだけ多くの Azure Monitor の機能を有効にすることです。

Azure Monitor によって収集されたデータは、[Azure Monitor Metrics](essentials/data-platform-metrics.md) または [Azure Monitor Logs](logs/data-platform-logs.md) に送信されます。 それぞれで、異なる種類のデータが格納され、異なる種類の分析とアラートが有効にされます。 両者の比較については「[Azure Monitor のメトリックとログを比較する](data-platform.md)」を参照し、さまざまなアラートの種類の説明については「[Microsoft Azure のアラートの概要](alerts/alerts-overview.md)」を参照してください。 

一部のデータは、さまざまな機能を使用して利用するために、Metrics と Logs の両方に送信できます。 このような場合は、それぞれを個別に構成する必要があります。 たとえば、メトリック データは、メトリックス エクスプローラーとメトリック アラートがサポートされている Metrics に、Azure リソースによって自動的に送信されます。 Log Analytics を使用して他のログ データと共にパフォーマンス傾向を分析できる Logs に同じメトリック データを送信するように、各リソースの診断設定を作成する必要があります。 以下のセクションでは、データが送信される場所を示し、可能なすべての場所にデータを送信するために必要な各ステップについて説明します。

Azure の外部でのリソースの監視や、Azure Monitor の外部へのデータの送信など、追加の要件がある場合があります。 このような要件は、この記事で説明されている機能の追加構成を使用して実現できます。 追加の構成オプションについては、各ステップで示されているドキュメントへのリンクを参照してください。

## <a name="collect-data-from-azure-resources"></a>Azure リソースからデータを収集する

> [!NOTE]
> Azure Monitor を使用した仮想マシンの監視の詳細なガイドについては、「[Azure Monitor を使用した Azure リソースの監視](essentials/monitor-azure-resource.md)」を参照してください。

Azure リソースの一部の監視は、構成を必要とせずに自動的に使用できますが、追加の監視データを収集するには構成手順を実行する必要があります。 次の表では、Azure リソースから使用可能なすべてのデータを収集するために必要な構成手順を示します。これには、Azure Monitor Metrics および Azure Monitor Logs にデータが送信されるステップが含まれています。 以下のセクションでは、各ステップについてさらに詳しく説明します。

[ ![Azure リソース監視をデプロイする](media/deploy/deploy-azure-resources.png) ](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>構成なし
Azure Monitor の次の機能は、Azure サブスクリプションを作成するときに、構成を行わなくても有効になります。 この監視に関連するコストはありません。

[Azure Active Directory ログ](../active-directory/reports-monitoring/overview-reports.md) - サインイン アクティビティと、Azure Active Directory で行われた変更の監査証跡の、テナント レベルの履歴が提供されます。 Azure Active Directory ログの詳細と Azure portal でそれらを表示する方法については、「[Azure Active Directory ポータルの監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md)」およびAzure Active Directory ポータルの および「[Azure Active Directory ポータルのサインイン アクティビティ レポート](../active-directory/reports-monitoring/concept-sign-ins.md)」を参照してください。

[アクティビティ ログ](essentials/platform-logs-overview.md) - 管理グループと、Azure で発生したサブスクリプション レベルのイベントに関する分析情報が提供されます。 新しい Azure リソースを作成したり、リソースを変更したり、重要なアクティビティを実行したりすると、アクティビティ ログにイベントが自動的に書き込まれます。 Azure portal でイベントを表示し、特定のイベントが作成されたときにアクティビティ ログ アラートを作成できます。 アクティビティ ログの詳細と Azure portal でそれを表示する方法については、「[Azure アクティビティ ログ](essentials/activity-log.md)」を参照してください。

[プラットフォーム メトリック](essentials/metrics-supported.md) - Azure サービスから [Azure Monitor Metrics](essentials/data-platform-metrics.md) に自動的に収集されます。 このデータは、多くの場合、Azure portal でさまざまなサービスの **[概要]** ページに表示されます。 Azure portal でのプラットフォーム メトリックの分析の詳細については、「[Azure メトリックス エクスプローラーの概要](essentials/metrics-getting-started.md)」を参照してください。 


### <a name="create-log-analytics-workspace"></a>Log Analytics ワークスペースの作成
[Azure Monitor Logs](logs/data-platform-logs.md) を有効にするには、少なくとも 1 つの Log Analytics ワークスペースが必要です。これは、Azure リソースからのログなどのデータの収集、Azure 仮想マシンのゲスト オペレーティング システムからのデータの収集、およびほとんどの Azure Monitor 分析情報のために必要です。 Azure Sentinel や Azure Security Center などの他のサービスでも、Log Analytics ワークスペースが使用され、Azure Monitor に使用するものと同じものを共有できます。 この監視をサポートするには 1 つのワークスペースから始めることができますが、複数のワークスペースを使用する場合のガイダンスについては、「[Azure Monitor ログのデプロイの設計](logs/design-logs-deployment.md)」を参照してください。

Log Analytics ワークスペースの作成にはコストがかかりませんが、そこに収集されるようにデータを構成すると、料金が発生する可能性があります。 「[Azure Monitor ログで使用量とコストを管理する](logs/manage-cost-storage.md)」をご覧ください。  

最初の Log Analytics ワークスペースの作成については、「[Azure ポータルで Log Analytics ワークスペースを作成する](logs/quick-create-workspace.md)」を参照してください アクセスの構成については、「[Azure Monitor でログ データとワークスペースへのアクセスを管理する](logs/manage-access.md)」を参照してください。 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>テナントとサブスクリプションのログを収集するための診断設定を作成する
テナントに対する [Azure Active Directory ログ](../active-directory/reports-monitoring/overview-reports.md)とサブスクリプションに対する[アクティビティ ログ](essentials/platform-logs-overview.md)は自動的に収集されますが、それらを Log Analytics ワークスペースに送信することで、Log Analytics のログ クエリを使用して、他のログ データと共にこれらのイベントを分析することができます。 これにより、Azure Active Directory ログに対してアラートを生成するための唯一の方法であるログ クエリ アラートを作成し、アクティビティ ログ アラートより複雑なロジックを提供することもできます。

アクティビティ ログをワークスペースに送信するためのコストは発生しませんが、Azure Active Directory ログに対するデータ インジェストと保持の料金が発生します。 

Log Analytics ワークスペースにログ エントリを送信するための、テナントとサブスクリプションに対する診断設定の作成については、「[Azure AD ログを Azure Monitor ログと統合する](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)」および「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](essentials/diagnostic-settings.md)」を参照してください。 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>リソース ログとプラットフォーム メトリックを収集するための診断設定を作成する
Azure のリソースでは、リソース内で実行された操作の詳細を提供する[リソース ログ](essentials/platform-logs-overview.md)が自動的に生成されます。 ただし、プラットフォーム メトリックとは異なり、リソース ログが収集されるように構成する必要があります。 それらを Log Analytics ワークスペースに送信し、Azure Monitor Logs で使用される他のデータと結合するには、診断設定を作成します。 同じ診断設定を使用して、ほとんどのリソースのプラットフォーム メトリックを、同じワークスペースに送信することもできます。これにより、ログ クエリを使用して他の収集データと共にメトリック データを分析することができます。

この収集にはコストがかかるので、多くのリソースに実装する前に、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。 また、ログ収集のコストの最適化の詳細については、「[Azure Monitor ログで使用量とコストを管理する](logs/manage-cost-storage.md)」を参照してください。

Azure リソース用の診断設定の作成については、「[Azure でリソース ログとメトリックを収集するための診断設定を作成する](essentials/diagnostic-settings.md#create-in-azure-portal)」を参照してください。 診断設定は Azure リソースごとに作成する必要があるため、[Azure policy](../governance/policy/overview.md) を使用して Azure リソースが作成されるたびに設定を自動的に作成する方法の詳細については、[大規模な Azure Monitor のデプロイ](deploy-scale.md)に関するページを参照してください。

### <a name="enable-insights-and-solutions"></a>分析情報とソリューションを有効にする
分析情報とソリューションでは、特定のサービスまたはソリューションに特化した監視が提供されます。 分析情報では、ブックなどの Azure Monitor の最新機能が使用されているため、分析情報はサービスで利用可能な場合に使用する必要があります。 すべての Azure サブスクリプションでそれを自動的に使用できますが、完全な機能のためには構成が必要になる場合があります。 通常は、前に構成したプラットフォーム メトリックとリソース ログが使用され、追加のデータを収集することができます。

ソリューションは、サブスクリプションごとに追加して、Azure Monitor Logs からのデータだけで動作させる必要があり、追加のログ データを収集することができます。

ソリューションや分析情報にコストはかかりませんが、それらによって収集されるデータに対して課金される場合があります。

Azure Monitor で利用可能な分析情報とソリューションの一覧については、「[Azure Monitor によって監視される内容](monitor-reference.md)」を参照してください。 固有の構成または価格の情報については、それぞれのドキュメントを参照してください。 

## <a name="collect-data-from-virtual-machines"></a>仮想マシンからデータを収集する

> [!NOTE]
> Azure Monitor を使用した仮想マシンの監視の詳細なガイドについては、「[Azure Monitor を使用して Azure 仮想マシンを監視する](vm/monitor-vm-azure.md)」を参照してください。 

仮想マシンでは、他の Azure リソースと同様のデータが生成されますが、ゲスト オペレーティング システムからデータを収集するにはエージェントが必要です。 Azure Monitor によって使用されるエージェントの比較については、「[Azure Monitor エージェントの概要](agents/agents-overview.md)」を参照してください。 

[VM 分析情報](vm/vminsights-overview.md)を使用すると、Log Analytics エージェントと Dependency Agent を使用して仮想マシンのゲスト オペレーティング システムからデータが収集されるため、この分析情報の実装の一部としてこれらのエージェントをデプロイできます。 これにより、Azure Security Center など、Log Analytics エージェントを使用する他のサービスに対してそれが有効になります。


[ ![Azure VM をデプロイする](media/deploy/deploy-azure-vm.png) ](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-vm-insights"></a>VM 分析情報用にワークスペースを構成する
VM 分析情報を使用するには、他の Azure リソースからデータを収集するために作成されるものと通常は同じ Log Analytics ワークスペースが必要です。 仮想マシンを有効にする前に、VM 分析情報に必要なソリューションをワークスペースに追加する必要があります。

VM 分析情報用の Log Analytics ワークスペースの構成の詳細については、「[VM 分析情報用に Log Analytics ワークスペースを構成する](vm/vminsights-configure-workspace.md)」を参照してください。

### <a name="enable-vm-insights-on-each-virtual-machine"></a>各仮想マシンで VM 分析情報を有効にする
ワークスペースを構成したら、Log Analytics エージェントと Dependency Agent をインストールすることにより、各仮想マシンを有効にすることができます。 これらのエージェントをインストールするには複数の方法があり、Azure Policy を使用すると各仮想マシンを作成時に自動的に構成できます。 VM 分析情報によって収集されるパフォーマンス データとプロセスの詳細は、Azure Monitor ログに格納されます。

仮想マシンにエージェントをデプロイし、監視用に有効にするオプションについては、「[VM 分析情報の有効化の概要](vm/vminsights-enable-overview.md)」を参照してください。

### <a name="configure-workspace-to-collect-events"></a>イベントを収集するようにワークスペースを構成する
VM 分析情報により、各仮想マシンのゲスト オペレーティング システムから、パフォーマンス データと、プロセスの詳細および依存関係が収集されます。 Log Analytics エージェントでも、Windows のイベント ログや Linux の syslog など、ゲストからログを収集できます。 接続されている Log Analytics ワークスペースから、これらのログの構成が取得されます。 ワークスペースを構成する必要があるのは 1 回だけであり、エージェントが接続するたびに、構成の変更がダウンロードされます。 

エージェントの仮想マシンから追加データを収集するように Log Analytics ワークスペースを構成する詳細については、「[Azure Monitor のエージェント データ ソース](agents/agent-data-sources.md)」を参照してください。

> [!NOTE]
> また、パフォーマンス カウンターを収集するようにワークスペースを構成することもできますが、VM 分析情報によって収集されるパフォーマンス データと重複している可能性が高いです。 ワークスペースによって収集されたパフォーマンス データが *Perf* テーブルに格納されるのに対し、VM 分析情報によって収集されたパフォーマンス データは *InsightsMetrics* テーブルに格納されます。 VM 分析情報によってまだ収集されていないカウンターが必要な場合にのみ、ワークスペースでパフォーマンスの収集を構成します。

### <a name="diagnostic-extension-and-telegraf-agent"></a>診断拡張機能と Telegraf エージェント
VM 分析情報によって使用される Log Analytics エージェントでは、パフォーマンス データが Log Analytics ワークスペースに送信されますが、Azure Monitor メトリックには送信されません。 このデータを Metrics に送信すると、メトリックス エクスプローラーで分析し、メトリック アラートで使用することができます。 これを行うには、Windows では診断拡張機能が、Linux では Telegraf エージェントが必要です。

これらのエージェントのインストールと構成の詳細については、「[Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成](agents/diagnostics-extension-windows-install.md)」および「[Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する](essentials/collect-custom-metrics-linux-telegraf.md)」を参照してください。


## <a name="monitor-applications"></a>アプリケーションの監視
カスタムア プリケーションを監視するために Azure Monitor によって使用される [Application Insights](app/app-insights-overview.md) を、監視するアプリケーションごとに構成する必要があります。 構成プロセスは、監視対象のアプリケーションの種類と、実行する監視の種類によって異なります。 Application Insights によって収集されたデータは、機能に応じて、Azure Monitor Metrics、Azure Monitor Logs、Azure Blob Storage に格納されます。 パフォーマンス データは Azure Monitor Metrics と Azure Monitor Logs の両方に格納され、追加の構成は必要ありません。

### <a name="create-an-application-resource"></a>アプリケーション リソースを作成する
監視するアプリケーションごとに、Application Insights でリソースを作成する必要があります。 Application Insights によって収集されたログ データは、ワークスペースベースのアプリケーションの Azure Monitor ログに格納されます。 クラシック アプリケーションのログ データは、[データの構造](logs/data-platform-logs.md#data-structure)に関するページで説明されているように、Log Analytics ワークスペースとは別に保存されます。

 アプリケーションを作成するときに、クラシックまたはワークスペース ベースのどちらを使用するかを選択する必要があります。 クラシック アプリケーションを作成するには、「[Application Insights リソースの作成](app/create-new-resource.md)」を参照してください。 ワークスペース ベースのアプリケーションを作成するには、「[ワークスペース ベースの Application Insights リソース (プレビュー)](app/create-workspace-resource.md)」を参照してください。

### <a name="configure-codeless-or-code-based-monitoring"></a>コード不要の監視またはコード ベースの監視を構成する
アプリケーションの監視を有効にするには、コード不要の監視またはコード ベースの監視を使用するかどうかを決定する必要があります。 構成プロセスは、この決定と監視対象のアプリケーションの種類によって異なります。

**コード不要の監視** は、実装するのが最も簡単で、コードを開発した後で構成できます。 コードを更新する必要はありません。 アプリケーションに応じて監視を有効にする方法の詳細については、次のリソースを参照してください。

- [Azure Web Apps でホストされているアプリケーション](app/azure-web-apps.md)
- [Java アプリケーション](app/java-in-process-agent.md)
- [Azure VM または Azure 仮想マシン スケール セット上の IIS でホストされる ASP.NET アプリケーション](app/azure-vm-vmss-apps.md)
- [IIS のオンプレミス VM でホストされる ASP.NET アプリケーション](app/monitor-performance-live-website-now.md)


**コード ベースの監視** では、さらにカスタマイズが可能で、追加のテレメトリが収集されますが、Application Insights SDK NuGet パッケージでコードに依存関係を追加する必要があります。 アプリケーションに応じて監視を有効にする方法の詳細については、次のリソースを参照してください。

- [ASP.NET アプリケーション](app/asp-net.md)
- [ASP.NET Core アプリケーション](app/asp-net-core.md)
- [.NET コンソール アプリケーション](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [その他のプラットフォーム](app/platforms.md)

### <a name="configure-availability-testing"></a>可用性テストを構成する
Application Insights の可用性テストは反復的なテストであり、世界中のポイントから定期的にアプリケーションの可用性と応答性が監視されます。 単純な ping テストなら無料で作成できます。一連の Web 要求を作成してユーザー トランザクションをシミュレートすることもできますが、関連コストが発生します。 

さまざまな種類のテストの概要と作成方法の詳細については、「[任意の Web サイトの可用性を監視する](app/monitor-web-app-availability.md)」を参照してください。

### <a name="configure-profiler"></a>Profiler を構成する
Application Insights の Profiler を使用すると、.NET アプリケーションのパフォーマンス トレースが提供されます。 特定の Web 要求の処理に最も長い時間がかかる "ホットな" コード パスを特定するのに役立ちます。 Profiler を構成するプロセスは、アプリケーションの種類によって異なります。 

Profiler の構成の詳細については、「[Application Insights を使用した Azure のプロファイル運用アプリケーション](app/profiler-overview.md)」を参照してください。

### <a name="configure-snapshot-debugger"></a>スナップショット デバッガーを構成する
Application Insights のスナップショット デバッガーを使用すると、.NET アプリケーションからの例外テレメトリを監視し、スローされる上位の例外のスナップショットを収集して、運用環境の問題を診断するために必要な情報を得ることができます。 スナップショット デバッガーを構成するためのプロセスは、アプリケーションの種類によって異なります。 

スナップショット デバッガーの構成の詳細については、「[.NET アプリでの例外でのデバッグ スナップショット](app/snapshot-debugger.md)」を参照してください。


## <a name="visualize-data"></a>データの視覚化
分析情報とソリューションには、データを分析するための独自のブックとビューが含まれています。 これらに加えて、Azure Monitor のデータ用のブックや、Azure Monitor のデータと Azure の他のサービスからのデータを組み合わせるダッシュボードなど、独自の[視覚化](visualizations.md)を作成することもできます。


### <a name="create-workbooks"></a>ブックを作成する
Azure Monitor の[ブック](visualize/workbooks-overview.md)を使用すると、Azure portal でリッチなビジュアル レポートを作成できます。 Azure Monitor Metrics と Azure Monitor Logs からのさまざまなデータ セットを組み合わせて、統合された対話型エクスペリエンスを作成できます。 Azure Monitor のメニューの **[ブック]** タブで、ブックのギャラリーにアクセスできます。 

カスタム ブックの作成の詳細については、「[Azure Monitor ブック](visualize/workbooks-overview.md)」を参照してください。

### <a name="create-dashboards"></a>ダッシュボードを作成する
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)は Azure の主要なダッシュボード テクノロジであり、Azure Monitor のデータと他のサービスからのデータを組み合わせて、Azure インフラストラクチャに対する 1 つのウィンドウを提供できます。 Azure Monitor Logs のデータが含まれるダッシュボードを作成する方法の詳細については、「[Log Analytics データのダッシュボードを作成して共有する](visualize/tutorial-logs-dashboards.md)」を参照してください。 

Application Insights のデータが含まれるダッシュボードを作成する方法の詳細については、「[Azure Application Insights を使ってカスタム KPI ダッシュボードを作成する](app/tutorial-app-dashboards.md)」を参照してください。 

## <a name="alerts"></a>警告
Azure Monitor の警告を使用すると、監視データで識別された重要なデータやパターンが事前に通知されます。 一部の分析情報では、構成なしで警告が生成されます。 その他のシナリオでは、分析するデータと、警告を生成するタイミングの条件、および警告が生成されたときに実行するアクションを定義するアクション グループが含まれる、[警告ルール](alerts/alerts-overview.md)を作成する必要があります。 


### <a name="create-action-groups"></a>アクション グループを作成する
[アクション グループ](alerts/action-groups.md)は、警告がトリガーされたときに実行するアクションを決定するために警告ルールによって使用される通知設定のコレクションです。 アクションの例としては、メールやテキストの送信、Webhook の呼び出し、ITSM ツールへのデータの送信などがあります。 各警告ルールには少なくとも 1 つのアクション グループが必要であり、1 つのアクション グループを複数の警告ルールで使用できます。

クション グループの作成の詳細と、含めることができるさまざまなアクションの説明については、「[Azure portal でのアクション グループの作成および管理](alerts/action-groups.md)」を参照してください。


### <a name="create-alert-rules"></a>アラート ルールの作成
使用するデータの種類によって定義される、複数の種類の警告ルールがあります。 それぞれの機能は異なり、コストが異なります。 従う必要がある基本的な戦略は、必要なロジックが提供される最低コストの警告ルールの種類を使用することです。

- [アクティビティ ログのルール](alerts/activity-log-alerts.md)。 指定した条件と一致する新しいアクティビティ ログ イベントへの応答として、警告を作成します。 これらの警告にはコストがかからないため、最初に選択する必要があります。 アクティビティ ログの警告の作成について詳しくは、「[Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](alerts/alerts-activity-log.md)」を参照してください。
- [メトリックの警告ルール](alerts/alerts-metric-overview.md)。 しきい値を超えた 1 つまたは複数のメトリック値に応答して、警告を作成します。 メトリックの警告はステートフルであり、値がしきい値を下回ると警告は自動的に閉じられ、状態が変化したときにだけ通知が送信されます。 メトリックの警告にはコストがかかりますが、これはログの警告より大幅に低くなります。 メトリックの警告の作成について詳しくは、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](alerts/alerts-metric.md)」を参照してください。
- [ログの警告ルール](alerts/alerts-unified-log.md)。 スケジュール クエリの結果が指定した条件と一致した場合に、警告が作成されます。 これらは、警告ルールの中で最も高価なものですが、最も複雑な条件を指定できます。 ログ クエリの警告の作成について詳しくは、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](alerts/alerts-log.md)」を参照してください。
- [アプリケーションの警告](app/monitor-web-app-availability.md)を使用すると、Web アプリケーションのプロアクティブなパフォーマンスと可用性のテストを実行できます。 簡単な ping テストは無料で実行できますが、より複雑なテストにはコストがかかります。 さまざまなテストの説明と作成方法の詳細については、「[任意の Web サイトの可用性を監視する](app/monitor-web-app-availability.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

- 「[Azure Policy を使用して大規模に Azure Monitor をデプロイする](deploy-scale.md)」を参照してください。