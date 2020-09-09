---
title: Data Lake Analytics 用の Azure セキュリティ ベースライン
description: Data Lake Analytics セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 33084d72e26f2926751def686c0a4c1d0485a5c2
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005132"
---
# <a name="azure-security-baseline-for-data-lake-analytics"></a>Data Lake Analytics 用の Azure セキュリティ ベースライン

Data Lake Analytics 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:Data Lake Analytics のファイアウォール設定を使用して、外部 IP 範囲を制限し、オンプレミスのクライアントとサード パーティのサービスからのアクセスを許可します。 ファイアウォール設定の構成は、ポータル、REST API、または PowerShell を使用して行うことができます。

* [ファイアウォール規則](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Azure PowerShell を使用する Azure Data Lake Analytics の管理](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: 適用できません。Azure Data Lake Analytics は仮想ネットワークでは実行されません。フェデレーション クエリを使用する場合、発信呼び出しは顧客の仮想ネットワークを経由してルーティングするように構成することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:適用できません。このコントロールは、Azure App Service または IaaS インスタンス上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Data Lake Analytics のファイアウォール設定を使用して、外部 IP 範囲を制限し、オンプレミスのクライアントとサード パーティのサービスからのアクセスを許可します。 ファイアウォール設定の構成は、ポータル、REST API、または PowerShell を使用して行うことができます。

* [ファイアウォール規則](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Azure PowerShell を使用する Azure Data Lake Analytics の管理](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:適用できません。Data Lake Analytics は、顧客の仮想ネットワーク内では実行されず、ネットワーク セキュリティ グループ (NSGs) を使用してネットワーク フロー ログを記録することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: 適用できません。Data Lake Analytics は、顧客のネットワークにデプロイされない PaaS オファリングです。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:適用できません。Data Lake Analytics は、顧客の仮想ネットワーク内では実行されず、ネットワーク セキュリティ グループ (NSGs) を使用することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: 適用できません。Data Lake Analytics は、顧客の仮想ネットワーク内では実行されず、ネットワーク セキュリティ グループ (NSGs) を使用することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: 適用できません。Data Lake Analytics は、顧客の仮想ネットワーク内では実行されず、ネットワーク セキュリティ グループ (NSGs) を使用することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: 適用できません。Data Lake Analytics は、顧客の仮想ネットワーク内では実行されず、ネットワーク セキュリティ グループ (NSGs) を使用することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: 適用できません。Microsoft が、Data Lake Analytics のタイム ソースを管理します。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor を介してログを取り込み、Data Lake Analytics の ’監査’ や ’要求’ 診断などのセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。必要に応じて、不変ストレージや保有期間の保持の強制などのセキュリティ機能を使用できます。

または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

* [Azure Data Lake Analytics の診断ログへのアクセス](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Data Lake Analytics の診断設定を有効にして、監査ログと要求ログにアクセスします。 これには、イベント ソース、日付、ユーザー、timestamp、およびその他の便利な要素などのデータが含まれます。

* [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Azure でのログ記録とログのさまざまな種類について](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期およびアーカイブ ストレージには Azure Storage アカウントを使用します。

* [Log Analytics でデータ保持期間を変更する](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Azure Storage アカウント ログの保持ポリシーを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Data Lake Analytics のリソースについて、異常な動作がないかログの分析と監視を行い、定期的に結果を確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。 または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Log Analytics ワークスペースの詳細について](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Azure Monitor でカスタム クエリを実行する方法](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Data Lake Analytics の診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics ワークスペースを Azure Sentinel にオンボードします。これは、セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されるためです。 これにより、プレイブック (自動化されたソリューション) を作成して、セキュリティの問題を修復するために使用できます。

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Log Analytics のログ データに関するアラートを送信する方法](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

* [Azure Data Lake Analytics の診断ログへのアクセス](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Data Lake Analytics では、マルウェア対策関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 組織のニーズに応じて、DNS ログ ソリューション用に Azure Marketplace のサードパーティ製ソリューションを実装します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。このコントロールは、顧客が基盤となるオペレーティング システムにアクセスできるコンピューティング リソースを対象としています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure AD の組み込みロールは、明示的に割り当てる必要があり、クエリ可能でなければなりません。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

* [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:Data Lake Analytics には既定のパスワードの概念がありません。認証は、Azure Active Directory によって提供され、Azure ロールベースのアクセス制御 (Azure RBAC) によってセキュリティ保護されるためです。

* [Azure Data Lake Analytics の概要](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。

また、Azure AD Privileged Identity Management および Azure Resource Manager を使用して、Just-In-Time アクセスを有効にすることもできます。

* [Privileged Identity Management について](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**:可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセスの推奨事項を使用してください。

* [Azure AD を使用した SSO の概要](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory の多要素認証 (MFA) を有効にし、Data Lake Analytics リソースを保護するための Azure Security Center ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center で ID とアクセスを監視する方法](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**:高度な特権を必要とする管理タスクには、セキュリティで保護された Azure マネージド ワークステーション (特権アクセス ワークステーション (PAW) とも呼ばれます) を使用します。

* [セキュリティで保護された Azure マネージド ワークステーションを理解する](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-managed-workstation)

* [Azure AD MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが環境で発生したときに、Azure Active Directory セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

* [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: Azure AD のネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。

* [Azure AD のネームド ロケーションを構成する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD には、Data Lake Analytics リソースに対するクライアントのアクセスをきめ細かく制御するロールベースのアクセス制御 (RBAC) が用意されています。

* [Azure AD インスタンスを作成して構成する方法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、Azure AD の ID およびアクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

* [Azure AD のレポートの概要](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure AD の ID およびアクセス レビューの使用方法](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Data Lake Analytics および Azure Active Directory の診断設定を有効にし、すべてのログを Log Analytics ワークスペースに送信します。 Log Analytics 内で、目的のアラート (無効なシークレットへのアクセスの試行など) を構成します。

* [Azure AD ログを Azure Monitor ログと統合する](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウントのサインイン動作の偏差に関するアラートを生成する

**ガイダンス**:Azure Active Directory のリスクおよび ID Protection 機能を使用して、Data Lake Analytics リソースに関連して検出された疑わしいアクションに対する自動応答を構成します。 組織のセキュリティ対応を実装するには、Azure Sentinel によって自動応答を有効にする必要があります。

* [Azure AD の危険なサインインを表示する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 適用できません。Azure Data Lake Analytics ではカスタマー ロックボックスはサポートされていません。

* [一般提供でサポートされるサービスとシナリオ](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Data Lake Analytics リソースを追跡しやすくするには、タグを使用します。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 環境やデータの機密度など、個々のセキュリティ ドメイン用の個別のサブスクリプション、管理グループを使用して分離を実装します。 Data Lake Analytics を制限して、アプリケーションやエンタープライズ環境で必要とされる Data Lake Analytics リソースへのアクセス レベルを制御できます。 ファイアウォール ルールが構成されている場合、指定された一連のネットワークを介してデータを要求するアプリケーションのみが、Data Lake Analytics リソースにアクセスできます。 Azure AD RBAC を使用して Azure Data Lake Analytics へのアクセスを制御できます。

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [ロールベースのアクセス制御の管理](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-role-based-access-control)

* [ファイアウォール規則](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Azure PowerShell を使用する Azure Data Lake Analytics の管理](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: データ損失防止機能は、Azure Data Lake Analytics リソースではまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft では顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

* [Azure Storage アカウントをセキュリティで保護する方法](https://docs.microsoft.com/azure/storage/common/storage-security-guide)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:Microsoft Azure リソースでは、既定で TLS 1.2 をネゴシエートします。 Data Lake Analytics リソースに接続しているすべてのクライアントが TLS 1.2 以上を使用してネゴシエートできることを確認します。

* [操作例の一覧](https://docs.microsoft.com/rest/api/datalakeanalytics/operations/list)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Data Lake Analytics リソースでは、データ特定機能をまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: ロールベースのアクセス制御 (RBAC) を使用して、ユーザーがサービスと対話する方法を制御します。

* [ロールベースのアクセス制御の管理](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-role-based-access-control)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: データは既定の Data Lake Storage Gen1 アカウントに格納されます。 保存データに関しては、Data Lake Storage Gen1 では "既定でオン" の透過的な暗号化がサポートされます。

* [Azure Data Lake Storage Gen1 でのデータの暗号化](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、Azure Data Lake Analytics リソースの実稼働インスタンスに対して変更が行われたときのアラートを作成します。

* [Azure アクティビティ ログ イベントのアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: ご利用の Azure Data Lake Analytics リソースをセキュリティで保護する方法については Azure Security Center からの推奨事項に従ってください。

Microsoft では、Azure Data Lake Analytics をサポートしている基になるシステムで脆弱性の管理を行います。

* [Azure Security Center の推奨事項について](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**:適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: 一般的なリスク スコアリング プログラム (例: Common Vulnerability Scoring System) またはサードパーティのスキャンツールによって提供された既定のリスク評価を使用します。

* [NIST の出版物 -- 一般的な脆弱性のスコアリング システム](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリおよび検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Azure Resource Graph Explorer で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

* [Azure Resource Graph Explorer を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure サブスクリプションを表示する方法](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC について](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:Azure Data Lake Analytics リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、Azure Policy を使用し、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類を制限します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

さらに、Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

* [ARM へのアクセスをブロックするように条件付きアクセスを構成する方法](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Data Lake Analytics の構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.DataLakeAnalytics" 名前空間で Azure Policy エイリアスを使用します。 次のように、Azure Data Lake Analytics に関連する組み込みのポリシー定義を使用することもできます。
- Data Lake Analytics の診断ログを有効にする必要がある

* [使用可能な Azure Policy エイリアスを表示する方法](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy の効果について](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなど、ご利用のコードを安全に格納して管理するには、Azure Repos を使用します。Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

* [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure DevOps でのアクセス許可とグループについて](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成するには、"Microsoft.DataLakeAnalytics" 名前空間で Azure Policy エイリアスを使用します。 Azure Data Lake Analytics リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] を使用します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: 適用できません。Data Lake Analytics では、顧客が管理する必要のあるシークレットは公開されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: 適用できません。Data Lake Analytics では、Azure マネージド ID はサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されたマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Data Lake Analytics など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data Lake Analytics、Blob Storage などの Azure リソースにアップロードされるすべてのコンテンツを事前にスキャンします。Microsoft は、これらのインスタンス内のデータにアクセスできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。このコントロールは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Data Lake Analytics ジョブ ログとデータ出力は、基になる Data Lake Storage Gen1 サービスに格納されます。 ADLCopy、Azure PowerShell、Azure Data Factory など、データをコピーするさまざまな方法があります。 また、Azure Automation を使用して、定期的にデータを自動的にバックアップすることもできます。

* [Storage Explorer を使用して Azure Data Lake Storage Gen1 リソースを管理する](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)

* [Azure Storage Blob から Azure Data Lake Storage Gen1 にデータをコピーする](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)

* [Azure Automation の概要](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Data Lake Analytics ジョブ ログとデータ出力は、基になる Data Lake Storage Gen1 サービスに格納されます。 ADLCopy、Azure PowerShell、Azure Data Factory など、データをコピーするさまざまな方法があります。

* [Storage Explorer を使用して Azure Data Lake Storage Gen1 リソースを管理する](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)

* [Azure Storage Blob から Azure Data Lake Storage Gen1 にデータをコピーする](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:データの整合性をテストするために、バックアップ データのデータ復元を定期的に実行します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Data Lake Storage Gen1 または Azure Storage 内に格納されている Data Lake Analytics バックアップは、既定で暗号化をサポートしており、無効にすることはできません。 バックアップを機密データとして扱い、このベースラインの一部として関連するアクセスとデータ保護制御を適用する必要があります。

* [Azure Data Lake Storage Gen1 に格納されているデータのセキュリティ保護](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data)

* [Azure Storage 内のデータへのアクセスの承認](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

* [Security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [タグを使用した Azure リソースの整理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて対応計画を見直します。

* [NIST の出版物 -- IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

* [連続エクスポートを構成する方法](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Azure Sentinel にアラートをストリーミングする方法](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。

* [ワークフローの自動化と Logic Apps を構成する方法](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

* [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
