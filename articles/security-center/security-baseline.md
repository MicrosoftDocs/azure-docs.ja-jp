---
title: Security Center 用の Azure セキュリティ ベースライン
description: Security Center セキュリティ ベースラインにより、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されています。
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 330b466360cc5687f78db81fbb4545e1c3409098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729880"
---
# <a name="azure-security-baseline-for-security-center"></a>Security Center 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)のガイダンスが Azure Security Center に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によって分類されます。これは、Azure セキュリティ ベンチマークと、Azure Security Center に適用できる関連ガイダンスによって定義されています。 Azure Security Center に適用できない **制御** は、除外されています。 Azure Security Center が Azure セキュリティ ベンチマークに完全にマップされる方法については、[完全な Azure Security Center セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure Security Center は、主要な Azure オファリングです。 仮想ネットワーク、サブネット、またはネットワーク セキュリティ グループを Security Center に直接関連付けることはできません。 コンピューティング リソースに対してデータ収集を有効にした場合、Log Analytics ワークスペースを使用して収集されたデータが Security Center に格納されます。このワークスペースは、仮想ネットワーク内のプライベート エンドポイント経由でワークスペース データにアクセスするために Private Link を使用するように構成できます。 また、データ収集を使用する場合、サーバーにデプロイされている Log Analytics エージェントに依存する Security Center によって、セキュリティ データが収集され、これらのコンピューティング リソースが保護されます。 Log Analytics エージェントにより、Security Center で適切に動作するために、特定のポートとプロトコルが開かれる必要があります。 ネットワークをロック ダウンして、これらの必要なポートとプロトコルのみを許可し、アプリケーションがネットワーク セキュリティ グループを介して動作するために必要な規則のみを追加します。

- [Azure Security Center でのデータ収集](security-center-enable-data-collection.md)

- [ネットワーク セキュリティ グループを使用したネットワーク トラフィックのフィルター処理](../virtual-network/tutorial-filter-network-traffic.md)

- [Log Analytics エージェントを使用するためのファイアウォールの要件](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [Azure Private Link について](../private-link/private-link-overview.md) 

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Security Center オファリングは仮想ネットワークと直接統合されませんが、ご利用のネットワークにデプロイされている Log Analytics エージェントで構成されたサーバーからデータを収集することができます。 Security Center にデータを送信するように構成されているサーバーによって、特定のポートとプロトコルが正常に通信できるようにする必要があります。 Azure Policy を使用して、これらのネットワーク リソースの標準的なセキュリティ構成を定義し、適用します。

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、ロールの割り当て、Azure Policy 割り当てなどの主要な環境成果物を単一のブループリント定義にパッケージ化すると、大規模な Azure デプロイを簡略化することもできます。 新しいサブスクリプションにブループリントを適用して、Security Center 構成および関連するネットワーク リソースを一貫して安全にデプロイできます。

- [Azure Security Center でのデータ収集](security-center-enable-data-collection.md)

- [Log Analytics エージェントを使用するためのファイアウォールの要件](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [ネットワークに関する Azure Policy のサンプル](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Security Center オファリングは仮想ネットワークと直接統合されませんが、ご利用のネットワークにデプロイされている Log Analytics エージェントで構成されたサーバーからデータを収集することができます。 Security Center にデータを送信するように構成されているサーバーによって、特定のポートとプロトコルが正常に通信できるようにする必要があります。 Azure Policy を使用して、これらのネットワーク リソースの標準的なセキュリティ構成を定義し、適用します。

ネットワーク セキュリティ グループや、セキュリティ ログを Azure Security Center に送信するように構成されているネットワーク上のサーバーなどの他のリソースには、リソース タグを使用します。 個々のネットワーク セキュリティ グループの規則については、"説明" フィールドを使用して、ネットワークとの間で送受信されるトラフィックを許可する規則を文書化します。 

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいたリソースの検索やアクション実行ができます。 

- [Azure Security Center でのデータ収集](security-center-enable-data-collection.md)

- [Log Analytics エージェントを使用するためのファイアウォールの要件](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md) 

- [Azure 仮想ネットワークの作成方法](../virtual-network/quick-create-portal.md) 

- [ネットワーク セキュリティ グループの規則を使用してネットワーク トラフィックをフィルター処理する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、リソース構成を監視し、Azure Security Center に関連するネットワーク リソースの変更を検出します。 Azure Monitor でアラートを作成して、重要なリソースへの変更が発生したときに通知を受け取ります。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:中央の Log Analytics ワークスペースを使用して、Azure Security Center とその接続されたソースによって生成されるセキュリティ データを集約します。 

接続されている Azure コンピューティング リソースから中央の Log Analytics ワークスペースにセキュリティ データとイベントを送信するように Security Center のデータ収集を構成します。 データ収集に加えて、連続エクスポート機能を使用して、Security Center によって生成されたセキュリティのアラートと推奨事項を中央の Log Analytics ワークスペースにストリーミングします。 Azure Monitor で、Security Center および接続された Azure リソースから生成されたセキュリティ データに対してクエリと分析を実行できます。 

または、Security Center によって生成されたデータを Azure Sentinel またはサードパーティの SIEM に送信することもできます。

- [継続的に Security Center データをエクスポートする](continuous-export.md)

- [Azure Security Center でのデータ収集](security-center-enable-data-collection.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法](../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Monitor のアクティビティ ログは自動的に使用可能になります。これらのログには、実行された操作、操作を開始したユーザー、いつ発生したかなど、Azure Security Center などのリソースに対するすべての書き込み操作が含まれます。 ログの統合と保持向上のために、Azure アクティビティ ログを Log Analytics ワークスペースに送信します。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/essentials/platform-logs-overview.md)

- [アクティビティ ログを Log Analytics ワークスペースに送信する](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期およびアーカイブ ストレージには Azure Storage アカウントを使用します。 

- [Log Analytics でデータ保持期間を変更する](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) 

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Security Center およびその接続されたソースによって生成されるログを分析および監視して異常な動作を検出し、結果を定期的に確認します。 Azure Monitor と Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

- [Log Analytics クエリの使用方法](../azure-monitor/logs/log-analytics-tutorial.md) 

- [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: アクティビティ ログによって記録される不要または異常なアクティビティ、または Azure Security Center によって生成されるデータをクエリするように Azure Monitor ログ アラートを構成します。 異常なアクティビティに対するログ アラートが開始された場合に、組織に通知され、アクションを実行できるようにアクション グループを設定します。 Security Center のワークフロー自動化機能を使用して、セキュリティのアラートと推奨事項でロジック アプリをトリガーします。 Security Center ワークフローを使用して、インシデント対応についてユーザーに通知したり、アラート情報に基づいてリソースを修復するアクションを実行したりできます。

または、Azure Security Center に関連し、生成されるデータを有効にして、Azure Sentinel にオンボードすることもできます。 Azure Sentinel により、セキュリティ関連の問題に対する脅威の自動応答を可能にするプレイブックがサポートされています。

- [Azure Security Center ワークフローの自動化](workflow-automation.md)

- [Azure Security Center でアラートを管理する方法](security-center-managing-and-responding-alerts.md) 

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

- [Azure Sentinel で脅威への自動対応を設定します](../sentinel/tutorial-respond-threats-playbook.md)

- [Azure Monitor でのログ アラート](../azure-monitor/alerts/alerts-unified-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure のロールベースのアクセス制御 (Azure RBAC) を使用すると、ロールの割り当てを通じて Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。 Azure Security Center には、"セキュリティ閲覧者" または "セキュリティ管理者" のロールが組み込まれています。これにより、ユーザーはセキュリティ ポリシーを読み取りまたは更新して、アラートと推奨事項を無視できます。

- [Azure Security Center におけるアクセス許可](security-center-permissions.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: Azure Platform 向けまたは Azure Security Center オファリングに固有の専用管理者アカウントの使用に関する標準操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、Azure Active Directory 内の管理者アカウントの数を監視します。 Security Center には、"セキュリティ管理者" のロールも組み込まれています。これを使用すると、ユーザーはセキュリティ ポリシーを更新したり、アラートと推奨事項を無視したりすることができ、確実にこのロールが割り当てられているすべてのユーザーを定期的に確認し、調整できます。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。

- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

- [Azure Security Center におけるアクセス許可](security-center-permissions.md)

- [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセスの推奨事項を使用してください。

- [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Security Center と Azure portal にアクセスするために Azure Active Directory MFA を有効にし、Security Center の ID とアクセスの推奨事項に従います。 

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center で ID とアクセスを監視する方法](security-center-identity-access.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:高度な特権を必要とする管理タスクには、セキュリティで保護された Azure マネージド ワークステーション (特権アクセス ワークステーション (PAW) とも呼ばれます) を使用します。

- [セキュリティで保護された Azure マネージド ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure AD MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Active Directory セキュリティ レポートと監視を使用して、環境内で疑わしいアクティビティや安全でないアクティビティが発生したときに検出します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](security-center-identity-access.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: Azure AD のネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。 

- [Azure AD のネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Security Center を使用する場合、主要な認証および承認システムとして Azure Active Directory (Azure AD) を使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。 Azure Security Center には、"セキュリティ管理者" などのロールが組み込まれています。これを使用すると、ユーザーはセキュリティ ポリシーを更新し、アラートと推奨事項を無視できます。

- [Azure Security Center におけるアクセス許可](security-center-permissions.md)

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory では、古いアカウントの検出に役立つログが提供されます。 また、Azure AD の ID およびアクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 Azure Security Center に関連するユーザー アクセスを定期的に確認して、適切なユーザーのみがアクセスを継続できるようにすることができます。 

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml) 

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure AD サインイン アクティビティ、監査、およびリスク イベント ログ ソースにアクセスできるため、任意の SIEM または監視ツールとの統合が可能です。 

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で必要なアラートを構成できます。  

- [Azure アクティビティ ログを Azure Monitor と統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する 

**ガイダンス**: Azure AD Identity Protection 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。 

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md) 

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: タグは、Azure Security Center から重要なセキュリティ情報を格納する Log Analytics ワークスペースなどの Azure リソースの追跡を支援するために使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。 アプリケーションやエンタープライズ環境で必要とされる Azure リソースへのアクセス レベルを制限できます。 Azure RBAC を使用して Azure リソースへのアクセスを制御できます。

既定では Azure Security Center データは Security Center バックエンド サービスに格納されます。 組織で、このデータを独自のリソースに保存するための要件を追加している場合は、Security Center のデータ、アラート、推奨事項を格納するように Log Analytics ワークスペースを構成できます。 独自のワークスペースを使用する場合は、データの発生元の環境に応じて異なるワークスペースを構成することで、さらに分離を追加できます。

- [継続的に Security Center データをエクスポートする](continuous-export.md)

- [Azure Security Center でのデータ収集](security-center-enable-data-collection.md)

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md) 

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md) 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。 Log Analytics エージェントで構成され、Azure Security Center にデータを送信する仮想マシンは、TLS 1.2 を使用するように構成する必要があります。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。 

- [Log Analytics に安全にデータを送信する](../azure-monitor/logs/data-security.md#sending-data-securely-using-tls-12)

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する 

**ガイダンス**: Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Security Center 関連のデータとリソースへのアクセスを管理します。 Azure Security Center には、"セキュリティ閲覧者" または "セキュリティ管理者" のロールが組み込まれています。これにより、ユーザーはセキュリティ ポリシーを読み取りまたは更新して、アラートと推奨事項を無視できます。 Security Center によって収集されたデータを格納する Log Analytics ワークスペースには、"Log Analytics 閲覧者"、"Log Analytics 共同作成者" など、割り当てることができる組み込みのロールもあります。 ユーザーが必要なタスクを完了するために必要とされる最小限の権限ロールを割り当てます。 たとえば、リソースのセキュリティ正常性に関する情報の表示のみが必要で、推奨事項の適用やポリシーの編集などの操作を行う必要がないユーザーには、閲覧者ロールを割り当ててください。

- [Azure Log Analytics ワークスペースのアクセス許可](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Azure Security Center におけるアクセス許可](security-center-permissions.md)

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Security Center により、構成済みの Log Analytics ワークスペースを使用して、生成されるデータ、アラート、推奨事項が格納されます。 Security Center データ収集用に構成したワークスペースのカスタマー マネージド キー (CMK) を構成します。 CMK により、ワークスペースに保存または送信されたすべてのデータを、自分が作成して所有する Azure Key Vault キーで暗号化できます。 

- [Azure Monitor のカスタマー マネージド キー](../azure-monitor/logs/customer-managed-keys.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Monitor を使用して、Azure Security Center に関連する重要な Azure リソースに変更が加えられたときにアラートを作成します。 これらの変更には、アラートや推奨事項の無効化、データ ストアの更新や削除など、Security Center に関連する構成を変更するすべてのアクションが含まれる場合があります。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

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

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内の Azure Security Center に関連するすべてのリソースをクエリを実行して検出します。 テナントで適切な (読み取り) アクセス許可を確保し、すべての Azure サブスクリプションを列挙して Security Center リソースを見つけます。 

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md) 

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: タグは、Azure Security Center から重要なセキュリティ情報を格納する Log Analytics ワークスペースなどの Azure リソースの追跡を支援するために使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure Security Center リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、Azure Policy を使用し、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類を制限します。

- 許可されないリソースの種類
- 許可されるリソースの種類

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

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。 

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。  環境に存在するすべての Azure リソースが承認されていることを確認します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Security Center に関連する Azure リソースは、組織のインベントリとレビュー プロセスの一環として、不要になったときに削除します。

- [Azure リソース グループとリソースの削除](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、サブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類
- 許可されるリソースの種類

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy を使用して Azure Security Center およびその接続されたワークスペースの標準セキュリティ構成を定義し、実装します。 "Microsoft.OperationalInsights" および "Microsoft.Security" 名前空間の Azure Policy エイリアスを使用して、カスタム Azure Policy 定義を作成し、Security Center とその Log Analytics ワークスペースの構成を監査または適用します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の "deny" と "deploy if not exist" の効果を使用します。 さらに、Azure Resource Manager テンプレートを使用して、組織に必要な Azure リソースのセキュリティ構成を維持できます。 

- [Azure Policy の効果について](../governance/policy/concepts/effects.md) 

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure Policy 定義、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。 Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。 

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 Azure Policy エイリアスを使用して、Azure Security Center 関連リソースの構成を監査または適用するカスタム ポリシーを作成します。 さらに、Azure Automation を使用して、構成の変更をデプロイすることもできます。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [エイリアスを使用する方法](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と、"Microsoft.OperationalInsights" および "Microsoft. Security" 名前空間の Azure Policy エイリアスを使用して、Azure リソース構成をアラート、監査、適用するカスタム ポリシーを作成します。 Azure リソースの構成を自動的に適用するには、Azure Policy の "audit"、"deny"、"deploy if not exist" を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Security Center により、構成済みの Log Analytics ワークスペースを使用して、生成されるデータ、アラート、推奨事項が格納されます。 Security Center データ収集用に構成したワークスペースのカスタマー マネージド キー (CMK) を構成します。 CMK により、ワークスペースに保存または送信されたすべてのデータを、自分が作成して所有する Azure Key Vault キーで暗号化できます。 

- [Azure Monitor のカスタマー マネージド キー](../azure-monitor/logs/customer-managed-keys.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**:Azure Security Center は、ファイルを格納または処理するためのものではありません。 Log Analytics ワークスペースなど、非コンピューティング Azure リソースにアップロードするコンテンツはすべて事前にスキャンする必要があります。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う 

**ガイダンス**: Infrastructure as Code (IAC) アプローチに従い、Azure Resource Manager を使用して、リソース関連の構成のバックアップとして使用できる JavaScript Object Notation (JSON) テンプレートに Azure Security Center 関連リソースをデプロイします。

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティ リソース用 Azure Resource Manager テンプレート](/azure/templates/microsoft.security/allversions)

- [Azure Automation について](../automation/automation-intro.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Security Center により、Log Analytics ワークスペースを使用して、生成されるデータ、アラート、推奨事項が格納されます。 カスタマー マネージド キーを有効にするために Security Center によって使用される Azure Monitor とワークスペースを構成できます。 カスタマー マネージド キーを格納するために Key Vault を使用している場合は、キーの定期的な自動バックアップを確実に実行してください。

- [Key Vault のキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Resource Manager でサポートされるテンプレート ファイルを使用して、定期的に復元を実行できることを確実にします。 バックアップされたカスタマー マネージド キーの復元をテストします。

- [Azure Resource Manager テンプレートを使用して Log Analytics ワークスペースを管理する](../azure-monitor/logs/resource-manager-workspace.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure DevOps を使用して、カスタム Azure Policy 定義や Azure Resource Manager テンプレートなどのコードを安全に格納し、管理します。 Azure DevOps で管理するリソースを保護するには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。 Azure ロールベースのアクセス制御を使用して、カスタマー マネージド キーを保護します。

また、Key Vault で論理的な削除と消去保護を有効にして、偶発的または悪意のある削除からキーを保護します。  Azure Storage を使用して Azure Resource Manager テンプレート バックアップを格納した場合、BLOB または BLOB のスナップショットが削除されたときに、論理的な削除機能でデータを保存および復旧することができます。 

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

- [Key Vault で論理的な削除と消去保護を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

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

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してサブスクリプションをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいてアラートの修復に優先順位を付けることは、お客様の責任です。 

- [Security alerts in Azure Security Center](security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて対応計画を見直します。 

- [NIST の出版物 -- IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 

- [連続エクスポートを構成する方法](continuous-export.md) 

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して自動的に応答をトリガーし、Azure リソースを保護できます。 

- [Security Center でワークフロー自動化を構成する方法](workflow-automation.md)

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