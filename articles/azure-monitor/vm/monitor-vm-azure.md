---
title: Azure Monitor を使用して Azure 仮想マシンを監視する
description: Azure Monitor を使用し、Azure の仮想マシンから監視データを収集して分析する方法について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 2c93471436030f9260f4fa0d95d656c27d382346
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047045"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Azure Monitor を使用して Azure 仮想マシンを監視する
この記事では、Azure Monitor を使用して Azure 仮想マシンから監視データを収集して分析し、正常性を維持する方法について説明します。 仮想マシンは、[他の Azure リソース](../essentials/monitor-azure-resource.md)と同様に、Azure Monitor を使用して可用性とパフォーマンスを監視できますが、ゲスト オペレーティング システムとそこで実行されるワークロードも監視する必要があるため、他のリソースと異なります。 

> [!NOTE]
> この記事では、Azure Monitor での仮想マシンの監視に関する概念とオプションの完全な概要について説明します。 基礎となる概念を重視せずに、仮想マシンの監視をすぐに開始するには、「[クイックスタート:Azure Monitor を使用して Azure 仮想マシンを監視する](./quick-monitor-azure-vm.md)」を参照してください。


## <a name="differences-from-other-azure-resources"></a>他の Azure リソースとの違い
「[Azure Monitor を使用した Azure リソースの監視](../essentials/monitor-azure-resource.md)」では、Azure リソースによって生成される監視データと、Azure Monitor の機能を使用してこのデータを分析してアラートを作成する方法について説明されています。 Azure 仮想マシンから同じ監視データを収集して操作することができますが、次の相違点があります。

-  [プラットフォームのメトリック](../essentials/data-platform-metrics.md)は、仮想マシンに対して自動的に収集されますが、[仮想マシンのホスト](#monitoring-data)に対してのみ収集されます。 ゲスト オペレーティング システムからパフォーマンス データを収集するには、エージェントが必要です。 
- 仮想マシンでは、Azure リソース内で実行された操作に関する分析情報を提供する [リソース ログ](../essentials/platform-logs-overview.md)は生成されません。 エージェントを使用して、ゲスト オペレーティング システムからログ データを収集します。
- 仮想マシンに[診断設定](../essentials/diagnostic-settings.md)を作成して、ストレージやイベント ハブなどの他のターゲットにプラットフォームのメトリックを送信することはできますが、Azure portal でこれらの診断設定を構成することはできません。 

## <a name="monitoring-data"></a>データの監視
Azure の仮想マシンによって、次の図に示すような[ログ](../logs/data-platform-logs.md)と[メトリック](../essentials/data-platform-metrics.md)が生成されます。

![概要](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>仮想マシンのホスト
「[データの監視](../essentials/monitor-azure-resource.md#monitoring-data)」で説明されている他の Azure リソースと同じように、Azure の仮想マシンによって、仮想マシンのホストに対して次のデータが生成されます。

- [プラットフォーム メトリック](../essentials/data-platform-metrics.md) - 一定の間隔で自動的に収集される、特定の時刻におけるリソースのいくつかの側面が記述されている数値。 プラットフォームのメトリックは仮想マシンのホストに対して収集されますが、ゲスト オペレーティング システムのメトリックを収集するには、診断拡張機能が必要です。
- [アクティビティ ログ](../essentials/platform-logs-overview.md) - 外部 (管理プレーン) からサブスクリプションの各 Azure リソースに対する操作についての分析情報を提供します。 仮想マシンの場合、これには、起動された時刻や構成の変更などの情報が含まれます。


### <a name="guest-operating-system"></a>ゲスト オペレーティング システム
仮想マシンのゲスト オペレーティング システムからデータを収集するには、各仮想マシンでローカルに実行され、Azure Monitor にデータを送信するエージェントが必要です。 Azure Monitor では、それぞれ異なるデータを収集し、異なる場所にデータを書き込む複数のエージェントを使用できます。 さまざまなエージェントの詳細な比較については、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」をご覧ください。 

- [Log Analytics エージェント](../agents/agents-overview.md#log-analytics-agent) - Azure、他のクラウド環境、オンプレミスの仮想マシンで使用できます。 Azure Monitor ログにデータを収集します。 VM Insights および監視ソリューションをサポートしています。 これは System Center Operations Manager に使用されているのと同じエージェントです。
- [Dependency Agent](../agents/agents-overview.md#dependency-agent) - 仮想マシンで実行されているプロセスとその依存関係に関するデータを収集します。 Azure にデータを送信するために Log Analytics エージェントに依存しており、 VM Insights、Service Map、Wire Data 2.0 の各ソリューションがサポートされています。
- [Azure 診断拡張機能](../agents/agents-overview.md#azure-diagnostics-extension) - Azure Monitor 仮想マシンでのみ使用できます。 複数の場所にデータを収集できますが、主に、Windows 仮想マシンの Azure Monitor メトリックにゲスト パフォーマンス データを収集するために使用されます。
- [Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md) - Linux VM から Azure Monitor メトリックにパフォーマンス データを収集します。


## <a name="configuration-requirements"></a>構成要件
仮想マシンを監視するために Azure Monitor のすべての機能を有効にするには、仮想マシンのホストとゲスト オペレーティング システムから監視データを [Azure Monitor メトリック](../logs/data-platform-logs.md)と [Azure Monitor ログ](../logs/data-platform-logs.md)の両方に収集する必要があります。 次の表に、このコレクションを有効にするために実行する必要がある構成の一覧を示します。 特定の要件に応じて、これらの手順のすべて実行しないことを選択することができます。

| 構成ステップ | 完了したアクション | 有効な機能 |
|:---|:---|:---|
| 構成なし | - ホスト プラットフォーム メトリックがメトリックに収集された。<br>- アクティビティ ログが収集された。 | - ホストのメトリックス エクスプローラー。<br>- ホストのメトリック アラート。<br>- アクティビティ ログ アラート。 |
| [VM Insights を有効にする](#enable-vm-insights) | - Log Analytics エージェントがインストールされた。<br>- 依存関係エージェントがインストールされた。<br>- ゲスト パフォーマンス データがログに収集された。<br>- プロセスと依存関係の詳細がログに収集された。 | - ゲスト パフォーマンス データのパフォーマンス グラフとブック。<br>- ゲスト パフォーマンス データのログ クエリ。<br>- ゲスト パフォーマンス データのログ アラート。<br>- 依存関係マップ。 |
| [診断拡張機能と Telegraf エージェントをインストールする](#enable-diagnostics-extension-and-telegraf-agent) | - ゲスト パフォーマンス データがメトリックに収集された。 | - ゲストのメトリックス エクスプローラー。<br>- ゲストのメトリック アラート。  |
| [Log Analytics ワークスペースを構成する](#configure-log-analytics-workspace) | - イベントがゲストから収集された。 | - ゲスト イベントのログ クエリ。<br>- ゲスト イベントのログ アラート。 |
| [仮想マシンの診断設定を作成する](#collect-platform-metrics-and-activity-log) | - プラットフォームのメトリックがログに収集された。<br>- アクティビティ ログがログに収集された。 | - ホスト メトリックのログ クエリ。<br>- ホスト メトリックのログ アラート。<br>- アクティビティ ログのログ クエリ。

これらの各構成ステップについては、以降のセクションで説明します。

### <a name="enable-vm-insights"></a>VM Insights を有効にする
[VM Insights](../vm/vminsights-overview.md) は、Azure Monitor 内の仮想マシンを監視するための主要なツールである Azure Monitor の[分析情報](../monitor-reference.md)です。 これにより、標準 Azure Monitor 機能に次の付加価値が提供されます。

- 仮想マシンのゲスト オペレーティング システムとワークロードの監視を有効にする Log Analytics エージェントと Dependency Agent のオンボードの簡略化。 
- 仮想マシンのゲスト オペレーティング システムからコア パフォーマンス メトリックの分析を可能にする、事前に定義された傾向を示すパフォーマンス グラフとブック。
- 各仮想マシンで実行されているプロセスと、他のマシンや外部ソースと相互接続されたコンポーネントを表示する依存関係マップ。

![VM Insights のパフォーマンス ビュー](media/monitor-vm-azure/vminsights-01.png)

![VM Insights のマップ ビュー](media/monitor-vm-azure/vminsights-02.png)


Azure portal の仮想マシン メニューの **[分析情報]** オプションで、VM Insights を有効にします。 詳細情報およびその他の構成方法については、「[VM Insights の有効化の概要](vminsights-enable-overview.md)」を参照してください。

![VM Insights を有効にする](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Log Analytics ワークスペースを構成する
VM Insights によって使用される Log Analytics エージェントは、[Log Analytics ワークスペース](../logs/data-platform-logs.md)にデータを送信します。 Log Analytics ワークスペースを構成することにより、エージェントから追加のパフォーマンス データ、イベント、およびその他の監視データの収集を有効にすることができます。 ワークスペースに接続しているすべてのエージェントによって構成が自動的にダウンロードされ、定義されたデータの収集がすぐに開始されるため、これは一度だけ構成すれば済みます。 

VM Insights からワークスペースの構成に直接アクセスするには、 **[始める]** から **[ワークスペースの構成]** を選択します。 ワークスペース名をクリックして、そのメニューを開きます。

![ワークスペースの構成](media/monitor-vm-azure/workspace-configuration.png)

ワークスペース メニューから **[詳細設定]** 、 **[データ]** の順に選択してデータ ソースを構成します。 Windows エージェントの場合は、 **[Windows イベント ログ]** を選択し、"*システム*" や "*アプリケーション*" などの共通イベント ログを追加します。 Linux エージェントの場合は、 **[Syslog]** を選択し、*kern* や "*デーモン*" などの一般的な機能を追加します。 使用可能なデータ ソースの一覧と構成の詳細については、「[Azure Monitor のエージェント データ ソース](../agents/agent-data-sources.md)」をご覧ください。 

![イベントの構成](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> ワークスペースの構成から収集するパフォーマンス カウンターを構成できますが、これは VM Insights によって収集されるパフォーマンス カウンターと重複している可能性があります。 VM Insights では、1 分間に 1 回の頻度で最も一般的なカウンターのセットが収集されます。 VM Insights によってまだ収集されていないカウンターを収集する場合、またはパフォーマンス データを使用している既存のクエリがある場合にのみ、ワークスペースで収集するパフォーマンス カウンターを構成します。


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>診断拡張機能と Telegraf エージェントを有効にする
VM Insights は、Log Analytics ワークスペースにデータを送信する Log Analytics エージェントに基づいています。 ログ クエリ、[ログ アラート](../alerts/alerts-log.md)、[ブック](../visualize/workbooks-overview.md)など、[Azure Monitor の複数の機能](../logs/log-query-overview.md)がサポートされています。 [診断拡張機能](../agents/diagnostics-extension-overview.md)により、Windows 仮想マシンのゲスト オペレーティング システムから Azure Storage にパフォーマンスデータが収集され、必要に応じて、パフォーマンス データが [Azure Monitor メトリック](../essentials/data-platform-metrics.md)に送信されます。 Linux 仮想マシンの場合、Azure メトリックにデータを送信するには、[Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md) が必要です。  これにより、[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)や[メトリック アラート](../alerts/alerts-metric.md)など、Azure Monitor の他の機能が有効になります。 また、Azure Event Hubs を使用して Azure Monitor の外部でイベントとパフォーマンス データを送信するように診断拡張機能を構成することもできます。

Azure portal で [VM] メニューの **[診断設定]** オプションから、単一の Windows 仮想マシンに診断拡張機能をインストールします。 **[シンク]** タブで **[Azure Monitor]** を有効にするオプションを選択します。複数の仮想マシンに対してテンプレートまたはコマンド ラインから拡張機能を有効にするには、「[インストールと構成](../agents/diagnostics-extension-overview.md#installation-and-configuration)」を参照してください。 Log Analytics エージェントとは異なり、収集するデータは、各仮想マシンの拡張機能の構成で定義されます。

![診断設定](media/monitor-vm-azure/diagnostic-setting.png)

Linux 仮想マシンでの Telegraf エージェントの構成の詳細については、「[Telegraf をインストールして構成する](../essentials/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf)」を参照してください。 Linux では **[診断設定]** メニュー オプションを使用できますが、これにより Azure ストレージへのデータの送信のみが許可されます。

### <a name="collect-platform-metrics-and-activity-log"></a>プラットフォームのメトリックとアクティビティ ログを収集する
Azure portal で、各仮想マシンのホストについて収集されたプラットフォームのメトリックとアクティビティ ログを表示できます。 このデータを VM Insights と同じ Log Analytics ワークスペースに収集して、仮想マシン用に収集された他の監視データを使用して分析します。 このコレクションは、[診断設定](../essentials/diagnostic-settings.md)を使用して構成されています。 [サブスクリプションの診断設定](../essentials/diagnostic-settings.md#create-in-azure-portal)を使用して、アクティビティ ログが収集されます。

仮想マシンの診断設定を使用して、プラットフォームのメトリックが収集されます。 他の Azure リソースとは異なり、Azure portal で仮想マシンの診断設定を作成することはできず、[別の方法](../essentials/diagnostic-settings.md#create-using-powershell)を使用する必要があります。 次の例は、PowerShell と CLI の両方を使用して仮想マシンのメトリックを収集する方法を示しています。

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```azurecli
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Azure Portal の監視機能 
仮想マシンに対する監視データの収集を構成すると、Azure portal でそれにアクセスするための複数のオプションがあります。

- すべての監視対象リソースのデータにアクセスするには、 **[Azure Monitor]** メニューを使用します。 
- 大規模な仮想マシンのセットを監視するために、VM Insights を使用します。
- Azure portal のメニューから、1 つの仮想マシンのデータを分析します。 次の表に、仮想マシンの監視メニューのさまざまなオプションを示します。

![Azure Portal の監視機能](media/monitor-vm-azure/monitor-menu.png)

| メニュー オプション | 説明 |
|:---|:---|
| 概要 | 仮想マシンのホストの[プラットフォームのメトリック](../essentials/data-platform-metrics.md)を表示します。 グラフをクリックすると、[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)でこのデータを操作することができます。 |
| アクティビティ ログ | 現在の仮想マシンに対してフィルター処理された[アクティビティ ログ](../essentials/activity-log.md#view-the-activity-log)のエントリ。 |
| 洞察 | 現在選択されている仮想マシンのマップを使用して [VM Insights](../vm/vminsights-overview.md) 開きます。 |
| 警告 | 現在の仮想マシンの[アラート](../alerts/alerts-overview.md)を表示します。  |
| メトリック | 現在の仮想マシンに設定されているスコープを使用して、[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)を開きます。 |
| 診断設定 | 現在の仮想マシンに対して[診断拡張機能](../agents/diagnostics-extension-overview.md)を有効にして構成します。 |
| Advisor の推奨事項 | [Azure Advisor](../../advisor/index.yml) からの現在の仮想マシンに関する推奨事項。 |
| ログ | 現在の仮想マシンに設定されている[スコープ](../logs/scope.md)を使用して、[Log Analytics](../logs/log-analytics-overview.md) を開きます。 |
| 接続モニター | [Network Watcher 接続モニター](../../network-watcher/connection-monitor-overview.md)を開いて、現在の仮想マシンと他の仮想マシンとの間の接続を監視します。 |


## <a name="analyzing-metric-data"></a>メトリック データの分析
メトリックス エクスプローラーを使用して仮想マシンのメトリックを分析するには、仮想マシンのメニューから **[メトリック]** を開きます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../essentials/metrics-getting-started.md)」を参照してください。 

仮想マシンでメトリックに使われる 3 つの名前空間があります。

| 名前空間 | 説明 | 要件 |
|:---|:---|:---|
| 仮想マシンのホスト | すべての Azure 仮想マシンについて、ホストのメトリックが自動的に収集されます。 メトリックの詳細なリストは、[Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) にあります。 | 自動的に収集され、構成は必要ありません。 |
| ゲスト (クラシック) | ゲスト オペレーティング システムとアプリケーションのパフォーマンス データの限られたセット。 メトリックス エクスプローラーで使用できますが、メトリック アラートなどの他の Azure Monitor 機能では使用できません。  | [診断の拡張機能](../agents/diagnostics-extension-overview.md)がインストールされていること。 データは Azure Storage から読み取られます。  |
| 仮想マシンのゲスト | メトリックを使用するすべての Azure Monitor 機能で使用可能な、ゲスト オペレーティング システムとアプリケーションのパフォーマンス データ。 | Windows の場合、[診断拡張機能がインストール](../agents/diagnostics-extension-overview.md)されて、Azure Monitor シンクが有効になっていること。 Linux の場合、[Telegraf エージェントがインストール](../essentials/collect-custom-metrics-linux-telegraf.md)されていること。 |

![Azure portal でのメトリック エクスプローラー](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>ログ データの分析
Azure 仮想マシンにより、次のデータが Azure Monitor ログに収集されます。 

VM Insights により、*InsightsMetrics* テーブルに書き込まれる、事前定義済みの一連のパフォーマンス カウンターの収集が有効になります。 これは、[Container Insights](../containers/container-insights-overview.md) で使用されるテーブルと同じです。 

| データ ソース | 必要条件 | テーブル |
|:---|:---|:---|
| VM Insights | 各仮想マシンで有効にする。 | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>詳細については、「[VM Insights からログをクエリする方法](../vm/vminsights-log-search.md)」をご覧ください。 |
| アクティビティ ログ | サブスクリプションの診断設定。 | AzureActivity |
| ホストのメトリック | 仮想マシンの診断設定。 | AzureMetrics |
| ゲスト オペレーティング システムからのデータ ソース | Log Analytics エージェントを有効にし、データ ソースを構成する。 | 各データ ソースのドキュメントを参照してください。 |


> [!NOTE]
> Log Analytics エージェントによって収集されたパフォーマンス データは *Perf* テーブルに書き込まれますが、VM Insights ではそれが *InsightsMetrics* テーブルに収集されます。 これは同じデータですが、テーブルの構造は異なります。 *Perf* に基づく既存のクエリがある場合、*InsightsMetrics* を使用するように書き直す必要があります。


## <a name="alerts"></a>警告
監視データで重要な条件が検出され、ロジック アプリの起動や webhook の呼び出しなどのアクションが起動される可能性がある場合は、Azure Monitor の[アラート](../alerts/alerts-overview.md)によって事前に通知されます。 アラート ルールにより、アラートをいつ作成するかを決定するために使用されるロジックが定義されます。 Azure Monitor により、アラート ルールによって使用されるデータが収集されますが、Azure サブスクリプションでアラート条件を定義するルールを作成する必要があります。

以降のセクションでは、アラート ルールの種類と、それぞれを使用する場合の推奨事項について説明します。 この推奨事項は、アラート ルールの種類の機能とコストに基づいています。 アラートの料金の詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。


### <a name="activity-log-alert-rules"></a>アクティビティ ログ アラート ルール
[アクティビティ ログ アラート ルール](../alerts/alerts-activity-log.md)は、特定の条件に一致するエントリがアクティビティ ログに作成されたときに発生します。 コストはかからないため、必要なロジックがアクティビティ ログに含まれている場合は、これを最初に選択します。 

アクティビティ ログ アラートのターゲット リソースには、特定の仮想マシン、リソース グループ内のすべての仮想マシン、またはサブスクリプション内のすべての仮想マシンを指定できます。

たとえば、シグナル名に [*仮想マシンを電源オフ*] を選択すると、重要な仮想マシンが停止された場合に、アラートが作成されます。

![アクティビティ ログ アラート](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>メトリック アラート ルール
[メトリック アラート ルール](../alerts/alerts-metric.md)は、メトリック値がしきい値を超えた場合に発生します。 特定のしきい値を定義したり、履歴データに基づいてしきい値を動的に決定することを Azure Monitor に許可したりすることができます。  可能な場合には常に、メトリック アラートをメトリック データで使用してください。その理由は、ログ アラート ルールよりもコストが低く、応答性が高いためです。 これらはステートフルでもあります。つまり、メトリックがしきい値を下回ると自動的に解決されます。

アクティビティ ログ アラートのターゲット リソースには、特定の仮想マシンまたはリソース グループ内のすべての仮想マシンを指定できます。

たとえば、仮想マシンのプロセッサが特定の値を超えたときにアラートを作成するには、シグナルの種類として "*CPU 使用率*" を使用してメトリック アラート ルールを作成します。 特定のしきい値を設定するか、動的しきい値の設定を Azure Monitor に許可します。 

![メトリック アラート](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>ログ アラート
[ログ アラート ルール](../alerts/alerts-log.md)は、スケジュールされたログ クエリの結果が特定の条件に一致した場合に発生します。 ログ クエリ アラートは、最もコストが高く、応答性が低いアラート ルールですが、最も多様なデータにアクセスすることができ、他のアラート ルールでは実行できない複雑なロジックを実行できます。 

ログ クエリのターゲット リソースは、Log Analytics ワークスペースです。 クエリ内の特定のコンピューターをフィルター処理します。

たとえば、特定のリソース グループ内の仮想マシンがオフラインかどうかを確認するアラートを作成するには、次のクエリを使用します。これにより、過去 10 分間にハートビートがなかった各コンピューターのレコードが返されます。 しきい値 1 を使用すると、少なくとも 1 台のコンピューターにハートビートがない場合に発生します。

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![ハートビートがない場合のログ アラート](media/monitor-vm-azure/log-alert-01.png)

サブスクリプション内のいずれかの Windows 仮想マシンで過剰な数の失敗したログオンが発生した場合にアラートを作成するには、過去 1 時間に失敗した各ログオン イベントのレコードが返される次のクエリを使用します。 失敗したログオンの許容回数に設定されたしきい値を使用します。 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![失敗したログオンのログ アラート](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager を使用すると、仮想マシン上のワークロードを詳細に監視できます。 監視プラットフォームと実装に関するさまざまな戦略の比較については、「[クラウド監視ガイド](/azure/cloud-adoption-framework/manage/monitor/)」を参照してください。

使用し続ける予定の既存の Operations Manager 環境がある場合は、それを Azure Monitor と統合して追加の機能を提供することができます。 Azure Monitor によって使用される Log Analytics エージェントは、監視対象の仮想マシンが両方にデータを送信できるように、Operations Manager で使用されるものと同じです。 VM Insights にエージェントを追加して、上で指定したように追加データを収集するようにワークスペースを構成する必要は依然としてありますが、仮想マシンは引き続き Operations Manager 環境で既存の管理パックを変更せずに実行できます。

既存の Operations Manager 機能を強化する Azure Monitor の機能には、次のようなものがあります。

- ログとパフォーマンス データを対話形式で分析するには、Log Analytics を使用します。
- 複数の仮想マシンにわたるアラート条件と、Operations Manager でアラートを使用できない長期的な傾向の使用を定義するには、ログ アラートを使用します。   

既存の Operations Manager 管理グループを自分の Log Analytics ワークスペースに接続する方法の詳細については、「[Operations Manager を Azure Monitor に接続する](../agents/om-agents.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

* [ログ クエリを使用して Azure Monitor ログのデータを分析する方法について学習する。](../logs/get-started-queries.md)
* [Azure Monitor のメトリックとログを使用したアラートについて学習する。](../alerts/alerts-overview.md)