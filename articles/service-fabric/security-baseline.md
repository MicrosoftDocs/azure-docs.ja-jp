---
title: Service Fabric 用の Azure セキュリティ ベースライン
description: Service Fabric セキュリティ ベースラインによって、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b2d86083077b95312415099f64a13509ed60091b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100582637"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Service Fabric 用の Azure セキュリティ ベースライン

Service Fabric 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)に関するページを参照してください。



## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: すべての Virtual Network サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループがあることを確認します。

* [テンプレートを使用して Azure Firewall をデプロイする](../firewall/deploy-template.md)

* [Azure ネットワーク セキュリティ グループ (NSG) を使用して境界ネットワークを作成する](../security/fundamentals/service-fabric-best-practices.md#use-network-isolation-and-security-with-azure-service-fabric)

* [Azure Service Fabric クラスターを既存の仮想ネットワークと統合する方法](./service-fabric-patterns-networking.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用して、Azure Service Fabric クラスターをセキュリティで保護するために使用される仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループのネットワーク保護に関する推奨事項を修正します。 ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Azure Log Analytics ワークスペースに送信し、Azure Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Azure Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

* [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Azure Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

* [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:ユーザー、デバイス、またはその他のアプリケーションに単一のイングレス ポイントを提供するフロントエンド ゲートウェイを提供します。 Azure API Management を Service Fabric と直接統合することで、バックエンド サービスへのアクセスをセキュリティで保護し、帯域幅調整を使用して DOS 攻撃を防止し、API キー、JWT トークン、証明書、およびその他の資格情報を検証することができます。

着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を重要な Web アプリケーションの前にデプロイすることを検討してください。 WAF の診断設定を有効にし、ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースにログを取り込みます。

* [Azure Service Fabric と API Management の概要](./service-fabric-api-management-overview.md)

* [内部 VNET 内の API Management と Application Gateway の統合](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: DDoS 攻撃からの保護のために、Azure Service Fabric クラスターがデプロイされている仮想ネットワーク上で Azure DDoS Standard Protection を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

* [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

* [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/azure-defender.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:Azure Service Fabric クラスターを保護するために使用されているサブネットに接続された ネットワーク セキュリティ グループ (NSG) 用に、NSG フロー ログを有効にします。 フロー レコードを生成するために、NSG フロー ログを Azure Storage アカウントに記録します。 異常なアクティビティを調査する必要がある場合は、Azure Network Watcher パケット キャプチャを有効にします。

* [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

* [Traffic Analytics を使用して NSG フロー ログを視覚化する](../network-watcher/traffic-analytics.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**: ペイロード検査能力を備えた IDS または IPS 機能をサポートする Azure Marketplace からのプランを選択します。 ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

悪意のあるトラフィックを検出および拒否できるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 信頼できる証明書に対して HTTPS/SSL を有効にした Web アプリケーションの Azure Application Gateway をデプロイします。

* [Application Gateway をデプロイする方法](../application-gateway/quick-create-portal.md)

* [HTTPS を使用するように Application Gateway を構成する方法](../application-gateway/create-ssl-portal.md)

* [Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散について](../application-gateway/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: 仮想ネットワーク サービス タグを使用して、Azure Service Fabric クラスターがデプロイされているサブネットに接続されたネットワーク セキュリティ グループ (NSG) に対して、ネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

* [仮想ネットワーク サービス タグ](../virtual-network/service-tags-overview.md)

* [Service Fabric のネットワークに関するベスト プラクティス](./service-fabric-best-practices-networking.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Service Fabric クラスターに関連するネットワーク リソースの標準的なセキュリティ構成を定義して、実装します。 Azure Service Fabric クラスターのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ServiceFabric" と "Microsoft.Network" 名前空間内で Azure Policy エイリアスを使用します。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

* [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Service Fabric クラスターに関連付けられているネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure コマンド ライン インターフェイス (CLI) を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Service Fabric のデプロイに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

* [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Azure Service Fabric クラスター コンポーネントのタイム ソースは Microsoft によって管理されており、コンピューティング デプロイの時刻同期を更新することができます。

* [Azure コンピューティング リソースの時刻同期を構成する方法](../virtual-machines/windows/time-sync.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: クラスターによって生成されたセキュリティ データを集計するために、Azure Monitor に Azure Service Fabric クラスターをオンボードできます。 Service Fabric での診断の問題と解決策の例を参照してください。

* [Service Fabric との Azure Monitor ログの統合を構成する](./service-fabric-diagnostics-oms-setup.md)

* [Azure Service Fabric でコンテナーを監視するために Azure Monitor ログを設定する](./service-fabric-tutorial-monitoring-wincontainers.md)

* [一般的な Service Fabric シナリオの診断](./service-fabric-diagnostics-common-scenarios.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Service Fabric クラスター用に Azure Monitor を有効にして、Log Analytics ワークスペースにダイレクトします。 これにより、すべての Azure Service Fabric クラスター ノードの関連するクラスター情報と OS メトリックがログに記録されます。

* [Service Fabric との Azure Monitor ログの統合を構成する](./service-fabric-diagnostics-oms-setup.md)

* [Azure Service Fabric でコンテナーを監視するために Azure Monitor ログを設定する](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Log Analytics エージェントをノードにデプロイする方法](./service-fabric-diagnostics-oms-agent.md)

* [Log Analytics のログ検索](../azure-monitor/logs/log-query-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: Azure Service Fabric クラスターを Azure Monitor にオンボードします。 使用される Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

* [Service Fabric との Azure Monitor ログの統合を構成する](./service-fabric-diagnostics-oms-setup.md)

* [Azure Service Fabric でコンテナーを監視するために Azure Monitor ログを設定する](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Log Analytics エージェントをノードにデプロイする方法](./service-fabric-diagnostics-oms-agent.md)

* [Log Analytics ワークスペースの保有期間を構成する方法](../azure-monitor/logs/manage-cost-storage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Service Fabric クラスターを Azure Monitor にオンボードします。 使用される Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

* [Service Fabric との Azure Monitor ログの統合を構成する](./service-fabric-diagnostics-oms-setup.md)

* [Azure Service Fabric でコンテナーを監視するために Azure Monitor ログを設定する](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Log Analytics エージェントをノードにデプロイする方法](./service-fabric-diagnostics-oms-agent.md)

* [Log Analytics ワークスペースの保有期間を構成する方法](../azure-monitor/logs/manage-cost-storage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Log Analytics ワークスペース クエリを使用して Azure Service Fabric ログに対してクエリを実行します。

* [Log Analytics のログ検索](../azure-monitor/logs/log-query-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Log Analytics ワークスペースを使用して、Azure Service Fabric クラスターに関連するセキュリティ ログやイベントでの異常なアクティビティを監視し、アラート通知を行います。

* [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

* [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: Windows Server 2016 には、Windows Defender が既定でインストールされます。 Windows Defender を使用していない場合は、お使いのマルウェア対策のドキュメントを参照して構成ルールを確認してください。 Windows Defender は、Linux ではサポートされていません。

* [詳しくは、Windows Server 2016 の Windows Defender ウイルス対策に関する記事をご覧ください](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: DNS ログ記録用のサード パーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**:ノード単位でのコンソールのログ記録を手動で構成します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Service Fabric クラスターのクラスター プロビジョニング中に作成されたローカル管理者アカウントや、作成したその他のすべてのアカウントのレコードを管理します。 さらに、Azure AD の統合が使用されている場合、Azure AD には、明示的に割り当てる必要があるためにクエリ可能な組み込みのロールが用意されています。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

* [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: クラスターをプロビジョニングする場合、Azure では、Web ポータル用の新しいパスワードを作成する必要があります。 変更する既定のパスワードはありませんが、Web ポータル アクセスに対して異なるパスワードを指定できます。

* [Azure portal で作成する](./service-fabric-cluster-creation-via-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: Azure Service Fabric の認証を Azure Active Directory と統合します。 専用の管理者アカウントの使用に関するポリシーと手順を作成します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

* [Azure Active Directory クライアント認証を設定する](./service-fabric-tutorial-create-vnet-and-windows-cluster.md#set-up-azure-active-directory-client-authentication)

* [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

* [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure AD MFA を有効にして、Azure Security Center ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: 多要素認証 (MFA) が構成された PAW (特権アクセス ワークステーション) を使用してログインし、Azure Service Fabric クラスターと関連リソースを構成します。

* [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときにログとアラートを生成します。 また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

* [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure AD のリスク検出の概要](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

* [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Service Fabric クラスターの管理エンドポイントへのアクセスをセキュリティで保護するには、Azure Active Directory (AAD) を中央認証および認可システムとして使用します。 AAD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、AAD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

* [AAD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Service Fabric クライアント認証用に Azure Active Directory を設定する](./service-fabric-cluster-creation-setup-aad.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Service Fabric クラスターで Azure Active Directory (AAD) の認証を使用します。 AAD には、古いアカウントの検出に役立つログが用意されています。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

* [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="311-alert-on-account-login-behavior-deviation"></a>3.11: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**:Azure Active Directory (AAD) サインインと監査ログを使用して、非アクティブ化されたアカウントへのアクセス試行の監視を行います。これらのログは、任意のサードパーティ製 SIEM/監視ツールに統合できます。

このプロセスを効率化するには、AAD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Azure Log Analytics ワークスペースに送信します。 Azure Log Analytics ワークスペース内に目的のアラートを構成します。

* [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure AD のリスクおよび ID Protectoin 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

* [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 使用できません。Azure Service Fabric ではまだカスタマー ロックボックスがサポートされていません。

* [カスタマー ロックボックスでサポートされているサービスの一覧](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:Azure Service Fabric クラスターのデプロイに関連するリソースに対してタグを使用すると、機密情報を格納または処理する Azure リソースの追跡に役立ちます。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは、VNet/サブネットで分割し、適切にタグを付け、NSG または Azure Firewall で保護する必要があります。 機密データを格納または処理するリソースは、十分に分離する必要があります。 機密データを格納または処理する仮想マシンでは、使用されていないときにはオフにするためのポリシーとプロシージャを実装します。

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Firewall でアラートまたはアラートと拒否を構成する方法](../firewall/threat-intel.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする自動ツールをネットワーク境界にデプロイします。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

機能。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

* [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Service Fabric クラスターのセキュリティに関するシナリオ](./service-fabric-cluster-security.md)

* [TLS 構成に関する Service Fabric のトラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: 適用できません。この推奨事項は、データを格納するように設計された非コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 機密情報を格納または処理する Azure Service Fabric クラスターの場合は、タグを使用してクラスターと関連リソースを機密としてマークします。 Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: すべての Azure リソースで保存時の暗号化を使用します。 Microsoft では、Azure に暗号化キーの管理を許可することをお勧めしていますが、一部のインスタンスでユーザーが自身のキーを管理するという選択肢もあります。

* [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md)

* [カスタマー マネージド暗号化キーを構成する方法](../storage/common/customer-managed-keys-configure-key-vault.md)

* [Windows での Azure Service Fabric クラスター ノードのディスク暗号化の有効化](./service-fabric-enable-azure-disk-encryption-windows.md)

* [Linux での Azure Service Fabric クラスター ノードのディスク暗号化の有効化](./service-fabric-enable-azure-disk-encryption-linux.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、重要な Azure リソースに変更が加えられたときのアラートを作成します。

* [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Service Fabric Fault Analysis Service と Chaos サービスを定期的に実行して、クラスター全体で障害をシミュレートし、サービスの堅牢性と信頼性を評価します。

Azure 仮想マシンとコンテナー イメージに対して脆弱性評価を実行することに関する Azure Security Center の推奨事項に従います。

ネットワーク デバイスと Web アプリケーションで脆弱性評価を実行するためのサードパーティ ソリューションを使用します。 リモート スキャンを実施する場合は、1 つの永続的な管理者アカウントを使用しないでください。 スキャン アカウントには、JIT プロビジョニングの方法論を実装することを検討してください。 スキャン アカウントの資格情報は保護と監視の対象とし、脆弱性のスキャンのためにのみ使用する必要があります。

* [Service Fabric Fault Analysis Service の概要](./service-fabric-testability-overview.md)

* [Service Fabric クラスターでの制御された混乱の誘発](./service-fabric-controlled-chaos.md)

* [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Azure Service Fabric クラスターの仮想マシン スケール セットで OS イメージの自動アップグレードを有効にします。

または、運用環境に移行する前に OS 修正プログラムをテストするには、スケール セットの OS イメージのアップグレードに手動トリガーを使用します。 手動トリガー オプションには、組み込みのロールバックが用意されていないことに注意してください。 Azure Automation の Update Management を使用して OS 修正プログラムを監視します。

* [Service Fabric クラスター ノードの修正プログラム管理](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Azure 仮想マシン スケール セットでの OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [最新のスケール セット モデルで VM を最新の状態にする方法](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

* [Azure Automation の Update Management の概要](../automation/update-management/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**:Azure Service Fabric クラスターの仮想マシン スケール セットで OS イメージの自動アップグレードを有効にします。 パッチ オーケストレーション アプリケーション (POA) は、Azure の外部でホストされている Service Fabric クラスターを対象とした別のソリューションです。 POA は Azure クラスターで使用できますが、追加のホスティング オーバーヘッドが伴います。

* [Service Fabric クラスター ノードの修正プログラム管理](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Azure 仮想マシン スケール セットでの OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [Service Fabric クラスターの OS 修正プログラム適用スケジュールを構成する方法](./service-fabric-patch-orchestration-application.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: スキャン結果を一定の間隔でエクスポートして結果を比較し、脆弱性が修復されていることを確認します。 Azure Security Center によって提案された脆弱性管理の推奨事項を使用する場合は、選択したソリューションのポータルに切り替えてスキャン データの履歴を表示できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:一般的なリスク スコアリング プログラム (Common Vulnerability Scoring System など)、またはサードパーティのスキャン ツールによって提供される既定のリスク評価を使用します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

* [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

* [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアを定義します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

許可されないリソースの種類

許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:クラスター ノード上で承認されていないソフトウェア アプリケーションを監視するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Resource Graph を使用して、Azure Service Fabric クラスターを含めて、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 検出された承認されていない Azure リソースを削除します。 Azure Service Fabric クラスター ノードの場合は、承認されていないソフトウェアの削除またはアラート通知を行うためのサードパーティ ソリューションを実装します。

* [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**:Azure Service Fabric クラスター ノードの場合は、承認されていないソフトウェアの実行を防止するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: Azure Policy を使用して、環境内でプロビジョニングできるサービスを制限します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: Azure Service Fabric クラスター ノードの場合は、承認されていないファイルの種類の実行を防止するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

* [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーの Azure コンピューティング リソース内でスクリプトを実行する機能を制限します。

* [たとえば、Windows 環境で PowerShell スクリプトの実行を制御する方法](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 業務に必要であっても、組織のリスクが高くなる可能性があるソフトウェアは、独自の仮想マシンや仮想ネットワーク内に隔離し、Azure Firewall またはネットワーク セキュリティ グループで十分に保護する必要があります。

* [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Service Fabric クラスターのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ServiceFabric" 名前空間内で Azure Policy エイリアスを使用します。

* [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: Azure Service Fabric のオペレーティング システム イメージは、Microsoft によって管理および保守されます。 クラスター ノードのオペレーティング システムにセキュリティで保護された構成を実装する責任は、お客様が負います。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure Service Fabric クラスターと関連リソースにセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: Azure Service Fabric クラスターのオペレーティング システム イメージは、Microsoft によって管理および保守されます。 OS レベルの状態構成を実装する責任は、お客様が負います。

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

* [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos のドキュメント](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: カスタム イメージを使用している場合は、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、確実に承認されたユーザーのみがイメージにアクセスできるようにします。 コンテナー イメージの場合は、Azure Container Registry に保存し、Azure RBAC を利用して、承認されたユーザーのみがイメージにアクセスできるようにします。

* [Azure RBAC について](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [コンテナー レジストリの Azure RBAC を理解する](../container-registry/container-registry-roles.md)

* [Azure RBAC を構成する方法](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成するには、"Microsoft.ServiceFabric" 名前空間で Azure Policy エイリアスを使用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Service Fabric クラスターの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ServiceFabric" 名前空間内で Azure Policy エイリアスを使用します。

* [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**:Azure Security Center を使用して、コンテナーの OS と Docker の設定のベースライン スキャンを実行します。

* [Azure Security Center のコンテナーの推奨事項を理解する](../security-center/container-security.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

* [Service Fabric での Azure のマネージド ID の使用](./concepts-managed-identity.md)

* [新しい Service Fabric クラスターのマネージド ID のサポートを構成する](./configure-new-azure-service-fabric-enable-managed-identity.md)

* [Service Fabric アプリケーションでマネージド ID を使用する](./how-to-managed-identity-service-fabric-app-code.md)

* [Service Fabric アプリケーションでの KeyVaultReference サポート](./service-fabric-keyvault-references.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID は、Azure にデプロイされた Service Fabric クラスターや、Azure リソースとしてデプロイされたアプリケーションで使用できます。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

* [Service Fabric での Azure のマネージド ID の使用](./concepts-managed-identity.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**:Azure Service Fabric のデプロイに関連するコードを使用する場合は、資格情報スキャナーを実装して、コード内で資格情報を識別することができます。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

Service Fabric クラスターの証明書を自動的にローテーションするには、Azure Key Vault を使用します。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

* [Service Fabric クラスターでの証明書管理](./cluster-security-certificate-management.md#certificate-rotation)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: Windows Server 2016 には、Windows Defender ウイルス対策が既定でインストールされます。 一部の SKU ではユーザー インターフェイスが既定でインストールされますが、必須ではありません。

Windows Defender を使用していない場合は、お使いのマルウェア対策のドキュメントで構成ルールを参照してください。 Windows Defender は、Linux ではサポートされていません。

* [Windows Server 2016 での Windows Defender ウイルス対策について](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: 適用できません。この推奨事項は、データを格納するように設計された非コンピューティング リソースを対象にしています。 Microsoft マルウェア対策は、Azure サービス (Service Fabric など) をサポートする、基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、データを格納するように設計された非コンピューティング リソースを対象にしています。 Microsoft マルウェア対策は、Azure サービス (Service Fabric など) をサポートする、基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Service Fabric のバックアップと復元サービスを使用すると、ステートフル サービスに格納された情報を簡単かつ自動的にバックアップできます。 定期的にアプリケーション データをバックアップすることは、データが失われたりサービスを利用できなくなったりしないように保護するために不可欠です。 Service Fabric には、オプションで提供されるバックアップと復元サービスがあります。このサービスを使用すると、追加のコードを記述することなく、ステートフルな Reliable Services の定期的なバックアップを構成できます (Actor Services も対象になります)。 これまでに実行したバックアップも簡単に復元できます。

* [Azure Service Fabric クラスターでの定期的なバックアップと復元](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Service Fabric クラスターでバックアップ復元サービスを有効にし、ステートフル サービスを定期的かつオンデマンドでバックアップするバックアップ ポリシーを作成します。 Azure Key Vault 内のカスタマー マネージド キーをバックアップします。

* [Azure Service Fabric クラスターでの定期的なバックアップと復元](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

* [Azure Service Fabric の定期バックアップ構成を理解する](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:バックアップ構成情報と利用可能なバックアップを定期的に確認することにより、確実にバックアップ復元サービスから復元を実行できるようにします。 バックアップされたカスタマー マネージド キーの復元をテストします。

* [Azure Service Fabric の定期バックアップ構成を理解する](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Azure Service Fabric でのバックアップの復元](./service-fabric-backup-restore-service-trigger-restore.md)

* [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Service Fabric のバックアップ復元サービスからのバックアップには、サブスクリプションの Azure Storage アカウントを使用します。 Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するには、ストレージ データの暗号化のためにカスタマー マネージド キーを用意します。

カスタマー マネージド キーを使用している場合は、キーを偶発的または悪意のある削除から保護するために、Key Vault の論理的な削除が有効であることを確認します。

* [Azure Storage の保存時の暗号化](../storage/common/storage-service-encryption.md)

* [Key Vault で論理的な削除を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールと、検出からインシデント後のレビューまでのインシデント対応と管理のフェーズを定義する、書面によるインシデント対応計画があることを確認します。

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [独自のインシデント対応計画を作成するために NIST の「コンピューター セキュリティ インシデント対応ガイド」を使用する](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してサブスクリプションをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいてアラートの修復に優先順位を付けることは、お客様の責任です。

* [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

* [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [IT 計画と機能に関するテスト、トレーニング、演習プログラムについての NIST のガイド](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

* [連続エクスポートを構成する方法](../security-center/continuous-export.md)

* [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

* [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

* [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する