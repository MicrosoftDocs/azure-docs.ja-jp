---
title: 仮想ネットワーク用の Azure セキュリティ ベースライン
description: 仮想ネットワーク セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 420cd4a5b39f7c5864cf9802d02643e23c8ad24e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666573"
---
# <a name="azure-security-baseline-for-virtual-network"></a>仮想ネットワーク用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Azure 仮想ネットワークに適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure 仮想ネットワークに適用できる関連ガイダンスによって定義されています。 Azure 仮想ネットワークに適用できない **制御** は、除外されています。

Azure 仮想ネットワークを完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Virtual Network セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳しくは、「[Azure Security ベンチマーク:ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**:セキュリティ センターを使用し、ネットワークの保護に関する推奨事項に従って、Azure のネットワーク リソースを保護します。 

ネットワーク セキュリティ グループのフロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics は、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。 

Azure Monitor ログを使用して、環境に関する分析情報を提供します。 ワークスペースは、データの照合と分析のために使用されます。ワークスペースは、他の Azure サービス (Application Insights や Security Center など) と統合できます。 

Azure ストレージ アカウントまたはイベント ハブに送信するリソース ログを選択します。 別のプラットフォームを使用してログを分析することもできます。 

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [ Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:DDoS 攻撃から保護するには、Azure 仮想ネットワークで DDoS (Azure 分散型サービス拒否) Standard 保護を有効にします。

有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンスベースのフィルター処理とともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。

セキュリティ センターの脅威保護機能を使用して、既知の悪意ある IP アドレスとの通信を検出します。

また、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限するネットワーク セキュリティ グループ構成を推奨するセキュリティ センターのアダプティブ ネットワーク強化機能を使用します。 

- [Azure portal を使用した Azure DDoS Protection Standard の管理](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall の脅威インテリジェンスベースのフィルター処理](../firewall/threat-intel.md)

- [Security Center での脅威の防止](../security-center/azure-defender.md)

- [Azure Security Center でのアダプティブ ネットワークのセキュリティ強化機能](../security-center/security-center-adaptive-network-hardening.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:VPN ゲートウェイのパケット キャプチャは、一般的に入手可能なパケット キャプチャ ツールに加えて使用することもできます。 

エージェント ベースの ソリューションまたは NVA ソリューションを使用することもできます。これらは、Azure Marketplace オファリングで入手できるパケット ブローカー パートナー ソリューションを通して ターミナル アクセス ポイント (TAP)/ネットワーク可視性機能を提供します。

- [VPN ゲートウェイのパケット キャプチャを構成する](../vpn-gateway/packet-capture.md) 

- [ネットワーク仮想アプライアンス パートナー](https://azure.microsoft.com/solutions/network-appliances)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**:脅威インテリジェンスが有効になった仮想ネットワーク上でデプロイされる Azure Firewall を使用します。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理を使用して、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックに対してアラートを送信したりトラフィックを拒否したりします。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 

ペイロード検査能力を備えた IDS または IPS 機能をサポートする Azure Marketplace からの適切なオファーを選択することもできます。

悪意のあるトラフィックを検出および拒否できるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:ネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときに、特定の IP アドレスの代わりにサービス タグを使用できます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (たとえば ApiManagement) を指定することにより、対応するサービスのトラフィックを許可または拒否します。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

アプリケーション セキュリティ グループを使用して、複雑なセキュリティ構成を簡略化することもできます。 アプリケーション セキュリティ グループを使用すると、アプリケーションの構造の自然な拡張として、ネットワーク セキュリティを構成できます。 これにより、仮想マシンをグループ化し、それらのグループに基づいてネットワーク セキュリティ ポリシーを定義することができます。

- [サービス タグの概要と使用](service-tags-overview.md)

- [アプリケーション セキュリティ グループの概要と使用](./network-security-groups-overview.md#application-security-groups)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure Policy を使用してネットワーク リソースの標準的なセキュリティ構成を定義して実装し、組み込みのネットワーク ポリシー定義で実装を確認します。

仮想ネットワークに関連する使用可能なセキュリティの推奨事項が含まれているセキュリティ センターの既定のポリシーを参照してください。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure ロールベースのアクセス制御 (Azure RBAC) の割り当て、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化します。 Azure Blueprint を新しいサブスクリプションに適用し、バージョン管理によって制御と管理を微調整できます。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](../governance/policy/samples/built-in-policies.md#network) 

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

- [Azure Security Center で監視を有効にします。](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループやその他のリソースにタグを使用します。 個々のネットワーク セキュリティ グループ規則については、[Description (説明)] フィールドを使用して、ネットワークとの間のトラフィックを許可するすべての規則に関するビジネス ニーズや期間などを指定します。
すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure CLI を選択して、タグに基づいたリソースの検索やアクションを実行します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、リソースの構成を監視し、仮想ネットワークに対する変更を検出します。 重要なリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:監査およびアクティビティのログ (イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素を含みます) にアクセスするために、Azure Monitor を有効にします。 

Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。
または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure アクティビティ ログ イベントを表示および取得する](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:監査およびアクティビティのログ (イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素を含みます) にアクセスするために、Azure Monitor を有効にします。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure アクティビティ ログ イベントを表示および取得する](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 セキュリティ ログ ストレージ保持の長期またはアーカイブ ストレージには Azure Storage アカウントを使用します。

- [Log Analytics でデータ保持期間を変更する](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかログの分析と監視を行って、定期的に結果を確認します。 Azure Monitor 内で Azure Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージに Azure Storage アカウントを使用します。 

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Log Analytics ワークスペースについて](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/logs/get-started-queries.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Log Analytics ワークスペースと共に Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。

または、Azure Sentinel または亜アラート用サード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [セキュリティ センターでアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**:組織のニーズに応じて、DNS ログ ソリューション用に Azure Marketplace のサードパーティ製ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:明示的な割り当てとクエリの実行が可能である Azure Active Directory (Azure AD) 組み込みの管理者ロールを使用します。 

Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 セキュリティ センターの ID とアクセス管理を使用して、管理者アカウントの数を監視します。

Microsoft サービスの Azure AD Privileged Identity Management の特権ロール、および Azure Resource Manager を使用して、Just-In-Time/Just-Enough-Access を有効にすることもできます。 

- [Privileged Identity Management について](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**:サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory (Azure AD) で SSO を使用します。 セキュリティ センターの ID とアクセス管理の推奨事項を使用します。

- [Azure Active Directory でのアプリケーションへのシングル サインオン](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) を有効にし、Security Center の ID とアクセス管理の推奨事項に従います。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**:Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure ネットワーク リソースにアクセスします。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Azure Active Directory (Azure AD) のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。 

Security Center のリスク検出アラートを Azure Monitor に取り込み、アクション グループを使用してカスタムのアラートまたは通知を構成します。

- [セキュリティ センターのリスク検出 (疑わしいアクティビティ) について](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [カスタムのアラートおよび通知用にアクション グループを構成する方法](../azure-monitor/alerts/action-groups.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Active Directory (Azure AD) を自社サービスの中央認証および承認システムとして使用します。 Azure AD では、データの保存時と転送時に強力な暗号化を使用してデータが保護され、さらに、ユーザーの資格情報がソルト化およびハッシュされ、安全に保存されます。  

- [Azure AD インスタンスを作成して構成する方法](../active-directory-domain-services/tutorial-create-instance.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Active Directory (Azure AD) を使用して、古いアカウントの検出に役立つログが提供されます。 

Azure ID アクセス レビューは、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理するために実行できます。 アクティブなユーザーのみが継続的なアクセス権を持っていることを確認するために、ユーザー アクセスを定期的に確認する必要があります。

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Active Directory (Azure AD) のサインイン アクティビティ、監査、リスク イベントのログ ソースを、自分のアクセスに基づいて任意の SIEM または監視ツールと統合します。 

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で任意のアラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**:Azure Active Directory (Azure AD) のリスクおよび Identity Protection 機能を使用して、自仮想ネットワークのユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 さらに詳しく調査するために、Azure Sentinel にデータを取り込みます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 仮想ネットワーク内の Azure リソースに接続するクライアントが TLS 1.2 以上をネゴシエートできるようにします。 保存時と転送時の暗号化に関するセキュリティ センターの推奨事項に従います。 

Microsoft では、Azure ネットワーク内で内部移動するデータやインターネットを経由してエンド ユーザーの元に外部移動するデータをセキュリティで保護する目的で顧客が利用できるいくつかのオプションを提供しています。 それらには、仮想プライベート ネットワーク (IPsec/IKE 暗号化を活用)、トランスポート層セキュリティ (TLS) 1.2 以降 (Application Gateway や Azure Front Door などの Azure コンポーネント経由で)、Azure 仮想マシン上に直接配置されるプロトコル (Windows IPsec や SMB など) などがあります。

また、顧客データの機密性と整合性を確保するため、Azure データ センター間を移動するあらゆる Azure トラフィックに対して、MACsec (データリンク層の IEEE 標準) を使用した "既定の暗号化" が有効になっています。

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する 

**ガイダンス**:Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、データとリソースへのアクセスを管理します。 それ以外の場合は、サービス固有のアクセス制御メソッドを使用します。 

組み込みロール (所有者、共同作成者、ネットワーク共同作成者など) を選択して、適切なスコープにロールを割り当てます。 たとえば、仮想ネットワークに必要な特定のアクセス許可を持つ仮想ネットワーク機能のサブセットを、これらのロールのいずれかに割り当てることができます。 

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [仮想ネットワークを計画する](virtual-network-vnet-plan-design-arm.md#permissions)

- [Azure 組み込みロールをレビューする](../role-based-access-control/built-in-roles.md#networking)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure アクティビティ ログで Azure Monitor を使用して、仮想ネットワークやネットワーク セキュリティ グループなど重要な Azure リソースに変更が加えられたときのアラートを作成します。

- [ネットワーク セキュリティ グループの診断ログ](virtual-network-nsg-manage-log.md)

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、仮想ネットワークやサブスクリプション内のサブネットなど、すべてのネットワーク リソースをクエリして検出します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md) 

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:仮想ネットワークや関連リソースを整理したり追跡したりするには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md) 

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md) 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成する必要があります。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類 

- 許可されるリソースの種類 

高セキュリティ ベースの環境 (Azure Storage アカウントを持つ環境など) で、Azure Resource Graph を使用してサブスクリプション内のリソースを照会または探索します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](./policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:お客様は、お客様の会社のポリシーで求められているように、Azure Policy を使用してリソースの作成や使用を防ぐことができます。 承認されていないリソースを削除するためのプロセスを実装します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類 

- 許可されるリソースの種類 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](./policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:Microsoft Azure Management アプリに「アクセスのブロック」を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure Conditional Access を使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Policy エイリアスを使用してカスタム ポリシーを作成し、 Azure ネットワーク リソースの構成を監査または強制し、組み込みの Azure Policy 定義も使用します。

JavaScript Object Notation (JSON) で任意の組み込みテンプレートをエクスポートし、構成が確実に組織のセキュリティ要件を満たすかそれを超えるように確認する必要があります。

Azure リソースの安全な構成基準として Security Center からの推奨事項を実装します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](./policy-reference.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:仮想ネットワークおよび関連リソースに関連付けられている Azure リソースを安全に構成するには、Azure Resource Manager テンプレートと Azure Policy を使用します。  Azure Resource Manager テンプレートは、仮想マシンを Azure リソースと共にデプロイするために使用される JSON (JavaScript Object Notation) ベースのファイルです。 Microsoft では、基本テンプレートに対してメンテナンスを実行します。  

Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] 効果を使用します。

- [Azure Resource Manager テンプレートの作成に関する情報](../virtual-machines/windows/ps-template.md) 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [仮想ネットワーク用の Azure Resource Manager テンプレート サンプル](template-samples.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](./policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。 その他にもあります。

コード、ビルド、作業の追跡など、Azure DevOps で管理するリソースにアクセスするには、許可が必要です。 ほとんどのアクセス許可は、組み込みのセキュリティ グループを通して付与されます。 Azure Active Directory (Azure AD) (Azure DevOps と統合されている場合)、または Active Directory (Team Foundation Server と統合されている場合) で定義されている特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与するか、それらのアクセス許可を拒否します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 Azure Policy エイリアスを使用してカスタム ポリシーを作成し、 Azure リソースおよび特定のリソースに関連する任意の込み込みポリシー定義のネットワーク構成を監査または強制します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [エイリアスを使用する方法](../governance/policy/concepts/definition-structure.md#aliases)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](./policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:セキュリティ センターを使用して、Azure 仮想ネットワークと関連リソースのベースライン スキャンを実行します。 Azure Policy を使用して Azure リソース構成をアラートおよび監査します。

- [セキュリティ センターの推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](./policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:マネージド サービス ID を Azure Key Vault と組み合わせて使用し、Azure 仮想ネットワークでホスティングされている Azure リソースのシークレット管理を簡素化し、セキュリティで保護します。

- [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [キー コンテナーを作成する方法](../key-vault/secrets/quick-create-portal.md) 

- [マネージド ID で Key Vault の認証を提供する方法](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳しくは、「[Azure Security ベンチマーク:データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**:Azure Resource Manager を使用して、仮想ネットワークおよび関連リソースをデプロイします。 Azure Resource Manager には、仮想ネットワークと関連リソースを復元するためのバックアップとして使用できるテンプレートをエクスポートする機能があります。  Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。

- [Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)

- [仮想ネットワーク用の Azure Resource Manager テンプレート サンプル](template-samples.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [リソース グループ - テンプレートのエクスポート](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation の概要](../automation/automation-intro.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**:Azure Resource Manager を使用して、仮想ネットワークおよび関連リソースをデプロイします。 Azure Resource Manager には、仮想ネットワークと関連リソースを復元するためのバックアップとして使用できるテンプレートをエクスポートする機能があります。 Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。 Azure Key Vault 内のカスタマー マネージド キーをバックアップします。

- [Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)

- [仮想ネットワーク用の Azure Resource Manager テンプレート サンプル](template-samples.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [リソース グループ - テンプレートのエクスポート](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation の概要](../automation/automation-intro.md)

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:分離サブスクリプションに Azure Resource Manager テンプレートのデプロイを定期的に実行し、バックアップの顧客管理キーの復元をテストします。

- [ARM テンプレートと Azure portal でリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure DevOps を使用して、カスタム Azure Policy 定義や Azure Resource Manager テンプレートなどのコードを安全に格納し、管理します。 

Azure Active Directory (Azure AD) (Azure DevOps と統合されている場合)、または Active Directory (Team Foundation Server と統合されている場合) で定義されている特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与するか、それらのアクセス許可を拒否します。  

Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、カスタマー マネージド キーを保護します。   

Key Vault で論理的な削除と消去保護を有効にして、偶発的または悪意のある削除からキーを保護します。  

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

- [Key Vault で論理的な削除と消去保護を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Azure Storage Blob の論理的な削除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。  

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [独自のインシデント対応計画の作成のための NIST の「コンピューター セキュリティ インシデント対応ガイド」](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

タグを使用してサブスクリプションを明確にマークし (運用、非運用など)、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security Center のセキュリティ アラート](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [セキュリティ センターのセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して セキュリティ センターのアラートと推奨事項をエクスポートすると、Azure リソースに対するリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 

Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングすることもできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:Security Center のワークフロー自動化機能を使用して、セキュリティのアラートと推奨事項に対して Logic Apps で応答を自動的にトリガーし、Azure リソースを保護します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する