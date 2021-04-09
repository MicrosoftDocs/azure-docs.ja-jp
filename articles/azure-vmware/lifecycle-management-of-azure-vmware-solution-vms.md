---
title: Azure VMware Solution VM のライフサイクル管理
description: Microsoft Azure ネイティブ ツールを使用して、Azure VMware Solution VM のライフサイクルのあらゆる側面を管理する方法について説明します。
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 2cb9964b68769b1e784cebf62b4d336b355c68fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572195"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware Solution VM のライフサイクル管理

Microsoft Azure ネイティブ ツールを使用すると、Azure 環境の仮想マシン (VM) を監視および管理できます。 さらに、Azure VMware Solution の VM とオンプレミスの VM を監視および管理することもできます。 この概要では、Azure の統合監視アーキテクチャについて説明します。また、Azure のネイティブ ツールを使用して、Azure VMware Solution VM をライフサイクル全体にわたって管理する方法についても説明します。

## <a name="benefits"></a>メリット

- Azure ネイティブ サービスを使用して、ハイブリッド環境 (Azure、Azure VMware Solution、オンプレミス) の VM を管理できます。
- Azure、Azure VMware Solution、およびオンプレミスの VM に対する統合された監視と可視性が得られます。
- Azure Automation の Azure Update Management を使用して、Windows マシンと Linux マシンの両方のオペレーティング システムの更新プログラムを管理できます。 
- Azure Security Center により、次のような高度な脅威保護が提供されます。
    - ファイルの整合性の監視
    - ファイルレスのセキュリティ アラート
    - オペレーティング システムのパッチ評価
    - セキュリティの誤った構成の評価
    - エンドポイント保護の評価 
- 新しい VM と既存の VM に対して、Azure Arc 対応サーバー VM 拡張機能のサポートを使用して、Log Analytics エージェントを簡単にデプロイできます。 
- Azure Monitor の Log Analytics ワークスペースにより、Log Analytics エージェントまたは拡張機能を使用して、ログ収集およびパフォーマンス カウンターの収集が可能になります。 データとログを 1 か所に収集し、そのデータを別の Azure ネイティブ サービスに提供します。 
- Azure Monitor には次のような利点もあります。 
    - シームレスな監視 
    - インフラストラクチャの可視性の向上 
    - インスタント通知 
    - 自動解決 
    - コスト効率 

## <a name="integrated-azure-monitoring-architecture"></a>Azure の統合監視アーキテクチャ

次の図は、Azure VMware Solution VM の統合監視アーキテクチャを示しています。

![Azure の統合監視アーキテクチャ](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>開始する前に

Azure を初めて使用する場合、または前に説明したサービスに慣れていない場合は、次の記事を確認してください。

- [Automation アカウントの認証の概要](../automation/automation-security-overview.md)
- [Azure Monitor ログのデプロイの設計](../azure-monitor/logs/design-logs-deployment.md)と [Azure Monitor](../azure-monitor/overview.md)
- Azure Security Center の[計画](../security-center/security-center-planning-and-operations-guide.md)と[サポートされているプラットフォーム](../security-center/security-center-os-coverage.md)
- [Azure Monitor for VMs の有効化の概要](../azure-monitor/vm/vminsights-enable-overview.md)
- 「[Azure Arc 対応サーバーとは](../azure-arc/servers/overview.md)」と「[Azure Arc 対応 Kubernetes とは](../azure-arc/kubernetes/overview.md)」
- [Update Management の概要](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Azure ネイティブ サービスの統合とデプロイ

### <a name="enable-azure-update-management"></a>Azure Update Management を有効にする

Azure Automation の Azure Update Management は、ハイブリッド環境の Windows マシンと Linux マシンのオペレーティング システムの更新プログラムを管理します。 パッチ適用のコンプライアンス状況を監視し、パッチ適用の逸脱を修復するためにアラートを Azure Monitor に転送します。 Azure Update Management は、保存されたデータを使用して VM 上の更新の状態を評価するために、Log Analytics ワークスペースに接続される必要があります。

1.  Azure Update Management に Log Analytics を追加するには、まず [Azure Automation アカウントを作成する](../automation/automation-create-standalone-account.md)必要があります。 テンプレートを使用してアカウントを作成したい場合は、「[Azure Resource Manager テンプレートを使用して Automation アカウントを作成する](../automation/quickstart-create-automation-account-template.md)」を参照してください。

2. **Log Analytics ワークスペース** により、Log Analytics エージェントまたは拡張機能を使用して、ログ収集およびパフォーマンス カウンターの収集が可能になります。 Log Analytics ワークスペースを作成するには、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」を参照してください。 必要に応じて、[CLI](../azure-monitor/logs/quick-create-workspace-cli.md)、[PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md)、または [Azure Resource Manager テンプレート](../azure-monitor/logs/resource-manager-workspace.md)を使用してワークスペースを作成することもできます。

3. VM に対して Azure Update Management を有効にするには、「[Automation アカウントから Update Management を有効にする](../automation/update-management/enable-from-automation-account.md)」を参照してください。 このプロセスでは、Log Analytics ワークスペースを Automation アカウントにリンクします。 
 
4. Azure Update Management に VM を追加したら、[VM に更新プログラムをデプロイして結果を確認する](../automation/update-management/deploy-updates.md)ことができます。 

### <a name="enable-azure-security-center"></a>Azure Security Center を有効にする

Azure Security Center は、クラウドとオンプレミスのハイブリッド ワークロード全体に対して高度な脅威保護を提供します。 Azure VMware Solution VM の脆弱性を評価し、必要に応じてアラートを発生させます。 このセキュリティ アラートは、解決のために Azure Monitor に転送できます。

Azure Security Center はデプロイを必要としません。 詳細については、[仮想マシンでサポートされている機能](../security-center/security-center-services.md)の一覧に関するページを参照してください。

1. Azure VMware Solution VM と Azure 以外の VM を Security Center に追加するには、「[クイック スタート:Azure Security Center を設定する](../security-center/security-center-get-started.md)」を参照してください。 

2. Azure VMware Solution VM または Azure 以外の環境の VM を追加した後、Security Center で Azure Defender を有効にします。 Security Center は、潜在的なセキュリティの問題について VM を評価します。 また、[概要] タブへの推奨事項の表示も行います。詳細については、「[Azure Security Center でのセキュリティの推奨事項](../security-center/security-center-recommendations.md)」を参照してください。

3. Azure Security Center では、セキュリティ ポリシーを定義できます。 セキュリティ ポリシーの構成の詳細については、「[セキュリティ ポリシーの操作](../security-center/tutorial-security-policy.md)」を参照してください。

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーへの VM のオンボード

Azure Arc によって、Azure VMware Solution、オンプレミス、またはその他のクラウド プラットフォームを含む、あらゆるインフラストラクチャに Azure の管理が拡張されます。

- 複数の Windows または Linux の VM に対して Azure Arc 対応サーバーを有効にすることの詳細については、「[ハイブリッド マシンを大規模に Azure に接続する](../azure-arc/servers/onboard-service-principal.md)」を参照してください。

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Arc 対応 Kubernetes を使用したハイブリッド Kubernetes クラスターのオンボード

Azure Arc 対応 Kubernetes を使用して、Azure VMware Solution 環境でホストされている Kubernetes クラスターをアタッチできます。 

- 詳細については、「[Azure Arc 対応オンボード サービス プリンシパルの作成](../azure-arc/kubernetes/create-onboarding-service-principal.md)」を参照してください。

### <a name="deploy-the-log-analytics-agent"></a>Log Analytics エージェントのデプロイ

Azure VMware Solution VM は、Log Analytics エージェント (Microsoft Monitoring Agent (MMA) または OMS Linux エージェントとも呼ばれます) を使用して監視できます。 既に、Azure Automation Update Management を有効にして、Log Analytics ワークスペースを作成しています。

- [Azure Arc 対応サーバー VM 拡張機能のサポート](../azure-arc/servers/manage-vm-extensions.md)を使用して、Log Analytics エージェントをデプロイします。

### <a name="enable-azure-monitor"></a>Azure Monitor を有効にする

Azure Monitor は、クラウドおよびオンプレミス環境からのテレメトリを収集、分析して対応する、包括的なソリューションです。 デプロイは必要ありません。 Azure Monitor を使用すると、ゲスト オペレーティング システムのパフォーマンスを監視し、Azure VMware Solution またはオンプレミスの VM のアプリケーションの依存関係を検出してマップすることができます。

- Azure Monitor を使用すると、さまざまなソースからデータを収集して、監視および分析できます。 詳細については、「[Azure Monitor で使用する監視データのソース](../azure-monitor/agents/data-sources.md)」を参照してください。

- 分析や視覚化、アラート用に、さまざまな種類のデータを収集します。 詳細については、「[Azure Monitor データ プラットフォーム](../azure-monitor/data-platform.md)」を参照してください。

- Log Analytics ワークスペースで Azure Monitor を構成するには、「[Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する](../azure-monitor/vm/vminsights-configure-workspace.md)」を参照してください。

- アラート ルールを作成することにより、高いリソースの使用率や不足しているパッチ、ディスク容量の不足、VM のハートビートなどの、環境内の問題を特定できます。 また、IT Service Management (ITSM) ツールにアラートを送信することで、検出されたイベントに対する自動応答を設定することもできます。 アラート検出の通知は、電子メールでも送信できます。 こうしたルールを作成するには、以下を参照してください。
    - [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-metric.md)。
    - [Azure Monitor を使用してログ アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-log.md)。
    - 自動アクションと通知を設定するための[アクション ルール](../azure-monitor/alerts/alerts-action-rules.md)。
    - [IT Service Management Connector を使用して Azure を ITSM ツールに接続する](../azure-monitor/alerts/itsmc-overview.md)。
