---
title: Azure Container Registry 用の Azure セキュリティ ベースライン
description: Azure Container Registry 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 0004b7689b6ede2200e3fe4cb06bba2bbc168d6d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259404"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure Container Registry 用の Azure セキュリティ ベースライン

Azure Container Registry 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: Azure Virtual Network では、ご利用の Azure リソースやオンプレミス リソースに合わせてセキュリティで保護されたプライベート ネットワークを実現できます。 Azure 仮想ネットワークからプライベート Azure コンテナー レジストリへのアクセスを制限することで、仮想ネットワーク内のリソースのみがレジストリにアクセスするようにすることができます。 クロスプレミスのシナリオでは、特定の IP アドレスからのレジストリ アクセスだけを許可するファイアウォール規則を構成することもできます。 ファイアウォールの内側から、ご利用のコンテナー レジストリにアクセスするためのファイアウォール アクセス規則とサービス タグを構成します。

Azure 仮想ネットワークまたはファイアウォール規則を使用して Azure コンテナー レジストリへのアクセスを制限する: https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用し、ネットワークの保護に関する推奨事項を修正することで、Azure 内でご利用のネットワーク リソースを容易に保護することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

ネットワーク リソースの保護: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用不可。 ベンチマークは、Web アプリケーションをホストしている Azure App Service またはコンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:DDoS 攻撃から保護するために、ご利用の仮想ネットワーク上で DDoS Standard 保護を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。  有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンスとともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。

Azure Security Center の Just In Time ネットワーク アクセスを使用すれば、限られた期間、承認された IP アドレスへのエンドポイントの公開を制限するように NSG を構成することができます。 また、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限する NSG 構成を推奨する Azure Security Center のアダプティブ ネットワークのセキュリティ強化を使用することもできます。

DDoS 保護を構成する方法:  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Firewall をデプロイする方法: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Security Center の統合された脅威インテリジェンスの概要: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Azure Security Center のアダプティブ ネットワークのセキュリティ強化の概要: https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center の Just In Time ネットワーク アクセス制御: https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: ご利用の Azure コンテナー レジストリを保護するために使用されているサブネットに接続された NSG 用に、ネットワーク セキュリティ グループ (NSG) フロー ログを有効にします。 フロー レコードを生成するために、NSG フロー ログを Azure Storage アカウントに記録することができます。 異常なアクティビティを調査する必要がある場合は、Azure Network Watcher パケット キャプチャを有効にします。

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher を有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: ペイロード検査能力を備えた IDS または IPS 機能をサポートする Azure Marketplace からのプランを選択します。 ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

悪意のあるトラフィックを検出および拒否できるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

Azure Marketplace:  https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Azure Firewall をデプロイする方法: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Firewall でアラートを構成する方法: https://docs.microsoft.com/azure/firewall/threat-intel


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用不可。 ベンチマークは、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: ご利用のコンテナー レジストリへのアクセスが必要なリソースについては、Azure Container Registry サービス用の仮想ネットワークサービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソース フィールドまたはターゲット フィールドにサービス タグ名 "AzureContainerRegistry" を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

サービス タグによるアクセスを許可する: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、ご利用の Azure コンテナー レジストリに関連付けられているネットワーク リソース用の標準的なセキュリティ構成を定義して実装します。 ご利用のコンテナー レジストリのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.ContainerRegistry" および "Microsoft.Network" 名前空間内で Azure Policy エイリアスを使用します。 

Azure Blueprints を使用して、Azure Resource Manager テンプレート、RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することができます。 ブループリントを新しいサブスクリプションに適用し、バージョン管理によって制御と管理を容易に微調整できます。

Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Blueprint を作成する方法: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: お客様は、Azure Blueprints を使用して、Azure Resource Manager テンプレート、RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することができます。 ブループリントを新しいサブスクリプションに適用し、バージョン管理によって制御と管理を容易に微調整できます。

Azure Blueprint を作成する方法: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: ネットワーク リソース構成を監視し、ご利用のコンテナー レジストリに関連したネットワーク リソースに関する変更を検出するには、Azure アクティビティ ログを使用します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

Azure アクティビティ ログ イベントを表示して取得する方法:  https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor でアラートを作成する方法:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Microsoft は Azure リソースのためにタイム ソースを管理していますが、コンピューティング リソースのために時刻同期設定を管理するオプションが用意されています。

Azure コンピューティング リソースの時刻同期を構成する方法: https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor を介してログを取り込んで、Azure コンテナー レジストリによって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

診断の評価と監査のための Azure Container Registry ログ:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Monitor を使用すると、ご利用のレジストリ内のユーザー駆動型イベントのリソース ログ (以前は "診断ログ" と呼ばれていました) を収集できます。 このデータを収集し使用することで、レジストリ認証イベントを監査し、プルやプッシュ イベントなどのレジストリ成果物に対する完全な活動証跡を提供すれば、ご利用のレジストリに関するセキュリティ問題を診断できるようになります。

診断の評価と監査のための Azure Container Registry ログ: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用不可。 ベンチマークは、コンピューティング リソースを対象としています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

Log Analytics ワークスペースのログ保持パラメーターを設定する方法: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないか Azure Container Registry ログを分析および監視し、結果を定期的に確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

診断の評価と監査のための Azure Container Registry ログ:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Log Analytics ワークスペースの概要: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor でカスタム クエリを実行する方法: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Log Analytics ワークスペースを使用して、ご利用の Azure コンテナー レジストリに関連するセキュリティ ログやイベントでの異常なアクティビティを監視し、アラート通知を行います。

診断の評価と監査のための Azure Container Registry ログ: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Log Analytics のログ データに関するアラートを送信する方法:  https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用不可。 Azure Container Registry では、マルウェア対策関連のログの処理または生成は行われません。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用不可。 Azure Container Registry はエンドポイントであり、通信が開始されることはありません。このサービスでは DNS に対するクエリは実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用不可。 ベンチマークは、コンピューティング リソースを対象としています。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (Azure AD) には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

Azure コンテナー レジストリごとに、組み込みの管理者アカウントが有効になっているか無効になっているかを追跡します。 使用されていない場合、アカウントは無効にします。

PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry 管理者アカウント:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションと Marketplace サービスについては、お客様が責任を負うものとします。

Azure コンテナー レジストリの既定の管理者アカウントが有効になっている場合は、複雑なパスワードが自動的に作成されます。これはローテーションされる必要があります。 使用されていない場合、アカウントは無効にします。

Azure Container Registry 管理者アカウント: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

また、コンテナー レジストリの組み込みの管理者アカウントを有効にする手順を作成します。 使用されていない場合、アカウントは無効にします。

Azure Security Center ID とアクセスの概要:  https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry 管理者アカウント:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: 可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

コンテナー レジストリに個別にアクセスするには、Azure Active Directory に統合された個別のログインを使用します。

Azure AD を使用した SSO の概要:  https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

コンテナー レジストリへの個別のログイン:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証 (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center で ID とアクセスを監視する方法: https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:MFA が構成されている PAW (特権アクセス ワークステーション) を使用して Azure リソースにログインし、構成します。

特権アクセス ワークステーションについて:  https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center の監視**: 該当なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

Azure でネームド ロケーションを構成する方法:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Azure AD インスタンスを作成して構成する方法: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

Azure AD のレポートの概要:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID アクセス レビューの使用方法:  https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意のセキュリティ情報イベント管理 (SIEM) または監視ツールとの統合が可能です。

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

Azure Activity Logs を Azure Monitor に統合する方法:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) のリスクおよび ID Protectoin 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 

Azure AD の危険なサインインを表示する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Identity Protection のリスク ポリシーを構成して有効にする方法: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 使用できません。Azure Container Registry ではカスタマー ロックボックスが現在サポートされていません。

カスタマー ロックボックスでサポートされているサービスの一覧: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure コンテナー レジストリの追跡を支援するには、リソース タグを使用します。

レジストリ内のコンテナー イメージまたはその他の成果物をタグ付けおよびバージョン管理し、イメージまたはリポジトリをロックすることで、機密情報を格納または処理するイメージの追跡を支援します。

タグを作成して使用する方法:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

コンテナー イメージのタグ付けとバージョン管理に関する推奨事項:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Azure コンテナー レジストリのコンテナー イメージをロックする:  https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 開発、テスト、および運用に対して個別のコンテナー レジストリ、サブスクリプション、管理グループを実装します。 機密データを格納または処理するリソースは、十分に分離する必要があります。

リソースは、仮想ネットワークまたはサブネットで分離し、適切にタグ付けし、さらにネットワーク セキュリティ グループ (NSG) または Azure Firewall を使用してセキュリティで保護する必要があります。

追加の Azure サブスクリプションを作成する方法:  https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法:  https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure 仮想ネットワークまたはファイアウォール規則を使用して Azure コンテナー レジストリへのアクセスを制限する: https://docs.microsoft.com/azure/container-registry/container-registry-vnet

セキュリティ構成を使用して NSG を作成する方法: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall をデプロイする方法:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Firewall でアラートまたはアラートと拒否を構成する方法:

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする自動ツールをネットワーク境界にデプロイします。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護について:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:ご利用の Azure Container Registry に接続されているクライアントがいずれも TLS 1.2 以上を確実にネゴシエートできるようにします。 Microsoft Azure リソースでは、既定で TLS 1.2 がネゴシエートされます。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

Azure での転送中の暗号化の概要:  https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Container Registry では、データの識別、分類、損失防止の各機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護の概要: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Active Directory (Azure AD) RBAC を使用して、Azure コンテナー レジストリ内のデータとリソースへのアクセスを制御します。 

Azure で RBAC を構成する方法:  https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure Container Registry のロールとアクセス許可:  https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: コンピューティング リソースでの準拠に必要な場合は、ホストベースの自動データ損失防止ソリューションなどのサードパーティ ツールを実装して、データがシステムからコピーされた場合でもデータへのアクセス制御を実施します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護について:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: すべての Azure リソースで保存時の暗号化を使用します。 既定では、Azure コンテナー レジストリ内のすべてのデータが、Microsoft のマネージド キーを使用して保存時に暗号化されます。

Azure での保存時の暗号化の概要: https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Azure Container Registry 内のカスタマー マネージド キー:  https://aka.ms/acr/cmk



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Monitor を使用すると、ご利用のレジストリ内のユーザー駆動型イベントのリソース ログ (以前は "診断ログ" と呼ばれていました) を収集できます。 このデータを収集し使用することで、レジストリ認証イベントを監査し、プルやプル イベントなどのレジストリ成果物に対する完全な活動証跡を提供すれば、ご利用のレジストリに関する操作上の問題を診断できるようになります。

診断の評価と監査のための Azure Container Registry ログ: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: ご利用のコンテナー イメージに対して脆弱性評価を実行する際は Azure Security Center からの推奨事項に従ってください。 必要があれば、Azure Marketplace からのサードパーティのソリューションをデプロイして、イメージの脆弱性評価を実行します。

Azure Security Center の脆弱性評価の推奨事項を実装する方法:  https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure Container Registry と Security Center の統合 (プレビュー):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**:Microsoft では、Azure Container Registry をサポートしている基になるシステムでパッチの管理を行います。

基本イメージに対するオペレーティング システムからの更新プログラムおよびその他のパッチが検出された場合は、コンテナー イメージの更新を自動化します。

Azure Container Registry タスクの基本イメージの更新について:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: サードパーティのソリューションを使用して、アプリケーション イメージにパッチを適用できます。  また、基本イメージでのセキュリティ パッチまたはその他の更新プログラムに基づいてコンテナー レジストリ内のアプリケーション イメージに対する更新を自動化するための Azure Container Registry タスクを実行することもできます。

ACR タスクの基本イメージの更新について:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: Azure Container Registry (ACR) を Azure Security Center に統合して、コンテナー イメージの脆弱性を定期的にスキャンできるようにします。 必要があれば、Azure Marketplace からのサードパーティのソリューションをデプロイして、イメージの脆弱性評価を定期的に実行します。

Azure Container Registry と Security Center の統合 (プレビュー):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure Container Registry (ACR) を Azure Security Center に統合することで、コンテナー イメージの脆弱性を定期的にスキャンし、リスクを分類できるようにします。 必要があれば、Azure Marketplace からのサードパーティのソリューションをデプロイして、イメージの定期的な脆弱性評価とリスクの分類を実行します。

Azure Container Registry と Security Center の統合 (プレビュー):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center の監視**: 該当なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。  テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

Azure Resource Graph を使用してクエリを作成する方法:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure サブスクリプションを表示する方法:  https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Azure RBAC について:  https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: Azure Container Registry では、レジストリ内のイメージのタグやマニフェストを含むメタデータが保持されます。 成果物にタグ付けするための推奨方法に従います。

レジストリ、リポジトリ、イメージについて: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

コンテナー イメージのタグ付けとバージョン管理に関する推奨事項: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure Container Registry では、レジストリ内のイメージのタグやマニフェストを含むメタデータが保持されます。 成果物にタグ付けするための推奨方法に従います。

レジストリ、リポジトリ、イメージについて: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

コンテナー イメージのタグ付けとバージョン管理に関する推奨事項:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースのインベントリを作成する必要があります。  

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。  環境に存在するすべての Azure リソースが承認されていることを確認します。

Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy を構成して管理する方法:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph を使用してクエリを作成する方法:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 異常な動作がないか Azure Container Registry ログを分析および監視し、結果を定期的に確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

診断の評価と監査のための Azure Container Registry ログ:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Log Analytics ワークスペースの概要:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor でカスタム クエリを実行する方法:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Automation は、ワークロードとリソースの展開、運用、および使用停止を完全に制御します。  承認されていない Azure リソースを削除するための独自のソリューションを実装することができます。 Azure Automation の概要:  https://docs.microsoft.com/azure/automation/automation-intro


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用不可。 ベンチマークは、コンピューティング リソース向けに設計されています。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: Azure Policy を活用して、ご利用の環境内でプロビジョニングできるサービスを制限します。

Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy を使用して特定のリソースの種類を拒否する方法:  https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用不可。 ベンチマークは、コンピューティング リソース向けに設計されています。



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの権限を制限する

**ガイダンス**: オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーの Azure コンピューティング リソース内でスクリプトを実行する機能を制限します。

条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法:  https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーの Azure コンピューティング リソース内でスクリプトを実行する機能を制限します。

たとえば、Windows 環境で PowerShell スクリプトの実行を制御する方法:  https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 業務に必要であっても、組織のリスクが高くなる可能性があるソフトウェアは、独自の仮想マシンや仮想ネットワーク内に隔離し、Azure Firewall またはネットワーク セキュリティ グループで十分に保護する必要があります。

仮想ネットワークを作成する方法:  https://docs.microsoft.com/azure/virtual-network/quick-create-portal

セキュリティ構成を使用して NSG を作成する方法:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy または Azure Security Center を使用して、すべての Azure リソースのセキュリティ構成を維持します。

Azure Policy を構成して管理する方法:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**:Azure Security Center の推奨事項 "仮想マシンのセキュリティ構成の脆弱性を修復する" を使用して、すべてのコンピューティング リソースのセキュリティ構成を維持します。

Azure Security Center の推奨事項を監視する方法:  https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure Security Center の推奨事項を修復する方法:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy の効果の概要:  https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用不可。 ベンチマークは、コンピューティング リソースを対象としています。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure Policy 定義を使用する場合は、Azure Repos を使用してコードを安全に格納して管理します。

Azure DevOps でコードを格納する方法:  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos のドキュメント:  https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**:適用不可。 ベンチマークは、コンピューティング リソースに適用されます。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: Azure Policy を使用して、システム構成をアラート、監査、および適用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy を構成して管理する方法:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用不可。 ベンチマークは、コンピューティング リソースに適用されます。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**:Azure Security Center を使用して、ご利用の Azure リソースのベースライン スキャンを実行します。

Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Security Center の推奨事項を修復する方法:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用不可。 ベンチマークは、コンピューティング リソースに適用されます。


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

Azure マネージド ID と統合する方法:  https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

キー コンテナーを作成する方法:  https://docs.microsoft.com/azure/key-vault/quick-create-portal

マネージド ID で Key Vault の認証を提供する方法:  https://docs.microsoft.com/azure/key-vault/managed-identity

Azure Container Registry タスクで Azure マネージド ID を使用する:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

マネージド ID を構成する方法:  https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

マネージド ID を使用して Azure コンテナー レジストリに対して認証する:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

資格情報スキャナーを設定する方法:  https://secdevtools.azurewebsites.net/helpcredscan.html


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: Azure Cloud Services と Virtual Machines に対して Microsoft Antimalware を使用し、リソースの継続的な監視と防御を行います。 Linux の場合は、サード パーティのマルウェア対策ソリューションを使用します。

Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法:  https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft Antimalware は、Azure サービス (Azure Container Registry など) をサポートする基になるホストで有効にされます。ただし、顧客のコンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage などの非コンピューティング Azure リソースにアップロードされようとしているファイルはすべて、事前にスキャンしてください。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用不可。 ベンチマークは、コンピューティング リソースを対象としています。 Microsoft は、基になるプラットフォーム用のマルウェア対策を処理します。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: ご利用の Microsoft Azure コンテナー レジストリ内のデータは常に自動的にレプリケートされることで、持続性と高可用性が確保されます。 ご利用のデータは Azure Container Registry によってコピーされるので、計画済みおよび計画外のイベントから保護されます。

必要に応じて、複数の Azure リージョンでレジストリ レプリカを維持するために、コンテナー レジストリの geo レプリケーションを行います。 

Azure Container Registry の geo レプリケーション:  https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: 必要に応じて、コンテナー イメージを、あるレジストリから別のレジストリにインポートすることでバックアップします。

Azure コマンドライン ツールまたは SDK を使用して、Azure Key Vault 内のカスタマー マネージド キーをバックアップします。

コンテナー レジストリにコンテナー イメージをインポートする:  https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Azure 上でキー コンテナーのキーをバックアップする方法:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure コマンドライン ツールまたは SDK を使用して、Azure Key Vault でバックアップされたカスタマー マネージド キーの復元をテストします。

Azure で Azure Key Vault キーを復元する方法:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: Azure Key Vault で論理的な削除を有効にすれば、偶発的な削除や悪意のある削除からキーを保護することができます。

Key Vault で論理的な削除を有効にする方法: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

Azure Security Center 内でワークフロー自動化を構成する方法:  https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center のインシデントの構造:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

顧客は、独自のインシデント対応計画の作成のために、NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます:  https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

NIST の出版物を参照してください。IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド:  https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。

Azure Security Center のセキュリティ連絡先を設定する方法:  https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

連続エクスポートを構成する方法:  https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel にアラートをストリーミングする方法:  https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

ワークフローの自動化と Logic Apps を構成する方法:  https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の活動規則 https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 に従ってください。

Microsoft マネージド クラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらの https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e で確認できます。



**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
