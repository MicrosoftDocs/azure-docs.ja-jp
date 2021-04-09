---
title: Azure Kubernetes Service 用の Azure セキュリティ ベースライン
description: Azure Kubernetes Service セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8fd058c88e11764a538f67fe49aba267e706bf11
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951838"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure Kubernetes Service 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Azure Kubernetes に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、**セキュリティ コントロール** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Kubernetes に適用できる関連ガイダンスによって定義されています。 Azure Kubernetes に適用できない **コントロール** は、除外されています。

 
Azure Kubernetes を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Kubernetes セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:既定では、Microsoft Azure Kubernetes Service (AKS) クラスターを作成すると、ネットワーク セキュリティ グループとルート テーブルが自動的に作成されます。 ロード バランサー、ポート マッピング、またはイングレス ルートでサービスを作成すると、トラフィック フローが適切になるように AKS によってネットワーク セキュリティ グループが自動的に変更されます。 ネットワーク セキュリティ グループは顧客ノードの仮想 NIC と自動的に関連付けられ、ルート テーブルは仮想ネットワーク上のサブネットと自動的に関連付けられます。 

AKS ネットワーク ポリシーを使用してネットワーク トラフィックを制限するには、名前空間とラベル セレクターの選択に基づいて、クラスター内の Linux ポッド間のイングレスおよびエグレス トラフィックの規則を定義します。 ネットワーク ポリシーを使用するには、Azure CNI プラグインを使用し、仮想ネットワークとサブネットを定義する必要があります。また、ネットワーク ポリシーを有効にできるのはクラスターの作成時だけです。 既存の AKS クラスターにはデプロイできません。

プライベート AKS クラスターを実装すると、AKS API サーバーとノード プールの間のネットワーク トラフィックがプライベート ネットワークにのみ保持されます。 コントロール プレーンまたは API サーバーは AKS マネージド Azure サブスクリプションに存在し、内部 (RFC1918) IP アドレスを使用しますが、顧客のクラスターまたはノード プールは独自のサブスクリプションに存在します。 サーバーとクラスターまたはノード プールは、API サーバー仮想ネットワーク内の Azure Private Link サービスと、顧客の AKS クラスターのサブネットで公開されているプライベート エンドポイントを使用して相互に通信します。  または、AKS API サーバーのパブリック エンドポイントを使用しながら、AKS API サーバーの許可された IP 範囲の機能によってアクセスを制限します。 

- [Azure Kubernetes Service (AKS) でのアプリケーションとクラスターに対するセキュリティの概念](concepts-security.md)

- [Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護](use-network-policies.md)

- [プライベート Azure Kubernetes Service クラスターを作成する](private-clusters.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**:Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure Kubernetes Service (AKS) で使用されているネットワーク リソースを保護します。 

ネットワーク セキュリティ グループ フロー ログを有効にし、監査のためにログを Azure Storage アカウントに送信します。 Log Analytics ワークスペースにフロー ログを送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フロー パターンに関する分析情報を得ることで、ネットワーク アクティビティを視覚化したり、ホット スポットやセキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの構成の誤りを特定したりすることもできます。

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [ネットワーク セキュリティのフロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:Web アプリケーションへの着信トラフィックをフィルター処理して追加のセキュリティ層を提供するには、Azure Application Gateway に対応する Web アプリケーション ファイアウォール (WAF) を AKS クラスターの前面に使用します。 Azure WAF では、このトラフィックに対するクロス サイト スクリプティングや cookie ポイズニングなどの攻撃に対して、Open Web Application Security Project (OWASP) によって提供される一連の規則が使用されます。 

AKS 環境で使用される API の認証、承認、調整、キャッシュ、変換、監視のために API ゲートウェイを使用します。 API ゲートウェイは、マイクロサービスへのフロント ドアとして機能し、マイクロサービスからクライアントを分離し、横断的な問題を処理する負担を排除することでマイクロサービスの複雑さを軽減します。

- [AKS でのネットワーク接続とセキュリティに関するベスト プラクティスを理解する](operator-best-practices-network.md)

- [Application Gateway イングレス コントローラー](../application-gateway/ingress-controller-overview.md)

- [Azure Kubernetes Service にデプロイされたマイクロサービスで Azure API Management を使用する](../api-management/api-management-kubernetes.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:分散型サービス拒否 (DDoS) 攻撃から保護するには、Azure Kubernetes Service (AKS) コンポーネントをデプロイする仮想ネットワークで Microsoft DDoS Standard 保護を有効にします。

既定では、AKS 内のポッド間ですべてのトラフィックが許可されるため、これらのポッド間のトラフィック フローを制御するために、ネットワーク ポリシー エンジンをインストールし、Kubernetes ネットワーク ポリシーを作成します。 ネットワーク ポリシーは、AKS の Linux ベースのノードとポッドに対してのみ使用する必要があります。 セキュリティを強化するには、ポッドの通信を制限する規則を定義します。 

割り当てられたラベル、名前空間、トラフィック ポートなどの設定に基づいて、トラフィックを許可するか拒否するかを選択します。 ポッドは AKS クラスター内で動的に作成されるため、必要なネットワーク ポリシーを自動的に適用できます。 

- [Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護](use-network-policies.md)

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:異常なアクティビティを調査するために必要な場合は、Network Watcher パケット キャプチャを使用します。 

サブスクリプションで仮想ネットワークを作成または更新すると、お使いの仮想ネットワークのリージョンで Network Watcher が自動的に有効になります。 PowerShell、Azure CLI、REST API、または Azure Resource Manager Client メソッドを使用して、Network Watcher の新しいインスタンスを作成することもできます

- [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**:Web アプリケーション ファイアウォール (WAF) で Azure Application Gateway を有効にした Azure Kubernetes Service (AKS) クラスターを保護します。 

ペイロード検査または動作分析に基づく侵入検出や侵入防止が必須でない場合は、WAF による Azure Application Gateway を、アラートと脅威をログに記録する「検出モード」、または検出された侵入や攻撃を積極的にブロックする「防止モード」で使用および構成できます。

- [WAF で AKS クラスターを保護するためのベスト プラクティスを理解する](./operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [Application Gateway (Azure WAF) をデプロイする方法](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:Azure Kubernetes Service (AKS) インスタンスに関連付けられているネットワーク セキュリティ グループに対するネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則の作成時に特定の IP アドレスの代わりにサービス タグを使用すると、サービス タグ名を指定することで対応するサービスのトラフィックを許可または拒否できます。 

サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

AKS クラスター内のノード プールに Azure タグを適用します。 これらは、仮想ネットワーク サービス タグとは異なり、ノード プール内の各ノードに適用され、アップグレード後も維持されます。 

- [サービス タグとその使用方法を理解する](../virtual-network/service-tags-overview.md)

- [AKS の NSG を理解する](support-policies.md)

- [Azure Kubernetes Service (AKS) でクラスター ノードに対するエグレス トラフィックを制御する](limit-egress-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure Policy を使用して、Azure Kubernetes Service (AKS) クラスターに関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 

AKS クラスターのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ContainerService" および "Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。 

また、AKS に関連する次のような組み込みのポリシー定義も使用します。

- Kubernetes Services では、許可する IP の範囲を定義する必要がある

- Kubernetes クラスターで HTTPS イングレスを強制する

- サービスが Kubernetes クラスター内の許可されたポートでのみリッスンするようにする

追加情報については、参照先のリンクをご覧ください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](../governance/policy/samples/built-in-policies.md#network)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Azure Kubernetes Service (AKS) クラスター間のトラフィック フローに対応するネットワーク セキュリティ グループやその他のリソースにタグを使用します。 個々のネットワーク セキュリティ グループ規則の [説明] フィールドを使用して、ネットワークとの間のトラフィックを許可するすべての規則のビジネス ニーズや期間などを指定します。

Azure Policy のタグ付けに関連したいずれかの組み込み定義 ("タグとその値が必要" など) を使用して、すべてのリソースがタグ付きで作成され、既存のタグ付けされていないリソースに関する通知が受信されるようにします。

ネットワーク ポリシーを使用して、名前空間とラベル セレクターに基づいてクラスター内の特定のネットワーク パスを許可するか拒否するかを選択します。 トラフィック構成規則の記述子として、これらの名前空間とラベルを使用します。 Azure PowerShell または Azure コマンド ライン インターフェイス (CLI) を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりします。

- [CLI での Azure Policy](/cli/azure/policy)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Kubernetes Service (AKS) クラスターに関連したネットワーク リソースの変更を検出します。 

重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。 アクティビティ ログの AzureContainerService ユーザーからのエントリは、プラットフォーム アクションとしてログに記録されます。 

Azure Monitor ログを使用して、AKS マスター コンポーネントである kube-apiserver と kube-controller-manager からのログを有効にして、照会します。 kubelet とコンテナー ランタイムを実行するノードを作成して管理し、マネージド Kubernetes API サーバーを通じてアプリケーションをデプロイします。 

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Kubernetes Service (AKS) での Kubernetes マスター ノード ログの有効化とレビュー](./view-control-plane-logs.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Azure Kubernetes Service (AKS) ノードでは、UDP ポート 123 およびネットワーク タイム プロトコル (NTP) と共に ntp.ubuntu.com を使用して時刻を同期します。 

カスタム DNS サーバーを使用している場合は、クラスター ノードから NTP サーバーにアクセスできることを確認してください。 

- [AKS クラスター ノードの NTP ドメインおよびポートの要件を理解する](limit-egress-traffic.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:マネージド サービスとして提供される Azure Kubernetes Service (AKS) マスター コンポーネントである kube-apiserver と kube-controller-manager からの監査ログを有効にします。 

- kube-auditaksService: コントロール プレーン操作に対する監査ログの表示名 (hcpService から) 

- masterclient: az aks get-credentials から取得する証明書 MasterClientCertificate に対する監査ログの表示名 

- nodeclient: エージェント ノードによって使用される ClientCertificate に対する表示名

kube-audit などの他の監査ログも有効にします。 

これらのログを Log Analytics または別のストレージ プラットフォームにエクスポートします。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

組織のビジネス要件に基づいて、Azure Sentinel またはサードパーティの SIEM に対してこのデータを有効にしてオンボードします。

- [ログのロールを含むログ スキーマを確認する](./view-control-plane-logs.md)

- [Azure Monitor for Containers を理解する](../azure-monitor/containers/container-insights-overview.md)

- [Azure Monitor for Containers を有効にする方法](../azure-monitor/containers/container-insights-onboard.md)

- [Azure Kubernetes Service (AKS) での Kubernetes マスター ノード ログの有効化とレビュー](./view-control-plane-logs.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:アクティビティ ログを使用して Azure Kubernetes Service (AKS) リソースに対するアクションを監視し、すべてのアクティビティとそれらの状態を表示します。 アクティビティ ログを使用して、サブスクリプション内のリソースに対して実行された操作を特定します。 

- 操作を開始したユーザー
- 操作が発生した時間
- 操作の状態
- 操作を調査するために役立つ可能性のあるその他のプロパティの値

Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 

次のような AKS マスター コンポーネントの監査ログを有効にします。 

- kube-auditaksService: コントロール プレーン操作に対する監査ログの表示名 (hcpService から) 

- masterclient: az aks get-credentials から取得する証明書 MasterClientCertificate に対する監査ログの表示名 

- nodeclient: エージェント ノードによって使用される ClientCertificate に対する表示名

kube-audit などの他の監査ログもオンにします。 

- [AKS で Kubernetes マスター ノード ログを有効にして確認する方法](./view-control-plane-logs.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**:AKS クラスター ノードからデータを収集するための Log Analytics エージェントの自動インストールを有効にします。 また、既定ではオフになっている Azure Security Center からの Log Analytics 監視エージェントの自動プロビジョニングを有効にします。 エージェントを手動でインストールすることもできます。 自動プロビジョニングがオンの場合、Security Center では、サポートされているすべての Azure VM と新しく作成される VM に Log Analytics エージェントをデプロイします。 Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM)、仮想マシン スケール セット、IaaS コンテナー (Kubernetes クラスター ノードなど) からデータを収集します。 データは、Azure Log Analytics エージェントを使用して収集されます。このエージェントは、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。 

不足している更新プログラム、OS のセキュリティ設定ミス、エンドポイント保護のステータス、正常性と脅威の検出を可視化するためには、データ収集が欠かせません。

- [Log Analytics エージェントの自動プロビジョニングを有効にする方法](../security-center/security-center-enable-data-collection.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Kubernetes Service (AKS) インスタンスを Azure Monitor にオンボードし、対応する Azure Log Analytics ワークスペースの保持期間を組織のコンプライアンス要件に従って設定します。 

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Azure Kubernetes Service (AKS) インスタンスを Azure Monitor にオンボードし、クラスターの診断設定を構成します。 

Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。 Azure Monitor ログは、Azure portal で、または CLI を通して有効にされ、管理されます。Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC) や Azure RBAC に対応する AKS クラスターと、RBAC に対応していない AKS クラスターの両方で動作します。

AKS マスター コンポーネントによって生成されたログを表示して、アプリケーションやサービスのトラブルシューティングを行います。 データを有効にして、Azure Sentinel またはサードパーティの SIEM にオンボードすると、ログの一元的な管理と監視が可能になります。

- [AKS で Kubernetes マスター ノード ログを有効にして確認する方法](./view-control-plane-logs.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/logs/get-started-queries.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:AKS ノードをより詳しく把握するには、Azure Kubernetes Service (AKS) を Security Center と組み合わせて使用します。 

ホストおよびクラスター レベルで検出された脅威や悪意のあるアクティビティに関する Security Center アラートを確認します。 Security Center には、ネットワーク データ、プロセス作成、Kubernetes 監査ログなど、AKS クラスターで発生する未加工のセキュリティ イベントの継続的な分析が実装されています。 このアクティビティが予期される動作であるかどうか、またはアプリケーションが誤動作しているかどうかを判断します。 Azure Monitor でメトリックとログを使用して、調査結果を実証します。 

- [Azure Kubernetes Service と Security Center の統合を理解する](../security-center/defender-for-kubernetes-introduction.md)

- [Azure Security Center Standard レベルを有効にする方法](../security-center/security-center-get-started.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:Azure 向けの Microsoft マルウェア対策を Azure Kubernetes Service (AKS) 仮想マシンと仮想マシン スケール セット ノードにインストールして有効にします。 Security Center でアラートを確認して修復します。

- [Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](../security/fundamentals/antimalware.md)

- [セキュリティ アラートのリファレンス ガイド](../security-center/alerts-reference.md)

- [コンテナーのアラート - Azure Kubernetes Service クラスター](../security-center/alerts-reference.md#alerts-akscluster)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**:Azure Kubernetes Service (AKS) では、クラスター DNS の管理と解決に CoreDNS プロジェクトが使用されます。

DNS クエリのログ記録を有効にするには、記載されている構成を coredns-custom ConfigMap に適用します。 

- [Azure Kubernetes Service で CoreDNS をカスタマイズする](coredns-custom.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**:Azure Kubernetes Service (AKS) で kubectl コマンドライン クライアントを使用して、Kubernetes クラスターを管理し、トラブルシューティングのために AKS ノードからログを取得します。 Azure Cloud Shell を使用している場合、kubectl は既にインストールされています。 kubectl をローカルにインストールするには、'Install-AzAksKubectl' コマンドレットを使用します。

- [クイックスタート - PowerShell を使用して Azure Kubernetes Service クラスターをデプロイする](kubernetes-walkthrough-powershell.md)

- [Azure Kubernetes Service (AKS) クラスター ノードから kubelet ログを取得する](kubelet-logs.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure Kubernetes Service (AKS) 自体には、通常のユーザー アカウントとパスワードを格納する ID 管理ソリューションはありません。 Azure Active Directory (Azure AD) 統合を使用すると、名前空間内またはクラスター間での Kubernetes リソースへのアクセス権をユーザーまたはグループに付与できます。

Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、AKS 管理グループのメンバーであるアカウントを検出します

Azure CLI を使用して「マネージド Kubernetes クラスターのアクセス資格情報の取得」などの操作を行うと、アクセスを定期的に調整しやすくなります。 このプロセスを実装して、AKS のもう 1 つのプライマリ ユーザー タイプであるサービス アカウントの更新されたインベントリを保持します。 Security Center の ID とアクセス管理の推奨事項を適用します。

- [AKS と Azure AD を統合する方法](azure-ad-integration-cli.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:Azure Kubernetes Service (AKS) には、一般的な既定のパスワードという概念がなく、通常のユーザー アカウントとパスワードを格納できる ID 管理ソリューションはありません。 Azure Active Directory (Azure AD) 統合を使用すると、名前空間内またはクラスター間での AKS リソースへのロールベースのアクセス権を付与できます。 

Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、AKS 管理グループのメンバーであるアカウントを検出します

- [AKS のアクセスと ID のオプションを理解する](concepts-identity.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**:Azure Kubernetes Service (AKS) クラスターのユーザー認証を Azure Active Directory (Azure AD) に統合します。 Azure AD 認証トークンを使って AKS クラスターにサインインします。 Kubernetes の構成 (kubeconfig) 情報とアクセス許可、名前空間、クラスター リソースへの管理者アクセスに対して、Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC) を構成します。 

専用の管理者アカウントの使用に関するポリシーと手順を作成します。 Security Center の ID とアクセス管理の推奨事項を実装します。

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

- [クラスター リソースへのアクセスを制御する](azure-ad-rbac.md)

- [Azure ロールベースのアクセス制御を使用する](control-kubeconfig-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:AKS クラスターの Azure Active Directory (Azure AD) 統合認証と共に Azure Kubernetes Service (AKS) のシングル サインオンを使用します。

- [Kubernetes ログ、イベント、ポッド メトリックをリアルタイムで表示する方法](../azure-monitor/containers/container-insights-livedata-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Kubernetes Service (AKS) の認証を Azure Active Directory (Azure AD) に統合します。 

Azure AD の多要素認証を有効にし、Security Center の ID とアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:多要素認証 (MFA) が構成された特権アクセス ワークステーション (PAW) を使用して、指定された Azure Kubernetes Service (AKS) クラスターと関連リソースにログインします。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証 (MFA) を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Azure Kubernetes Service (AKS) の Azure AD 統合認証と共に Azure Active Directory (Azure AD) のセキュリティ レポートを使用します。 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、アラートを生成できます。 Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**:条件付きアクセスのネームド ロケーションを使用して、IP アドレスの範囲、国、またはリージョンの特定の論理グループからのみ Azure Kubernetes Service (AKS) クラスターへのアクセスを許可します。 そのためには、AKS の認証を Azure Active Directory (Azure AD) と統合する必要があります。

AKS API サーバーは、リソースの作成やノードの数のスケーリングなどのクラスター内のアクションを実行するための要求を受信するため、API サーバーへのアクセスを限られた IP アドレスの範囲からに制限します。 

- [Azure Kubernetes Service (AKS) で許可された IP アドレス範囲を使用して API サーバーへのアクセスをセキュリティで保護する](api-server-authorized-ip-ranges.md)

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Kubernetes Service (AKS) の中央認証および承認システムとして Azure Active Directory (Azure AD) を使用します。 Azure AD では、データの保存時と転送時に強力な暗号化を使用してデータが保護され、ユーザーの資格情報がソルト化およびハッシュされ、安全に保存されます。

Azure ロールベースのアクセス制御 (RBAC) と共に、AKS の組み込みロールであるリソース ポリシー共同作成者と所有者を使用して、Kubernetes クラスターへのポリシー割り当て操作を行います

- [Azure Policy の概要](../governance/policy/overview.md)

- [Azure AD と AKS を統合する方法](azure-ad-integration-cli.md)

- [AKS マネージド Azure AD を統合する](managed-aad.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Kubernetes Service (AKS) の Azure AD 統合認証と共に Azure Active Directory (Azure AD) のセキュリティ レポートを使用します。 Azure AD ログを検索して古いアカウントを検出します。 

Azure ID アクセス レビューを実行して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理します。 Security Center の ID とアクセスの推奨事項を修復します。

サポートまたはトラブルシューティングの目的で使用されるロールに注意してください。 たとえば、Microsoft サポートが (ユーザーの同意を得て) 実行するクラスター アクションは、aks-support-rolebinding と言う名前の Kubernetes の組み込み "edit" ロールに基づいて行われます。 このロールでは、クラスターの構成とリソースを編集してクラスターに関する問題のトラブルシューティングと診断を行うために、AKS のサポートが有効になっています。 ただし、このロールでは、アクセス許可を変更したり、ロールまたはロール バインドを作成したりすることはできません。 このロールによるアクセスは、アクティブなサポート チケットで Just-In-Time (JIT) アクセスを使用する場合にのみ有効になります。
 
- [Azure Kubernetes Service (AKS) でのアクセスと ID オプション](concepts-identity.md)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Kubernetes Service (AKS) のユーザー認証を Azure Active Directory (Azure AD) に統合します。 Azure AD の診断設定を作成し、監査ログとサインイン ログを Azure Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で、必要なアラート (非アクティブ化されたアカウントがログインしようとした場合など) を構成します。
- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Monitor を使用してログ アラートを作成、表示、管理する方法](../azure-monitor/alerts/alerts-log.md)。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**:Azure Kubernetes Service (AKS) のユーザー認証を Azure Active Directory (Azure AD) に統合します。 ユーザー ID に関連する検出された疑わしいアクションへの自動応答を構成するには、Azure AD のリスク検出および Identity Protection 機能を使用します。 ビジネス ニーズに基づいてさらに詳しく調査するために、Azure Sentinel にデータを取り込みます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:Azure Kubernetes Service (AKS) デプロイに関連するリソースに対してタグを使用すると、機密情報を格納または処理する Azure リソースを追跡しやすくなります。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [マネージド クラスターのタグを更新する](/rest/api/aks/managedclusters/updatetags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:Azure Kubernetes Service (AKS) で同じクラスター内のチームとワークロードを論理的に分離して、各チームで必要なリソースをスコープとする最小限の数の特権を提供します。 

Kubernetes の名前空間を使用して、論理的な分離境界を作成します。 スケジュール設定、ネットワーク、認証/承認、コンテナーなど、分離とマルチテナントに関する Kubernetes の追加機能の実装を検討してください。

開発、テスト、運用環境で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 適切にタグ付けされた個別の仮想ネットワークに AKS クラスターをデプロイすることにより、ネットワークで AKS クラスターを分離します。

- [AKS でのクラスター分離に関するベスト プラクティスについて学ぶ](operator-best-practices-cluster-isolation.md)

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [AKS でのネットワーク接続とセキュリティに関するベスト プラクティスを理解する](operator-best-practices-network.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする、Azure Marketplace からのサードパーティ ソリューションをネットワーク境界で使用します。

Microsoft では、基になるプラットフォームを管理し、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くしています。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [AKS の機能に必要なポート、アドレス、ドメイン名の一覧](limit-egress-traffic.md)

- [Azure Firewall の診断設定を構成する方法](../firewall/firewall-diagnostics.md)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:Azure Kubernetes Service (AKS) デプロイで、HTTPS イングレス コントローラーを作成し、独自の TLS 証明書 (または必要に応じて Let's Encrypt) を使用します。 

Kubernetes エグレス トラフィックは、既定では HTTPS/TLS で暗号化されます。 追加の監視として、暗号化されていない可能性がある AKS インスタンスからのエグレス トラフィックを確認します。 場合によっては、更新プログラムを取得するための NTP トラフィック、DNS トラフィック、HTTP トラフィックがこれに含まれます。 

- [AKS で HTTPS イングレス コントローラーを作成し、独自の TLS 証明書を使用する方法](ingress-own-tls.md)

- [AKS で Let's Encrypt を使用して HTTPS イングレス コントローラーを作成する方法](ingress-tls.md)

- [AKS で使用される可能性がある送信ポートとプロトコル](limit-egress-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。
Microsoft では、基になるプラットフォームを管理し、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くしています。 

Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure Resource Manager 上に構築された Azure ロールベースのアクセス制御 (Azure RBAC) 認証システムを使用して、Azure リソースのきめ細かいアクセス管理を行います。

ユーザー認証に Azure Active Directory (Azure AD) を使うように Azure Kubernetes Service (AKS) を構成します。 この構成を使用する Azure AD 認証トークンを使って AKS クラスターにサインインします。 

Azure RBAC と共に、AKS の組み込みロールであるリソース ポリシー共同作成者と所有者を使用して、AKS クラスターへのポリシー割り当て操作を行います

- [AKS で Azure RBAC と Azure AD の ID を使用してクラスター リソースへのアクセスを制御する方法](azure-ad-rbac.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。
Microsoft では、基になるプラットフォームを管理し、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くしています。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Azure Kubernetes Service (AKS) のボリュームに対して提供される 2 つの主な種類のストレージが、Azure ディスクまたは Azure Files でサポートされます。 どちらの種類のストレージでも、Azure Storage Service Encryption (SSE) が使用され、セキュリティを強化するために保存データが暗号化されます。 規定では、データは Microsoft のマネージド キーで暗号化されます。

暗号化キーの制御を強化するため、カスタマー マネージド キーを使用した保存時の暗号化によって AKS クラスターの OS ディスクとデータ ディスクの両方を暗号化できます。 お客様は、キーのバックアップやローテーションなどのキー管理アクティビティに対して責任を負います。 ディスクは現在、AKS ノード レベルで Azure Disk Encryption を使用して暗号化することはできません。

- [Azure Kubernetes Services (AKS) のストレージとバックアップに関するベスト プラクティス](operator-best-practices-storage.md)

- [Azure Kubernetes Service (AKS) の Azure ディスクに独自のキー (BYOK) を使用する](azure-disk-customer-managed-keys.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure Monitor for Containers を使用して、Azure Kubernetes Service (AKS) でホストされるマネージド Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスを監視します。 

ノードまたはコンテナーの CPU およびメモリの使用率が定義されたしきい値を超えた場合や、インフラストラクチャまたはノードの正常性ロールアップでクラスターの正常性状態の変更が発生した場合に、事前通知またはログ作成を行うようにアラートを構成します。 

Azure アクティビティ ログを使用して、AKS クラスターと関連リソースを概要レベルで監視します。 Prometheus と統合して、クエリを使用してノードと Kubernetes から収集したアプリケーションとワークロードのメトリックを表示し、カスタム アラート、ダッシュボード、詳細な分析を作成します。

- [Azure Monitor for Containers を理解する](../azure-monitor/containers/container-insights-overview.md)

- [Azure Monitor for containers を有効にする方法](../azure-monitor/containers/container-insights-onboard.md)

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:Security Center を使用して、Azure Kubernetes Service (AKS) インスタンスを含む Azure Container Registry の脆弱性を監視します。 Security Center でコンテナー レジストリ バンドルを有効にして、レジストリにプッシュされたイメージを Security Center でスキャンできるようにします。

Security Center で Qualys を使用してイメージをスキャンした後で問題が見つかると、Security Center ダッシュボードで通知されます。 コンテナー レジストリ バンドル機能を使用すると、Azure Resource Manager ベースのレジストリで使用されているイメージの脆弱性をより詳しく把握できます。 

Security Center を使用して、すべての脆弱性に関する実行可能な推奨事項を提示します。 これらの推奨事項には、重要度の分類と修復に関するガイダンスが含まれます。 

- [Azure Kubernetes サービス (AKS) でのコンテナー イメージの管理とセキュリティに関するベスト プラクティス](../security-center/defender-for-container-registries-introduction.md)

- [AKS でのコンテナー イメージの管理とセキュリティに関するベスト プラクティスを理解する](operator-best-practices-container-image-management.md)

- [コンテナー レジストリと Azure Security Center の統合を理解する](../security-center/defender-for-container-registries-introduction.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**:顧客の Azure Kubernetes Service (AKS) クラスターを保護するために、Linux ノードにセキュリティ更新プログラムが自動的に適用されます。 これらの更新プログラムには、OS のセキュリティ修正プログラムやカーネルの更新プログラムが含まれています。 

Windows Server ノードは毎日の更新プログラムを受信しないため、Windows Server ノードを最新の状態に保つプロセスは Linux を実行しているノードと異なることに注意してください。 代わりに、お客様は AKS クラスター内の Windows Server ノード プールでアップグレードを実行する必要があります。このアップグレードでは、最新のベース Windows Server イメージと修正プログラムが適用された新しいノードが、Azure コントロール パネルまたは Azure CLI を使用してデプロイされます。 これらの更新プログラムには、AKS に対するセキュリティまたは機能の強化が含まれています。

- [Linux を実行する AKS クラスター ノードに更新プログラムが適用されるしくみを理解する](node-updates-kured.md)

- [Windows Server ノードを使用する AKS クラスターの AKS ノード プールをアップグレードする方法](./use-multiple-node-pools.md#upgrade-a-node-pool)

- [Azure Kubernetes Service (AKS) ノード イメージのアップグレード](node-image-upgrade.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**:手動のプロセスを実装して、Azure Kubernetes Service (AKS) クラスター ノードのサードパーティ アプリケーションがクラスターの有効期間を通して修正プログラムが適用された状態に維持されるようにします。 これには、自動更新の有効化、ノードの監視、または定期的な再起動の実行が必要になる場合があります。

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**:Security Center のスキャン結果を一定の間隔でエクスポートして結果を比較し、脆弱性が修復されていることを確認します。

PowerShell コマンドレット "Get-AzSecurityTask" を使用して、セキュリティ体制を強化し、脆弱性スキャンの結果を修復するために実行することが推奨される Security Center のセキュリティ タスクを自動的に取得します。

- [PowerShell を使用して Azure Security Center で検出された脆弱性を表示する方法](/powershell/module/az.security/get-azsecuritytask)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:Security Center によって提供される重要度評価を使用して、脆弱性の修復に優先順位を付けます。 

組み込みの脆弱性評価ツール (Azure によって提供される Qualys や Rapid7 など) を使用する場合は、共通脆弱性評価システム (CVSS) (またはスキャン ツールによって提供される別の評価システム) を使用します。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワークなど) のクエリまたは検出を実行するには、Azure Resource Graph を使用します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager ベースのリソースを作成して使用することを強くお勧めします。

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを使用して Azure リソースにタグを適用し、それらを各分類に論理的に整理します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 

Azure Kubernetes Service (AKS) ノード プールの作成時に、テイント、ラベル、またはタグを適用します。 そのノード プール内のすべてのノードも、そのテイント、ラベル、タグを継承します。

テイント、ラベル、タグを使用すると、定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで確実に削除されるようにすることができます。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [マネージド クラスター - タグを更新する](/rest/api/aks/managedclusters/updatetags)

- [テイント、ラベル、またはタグをノード プールに指定する](./use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:組織のビジネス ニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアの一覧を定義します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
-   許可されないリソースの種類 

-   許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 組織のビジネス要件に応じて、環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:Azure Automation の変更履歴とインベントリ機能を使用して、環境にインストールされているソフトウェアを確認します。 

コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集して表示し、承認されていないソフトウェア アプリケーションを監視します。 

マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

- [Azure 仮想マシンのインベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:Azure Automation の変更履歴とインベントリ機能を使用して、環境にインストールされているソフトウェアを確認します。 

コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集して表示し、承認されていないソフトウェア アプリケーションを監視します。 

マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

- [Azure 仮想マシンのインベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

- [ファイルの整合性の監視を使用する方法](../security-center/security-center-file-integrity-monitoring.md)

- [Azure Change Tracking を理解する](../automation/change-tracking/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**:Azure Automation の変更履歴とインベントリ機能を使用して、環境にインストールされているソフトウェアを確認します。 

コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集して表示し、承認されていないソフトウェア アプリケーションを監視します。 

マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

Security Center で、環境に存在するアプリケーションの適応型アプリケーション分析を有効にします。

- [Azure 仮想マシンのインベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**:Azure Policy を使用してサブスクリプション内に作成できるリソースの種類に制限を適用するには、組み込みのポリシー定義を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。
- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12:コンピューティング リソースでスクリプトを実行するユーザーの機能を制限する

**ガイダンス**:Azure Kubernetes Service (AKS) 自体には、通常のユーザー アカウントとパスワードを格納する ID 管理ソリューションはありません。 代わりに、AKS クラスターの場合は、統合された ID ソリューションとして Azure Active Directory (Azure AD) を使用します。

Azure AD 統合を使用して、名前空間内またはクラスター間での Kubernetes リソースへのアクセス権をユーザーまたはグループに付与します。

Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、AKS 管理グループのメンバーであるアカウントを検出し、それを使用してアクセスを定期的に調整します。 Azure CLI を使用して「マネージド Kubernetes クラスターのアクセス資格情報の取得」などの操作を行います。 Security Center の ID とアクセス管理の推奨事項を実装します。

- [Azure CLI で AKS を管理する](/cli/azure/aks)

- [AKS と Azure AD の統合を理解する](concepts-identity.md)

- [AKS と Azure AD を統合する方法](azure-ad-integration-cli.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**:Azure Kubernetes Service (AKS) の機能を使用して、同じクラスター内のチームとワークロードを論理的に分離し、各チームで必要なリソースをスコープとする最小限の数の特権を提供します。 

Kubernetes の名前空間を実装して、論理的な分離境界を作成します。 Azure Kubernetes Service (AKS) インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ContainerService" 名前空間で Azure Policy エイリアスを使用します。 

次を含む分離とマルチテナントに関する Kubernetes の追加機能と考慮事項を確認し、実装します: スケジュール設定、ネットワーク、認証と認可、コンテナー。 また、開発、テスト、運用で別々のサブスクリプションと管理グループを使用します。 個別の仮想ネットワーク (適切にタグ付けされ、Web アプリケーション ファイアウォール (WAF) で保護されたサブネット) を使用して AKS クラスターを分離します。

- [AKS でのクラスター分離に関するベスト プラクティスについて学ぶ](operator-best-practices-cluster-isolation.md)

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [AKS でのネットワーク接続とセキュリティに関するベスト プラクティスを理解する](operator-best-practices-network.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Kubernetes Service (AKS) インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ContainerService" 名前空間で Azure Policy エイリアスを使用します。 組み込みの Azure Policy 定義を使用します。

AKS の組み込みポリシー定義の例を次に示します。

- Kubernetes クラスターで HTTPS イングレスを強制する

- Kubernetes Services では、許可する IP の範囲を定義する必要がある

- Kubernetes Services ではロールベースのアクセス制御 (RBAC) を使用する必要がある

- Kubernetes クラスター内で許可されたコンテナー イメージのみを実行する

Azure Resource Manager を使用して、JavaScript Object Notation (JSON) で AKS 構成のテンプレートをエクスポートします。 これを定期的に確認して、これらの構成が組織のセキュリティ要件を確実に満たすようにします。 Azure リソースの安全な構成基準として、Azure Security Center からの推奨事項を使用します。 

- [AKS ポッドのセキュリティ ポリシーを構成して管理する方法](use-pod-security-policies.md)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**:Azure Kubernetes Clusters (AKS) クラスターは、セキュリティが最適化された OS を実行しているホスト仮想マシンにデプロイされます。 ホスト OS には、攻撃の対象となる領域を減らし、安全な方法でコンテナーをデプロイできるようにするためのセキュリティ強化手順が組み込まれています。 

Azure では、AKS 仮想マシン ホストに対して更新プログラム (セキュリティ更新プログラムを含む) が毎日適用され、一部の更新プログラムは再起動を必要とします。 お客様は、必要に応じて、AKS 仮想マシン ホストの再起動をスケジュールする必要があります。 

- [AKS エージェント ノード ホスト OS のセキュリティ強化](security-hardened-vm-host-image.md)

- [AKS 仮想マシン ホストでのセキュリティ強化を理解する](security-hardened-vm-host-image.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:ポッド セキュリティ ポリシーを使用して Azure Kubernetes Service (AKS) クラスターを保護します。  クラスターのセキュリティを向上させるには、スケジュールできるポッドを制限します。 

許可されていないリソースを要求するポッドは、AKS クラスターで実行できません。 

また、AKS デプロイに関連した Azure リソース (仮想ネットワーク、サブネット、Azure Firewall、ストレージ アカウントなど) にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] の効果を使用します。 

次の名前空間のエイリアスを使用して、カスタム Azure Policy 定義を作成できます。 

- Microsoft.ContainerService

- Microsoft.Network

追加情報については、参照先のリンクをご覧ください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**:Azure Kubernetes Service (AKS) クラスターは、セキュリティが最適化された OS を実行しているホスト仮想マシンにデプロイされます。 ホスト OS には、攻撃の対象となる領域を減らし、安全な方法でコンテナーをデプロイできるようにするためのセキュリティ強化手順が組み込まれています。 

ホスト OS に組み込まれている Center for Internet Security (CIS) コントロールの一覧を参照してください。  

- [AKS エージェント ノード ホスト OS のセキュリティ強化](security-hardened-vm-host-image.md)

- [AKS クラスターの構成の状態を理解する](./concepts-clusters-workloads.md#control-plane)

- [AKS 仮想マシン ホストでのセキュリティ強化を理解する](security-hardened-vm-host-image.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure Policy 定義を使用する場合は、Azure Repos を使用して構成を安全に格納して管理します。 Azure Resource Manager を使用して、JavaScript Object Notation (JSON) で Azure Kubernetes Service (AKS) 構成のテンプレートをエクスポートします。 これを定期的に確認して、構成が組織のセキュリティ要件を確実に満たすようにします。

Kubernetes クラスターのリソースを宣言する構成ファイルを作成するには、Terraform などのサードパーティ ソリューションを実装します。 セキュリティのベストプラクティスを実装し、構成をセキュリティで保護された場所にコードとして保存することで、AKS のデプロイを強化できます。

- [Kubernetes クラスターを定義する](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [AKS エージェント ノード ホスト OS のセキュリティ強化](security-hardened-vm-host-image.md)

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**:Azure Kubernetes Service (AKS) には適用されません。 AKS では、セキュリティが最適化されたホスト オペレーティング システム (OS) が既定で提供されます。 現在、代替またはカスタムのオペレーティング システムを選択するオプションはありません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、サブスクリプション内に作成できるリソースの種類に制限を適用するには、組み込みのポリシー定義と "Microsoft.ContainerService" 名前空間の Azure Policy エイリアスを使用します。 

カスタム ポリシーを作成して、システム構成を監査し、適用します。 ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [エイリアスを使用する方法](../governance/policy/concepts/definition-structure.md#aliases)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**:Azure Kubernetes Service (AKS) クラスターは、セキュリティが最適化された OS を実行しているホスト仮想マシンにデプロイされます。 ホスト OS には、攻撃の対象となる領域を減らし、安全な方法でコンテナーをデプロイできるようにするためのセキュリティ強化手順が組み込まれています。 

AKS ホストに組み込まれている Center for Internet Security (CIS) コントロールの一覧を参照してください。  

- [AKS エージェント ノード ホスト OS のセキュリティ強化](security-hardened-vm-host-image.md)

- [AKS 仮想マシン ホストでのセキュリティ強化を理解する](security-hardened-vm-host-image.md)

- [AKS クラスターの構成の状態を理解する](./concepts-clusters-workloads.md#control-plane)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:Security Center を使用して、Azure Kubernetes Service (AKS) デプロイに関連するリソースのベースライン スキャンを実行します。 リソースの例として、AKS クラスター自体、AKS クラスターがデプロイされた仮想ネットワーク、Terraform 状態を追跡するために使用される Azure ストレージ アカウント、AKS クラスターの OS とデータ ディスクの暗号化キーに使用されている Azure Key Vault インスタンスなどがあります (ただし、これらに限定されません)。

- [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**:Security Center の「計算とアプリ」セクションに示されたコンテナーの推奨事項を使用して、Azure Kubernetes Service (AKS) クラスターのベースライン スキャンを実行します。 

構成の問題または脆弱性が見つかった場合は、Security Center ダッシュボードで通知されます。 その場合、Security Center でイメージをスキャンできるようにするオプションのコンテナー レジストリ バンドルを有効にする必要があります。  

- [Azure Security Center のコンテナーの推奨事項を理解する](../security-center/container-security.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:FlexVolume ドライブを使用して、Azure Key Vault を Azure Kubernetes Service (AKS) クラスターと統合します。 Azure Key Vault を使用して、資格情報、ストレージ アカウント キー、証明書などのシークレットを格納し、定期的にローテーションします。 FlexVolume ドライバーを使用すると、AKS クラスターは、Key Vault からネイティブに資格情報を取得し、要求元のポッドにのみ安全に提供することができます。 ポッドのマネージド ID を使用して、Key Vault へのアクセスを要求し、FlexVolume ドライバーを介して必要な資格情報を取得します。 Key Vault の論理的な削除を確実に有効にします。 

アプリケーション コードで資格情報を定義しないようにして、資格情報の公開を制限します。 

固定または共有の資格情報を使用しないようにします。 

- [Azure Kubernetes Service (AKS) でのアプリケーションとクラスターに対するセキュリティの概念](concepts-security.md)

- [AKS クラスターで Key Vault を使用する方法](./developer-best-practices-pod-security.md#limit-credential-exposure)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:セキュリティのベスト プラクティスとして、アプリケーション コードで資格情報を定義しないでください。 Azure リソースのマネージド ID を使用して、ポッドが自身をサポートする Azure 内の任意のサービス (Azure Key Vault を含む) に対して自身を認証できるようにします。 ポッドには、Azure Active Directory (Azure AD) に対して認証を行い、デジタル トークンを受信するために、Azure ID が割り当てられます。このデジタル トークンを他の Azure サービスに提示すると、ポッドにそのサービスへのアクセス権が付与されているかどうかが確認され、必要なアクションが実行されます。

ポッドで管理されている ID は、Linux のポッドおよびコンテナー イメージでの使用のみを目的としていることに注意してください。 デジタル キーと資格情報を格納および取得するには、Azure Key Vault をプロビジョニングします。 キー (OS ディスクや AKS クラスターのデータの暗号化に使用されるキーなど) は、Azure Key Vault に格納できます。

AKS クラスターでは、サービス プリンシパルを使用することもできます。 ただし、サービス プリンシパルを使用するクラスターでは、サービス プリンシパルを更新しないと、最終的にそのクラスターの動作を継続できない状態になる可能性があります。 サービス プリンシパルを管理しなければならない場合、複雑さが増すので、代わりにマネージ ID を使用した方が簡単です。 アクセス許可の要件は、サービス プリンシパルにおいてもマネージド ID においても同じです。

- [Azure Kubernetes Service (AKS) で使用するマネージド ID と Key Vault を理解する](./developer-best-practices-pod-security.md#limit-credential-exposure)

- [Azure AD ポッド ID](https://github.com/Azure/aad-pod-identity)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、推奨事項に従って Azure Key Vault などのより安全な場所に移動しやすくなります。

アプリケーション コードで資格情報を定義しないようにして、資格情報の公開を制限します。 また、共有の資格情報を使用しないようにします。 デジタル キーと資格情報を保存および取得するには、Azure Key Vault を使用する必要があります。 その他のリソースへのアクセスをポッドが要求できるようにするには、Azure リソースのマネージド ID を使用します。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [ポッドのセキュリティに関する開発者のベスト プラクティス](developer-best-practices-pod-security.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**:AKS は、ユーザーに代わってエージェント ノードのライフサイクルと操作を管理します。エージェント ノードに関連付けられている IaaS リソースの変更はサポートされていません。 ただし、Linux ノードでは、デーモン セットを使用してマルウェア対策ソリューションなどのカスタム ソフトウェアをインストールできます。

- [セキュリティ アラートのリファレンス ガイド](../security-center/alerts-reference.md)

- [コンテナーのアラート - Azure Kubernetes Service クラスター](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS の共有責任とデーモン セット](./support-policies.md#shared-responsibility)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**:AKS リソースにアップロードされているファイルを事前にスキャンします。 Azure ストレージ アカウントを、データ ストアとして、または AKS クラスターの Terraform 状態を追跡するために使用する場合は、データ サービスに対する Security Center の脅威検出を使用して、ストレージ アカウントにアップロードされたマルウェアを検出します。 

- [データ サービスに対する Azure Security Center の脅威検出について](../security-center/azure-defender.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**:AKS は、ユーザーに代わってエージェント ノードのライフサイクルと操作を管理します。エージェント ノードに関連付けられている IaaS リソースの変更はサポートされていません。 ただし、Linux ノードでは、デーモン セットを使用してマルウェア対策ソリューションなどのカスタム ソフトウェアをインストールできます。

- [セキュリティ アラートのリファレンス ガイド](../security-center/alerts-reference.md)

- [コンテナーのアラート - Azure Kubernetes Service クラスター](../security-center/alerts-reference.md#alerts-akscluster)

- [AKS の共有責任とデーモン セット](./support-policies.md#shared-responsibility)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**:Velero など、ご利用のストレージの種類に適したツールを使ってデータをバックアップします。Velero では、追加のクラスター リソースおよび構成と共に永続ボリュームをバックアップできます。 それらのバックアップの整合性とセキュリティを定期的に確認します。 

バックアップの前に、アプリケーションから状態を削除します。 それができない場合は、永続ボリュームからデータをバックアップし、復元操作を定期的にテストしてデータの整合性と必要なプロセスを確認します。

- [AKS でのストレージとバックアップに関するベスト プラクティス](operator-best-practices-storage.md)

- [AKS での事業継続とディザスター リカバリーに関するベスト プラクティス](operator-best-practices-multi-region.md)

- [Azure Site Recovery を理解する](../site-recovery/site-recovery-overview.md)

- [Azure で Velero を設定する方法](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**:Velero など、ご利用のストレージの種類に適したツールを使ってデータをバックアップします。Velero では、追加のクラスター リソースおよび構成と共に永続ボリュームをバックアップできます。 

PowerShell コマンドを使用して、Key Vault の証明書、キー、マネージド ストレージ アカウント、シークレットの自動バックアップを定期的に行います。 

- [Key Vault の証明書をバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Key Vault のキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Key Vault のマネージド ストレージ アカウントをバックアップする方法](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Key Vault のシークレットをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Azure Backup を有効にする方法](../backup/index.yml)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:Velero バックアップ内のコンテンツのデータ復元を定期的に実行します。 必要に応じて、分離された仮想ネットワークへの復元をテストします。

PowerShell コマンドを使用して、Key Vault の証明書、キー、マネージド ストレージ アカウント、シークレットのデータ復元を定期的に行います。

- [Key Vault の証明書を復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultcertificate?amp;preserve-view=true&view=azps-4.8.0)

- [Key Vault のキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

- [Key Vault のマネージド ストレージ アカウントを復元する方法](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault のシークレットを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultsecret?amp;preserve-view=true&view=azps-4.8.0)

- [Azure 仮想マシンのバックアップからファイルを復旧する方法](../backup/backup-azure-restore-files-from-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**:Velero など、ご利用のストレージの種類に適したツールを使ってデータをバックアップします。Velero では、追加のクラスター リソースおよび構成と共に永続ボリュームをバックアップできます。 

Azure Kubernetes Service (AKS) デプロイで Azure Key Vault が使用されている場合は、Key Vault での論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

- [Azure Storage Service Encryption を理解する](../storage/common/storage-service-encryption.md)

- [Key Vault で論理的な削除を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST のコンピューター セキュリティ インシデント処理ガイド](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center でアラートに重大度を割り当てることで、最初に調査する必要があるアラートに優先順位を付けます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。
サブスクリプション (運用、非運用など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて対応計画を見直します。

- [IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 

事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:Security Center のアラートと推奨事項を、その連続エクスポート機能を使用してエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 

必要と組織のビジネス要件に応じて、Security Center データ コネクタを選択してアラートを Azure Sentinel にストリーミングします。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft によって管理されているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストの、Microsoft の戦略と実施の詳細については、以下のリンクを参照してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する