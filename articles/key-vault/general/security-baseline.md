---
title: Key Vault 用の Azure セキュリティ ベースライン
description: Key Vault 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad47ac7d51de6ab497d7061961c14b5324fab931
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98202455"
---
# <a name="azure-security-baseline-for-key-vault"></a>Key Vault 用の Azure セキュリティ ベースライン

Key Vault 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](../../security/benchmarks/security-baselines-overview.md)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: Azure Key Vault を Azure Private Link と統合します。 

Azure Private Link サービスを使用すると、ご自分の仮想ネットワーク内のプライベート エンドポイント経由で Azure サービス (Azure Key Vault など) と Azure でホストされている顧客サービスやパートナー サービスにアクセスできます。

Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するためのネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute または VPN 接続、パブリック IP アドレスは必要ありません。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 最高レベルの細分性でアクセスを制御しながら Azure リソースのインスタンスに接続できます。

Key Vault を Azure Private Link と統合する方法:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure の Key Vault で構成されたリソースを保護します。 

Azure Security Center によって提供されるネットワーク セキュリティの詳細については、次の記事をご覧ください。 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 分散型サービス拒否攻撃からの保護のために、Key Vault インスタンスに関連付けられている Azure 仮想ネットワーク上で Azure DDoS Protection Standard を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

 
Azure portal を使用した Azure DDoS Protection Standard の管理: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Security Center での Azure サービス レイヤーの脅威検出: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: Azure Key Vault では、ネットワーク セキュリティ グループ (NSG) を使用しないため、Azure Key Vault のフロー ログはキャプチャされません。 代わりに、Azure Private Link を使用して Azure Key Vault インスタンスを保護し、診断設定を有効にしてメトリックと監査イベントを記録します。

Key Vault を Azure Private Link と統合する:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault のログ記録: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: この要件を満たすには、Azure Key Vault 用 Advanced Threat Protection (ATP) を構成します。 ATP によってセキュリティ インテリジェンスにレイヤーが追加されます。 このツールを使用すると、Azure Key Vault アカウントに対して害を及ぼす可能性のあるアクセスの試行または悪用を検出できます。

Azure Security Center で異常なアクティビティが検出されると、アラートが表示されます。 また、サブスクリプション管理者に、不審なアクティビティの詳細と、特定された脅威を調査して修復する方法に関する推奨事項をメールで送信することもできます。

Azure Key Vault 用 Advanced Threat Protection を設定する:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Key Vault インスタンスへのアクセスが必要なリソースについては、Azure Key Vault の Azure サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure サービス タグの概要: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Key Vault インスタンスに関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Key Vault インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.KeyVault" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用します。 次のように、Azure Key Vault に関連する組み込みのポリシー定義を使用することもできます。

Key Vault で仮想ネットワーク サービス エンドポイントを使用する必要がある

チュートリアル:コンプライアンスを強制するポリシーの作成と管理:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy のサンプル:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

クイック スタート:ポータル内でブループリントを定義して割り当てる:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Key Vault インスタンスのネットワーク セキュリティとトラフィック フローに関連するリソースにタグを使用すると、メタデータで論理的にリソースを整理できます。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

タグを使用した Azure リソースの整理:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Key Vault インスタンスに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

Azure アクティビティ ログ イベントを表示して取得する:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: 適用できません。Microsoft では、Azure Key Vault などの Azure リソースに使用するタイム ソースを、ログ内にタイムスタンプとして保持します。


**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor を介してログを取り込み、Azure Key Vault によって生成されたセキュリティ データを集計します。 Azure Monitor 内で Azure Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージに Azure Storage アカウントを使用します。 または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

Azure Key Vault のログ記録:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

クイック スタート:Azure Sentinel をオンボードする方法:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: 監査ログ、セキュリティ ログ、および診断ログにアクセスするため、Azure Key Vault インスタンスに関する診断設定を有効にします。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。

Azure Key Vault のログ記録:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、Azure Key Vault ログを保持するために使用される Log Analytics ワークスペースについて、組織のコンプライアンス規則に従って保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

データ保持期間の変更: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Key Vault で保護されたリソースについて、異常な動作がないかログの分析と監視を行い、定期的に結果を確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。 または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

クイック スタート:Azure Sentinel をオンボードする:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Monitor で Log Analytics の使用を開始する:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor でログ クエリの使用を開始する:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Security Center で、Key Vault 用 Advanced Threat Protection (ATP) を有効にします。 Azure Key Vault で診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics ワークスペースを Azure Sentinel にオンボードします。セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されます。 これにより、プレイブック (自動化されたソリューション) を作成して、セキュリティの問題を修復するために使用できます。

クイック スタート:Azure Sentinel をオンボードする:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Security Center でのセキュリティ アラートの管理と対応:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Azure Monitor のアラートを使用してイベントに応答する:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Azure Key Vault では、マルウェア対策関連のログを処理したり生成したりしません。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Key Vault では、DNS 関連のログを処理したり生成したりしません。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory で登録されたアプリケーションのインベントリと、Azure Key Vault のキー、シークレット、証明書にアクセスできるすべてのユーザー アカウントを維持します。 Azure portal または PowerShell を使用して、Key Vault へのアクセスのクエリと調整を行うことができます。 PowerShell でアクセスを表示するには、次のコマンドを使用します。

(Get-AzResource -ResourceId [KeyVaultResourceID]).Properties.AccessPolicies

Azure Active Directory にアプリケーションを登録する:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

キー コンテナーへのアクセスをセキュリティで保護する:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:適用できません。認証は Active Directory によって提供され、Azure ロールベースのアクセス制御 (Azure RBAC) でセキュリティ保護されるため、Azure Key Vault には既定のパスワードという概念がありません。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: Azure Key Vault インスタンスにアクセスできる専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理 (現在はプレビュー段階) を使用して、アクティブな管理者アカウントの数を監視します。

ID とアクセスを監視する (プレビュー):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: Azure サービス プリンシパルを AppId、TenantID、および ClientSecret と共に使用すると、アプリケーションをシームレスに認証し、Azure Key Vault のシークレットへのアクセスに使用されるトークンを取得できます。

.NET を使用した Azure Key Vault に対するサービス間認証:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory Multi-Factor Authentication を有効にし、イベント ハブ対応リソースを保護するための Azure Security Center ID とアクセス管理 (現在はプレビュー段階) の推奨事項に従います。

クラウド ベースの Azure AD Multi-Factor Authentication のデプロイの計画

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

ID とアクセスを監視する (プレビュー):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:Azure AD Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Key Vault 対応リソースを構成します。 

特権アクセス ワークステーション: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/ 

クラウド ベースの Azure AD Multi-Factor Authentication のデプロイの計画: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全ではないアクティビティが発生したときにログとアラートを生成します。 Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。 追加のログ記録を行うには、Azure Security Center のリスク検出アラートを Azure Monitor に送信し、アクショングループを使用してカスタムのアラートまたは通知を構成します。

Azure Key Vault 用 Advanced Threat Protection (ATP) を有効にして、疑わしいアクティビティに関するアラートを生成します。

Azure AD Privileged Identity Management (PIM) をデプロイする: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure Key Vault 用 Advanced Threat Protection を設定する (プレビュー): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Azure Key Vault のアラート (プレビュー): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory リスク検出: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Azure portal でのアクション グループの作成および管理: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセス ポリシーの場所の条件を構成し、ネームド ロケーションを管理します。 ネームド ロケーションを使用すると、IP アドレス範囲または国や地域の論理グループを作成できます。 機密性の高いリソース (Key Vault のシークレットなど) へのアクセスを、構成したネームド ロケーションに制限することができます。

Azure Active Directory 条件付きアクセスの場所の条件の概要: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Key Vault などの Azure リソースの主要な認証および承認システムとして Azure Active Directory (Azure AD) を使用します。 これにより、Azure ロールベースのアクセス制御 (Azure RBAC) で機密性の高いリソースを管理できるようになります。

 

クイック スタート:Azure Active Directory で新しいテナントを作成する:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) のログを確認して、Azure Key Vault 管理者ロールを持つ古いアカウントを検出します。 また、Azure AD アクセス レビューを使用して、グループ メンバーシップ、Azure Key Vault へのアクセスに使用される可能性のあるエンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的 (たとえば、90 日ごと) に確認し、正当なユーザーだけが継続してアクセスできるようにする必要があります。

Azure Active Directory のレポートと監視のドキュメント:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure AD アクセス レビューとは:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure Key Vault および Azure Active Directory の診断設定を有効にし、すべてのログを Log Analytics ワークスペースに送信します。 Log Analytics 内で、目的のアラート (無効なシークレットへのアクセスの試行など) を構成します。

Azure AD ログを Azure Monitor ログと統合する: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

古い Key Vault ソリューションからの移行: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory の Identity Protection およびリスク検出機能を使用すると、Azure Key Vault で保護されたリソースに関連して検出された疑わしいアクションへの自動応答を構成できます。 組織のセキュリティ対応を実装するには、Azure Sentinel によって自動応答を有効にする必要があります。 

Azure Active Directory ポータルのリスクの高いサインイン レポート: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

方法:リスク ポリシーを構成して有効にする: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 適用できません。Azure Key Vault ではカスタマー ロックボックスはサポートされていません。

一般提供でサポートされるサービスとシナリオ: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: タグを使用すると、Azure Key Vault 対応リソースに関する機密情報を格納または処理する Azure リソースの追跡に役立ちます。 

タグを使用した Azure リソースの整理: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: Azure Key Vault へのアクセスをセキュリティで保護するには、特定のサブネットへのアクセスを制限するように構成された仮想ネットワーク サービス エンドポイントを使用します。

ファイアウォール規則が有効になると、許可されたサブネットまたは IP アドレス範囲から要求が送信された場合にのみ、Azure Key Vault データ プレーンの操作を実行できます。 これは、Azure portal での Azure Key Vault へのアクセスにも適用されます。 Azure portal からキー コンテナーを参照することはできますが、クライアント マシンが許可リストに登録されていない場合は、キー、シークレット、または証明書を一覧表示できないことがあります。 これは、Azure Key Vault の選択機能およびその他の Azure サービスにも影響します。 キー コンテナーを一覧表示できても、キーを一覧表示できないことがあります (ファイアウォール規則によってクライアント マシンでその操作が許可されていない場合)。

Azure Key Vault のファイアウォールと仮想ネットワークを構成する: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault の仮想ネットワーク サービス エンドポイント: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Azure Key Vault 内に格納されているすべてのデータは、機密データと見なされます。 Azure Key Vault データ プレーンのアクセス制御を使用して、Azure Key Vault のシークレットへのアクセスを制御します。 Key Vault の組み込みのファイアウォールを使用して、ネットワーク層でアクセスを制御することもできます。 Azure Key Vault へのアクセスを監視するには、Key Vault の診断設定を有効にし、Azure Storage アカウントまたは Log Analytics ワークスペースにログを送信します。

キー コンテナーへのアクセスをセキュリティで保護する: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Azure Key Vault のファイアウォールと仮想ネットワークを構成する: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault のログ記録: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: 認証、管理、データ プレーン アクセスに関する Azure Key Vault へのトラフィックはすべて暗号化され、HTTPS (ポート 443) 経由で送信されます。 (ただし CRL については、トラフィックが HTTP (ポート 80) 経由になる場合があります。)Azure Key Vault は引き続き TLS 1.1 と TLS 1.0 のデータの取り込みを許可します。 クライアント側の構成により、データを TLS 1.2 に制限することもできます。



ファイアウォールの向こう側にある Azure Key Vault へのアクセス: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 適用できません。Azure Key Vault 内のすべてのデータ (シークレット、キー、および証明書) は機密性が高いと見なされます。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Key Vault インスタンスの管理とデータプレーンへのアクセスをセキュリティで保護します。

キー コンテナーへのアクセスをセキュリティで保護する:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: Microsoft では、Azure Key Vault 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

Azure Key Vault とは?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure での顧客データの保護:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: すべての管理オブジェクト (キー、証明書、およびシークレット) は、Azure Key Vault で保存時に暗号化されます。

サポート ドキュメント:

- [暗号化モデルとキー管理テーブル](../../security/fundamentals/encryption-atrest.md)


**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Monitor の Azure Key Vault Analytics ソリューションを使用して、Azure Key Vault の監査イベント ログを確認します。

Azure Monitor の Azure Key Vault Analytics ソリューション:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Microsoft では、Azure Key Vault をサポートしている基になるシステムで脆弱性の管理を行います。


**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。Microsoft では、Key Vault をサポートしている基になるシステムでパッチの管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: Microsoft では、Key Vault をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。

Azure Security Center でセキュア スコアを向上する:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (Azure Key Vault インスタンスを含む) のクエリや検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

クイック スタート:Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

現在のアカウントでアクセスできるサブスクリプションを取得する:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure ロールベースのアクセス制御 (Azure RBAC) とは

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを提供する Azure Key Vault リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、Azure Key Vault インスタンスと関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

追加 Azure サブスクリプションの作成:

https://docs.microsoft.com/azure/billing/billing-create-subscription

リソースの整理と管理のための管理グループを作成する:

https://docs.microsoft.com/azure/governance/management-groups/create

タグを使用した Azure リソースの整理: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**:コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアの一覧を定義します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用すると、次の組み込みのポリシー定義によって、顧客のサブスクリプション内に作成できるリソースの種類に制限を設けることができます。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。

チュートリアル:コンプライアンスを強制するポリシーの作成と管理: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

クイック スタート:Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースだけでなくAzure 全体を対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: Azure Policy を使用すると、次の組み込みのポリシー定義によって、顧客のサブスクリプション内に作成できるリソースの種類に制限を設けることができます。

- 許可されないリソースの種類

- 許可されるリソースの種類

チュートリアル:コンプライアンスを強制するポリシーの作成と管理: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy のサンプル: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの権限を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager (ARM) を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、Key Vault の構成を含むリソースなど、高セキュリティ環境内のリソースの作成と変更を防ぐことができます。

条件付きアクセスを使用して Azure 管理へのアクセスを管理する:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Key Vault インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.KeyVault" 名前空間で Azure Policy エイリアスを使用します。 Azure Key Vault に次のような組み込みの Azure Policy 定義を使用することもできます。

キー コンテナー オブジェクトが回復可能でなければならない

Key Vault の診断設定を Log Analytics ワークスペースにデプロイする

Key Vault で診断ログを有効にする必要がある

Key Vault で仮想ネットワーク サービス エンドポイントを使用する必要がある

Key Vault の診断設定をイベント ハブにデプロイする

Azure Key Vault インスタンスの安全な構成基準として Azure Security Center の推奨事項を使用します。

使用可能な Azure Policy エイリアスを表示する方法:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

チュートリアル:コンプライアンスを強制するポリシーの作成と管理:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure Key Vault 対応リソース全体にセキュリティで保護された設定を適用するには、Azure Policy [拒否] と [存在する場合はデプロイする] を使用します。 

チュートリアル:コンプライアンスを強制するポリシーの作成と管理:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Azure Policy の効果について: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: Azure Key Vault 対応リソースにカスタムの Azure Policy 定義を使用している場合は、Azure Repos を使用してコードを安全に格納して管理します。

Azure DevOps にコードを格納する方法: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure Repos のドキュメント: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: "Microsoft.KeyVault" 名前空間で Azure Policy エイリアスを使用して、システム構成を通知、監査、適用するためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: Azure Security Center を使用して、Azure Key Vault 対応リソースのベースライン スキャンを実行します。 

  

Azure Security Center の推奨事項を修復する方法 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。このベンチマークは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。 Azure Key Vault の論理的な削除が有効になっていることを確認します。

Azure マネージド ID と統合する方法:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

キー コンテナーを作成する方法:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Key Vault に対して認証を行う方法:

https://docs.microsoft.com/azure/key-vault/general/authentication

Key Vault のアクセス ポリシーを割り当てる方法:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。 

  

* [Azure マネージド ID と統合する方法](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [キー コンテナーを作成する方法](quick-create-portal.md)

* [Key Vault に対して認証を行う方法](authentication.md)

* [Key Vault のアクセス ポリシーを割り当てる方法](assign-access-policy-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。  
  
 資格情報スキャナーを設定する方法: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft は、基になるプラットフォーム用のマルウェア対策を処理します。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Key Vault など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

Azure Key Vault などの非コンピューティング Azure リソースにアップロードまたは送信されるコンテンツはすべて事前にスキャンします。 Microsoft は、これらのインスタンス内のデータにアクセスできません。

Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware の概要: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft は、基になるプラットフォーム用のマルウェア対策を処理します。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: 次の PowerShell コマンドを使用して、Key Vault の証明書、キー、マネージド ストレージ アカウント、およびシークレットの定期的な自動バックアップを行います。

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

必要に応じて、Azure Backup 内に Key Vault のバックアップを格納することもできます。

Key Vault の証明書をバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Key Vault のキーをバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Key Vault のマネージド ストレージ アカウントをバックアップする方法: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Key Vault のシークレットをバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure Backup を有効にする方法: https://docs.microsoft.com/azure/backup



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: 次の PowerShell コマンドを使用して、Key Vault の証明書、キー、マネージド ストレージ アカウント、およびシークレットのバックアップを行います。

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

必要に応じて、Azure Backup 内に Key Vault のバックアップを格納することもできます。

Key Vault の証明書をバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Key Vault のキーをバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Key Vault のマネージド ストレージ アカウントをバックアップする方法: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Key Vault のシークレットをバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure Backup を有効にする方法: https://docs.microsoft.com/azure/backup



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: 次の PowerShell コマンドを使用して、Key Vault の証明書、キー、マネージド ストレージ アカウント、およびシークレットのデータ復元を定期的に行います。

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Key Vault の証明書を復元する方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Key Vault のキーを復元する方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Key Vault のマネージド ストレージ アカウントを復元する方法: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Key Vault のシークレットを復元する方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: Azure Key Vault に対して論理的な削除が有効になっていることを確認します。 論理的な削除では、削除されたキーコンテナーと、キー、シークレット、証明書などのコンテナー オブジェクトを復元できます。 

Azure Key Vault の論理的な削除を使用する方法: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。 これらのプロセスでは、機密性の高いシステム (Key Vault のシークレットを使用するシステムなど) の保護に焦点を当てる必要があります。

Azure Security Center 内でワークフロー自動化を構成する方法: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center のインシデントの構造:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

お客様は、独自のインシデント対応計画の作成のために、NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます。 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。 また、サブスクリプション ( 運用、非運用など) を明確にマークし、Azure リソース (特に、Azure Key Vault のシークレットなどの機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: システムのインシデント対応機能を定期的にテストして、Azure Key Vault インスタンスと関連リソースを保護するための演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

NIST の出版物を参照してください。IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。

Azure Security Center のセキュリティ連絡先を設定する方法:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure Key Vault 対応リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。  Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 

 

連続エクスポートを構成する方法: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Azure Sentinel にアラートをストリーミングする方法: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure Key Vault で保護されたリソースを保護できます。 

 

ワークフローの自動化と Logic Apps を構成する方法: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします

**ガイダンス**: Azure Key Vault サービスに対して侵入テストを直接実行することはありませんが、シークレットのセキュリティを確保するために、Key Vault を使用している Azure リソースをテストすることをお勧めします。

侵入テストが Microsoft のポリシーに違反しないように、Microsoft の活動規則に従う必要があります。

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Microsoft が管理するクラウド インフラストラクチャ、サービス、およびアプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、次を参照してください。 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../../security/benchmarks/security-baselines-overview.md)の詳細について学習する