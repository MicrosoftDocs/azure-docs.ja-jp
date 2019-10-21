---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: 93a2554b5d3cc24e1b5fc1e3d0f18ed1bfe0579c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71692039"
---
Azure でホストされている VM が大幅に増加すると、サポートするアプリケーションやインフラストラクチャサービスに影響を与えるパフォーマンスと正常性の問題を特定することが重要になります。 基本的な監視は、CPU 使用率、ディスク使用率、メモリ使用率、およびホスト ハイパーバイザーによって収集されたネットワーク トラフィックのメトリックの種類ごとに、Azure によって既定で提供されます。 [拡張機能](../articles/virtual-machines/windows/extensions-features.md)を使用して追加のメトリックとログ データを収集し、ゲスト オペレーティング システムから VM の診断を構成することができます。

VM 内で実行されているゲスト オペレーティング システム、.NET ベースまたは Java web アプリケーション コンポーネントのパフォーマンスと正常性の問題を検出して診断するために、Azure Monitor では、Azure Monitor for VMs や Application Insights などの包括的な機能を備えた一元的な監視が提供されます。

## <a name="diagnostics-and-metrics"></a>診断とメトリック 

Azure Portal、Azure CLI、Azure PowerShell、アプリケーション プログラミング インターフェイス (API) のプログラミングで、[メトリック](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)を使った[診断データ](https://docs.microsoft.com/cli/azure/vm/diagnostics)の収集を設定し、監視することができます。 たとえば、次のようなことができます。

- **VM の基本的なメトリックを観察する。** Azure Portal の [概要] 画面には、CPU 使用率、ネットワーク使用率、ディスクの総バイト数、1 秒あたりのディスクの操作数など、基本的なメトリックが表示されます。

- **Azure Portal を使ってブート診断の収集を有効にして表示する。** 独自のイメージを Azure に導入した場合に限らず、いずれかのプラットフォーム イメージをブートするときでも、VM は、さまざまな理由で起動できない状態に陥ることがあります。 VM を作成するときに、[設定] 画面の [監視] セクションで [ブート診断] の **[有効]** をクリックすることで、ブート診断を簡単に有効にできます。

    VM が起動すると、ブート診断エージェントによってブート出力がキャプチャされて Azure Storage に格納されます。 VM の起動に関する問題は、このデータを使ってトラブルシューティングすることができます。 コマンド ライン ツールで VM を作成した場合、自動的にはブート診断が有効になりません。 ブート診断を有効にするにはまず、ブート ログを格納するためのストレージ アカウントを作成しておく必要があります。 Azure Portal でブート診断を有効にした場合、ストレージ アカウントが自動的に作成されます。

    VM の作成時に有効にしなかった場合、ブート診断は後から [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)、または [Azure Resource Manager テンプレート](../articles/virtual-machines/windows/extensions-diagnostics-template.md)を使っていつでも有効にできます。

- **ゲスト OS の診断データの収集を有効にする。** VM を作成するときに、設定画面でゲスト OS の診断を有効にすることができます。 診断データの収集を有効にすると、[Linux 用 IaaSDiagnostics 拡張機能](../articles/virtual-machines/linux/diagnostic-extension.md)または [Windows 用 IaaSDiagnostics 拡張機能](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)が VM に追加され、ディスク、CPU、メモリに関して追加のデータを収集できるようになります。

    収集された診断データを利用して、VM の自動スケールを構成できます。 データを保存するための [Azure Monitor ログ](../articles/azure-monitor/platform/data-platform-logs.md)を構成し、パフォーマンスの異常を知らせるアラートを設定することもできます。

## <a name="alerts"></a>アラート

特定のパフォーマンス メトリックに基づいて[アラート](../articles/azure-monitor/platform/alerts-overview.md)を作成できます。 たとえば平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、それらの問題に関するアラートを受け取ることができます。 アラートの構成は、[Azure portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)、[Azure Resource Manager テンプレート](../articles/azure-monitor/platform/alerts-metric-create-templates.md)、[Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli) のいずれかで行うことができます。

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) は、Azureサービスの問題が影響を及ぼす場合に、パーソナライズされたガイダンスとサポートを提供するほか、今後の計画済みメンテナンスに向けた準備を支援します。 Azure Service Health を利用すれば、お客様とそのチームは、対象を絞った柔軟な通知を受けられます。

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md) によって、Azure で発生した問題がお客様のリソースに影響を及ぼす場合に、診断とサポートを受けられます。 リソースの現在と過去の正常性に関する情報が表示され、問題を軽減するのに役立ちます。 Resource Health は、Azure のサービスの問題についてサポートが必要な場合にテクニカル サポートを提供します。

## <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]

[Azure アクティビティ ログ](../articles/azure-monitor/platform/activity-logs-overview.md)は、Azure で発生したサブスクリプションレベルのイベントの分析に利用できるサブスクリプション ログです。 このログには、Azure Resource Manager の運用データからサービス正常性イベントの更新まで、さまざまなデータが含まれています。 Azure Portal で [アクティビティ ログ] をクリックすると、VM のログを表示できます。

たとえば、アクティビティ ログでは次のことを実行できます。

- [アクティビティ ログ イベントに対するアラートを設定する](../articles/azure-monitor/platform/activity-logs-overview.md)。
- サードパーティのサービスや Power BI などのカスタム分析ソリューションで取り込むために、アクティビティ ログを[イベント ハブにストリーム配信](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md)する。
- [Power BI コンテンツ パック](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)を使用して Power BI で分析する。
- アーカイブや手動での検査に使用するためにアクティビティ ログを[ストレージ アカウントに保存](../articles/azure-monitor/platform/archive-activity-log.md)する。 ログ プロファイルを使用して、リテンション期間 (日数) を指定できます。

アクティビティ ログ データには、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/)、[Azure CLI](https://docs.microsoft.com/cli/azure/monitor)、[Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) のいずれかを使ってアクセスすることもできます

[Azure リソース ログ](../articles/azure-monitor/platform/resource-logs-overview.md)は、VM から出力されるログです。その操作に関する豊富なデータが提供されます。 リソース ログは、VM 内で実行された操作についての分析情報が得られるという点でアクティビティ ログとは異なります。

たとえば、診断ログでは次のことを実行できます。

- 監査や手動での検査に使用するために診断ログを[ストレージ アカウント](../articles/azure-monitor/platform/archive-diagnostic-logs.md)に保存する。 リソース診断設定を使用して、リテンション期間 (日数) を指定できます。
- サードパーティのサービスや Power BI などのカスタム分析ソリューションで取り込むために、[Event Hubs にストリーム配信](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md)する。
- これを [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md) で分析する。

## <a name="advanced-monitoring"></a>高度な監視

Azure VM と仮想マシン スケール セットでサポートされているアプリケーションまたはサービスを表示するには、VM で実行されているゲスト OS またはワークロードの問題を識別して、アプリケーションの可用性やパフォーマンスに影響があるかどうか、またはアプリケーションの問題であるかどうかを把握し、[Azure Monitor for VMs](../articles/azure-monitor/insights/vminsights-overview.md) と [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) の両方を有効にします。

Azure Monitor for VMs では、お使いの Azure 仮想マシン (VM) の大規模な監視が行われます。それを行うために、さまざまなプロセスや、検出したその他のリソースおよび外部プロセスに対する相互接続された依存関係など、Windows および Linux VM のパフォーマンスと正常性が分析されます。 これには、問題を調査し、VM の容量を評価する際に役立つ、いくつかの傾向パフォーマンス グラフが含まれています。 依存関係マップには、監視対象のマシン、監視対象外のマシン、プロセスとこれらのマシン間の失敗したネットワーク接続とアクティブなネットワーク接続が表示され、標準ネットワーク接続メトリックでの傾向グラフが表示されます。 Application Insights と組み合わせることで、アプリケーションを監視し、HTTP 要求や例外などのテレメトリをキャプチャできるため、VM とアプリケーションの間の問題を相互に関連付けることができます。 Azure Monitor for VMs によって収集された監視データから検出された重要な条件についてアラートを生成するには [Azure Monitor アラート](../articles/azure-monitor/platform/alerts-overview.md)を構成します。

## <a name="next-steps"></a>次の手順

- 「[Azure PowerShell を使用した Windows 仮想マシンの監視](../articles/virtual-machines/windows/tutorial-monitoring.md)」または [Azure CLI を使用した Linux 仮想マシンの監視](../articles/virtual-machines/linux/tutorial-monitoring.md)に関するページの手順を参照します。

- [監視と診断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)に関するベスト プラクティスについて詳しい情報を入手します。
