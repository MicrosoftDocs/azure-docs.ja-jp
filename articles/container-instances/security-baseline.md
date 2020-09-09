---
title: Container Instances の Azure セキュリティ ベースライン
description: Container Instances の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 996793f2851949f7474312a18ccff04e88db2232
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259161"
---
# <a name="azure-security-baseline-for-container-instances"></a>Container Instances の Azure セキュリティ ベースライン

Container Instances 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: Azure Virtual Network では、ご利用の Azure リソースやオンプレミス リソースに合わせてセキュリティで保護されたプライベート ネットワークを実現できます。 Azure Container Instances 内のコンテナー グループを Azure 仮想ネットワークと統合します。 

* [仮想ネットワークのシナリオとリソース - Azure Container Instances](./container-instances-virtual-network-concepts.md)

* [コンテナー インスタンスを Azure 仮想ネットワークにデプロイする](./container-instances-vnet.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall をデプロイして構成する方法](../firewall/tutorial-firewall-deploy-portal.md)


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用し、ネットワークの保護に関する推奨事項を修正することで、Azure 内でご利用のネットワーク リソースを容易に保護することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。

* [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [ネットワーク リソースの保護](../security-center/security-center-network-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を Azure Container Instances にホストされている重要な Web アプリケーションの前にデプロイします。 WAF の診断設定を有効にし、ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースにログを取り込みます。

* [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: DDoS 攻撃から保護するために、Azure Virtual Network で DDoS Standard 保護を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある IP アドレスとの通信を拒否します。 有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンスとともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。 Azure Security Center の Just In Time ネットワーク アクセスを使用して、NSG を構成し、限られた期間、承認された IP アドレスへのエンドポイントの公開を制限します。 Azure Security Center のアダプティブ ネットワークのセキュリティ強化を使用して、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限する NSG 構成を推奨します。 

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/threat-protection.md)

* [Azure Security Center のアダプティブ ネットワークのセキュリティ強化について](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center の Just In Time ネットワーク アクセス制御](../security-center/security-center-just-in-time.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: 仮想ネットワークの実装でネットワーク セキュリティ グループ (NSG) を使用する場合は、Azure Container Instances に委任されたサブネットにアタッチされている NSG に対して、NSG フロー ログを有効にします。 フロー レコードを生成するために、NSG フロー ログを Azure Storage アカウントに記録します。 異常なアクティビティを調査する必要がある場合は、Azure Network Watcher パケット キャプチャを有効にします。

* [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: ペイロード検査能力を備えた IDS または IPS 機能をサポートする Azure Marketplace からのプランを選択します。 ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

悪意のあるトラフィックを検出および拒否できるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

* [仮想ネットワークでのデプロイ - Azure Container Instances](./container-instances-vnet.md) 



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 信頼できる証明書に対して HTTPS/SSL を有効にした Web アプリケーションの Azure Application Gateway をデプロイします。

* [Application Gateway をデプロイする方法](../application-gateway/quick-create-portal.md)

* [HTTPS を使用するように Application Gateway を構成する方法](../application-gateway/create-ssl-portal.md) 

* [Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散について](../application-gateway/overview.md)

* [コンテナー グループの静的 IP アドレスの公開](./container-instances-application-gateway.md)

* [仮想ネットワークでのデプロイ - Azure Container Instances](./container-instances-vnet.md)



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Virtual Network サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall のネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 

また、アプリケーション セキュリティグループを使用して、複雑なセキュリティ構成を簡略化することもできます。 アプリケーション セキュリティ グループを使用すると、ネットワーク セキュリティをアプリケーションの構造の自然な拡張として構成でき、仮想マシンをグループ化して、それらのグループに基づくネットワーク セキュリティ ポリシーを定義できます。 

* [サービス タグの概要と使用](../virtual-network/service-tags-overview.md) 

* [アプリケーション セキュリティ グループの概要と使用](../virtual-network/security-overview.md#application-security-groups)

* [仮想ネットワークでのデプロイ - Azure Container Instances](./container-instances-vnet.md)



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Blueprints を使用して、Azure Resource Manager テンプレート、RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションに適用し、バージョン管理によって制御と管理を微調整できます。 

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [ネットワークに関する Azure Policy のサンプル](/azure/governance/policy/samples/#network)

* [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: ネットワークのセキュリティおよびトラフィック フローに関連する NSG およびその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [仮想ネットワークでのデプロイ - Azure Container Instances](./container-instances-vnet.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: ネットワーク リソース構成を監視し、ご利用のコンテナー インスタンスに関連したネットワーク リソースに関する変更を検出するには、Azure アクティビティ ログを使用します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

* [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Azure Monitor でアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft は Azure リソースのためにタイム ソースを管理していますが、コンピューティング リソースのために時刻同期設定を管理するオプションが用意されています。 たとえば、実行中のコンテナーで時刻同期コマンドを実行します。

* [Azure コンピューティング リソースの時刻同期を構成する方法](../virtual-machines/windows/time-sync.md)

* [実行中の Azure Container Instances でコマンドを実行する](./container-instances-exec.md)



**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor を介してログを取り込んで、Azure コンテナー グループによって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

* [Azure Monitor ログによるコンテナー グループおよびインスタンスのログ記録](./container-instances-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Monitor を使用すると、ご利用のレジストリ内のユーザー駆動型イベントのリソース ログ (以前は "診断ログ" と呼ばれていました) を収集できます。 Azure Container Instances には、コンテナー グループのログとイベント データ、コンテナー ログを Azure Monitor ログに送信するための組み込みサポートが含まれています。

* [Azure Monitor ログによるコンテナー グループおよびインスタンスのログ記録](../container-registry/container-registry-diagnostics-audit-logs.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用不可。 このガイドラインは、IaaS コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

* [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:異常な動作がないかログの分析と監視を行って、定期的に結果を確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。 

* [Azure Monitor ログによるコンテナー グループおよびインスタンスのログ記録](./container-instances-log-analytics.md)

* [Log Analytics ワークスペースについて](../azure-monitor/log-query/get-started-portal.md)

* [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Log Analytics ワークスペースを使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。 

* [Azure Monitor ログによるコンテナー グループおよびインスタンスのログ記録](./container-instances-log-analytics.md)

* [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:コンテナーで実行するために必要な場合は、独自のマルウェア対策ソリューションとイベント収集を提供します。 


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: コンテナー内の DNS ログのクエリを実行するために必要な場合は、独自のソリューションを提供します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 必要に応じて、実行中のコンテナー インスタンスでコンソール ログを構成します。

* [実行中の Azure Container Instances でコマンドを実行する](./container-instances-exec.md)



**Azure Security Center の監視**: 適用外

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (Azure AD) には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

Azure コンテナー レジストリを Azure Container Instances と共に使用する場合は、Azure コンテナー レジストリごとに、組み込みの管理者アカウントが有効になっているか無効になっているかを追跡します。 使用されていない場合、アカウントは無効にします。

* [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Azure Container Registry 管理者アカウント](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションと Marketplace サービスについては、お客様が責任を負うものとします。

Azure コンテナー レジストリを Azure Container Instances と共に使用し、Azure コンテナー レジストリの既定の管理者アカウントが有効になっている場合は、複雑なパスワードが自動的に作成されます。これはローテーションされる必要があります。 使用されていない場合、アカウントは無効にします。

* [Azure Container Registry 管理者アカウント](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

Azure コンテナー レジストリを Azure Container Instances と共に使用する場合は、コンテナー レジストリの組み込みの管理者アカウントを有効にする手順を作成します。 使用されていない場合、アカウントは無効にします。

* [Azure Security Center ID とアクセスについて](../security-center/security-center-identity-access.md)

* [Azure Container Registry 管理者アカウント](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

* [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証 (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: MFA が構成されている PAW (特権アクセス ワークステーション) を使用して Azure リソースにログインし、構成します。

* [特権アクセス ワークステーションについて](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

* [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

* [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**:条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

* [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

* [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

* [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

* [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意のセキュリティ情報イベント管理 (SIEM) または監視ツールとの統合が可能です。

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

* [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) のリスクおよび ID Protectoin 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。

* [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 現在は使用できません。現在、Azure Container Instances ではカスタマー ロックボックスはサポートされていません。

* [カスタマー ロックボックスでサポートされているサービスの一覧](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure コンテナー インスタンスの追跡を支援するには、リソース タグを使用します。 

コンテナー イメージのタグ付けとバージョン管理で、機密情報を格納または処理するイメージの追跡を支援します。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [コンテナー イメージのタグ付けとバージョン管理に関する推奨事項](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは、VNet/サブネットで分割し、適切にタグを付け、NSG または Azure Firewall で保護する必要があります。 機密データを格納または処理するリソースは、十分に分離する必要があります。

* [実行中の Azure Container Instances でコマンドを実行する](./container-instances-exec.md)

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create.md)

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [仮想ネットワークでのデプロイ - Azure Container Instances](./container-instances-vnet.md) 
* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Firewall でアラートまたはアラートと拒否を構成する方法](../firewall/threat-intel.md)


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする自動ツールをネットワーク境界にデプロイします。 Azure ファイル共有およびコンテナー インスタンスにマウントされている他のボリュームからの未承認の情報転送を監視し、ブロックします。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

* [仮想ネットワークでのデプロイ - Azure Container Instances](./container-instances-vnet.md) 

* [Azure Container Instances に Azure ファイル共有をマウントする](./container-instances-volume-azure-files.md)


**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: ご利用の Azure コンテナー グループに接続されているクライアントがいずれも TLS 1.2 以上を確実にネゴシエートできるようにします。 Microsoft Azure リソースでは、既定で TLS 1.2 がネゴシエートされます。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

* [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Container Instances では、データの識別、分類、損失防止の各機能は現時点では使用できません。 そのため、機密情報を処理する可能性のあるコンテナー グループにはタグを付けます。また、コンプライアンスの目的で必要な場合はサードパーティ製のソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure AD RBAC を使用して、Azure Container Instances のデータとリソースへのアクセスを制御します。 

* [Azure で RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: 既定では、Azure Container Instances 内のすべてのデプロイ データが、Microsoft のマネージド キーを使用して保存時に暗号化されます。 必要に応じて、独自のキー (ユーザーが管理するキー) で暗号化を管理します。

* [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md)

* [Azure Container Instances でデプロイ データを暗号化する](./container-instances-encrypt-data.md)



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、コンテナー グループとコンテナー インスタンスに対して変更が行われたときのアラートを作成します。 

* [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: プライベート レジストリ内のコンテナー イメージをスキャンし、潜在的な脆弱性を識別するためのソリューションを実装します。 Azure Container Registry に保存されているコンテナー イメージに対して脆弱性評価を実行する際は Azure Security Center からの推奨事項に従ってください。 必要があれば、Azure Marketplace からのサードパーティのソリューションをデプロイして、イメージの脆弱性評価を実行します。

* [Azure Container Instances のセキュリティに関する考慮事項](./container-instances-image-security.md)

* [Azure Container Registry と Security Center の統合](../security-center/azure-container-registry-integration.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Microsoft では、実行中のコンテナーをサポートしている基になるシステムでパッチの管理を行います。

カスタムまたはサード パーティのソリューションを使用して、コンテナー イメージを修正します。 コンテナー イメージを Azure Container Registry に保存している場合は、基本 OS イメージでのセキュリティ パッチまたはその他の更新プログラムに基づいてコンテナー レジストリ内のアプリケーション イメージに対する更新を自動化するための Azure Container Registry タスクを実行することもできます。

* [Azure Container Instances のセキュリティに関する考慮事項](./container-instances-image-security.md)

* [Azure Container Registry タスクの基本イメージの更新について](../container-registry/container-registry-tasks-base-images.md)


**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: カスタムまたはサード パーティのソリューションを使用して、コンテナー イメージを修正します。 コンテナー イメージを Azure Container Registry に保存している場合は、基本 OS イメージでのセキュリティ パッチまたはその他の更新プログラムに基づいてコンテナー レジストリ内のアプリケーション イメージに対する更新を自動化するための Azure Container Registry タスクを実行することもできます。

* [Azure Container Instances のセキュリティに関する考慮事項](./container-instances-image-security.md)

* [ACR タスクの基本イメージの更新について](../container-registry/container-registry-tasks-base-images.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: イメージのスキャン結果を一定の間隔でエクスポートして結果を比較し、脆弱性が修復されていることを確認します。 コンテナー イメージを Azure Container Registry に保存している場合は、レジストリを Azure Security Center に統合することで、コンテナー イメージの脆弱性を定期的にスキャンできるようにします。 必要があれば、Azure Marketplace からのサードパーティのソリューションをデプロイして、イメージの脆弱性評価を定期的に実行します。

* [Azure Container Instances のセキュリティに関する考慮事項](./container-instances-image-security.md)

* [Azure Container Registry と Security Center の統合](../security-center/azure-container-registry-integration.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: コンテナー イメージを Azure Container Registry に保存している場合は、レジストリを Azure Security Center に統合することで、コンテナー イメージの脆弱性を定期的にスキャンし、リスクを分類できるようにします。 必要があれば、Azure Marketplace からのサードパーティのソリューションをデプロイして、イメージの定期的な脆弱性評価とリスクの分類を実行します。

* [Azure Container Instances のセキュリティに関する考慮事項](./container-instances-image-security.md)

* [Azure Container Registry と Security Center の統合](../security-center/azure-container-registry-integration.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

* [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

* [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure Container Instances および関連リソースにタグを適用し、論理的に分類してまとめます。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create.md)

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースのインベントリを作成する必要があります。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

* [Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査](../container-registry/container-registry-azure-policy.md)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 独自のソリューションまたはサードパーティのソリューションを実装して、承認されたコンテナー化アプリケーションのソフトウェア インベントリを作成します。 

プライベート レジストリ内のコンテナー イメージをスキャンし、潜在的な脆弱性を識別するためのソリューションを実装します。 Azure Container Registry に保存されているコンテナー イメージに対して脆弱性評価を実行する際は Azure Security Center からの推奨事項に従ってください。 必要があれば、Azure Marketplace からのサードパーティのソリューションをデプロイして、イメージの脆弱性評価を実行します。

異常な動作がないか Azure Container Instances ログを監視し、結果を定期的に確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

* [Azure Monitor ログによるコンテナー グループおよびインスタンスのログ記録](./container-instances-log-analytics.md)

* [Log Analytics ワークスペースについて](../azure-monitor/log-query/get-started-portal.md)

* [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/log-query/get-started-queries.md)

* [Azure Container Instances のセキュリティに関する考慮事項](./container-instances-image-security.md)
* [Azure Container Registry と Security Center の統合](../security-center/azure-container-registry-integration.md)



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Automation は、ワークロードとリソースの展開、運用、および使用停止を完全に制御します。 承認されていない Azure リソースとソフトウェア アプリケーションを削除するための独自のソリューションを実装することができます。

* [Azure Automation の概要](../automation/automation-intro.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: コンテナー イメージのタグ付けとバージョン管理で、承認済みアプリケーションを実行するイメージの追跡を支援します。
* [コンテナー イメージのタグ付けとバージョン管理に関する推奨事項](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: Azure Policy を使用して、環境内でプロビジョニングできるサービスを制限します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: コンテナー イメージのタグ付けとバージョン管理で、承認済みアプリケーションを実行するイメージの追跡を支援します。
* [コンテナー イメージのタグ付けとバージョン管理に関する推奨事項](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsbrdiv"></a>6.11: <div>ユーザーがスクリプトを使用して Azure Resource Manager と対話する機能を制限する<br></div>

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。 

* [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: Azure Container Instances にアクセスできるすべてのユーザーは、コンテナー内でスクリプトを実行できます。

さまざまな Azure サブスクリプションまたは管理グループを使用して Azure Container Instances リソースへのアクセスを管理および確認するか、または仮想ネットワークと NSG、または Azure Firewall を使用してリソースを分離します。

* [実行中の Azure Container Instances でコマンドを実行する](./container-instances-exec.md)

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create.md)

* [仮想ネットワークでのデプロイ - Azure Container Instances](./container-instances-vnet.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 業務に必要であっても、組織のリスクが高くなる可能性があるソフトウェアは、独自の仮想ネットワーク内に隔離し、Azure Firewall またはネットワーク セキュリティ グループで十分に保護する必要があります。

* [仮想ネットワークでのデプロイ - Azure Container Instances](./container-instances-vnet.md) 

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Resource Manager テンプレートを使用するか、YAML ファイルにエクスポートして、承認されたコンテナー グループ構成を維持します。 Azure Policy を使用して、関連する Azure リソースのセキュリティ構成を維持します。

* [Azure Container Instances のコンテナー グループ](container-instances-container-groups.md#deployment)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: カスタムまたはサード パーティのソリューションを使用して、コンテナー イメージを修正します。 コンテナー イメージを Azure Container Registry に保存している場合は、基本 OS イメージでのセキュリティ パッチまたはその他の更新プログラムに基づいてコンテナー レジストリ内のアプリケーション イメージに対する更新を自動化するための Azure Container Registry タスクを実行することもできます。 

* [Azure Container Registry タスクの基本イメージの更新について](../container-registry/container-registry-tasks-base-images.md)



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: プライベート レジストリ内のコンテナー イメージをスキャンし、OS 構成の潜在的な脆弱性を識別するためのソリューションを実装します。 Azure Container Registry に保存されているコンテナー イメージに対して脆弱性評価を実行する際は Azure Security Center からの推奨事項に従ってください。 必要があれば、Azure Marketplace からのサードパーティのソリューションをデプロイして、イメージの脆弱性評価を実行します。

カスタムまたはサード パーティのソリューションを使用して、コンテナー イメージを修正します。 コンテナー イメージを Azure Container Registry に保存している場合は、基本 OS イメージでのセキュリティ パッチまたはその他の更新プログラムに基づいてコンテナー レジストリ内のアプリケーション イメージに対する更新を自動化するための Azure Container Registry タスクを実行することもできます。 

* [Azure Container Instances のコンテナーの監視とスキャンに関するセキュリティについての推奨事項](./container-instances-image-security.md)

* [Azure Container Registry と Security Center の統合](../security-center/azure-container-registry-integration.md)
* [Azure Container Registry タスクの基本イメージの更新について](../container-registry/container-registry-tasks-base-images.md)



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:ARM テンプレート、YAML ファイル、カスタム Azure ポリシーの定義をソース管理に安全に保存し、管理します。

* [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos のドキュメント](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: コンテナー イメージを Azure Container Registry に保存し、RBAC を利用して、承認されたユーザーだけがイメージにアクセスできるようにします。

* [Azure での RBAC の概要](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [コンテナー レジストリの RBAC を理解する](../container-registry/container-registry-roles.md)

* [Azure で RBAC を構成する方法](../role-based-access-control/quickstart-assign-role-user-portal.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、システム構成をアラート、監査、および適用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**:適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: Azure Security Center を使用して、ご利用の Azure リソースのベースライン スキャンを実行します。

Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

* [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

* [Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査](../container-registry/container-registry-azure-policy.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: Azure Container Registry を使用してコンテナー イメージを保存している場合は、Azure Security Center を使用して、コンテナーの OS と Docker の設定のベースライン スキャンを実行します。

* [Azure Security Center のコンテナーの推奨事項を理解する](../security-center/container-security.md)


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

* [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [キー コンテナーを作成する方法](../key-vault/secrets/quick-create-portal.md)

* [マネージド ID で Key Vault の認証を提供する方法](../key-vault/general/managed-identity.md)

* [Azure Container Instances でマネージド ID を使用する方法](./container-instances-managed-identity.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

* [マネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Azure Container Instances でマネージド ID を使用する方法](./container-instances-managed-identity.md)



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft Antimalware は、Azure サービス (Azure Container Instances など) をサポートする基になるホストで有効にされます。ただし、顧客のコンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage などの非コンピューティング Azure リソースにアップロードされようとしているファイルはすべて、事前にスキャンしてください。 


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure Container Instances など) をサポートする基になるホストで有効にされます。ただし、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Azure Backup を有効にし、バックアップ ソース (コンテナー グループにマウントされたファイル共有など)、および必要な頻度と保持期間を構成します。 

* [Azure Backup を有効にする方法](../backup/index.yml)

* [Azure Container Instances に Azure ファイル共有をマウントする](./container-instances-volume-azure-files.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**:Azure コマンドライン ツールまたは SDK を使用して、Azure Key Vault 内のカスタマー マネージド キーをバックアップします。

必要に応じて、コンテナー イメージを、あるレジストリから別のレジストリにインポートすることでバックアップします。
* [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [コンテナー レジストリにコンテナー イメージをインポートする](../container-registry/container-registry-import-images.md)



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure コマンドライン ツールまたは SDK を使用して、Azure Key Vault でバックアップされたカスタマー マネージド キーの復元をテストします。

* [Azure で Azure Key Vault キーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [デプロイ データを暗号化する - Azure Container Instances](./container-instances-encrypt-data.md)



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: Azure Key Vault で論理的な削除を有効にすれば、偶発的な削除や悪意のある削除からキーを保護することができます。

* [Key Vault で論理的な削除を有効にする方法](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます。

* [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [NIST コンピューター セキュリティ インシデント処理ガイド](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

* [連続エクスポートを構成する方法](../security-center/continuous-export.md)

* [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

* [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

* [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)


**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
