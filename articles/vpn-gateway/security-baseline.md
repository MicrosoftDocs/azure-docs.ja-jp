---
title: VPN Gateway に対する Azure セキュリティ ベースライン
description: VPN Gateway セキュリティ ベースラインにより、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0f1dcb82c3a8d3819e8d316ef8efe2e3256e3db4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218997"
---
# <a name="azure-security-baseline-for-vpn-gateway"></a>VPN Gateway に対する Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが VPN Gateway に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、VPN Gateway に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 VPN Gateway に適用されきない **コントロール** は、除外されています。

Azure セキュリティ ベンチマークに対する VPN Gateway の完全なマッピングを確認するには、[VPN Gateway の完全なセキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:VPN Gateway のサブネットを使用する場合は、ゲートウェイ サブネットにネットワーク セキュリティ グループ (NSG) を関連付けないようにしてください。 このサブネットにネットワーク セキュリティ グループを関連付けると、VPN ゲートウェイが正常に動作しなくなることがあります。  ただし、お客様の仮想ネットワーク内にある他の VPN Gateway 以外のサブネットについては、ネットワーク セキュリティ グループを有効にします。

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md) 

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md) 

- [Azure portal を使用してルートベースの VPN ゲートウェイを作成する](./tutorial-create-gateway-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure のネットワーク リソースを保護します。 

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:必要に応じて、ゲートウェイまたは特定の接続で、VPN ゲートウェイのパケット キャプチャを有効にします。

- [VPN ゲートウェイのパケット キャプチャを構成する](packet-capture.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、ネットワーク リソースの標準的なセキュリティ構成を定義し、実装します。

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure RBAC の割り当て、Azure Policy の割り当てなどの主要な環境成果物を、単一のブループリント定義にパッケージ化することにより、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新規または既存のサブスクリプションに適用し、バージョン管理によって制御と管理を微調整できます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [ネットワークに関する Azure Policy のサンプル](../governance/policy/samples/built-in-policies.md#network) 

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、リソースの構成を監視し、仮想ネットワーク リソースに対する変更を検出します。 Azure Monitor 内で、VPN ゲートウェイに関連する重要なリソースへの変更が発生するとトリガーされるアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:Azure Monitor 経由でアクティビティ ログと診断ログを取り込み、VPN ゲートウェイ リソースなどのネットワーク リソースによって生成されるセキュリティ データを集約します。 ログ データに対してクエリと分析を実行するには Azure Monitor を使用し、これらのログの長期およびアーカイブ ストレージには Azure ストレージ アカウントを使用します。 

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [VPN Gateway からの診断ログ イベントにアラートを設定する](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:監査ログ、セキュリティ ログ、診断ログへのアクセスのため、お使いの VPN ゲートウェイ リソースに対する診断設定を有効にします。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。 

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期およびアーカイブ ストレージには Azure Storage アカウントを使用します。 

- [Log Analytics でデータ保持期間を変更する](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) 

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかログの分析と監視を行って、定期的に結果を確認します。 Azure Monitor と Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。 

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

- [Log Analytics クエリの使用方法](../azure-monitor/logs/log-analytics-tutorial.md) 

- [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Log Analytics ワークスペースと共に Azure Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**:組織の要件に応じて、DNS ログ ソリューション用に Azure Marketplace のサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure のロールベースのアクセス制御 (Azure RBAC) を使用すると、ロールの割り当てを通じて Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 

また、Azure AD Privileged Identity Management および Azure Resource Manager を使用して、Just-In-Time アクセスを有効にすることもできます。 

- [Privileged Identity Management について](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**:可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセスの推奨事項を使用してください。 

- [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure AD MFA を有効にして、Azure Security Center ID とアクセスの推奨事項に従います。 

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**:高度な特権を必要とする管理タスクには、セキュリティで保護された Azure マネージド ワークステーション (特権アクセス ワークステーション (PAW) とも呼ばれます) を使用します。

- [セキュリティで保護された Azure マネージド ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure AD MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Active Directory セキュリティ レポートと監視を使用して、環境内で疑わしいアクティビティや安全でないアクティビティが発生したときに検出します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: Azure AD のネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。 

- [Azure AD のネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。 

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure AD の ID およびアクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml) 

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure AD サインイン アクティビティ、監査、およびリスク イベント ログ ソースにアクセスできるため、任意の SIEM または監視ツールとの統合が可能です。 

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で必要なアラートを構成できます。 

- [Azure アクティビティ ログを Azure Monitor と統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure AD Identity Protection 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。 

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:VPN ゲートウェイには、お客様の仮想ネットワークごとに専用の VM インスタンスがあります。 環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメインに対して個別の仮想ネットワーク、サブスクリプション、管理グループを使用して、分離を実装します。 アプリケーションやエンタープライズ環境で必要とされる Azure リソースへのアクセス レベルを制限できます。 Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを制御できます。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Azure Marketplace からのサードパーティ ソリューションをネットワーク境界で使用して、機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックします。 

Microsoft によって管理される基になるプラットフォームの場合、Microsoft では顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:VPN ゲートウェイにより、Azure VPN ゲートウェイとお客様のオンプレミスの VPN デバイス (S2S) または VPN クライアント (P2S) の間で、顧客パケットが暗号化されます。 VPN ゲートウェイはまた、VNet 間の暗号化もサポートします。

仮想ネットワーク内の該当するリソースについて、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

- [VPN の種類について](vpn-gateway-about-vpn-gateway-settings.md#vpntype)

- [サイト間 VPN Gateway 接続用の VPN デバイスと IPsec/IKE パラメーターについて](vpn-gateway-about-vpn-devices.md#ipsec)

- [暗号化要件と Azure VPN ゲートウェイについて](vpn-gateway-about-compliance-crypto.md)

- [サイト間 VPN または VNet 対 VNet 接続用の IPsec/IKE ポリシーを構成する](vpn-gateway-ipsecikepolicy-rm-powershell.md)

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:サードパーティのアクティブ検出ツールを使用して、お客様のテクノロジ システムによって格納、処理、または送信されるすべての機密情報 (オンサイトにあるもの、またはリモート サービス プロバイダーにあるものなど) を特定し、組織の機密情報インベントリを更新します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用してデータとリソースへのアクセスを制御します。それ以外の場合は、サービス固有のアクセス制御方法を使用します。 組み込みロール (所有者、共同作成者、ネットワーク共同作成者など) を使用して、適切なスコープにロールを割り当てます。 カスタム ロールを作成し、仮想ネットワーク、サブネット、VPN ゲートウェイ、ネットワーク インターフェイス、ネットワーク セキュリティ グループ、ルート テーブルに必要な特定のアクセス許可をロールに割り当てることにより、仮想ネットワークの機能のサブセットに対して特定のアクセス許可を割り当てます。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [仮想ネットワークを計画する](../virtual-network/virtual-network-vnet-plan-design-arm.md#permissions)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:VPN ゲートウェイなどの重要な Azure リソースに対して変更が行われたら Azure アクティビティ ログをトリガーするように、Azure Monitor アラートを構成します。 

- [VPN Gateway メトリックにアラートを設定する](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)

- [VPN Gateway からの診断ログ イベントにアラートを設定する](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: 一般的なリスク スコアリング プログラム (例: Common Vulnerability Scoring System) またはサードパーティのスキャンツールによって提供された既定のリスク評価を使用します。

- [NIST の出版物 -- 一般的な脆弱性のスコアリング システム](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内の VPN ゲートウェイに関連するすべてのリソースのクエリと検出を行います。 テナントにおける適切な (読み取り) アクセス許可を持っていること、およびサブスクリプション内のすべてのリソースを列挙できることを確実にします。 また、Azure CLI を使用して VPN Gateway リソースを列挙することもできます。

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [VPN Gateway 用の Azure CLI](/cli/azure/network/vnet-gateway)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:VPN Gateway リソースにタグを適用し、定義されている分類に従ってそれらを論理的に編成します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:タグ付け、管理グループ、個別のサブスクリプションを必要に応じて使用し、VPN Gateway リソースを整理および追跡します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。 

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md) 

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md) 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:次の組み込みポリシー定義を使用してお客様のサブスクリプション内に作成できるリソースの種類に対し、Azure ポリシーを使用して制限を適用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、サブスクリプション内のリソースのクエリと検出を行うには、Azure Resource Graph を使用します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](../virtual-network/policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:お客様は、組織のセキュリティ要件に応じて、Azure Policy の定義を割り当てることにより、リソースの作成や使用を防ぐことができます。 ただし、承認または許可されていないリソースを削除するには、独自のプロセスを実装する必要があります。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、サブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](../virtual-network/policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure AD 条件付きアクセスを使用します。 

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Policy の別名を使用して、VPN Gateway などの Azure ネットワーク リソースの構成を監査または適用するためのカスタム ポリシーを作成します。 組み込みの Azure Policy 定義を使用することもできます。

Azure Resource Manager には、JavaScript Object Notation (JSON) でテンプレートをエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすかそれを超えるように確認する必要があります。

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からの推奨事項を使用することもできます。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](../virtual-network/policy-reference.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure Resource Manager テンプレートと Azure Policy の割り当てを使用して、VPN Gateway に関連付けられている Azure リソースと関連リソースを安全に構成します。 Azure Resource Manager テンプレートは、仮想マシンを Azure リソースと共にデプロイするために使用される JSON ベースのファイルであり、カスタム テンプレートは維持される必要があります。 Microsoft では、基本テンプレートに対してメンテナンスを実行します。  Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] モードと [deploy if not exist] モードを使用します。

- [Azure Resource Manager テンプレートの作成に関する情報](../virtual-machines/windows/ps-template.md)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [仮想ネットワーク用の Azure Resource Manager テンプレート サンプル](../virtual-network/template-samples.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](../virtual-network/policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure Policy 定義、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。 Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。 

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 Azure リソースの構成を監査または適用するには、Azure Policy の別名を使用してカスタム ポリシーを作成します。 また、特定のリソースに関連する組み込みのポリシー定義を使用することもできます。 さらに、Azure Automation を使用して、構成の変更をデプロイすることもできます。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [エイリアスを使用する方法](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:Azure Policy の定義を割り当てて、VPN Gateway リソースに関連するリソース構成を測定します。 Azure Policy の分析情報を使用して、リソースの構成を監査し、重要な構成の変更について通知します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](../virtual-network/policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:VPN Gateway サービスにより、お客様の事前共有キーと証明書が、暗号化された形式で内部に格納され、転送されます。 お客様は、事前共有キーまたは証明書を独自のシステムで保護する必要があります。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Azure P2S VPN により、次の 3 つの認証方法がサポートされています。

- 証明書ベース
- RADIUS
- Azure Active Directory (Azure AD)

マネージド ID を利用できるため、Azure AD が推奨されます。

- [テナントの構成](openvpn-azure-ad-tenant.md)

- [複数のクライアント アプリでテナントを構成する](openvpn-azure-ad-tenant-multi-app.md)

- [Multi-Factor Authentication を構成する](openvpn-azure-ad-mfa.md)

- [VPN クライアントの構成](openvpn-azure-ad-client.md)

- [マネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳しくは、「[Azure Security ベンチマーク:データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**:Azure Resource Manager を使用して、VPN Gateway リソースをデプロイします。 Azure Resource Manager には、VPN Gateway リソースを復元するためのバックアップとして使用できるテンプレートをエクスポートする機能があります。 Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。

- [Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)

- [仮想ネットワーク用の Azure Resource Manager テンプレート サンプル](../virtual-network/template-samples.md)

- [リソース グループ - テンプレートのエクスポート](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation の概要](../automation/automation-intro.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**:Azure Resource Manager を使用して、VPN Gateway リソースをデプロイします。 Azure Resource Manager には、VPN Gateway と関連リソースを復元するためのバックアップとして使用できるテンプレートをエクスポートする機能があります。 Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [リソース グループ - テンプレートのエクスポート](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation の概要](../automation/automation-intro.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:必要に応じて、分離サブスクリプションに Azure Resource Manager テンプレートのデプロイを定期的に実行できるようにします。

- [ARM テンプレートと Azure portal でリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure DevOps を使用して、カスタム Azure Policy 定義や Azure Resource Manager テンプレートなどのコードを安全に格納し、管理します。 Azure DevOps で管理するリソースを保護するには、特定のユーザー、組み込みセキュリティ グループ、または Azure Active Directory (Azure AD) (Azure DevOps に統合されている場合) で定義されているグループに、アクセス許可を付与または拒否できます。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

- [Azure Storage Blob の論理的な削除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールと、検出からインシデント後のレビューまでのインシデント対応と管理のフェーズを定義する、書面によるインシデント対応計画があることを確認します。 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [独自のインシデント対応計画を作成するために NIST の「コンピューター セキュリティ インシデント対応ガイド」を使用する](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してサブスクリプションをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいてアラートの修復に優先順位を付けることは、お客様の責任です。 

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて対応計画を見直します。 

- [NIST の出版物 -- IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 

- [連続エクスポートを構成する方法](../security-center/continuous-export.md) 

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して自動的に応答をトリガーし、Azure リソースを保護できます。 

- [Security Center でワークフロー自動化を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する