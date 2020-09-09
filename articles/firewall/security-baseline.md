---
title: Azure Firewall 用の Azure セキュリティ ベースライン
description: Azure Firewall セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関するレコメンデーションを実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: firewall
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 869c6590146561f6f2f50694c5cc3f79530dad25
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854157"
---
# <a name="azure-security-baseline-for-azure-firewall"></a>Azure Firewall 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインは、[Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)のガイダンスを Azure Firewall に適用します。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御**によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Firewall に適用できる関連ガイダンスによって定義されています。 Azure Firewall に適用できない**制御**は、除外されています。 Azure Firewall を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Policy セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳しくは、「[Azure Security ベンチマーク:ネットワークのセキュリティ](/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure Firewall は、ファイアウォールで処理されるトラフィックをログに記録するために Azure Monitor に統合されています。

さらに、Azure Security Center を使用し、ネットワークの保護に関するレコメンデーションに従って、Azure Firewall に関連するネットワーク リソースをセキュリティで保護できます。

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 脅威インテリジェンスベースのフィルター処理を有効にして、既知の悪意のある IP アドレスとドメインとの間のトラフィックの警告と拒否を行います。 ファイアウォール用に脅威インテリジェンスベースのフィルター処理を有効にして、既知の悪意のある IP アドレスとドメインとの間のトラフィックの警告と拒否を行うことができます。

- [Azure Firewall の脅威インテリジェンスベースのフィルター処理](threat-intel.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Firewall では、サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。

Azure Firewall サービス タグは、ネットワーク ルールのターゲット フィールドで使用し、Azure Firewall でネットワーク アクセス制御を定義できます。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。

さらに、IP グループなどのユーザー定義のタグもサポートされており、ネットワーク ルールやアプリケーション ルールで使用できます。 アプリケーション ルールでの FQDN タグは、必要なアウトバウンド ネットワーク トラフィックがファイアウォールを通過できるようにするためにサポートされています。

独自のサービス タグを作成したり、タグに含まれる IP アドレスを指定したりすることはできないので注意してください。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

 

- [Azure Firewall サービス タグ](service-tags.md)

- [利用可能なサービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)

- [Azure Firewall での IP グループ](ip-groups.md)

- [FQDN タグの概要](fqdn-tags.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Firewall では、Azure policy がまだ完全にサポートされていません。 Azure Firewall Manager を使用して、セキュリティ構成の標準化を実現できます。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、RBAC コントロール、ポリシーなどの主要な環境アーティファクトを単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションに適用し、バージョン管理によって制御と管理を微調整できます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](/azure/governance/policy/samples/#network)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用してリソース構成を監視し、Azure Firewall リソースに対する変更を検出します。 重要なリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure Firewall のログとメトリックを監視する](/azure/firewall/tutorial-diagnostics)

- [Azure アクティビティ ログ イベントを表示して取得する方法](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md) 

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳しくは、「[Azure Security ベンチマーク:ログ記録と監視](/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft は、Azure Firewall 用の Azure リソースのタイム ソースを保持しています。 ユーザーは、このアクセスまたは環境内で使用するタイム サーバーを許可するネットワーク ルールを作成する必要があります。

- [NTP サーバー アクセス](protect-windows-virtual-desktop.md#additional-considerations)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: ログ データを有効にし、Azure Sentinel またはサードパーティの SIEM にオンボードして、さまざまなログのセキュリティ ログの一元管理を可能にします。

アクティビティ ログを使用すると、Azure Firewall での操作を監査したり、リソースに対するアクションを監視したりできます。 アクティビティ ログには、読み取り操作 (GET) を除く、リソースへのすべての書き込み操作 (PUT、POST、DELETE) が含まれています。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。

Azure Firewall では、次の診断ログを提供して、お客様のアプリケーションとネットワーク ルールに関する情報も提供します。

アプリケーション ルール ログ:構成されているアプリケーション ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます。

ネットワーク ルール ログ:構成されているネットワーク ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます

注:両方のログは、環境内の Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信されて、Azure Monitor ログに送信できます。

- [Azure Firewall ログ](logs-and-metrics.md)

アクティビティ ログ内のリソース アクションの一覧:Azure Resource Manager のリソース プロバイダー操作

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/platform/diagnostic-settings.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:アクティビティ ログを使用すると、Azure Firewall での操作を監査したり、リソースに対するアクションを監視したりできます。 アクティビティ ログには、読み取り操作 (GET) を除く、Azure リソースへのすべての書き込み操作 (PUT、POST、DELETE) が含まれています。 Azure Firewall では、次の診断ログを提供して、お客様のアプリケーションとネットワーク ルールに関する情報も提供します。 

アプリケーション ルール ログ:構成されているアプリケーション ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます。

ネットワーク ルール ログ:構成されているネットワーク ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます

両方のログは、環境内の Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信されて、Azure Monitor ログに送信できることに注意してください。

- [Azure Firewall ログ](logs-and-metrics.md)

- [アクティビティ ログ内のリソース アクションの一覧](../role-based-access-control/resource-provider-operations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って、Log Analytics ワークスペースの保持期間を設定できます。 データ保持期間は、選択された価格レベルに応じて、すべてのワークスペースで 30 から 730 日 (2 年) に構成できます。

ログ ストレージのリテンション期間には、次の 3 つのオプションがあります。

ストレージ アカウントは、ログを長期間保存し、必要に応じて参照する場合に最適です。

イベント ハブは、他のセキュリティ情報/イベント管理 (SEIM) ツールと統合してリソースに関するアラートを取得する場合に便利なオプションです。

Azure Monitor ログは、アプリケーションをリアルタイムに監視したり、傾向を見たりする一般的な用途に最適です。

- [Azure Firewall のログとメトリック](logs-and-metrics.md)

- [Log Analytics でデータ保持期間を変更する](../azure-monitor/platform/manage-cost-storage.md)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Firewall は、ファイアウォール ログの表示と分析について Azure Monitor と統合されています。 Log Analytics、Azure Storage、または Event Hubs にログを送信できます。 Log Analytics や、Excel や Power BI などのさまざまなツールで分析できます。 Azure Firewall ログにはいくつかの種類があります。

アクティビティ ログを使用すると、Azure Firewall での操作を監査したり、リソースに対するアクションを監視したりできます。 アクティビティ ログには、読み取り操作 (GET) を除く、リソースへのすべての書き込み操作 (PUT、POST、DELETE) が含まれます。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。

Azure Firewall では、診断ログを提供して、お客様のアプリケーションとネットワーク ルールに関する情報も提供します。

構成されているアプリケーション ルールのいずれかと一致する新しい各接続によって、許可/拒否された接続のログが生成されると、アプリケーション ルール ログが作成されます。 

構成されているネットワーク ルールのいずれかと一致する新しい各接続によって、許可/拒否された接続のログが生成されると、ネットワーク ルール ログが作成されます

両方のログは、環境内の Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信されて、Azure Monitor ログに送信できることに注意してください。

Azure Monitor ログは、アプリケーションをリアルタイムに監視したり、傾向を見たりする一般的な用途に使用できます。

- [Azure Firewall のログとメトリック](logs-and-metrics.md)

- [診断ログ](logs-and-metrics.md#diagnostic-logs)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Log Analytics ワークスペースと共に Azure Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。 

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳しくは、「[Azure Security ベンチマーク:ID およびアクセス制御](/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure AD の組み込みロールは、明示的に割り当てる必要があり、クエリ可能でなければなりません。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

また、Microsoft サービスの Azure AD Privileged Identity Management の特権ロール、および Azure Resource Manager を使用して、Just-In-Time/Just-Enough-Access を有効にすることもできます。

- [Privileged Identity Management について](/azure/active-directory/privileged-identity-management)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**:可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

- [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory の Multi-Factor Authentication (MFA) を有効にし、Azure Security Center ID とアクセス管理のレコメンデーションに従います。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: 多要素認証 (MFA) が構成された PAW (特権アクセス ワークステーション) を使用してログインし、Azure Firewall と関連リソースを構成します。 

- [特権アクセス ワークステーションについて](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが環境で発生したときに、Azure Active Directory セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。 

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。 

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。 

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

- [Azure AD のレポートの概要](/azure/active-directory/reports-monitoring/)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure AD サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM/監視ツールとの統合が可能です。 

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。 

- [Azure アクティビティ ログを Azure Monitor に統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure AD のリスクおよび ID Protectoin 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。 

- [Azure AD の危険なサインインを表示する方法](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳しくは、「[Azure Security ベンチマーク:データ保護](/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:機密情報を格納または処理する Azure Firewall および関連リソースを追跡しやすくするには、タグを使用します。 

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。 アプリケーションやエンタープライズ環境で必要とされる Azure Firewall リソースへのアクセス レベルを制限できます。 Azure Active Directory のロールベースのアクセス制御を使用して、Azure リソースへのアクセスを制御できます。 

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](../governance/management-groups/create.md)

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする、Azure Marketplace からのサードパーティ ソリューションをネットワーク境界で利用します。 

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 AzureFirewall と関連リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。 

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。 

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: サードパーティ アクティブ検出ツールを使用して、Azure Firewall と関連リソースを使用する Azure リソースに格納されているすべての機密情報を識別し、組織の機密情報のインベントリを更新します。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Active Directory のロールベースのアクセス制御 (RBAC) を使用して、Azure Firewall と関連リソースへのアクセスを制御します。

- [Azure で RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Firewall と関連リソースを使用するすべての Azure リソースで保存時の暗号化を使用します。 Microsoft では、Azure に暗号化キーの管理を許可することをお勧めしていますが、一部のインスタンスでユーザーが自身のキーを管理するという選択肢もあります。 

- [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md)

- [ユーザーが管理する暗号化キーを構成する方法](../storage/common/storage-encryption-keys-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、Azure Firewall に変更が加えられたときのアラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳しくは、「[Azure Security ベンチマーク:インベントリと資産の管理](/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: Azure Firewall と関連リソースにタグを適用して、それらを論理的に整理して分類するためのメタデータを提供します。 

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用して、Azure Firewall や関連リソースを整理し追跡します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](../governance/management-groups/create.md)

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、構成を含む、承認された Azure Firewall リソースのインベントリを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Resource Graph を使用して、サブスクリプション内の Azure Firewall リソースのクエリまたは検出を行います。 環境に存在するすべての Azure Firewall と関連リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 承認されていない Azure Firewall と関連リソースを削除するための独自のプロセスを実装します。 サードパーティ ソリューションを使用して、承認されていない Azure Firewall と関連リソースを特定することもできます

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: Azure Policy を使用して、環境内でプロビジョニングできるサービスを制限します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。 

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: ビジネスの運営に必要なアプリケーション、または組織のリスク プロファイルが異なる環境は、個別の Azure Firewall インスタンスで隔離し、分離する必要があります。

- [Azure portal を使用して Azure Firewall をデプロイして構成する](deploy-cli.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳しくは、「[Azure Security ベンチマーク:セキュリティで保護された構成](/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Resource Manager には、JavaScript Object Notation (JSON) でテンプレートをエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすかそれを超えるように確認する必要があります。

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からのレコメンデーションを使用することもできます。

現時点では、Azure Policy は完全にはサポートされていません。 

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure Firewall および関連リソース全体にセキュリティで保護された設定を適用するには、Azure Policy [拒否] と [存在する場合はデプロイする] を使用します。  さらに、Azure Resource Manager テンプレートを使用して、組織に必要な Azure Firewall および関連リソースのセキュリティ構成を維持できます。

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: Azure DevOps を使用して、カスタム Azure ポリシーや Azure Resource Manager テンプレートなどのコードを安全に格納し、管理します。 Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

- [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: Azure Policy を使用して、Azure Firewall と関連リソースの標準的なセキュリティ構成を定義して実装します。 ご利用の Azure Firewall リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 また、特定のリソースに関連する組み込みのポリシー定義を使用することもできます。  

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [エイリアスを使用する方法](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用して、Azure Resource Manager への Azure AD 認証をサポートする任意のサービスに対して認証することができ、API/Azure Portal/CLI/PowerShell で使用できます。

- [マネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳しくは、「[Azure Security ベンチマーク:データの復旧](/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Azure Resource Manager を使用して、Azure Firewall と関連リソースを JavaScript Object Notation (JSON) テンプレートにエクスポートして、それを Azure Firewall と関連構成のバックアップとして使用できます。  また、Azure portal から Azure Firewall のテンプレートのエクスポート機能を使用して Azure Firewall 構成をエクスポートすることもできます。  バックアップ スクリプトを自動的に実行するには、Azure Automation を使用します。

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [テンプレートを使用して Azure Firewall をデプロイする](deploy-template.md)

- [Microsoft ネットワーク Azure Firewall テンプレート リファレンス](/azure/templates/microsoft.network/azurefirewalls)

- [Azure Automation について](../automation/automation-intro.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Resource Manager を使用して、Azure Firewall と関連リソースを JavaScript Object Notation (JSON) テンプレートにエクスポートして、それを Azure Firewall と関連構成のバックアップとして使用できます。  また、Azure portal から Azure Firewall のテンプレートのエクスポート機能を使用して Azure Firewall 構成をエクスポートすることもできます。

- [テンプレートを使用して Azure Firewall をデプロイする](deploy-template.md)

- [Microsoft ネットワーク Azure Firewall テンプレート リファレンス](/azure/templates/microsoft.network/azurefirewalls)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Resource Manager テンプレートによってサポートされるファイルを使用して、定期的に復元を実行できることを確認します。  

- [テンプレートを使用して Azure Firewall をデプロイする](deploy-template.md)

- [Microsoft ネットワーク Azure Firewall テンプレート リファレンス](/azure/templates/microsoft.network/azurefirewalls)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure DevOps を使用して、Azure ポリシー、Azure Resource Manager テンプレートなどのコードを安全に格納し、管理します。 Azure DevOps で管理するリソースを保護するには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

- [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳しくは、「[Azure Security ベンチマーク:インシデント対応](/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [独自のインシデント対応計画の作成のための NIST の「コンピューター セキュリティ インシデント対応ガイド」](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。 

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。 

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。 

- [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。

連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。 

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳しくは、「[Azure Security ベンチマーク:侵入テストとレッド チーム演習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
