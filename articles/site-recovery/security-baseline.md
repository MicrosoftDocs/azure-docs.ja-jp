---
title: Site Recovery 用の Azure セキュリティ ベースライン
description: Site Recovery セキュリティ ベースラインにより、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf91c0a6e533acb326fe815c3e3c1088c959a603
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576719"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Site Recovery 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Site Recovery に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、Azure セキュリティ ベンチマークと、Site Recovery に適用される関連ガイダンスによって定義された **セキュリティ コントロール** によってグループ化されています。 Site Recovery に適用されない **コントロール** は、除外されています。 

Site Recovery がどのように Azure セキュリティ ベンチマークに完全にマップされているかを確認するには、[Site Recovery セキュリティ ベースラインの完全なマッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:Microsoft Azure Site Recovery では、Azure Virtual Network へのデプロイはサポートされていません。 Azure プライベート エンドポイントを使用して Site Recovery サービスを構成し、セキュリティで保護された通信をネットワーク上で実施します。

- [Azure Site Recovery プライベート リンクのサポート](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:Site Recovery サービスではサービス タグがサポートされています。これを使用することで、お客様は特定のサービスとポートに対してのみトラフィックを開くことができます。 お客様は、Site Recovery サービスへの発信アクセスを許可するために、ファイアウォールまたはネットワーク セキュリティ グループで "AzureSiteRecovery" サービス タグを許可する必要があります。

- [サービスタグを使用した送信接続](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループやその他のリソースに対して、リソース タグを使用してください。 個々のネットワーク セキュリティ グループの規則については、"説明" フィールドを使用して、ネットワークとの間のトラフィックを許可する規則を文書化します。 

すべてのリソースがタグ付きで作成され、タグ付けされていない既存のリソースがユーザーに通知されるようにするには、タグ付けに関連したすべての組み込み Azure Policy 定義 ("タグとその値が必要" など) を組み入れます。 

Azure PowerShell または Azure CLI を使用して、タグに基づいたリソースの検索やアクション実行ができます。 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md) 

- [Azure 仮想ネットワークの作成方法](../virtual-network/quick-create-portal.md) 

- [ネットワーク セキュリティ グループの規則を使用してネットワーク トラフィックをフィルター処理する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、Site Recovery サービスに関連するネットワーク リソース構成の変更を監視します。 重要な Site Recovery のネットワーク リソースが変更されたときに通知を受け取るように、Azure Monitor でアラートを作成します。

- [Azure アクティビティ ログ イベントを表示および取得する](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:監査ログ用の Azure アクティビティ ログの診断設定を有効にし、Log Analytics ワークスペース、Azure Storage アカウント、または Azure イベント ハブにログを送信してアーカイブします。

Azure アクティビティ ログのデータを使用して、Azure リソースに対して実行された書き込み操作 (PUT、POST、DELETE) について、"いつ誰れが何を" 行ったのかを判断します。

Site Recovery ログを Azure Monitor に取り込んで、生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリと分析を行い、長期またはアーカイブでの保管にはストレージ アカウントを使用します。 また、Azure Sentinel またはサードパーティのセキュリティ インシデントおよびイベント管理 (SIEM) ソリューションに対してデータを有効にしてオンボードすることもできます。

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor ログを使用した Site Recovery の監視](monitor-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:監査ログ用の Azure アクティビティ ログの診断設定を有効にし、Log Analytics ワークスペース、Azure Storage アカウント、または Azure イベント ハブにログを送信してアーカイブします。 

Azure アクティビティ ログのデータを使用して、Azure リソースに対して実行された書き込み操作 (PUT、POST、DELETE) について、"いつ誰れが何を" 行ったのかを判断します。

Site Recovery ログを Azure Monitor に取り込んで、生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリと分析を行い、長期またはアーカイブでの保管にはストレージ アカウントを使用します。 Azure Sentinel またはサードパーティのセキュリティ インシデントおよびイベント管理 (SIEM) ソリューションに対してデータを有効にしてオンボードします。

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor ログを使用した Site Recovery の監視](monitor-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:組織のコンプライアンス規則に従って、Azure Monitor を使用して、Azure Recovery Services コンテナーに関連付けられている Log Analytics ワークスペースのログの保持期間を設定します。 

- [ログ保持期間のパラメーターを設定する方法](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure アクティビティ ログの診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 

Log Analytics でクエリを実行して、用語の検索し、傾向の特定、パターンの分析を行います。また、Recovery Services コンテナーから収集されたアクティビティ ログ データに関する分析情報を取得します。

- [Site Recovery の監視](site-recovery-monitor-and-troubleshoot.md)

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する方法](../azure-monitor/essentials/activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Azure Monitor ログと Log Analytics を使用して、Azure Site Recovery によってレプリケートされたマシンを監視します。 Azure Monitor 内で Log Analytics を使用して、ログ クエリを作成およびテストしたり、ログ データを対話形式で分析したりします。 Azure Monitor によって、アクティビティとリソースのログが他の監視データと共に収集されます。 

ログの結果を可視化してクエリを実行し、監視対象データに基づいてアクションを実行するようにアラートを構成します。 Log Analytics ワークスペースのアラートを Azure Sentinel に設定します。これによって、セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されます。 これにより、プレイブックなどの自動化されたソリューションを作成して、セキュリティの問題を修復するために使用できます。 Azure Monitor を使用して、Log Analytics ワークスペースでカスタムのログ アラートを作成します。 

- [Site Recovery の監視](site-recovery-monitor-and-troubleshoot.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Monitor を使用してログ アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:ロールは既定では割り当てられません。 ビジネス ニーズに基づいて明示的に割り当てる必要があります。 すべてのロールの割り当ては、PowerShell CLI または Azure Active Directory (Azure AD) を使用して確認でき、これにより、管理グループのメンバーであるアカウントを検出できます。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Security Center の ID とアクセス管理の機能を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡するには、Security Center の推奨事項や組み込みの Azure ポリシーを使用します。次に例を示します。 

- 複数の所有者がサブスクリプションに割り当てられている必要がある 
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある 

- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

管理アカウントの ID とアクセス制御を追跡するプロセスを作成し、それを定期的に確認します。

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:サービス プリンシパルで Azure アプリの登録を使用して、API 呼び出しを介して Recovery Services コンテナーと対話するために使用するトークンを取得します。

- [Azure REST API を呼び出す方法](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [クライアント アプリケーション (サービス プリンシパル) を Azure AD に登録する方法](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Recovery Services API に関する情報](/rest/api/recoveryservices)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure AD の多要素認証を有効にして、Security Center の ID とアクセスの推奨事項に従います。 
- [Azure AD Multi-Factor Authentication のデプロイを計画する](../active-directory/authentication/howto-mfa-getstarted.md)

- [ID とアクセスを監視する](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:セキュリティで保護された、Azure で管理されているワークステーション (特権アクセス ワークステーション (PAW) とも呼ばれる) を Azure 多要素認証と共に使用して管理タスクを実行し、Site Recovery リソースに対して特権操作を実行します。

- [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) (Privileged Access Workstation)

- [クラウドベースの Azure AD Multi-Factor Authentication のデプロイの計画](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Azure AD の Privileged Identity Management (PIM) 機能を使用して、環境内で疑わしいまたは安全でないアクティビティが発生したときにログやアラートを生成します。
Azure AD のリスク検出機能を使用して、危険なユーザー動作に関するアラートとレポートを表示します。

- [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD のリスク検出の概要](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**:条件付きアクセスのネームド ロケーションを使用して、IP アドレスの範囲、リージョン、または国の特定の論理グループからのみ Azure portal へのアクセスを許可します。
- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure AD を Site Recovery の中央認証および承認システムとして使用します。 Azure AD では、保存時と転送時のデータに対して強力な暗号化を使用してデータが保護され、さらにユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。 

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure AD ログを使用して、古いアカウントを検出します。 

Azure AD の ID およびアクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理します。 

アクセス レビューが完了したユーザーだけが継続的にアクセスできるように、定期的にユーザー アクセスをレビューするプロセスを作成します。 

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure AD を Site Recovery リソースの中央認証および承認システムとして使用します。 Azure AD では、データの保存時と転送時に強力な暗号化を使用してデータが保護され、さらに、ユーザーの資格情報がソルト化およびハッシュされ、安全に保存されます。

Azure AD サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、それらを Azure Sentinel、または Azure Marketplace から入手できる任意の SIEM や監視ツールと統合できます。

このプロセスをさらに効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査とサインインのログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で必要なアラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**:Recovery Services コンテナーの中央認証および承認システムとして Azure AD を使用します。 

Azure AD の Identity Protection 機能を使用して、アカウント ログイン動作の検出を行い、ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成します。 また、さらに詳しく調査するために、Azure Sentinel にデータを取り込みます。

- [Azure AD のリスクの高いサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:Recovery Services コンテナーの開発、テスト、運用用に、別々のサブスクリプションまたは管理グループを実装します。 リソースは、仮想ネットワークまたはサブネットで分離し、適切にタグ付けし、さらにネットワーク セキュリティ グループまたは Azure Firewall を使用してセキュリティで保護します。 

機密データを格納または処理する仮想マシンは、使用されていないときはオフにします。 これを繰り返しプロセスにするためにポリシーと手順を実装します。 

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [Site Recovery の概要](site-recovery-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:プライベート リンクまたはプライベート エンドポイント、ネットワーク セキュリティ グループ、およびサービス タグを使用して、Site Recovery が有効になっている仮想マシンからのデータ流出の可能性を軽減します。

Microsoft では、Site Recovery によって使用される、基になるプラットフォームを管理し、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護しています。 Microsoft では、Azure 内の顧客データが確実にセキュリティで保護されるように、一連の堅牢なデータ保護制御と機能を実装して管理しています。 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

- [Azure プライベート エンドポイントを使用して仮想マシンをレプリケートする](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Azure Site Recovery サービス タグを使用して仮想マシンをレプリケートする](azure-to-azure-about-networking.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:Site Recovery では、Azure ワークロード サーバーから、Recovery Services コンテナーの背後でホストされている Site Recovery サービスへ、Advanced Encryption Standard (AES 256) を使用して暗号化された https チャネルが使用されます。

Site Recovery でサポートされている現在の TLS バージョンは、2019 年の終わりまでに実装されたリージョンでは TLS 1.0、TLS 1.1、TLS 1.2 です。 新しいリージョンでサポートされている TLS バージョンは、TLS1.2 のみになります。

- [Azure Site Recovery での転送中の暗号化について](physical-azure-set-up-source.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:Site Recovery では、データの識別、分類、損失防止機能はまだ使用できません。 

必要に応じて、コンプライアンスのためにサードパーティ ソリューションを実装してください。

Microsoft では、Site Recovery によって使用される、基になるプラットフォームを管理し、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護しています。 Azure 内の顧客データを確実にセキュリティで保護するために一連の堅牢なデータ保護制御と機能を実装して管理しています。 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Site Recovery リソースに関連するデータとリソースへのアクセスを管理します。 

Azure RBAC を使用して作業の職務を分離し、それらに必要となる適切なアクセス権を付与します。 Site Recovery の管理操作を制御するための組み込み Site Recovery ロールを使用してください。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [ロールベースのアクセス制御を使用して Azure Site Recovery を管理する](site-recovery-role-based-linked-access-control.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:プラットフォームとカスタマー マネージド キーの両方を使用した二重暗号化を有効化します。 この機能は、Site Recovery で使用できます。 

Site Recovery では、データの保存時の暗号化がサポートされています。 Azure IaaS ワークロードの場合、データは Storage Service Encryption (SSE) を使用して保存時に暗号化されます。 

カスタマー マネージド キーを使って暗号化された Recovery Services コンテナーを使用している場合、暗号化キーにアクセスできるのはその顧客だけです。 Microsoft はコピーを保持しておらず、キーにアクセスすることもできません。また、どの時点においても、プライマリからディザスター リカバリーの場所に転送されたデータの暗号化を解除することはありません。 

- [Azure Site Recovery に対するカスタマー マネージド キーのサポート](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure Monitor と Azure アクティビティ ログを使用して、重要なリソースに変更が加えられたときにアラートを作成します。 これらのリソースとしては、Recovery Services コンテナーの運用インスタンス、Site Recovery サービスのリソースおよび関連リソースなどがあります。
- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内の (Recovery Services コンテナーを含む) すべてのリソースのクエリまたは検出を行います。 テナントで適切な読み取りアクセス許可を確認し、すべての Azure サブスクリプション、およびサブスクリプション内のリソースを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータで Site Recovery によって使用される Recovery Services コンテナーとその他の関連リソースにタグを適用して、それらを論理的に整理して分類します。
- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じてタグ付け、管理グループ、個別のサブスクリプションを使用して、Site Recovery (Recovery Services コンテナー) とその他の関連リソースの整理と追跡を行います。 

さらに、Azure Policy を使用し、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類を制限します。 

- 許可されないリソースの種類
- 許可されるリソースの種類

定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:顧客の組織の要件に基づいて、承認された Azure リソースと、コンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。 

- 許可されないリソースの種類 
- 許可されるリソースの種類

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類 
- 許可されるリソースの種類

Azure でポリシーを作成および管理する方法を理解することは、企業の標準とサービス レベル アグリーメントへの準拠を維持するために重要です。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。 これにより、高セキュリティ環境内でのリソースの作成や変更を防ぐことができます。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy を使用して、Recovery Services コンテナーの標準的なセキュリティ構成を定義して実装します。 

"Microsoft RecoveryServices" 名前空間で Azure Policy エイリアスを使用して、Site Recovery サービスの Recovery Services コンテナー リソースの構成を監査または適用するためのカスタム ポリシーを作成します。
- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] 効果を使用します。
- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:Recovery Services コンテナーと関連リソースにカスタム Azure Policy 定義を使用している場合は、Azure Repos を選択して、コードを安全に格納して管理します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos のドキュメント](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.RecoveryServices" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 

さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.RecoveryServices" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 

Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] の効果を使用します。
- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:お客様は、Azure Disk Encryption 対応の仮想マシンのディザスター リカバリーを有効にしながら、Azure Key Vault と統合された Site Recovery シークレットを管理する必要があります。 

- [キー コンテナーを作成する方法](../key-vault/general/quick-create-portal.md)

- [キー コンテナーに対して認証を行う方法](../key-vault/general/authentication.md)

- [キー コンテナーのアクセス ポリシーを割り当てる方法](../key-vault/general/assign-access-policy-portal.md)

- [Site Recovery を使用して Azure Disk Encryption 対応仮想マシンの DR を有効にする方法](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Site Recovery では、顧客が Recovery Services コンテナーでシステム マネージド ID を有効にできる場合にのみ、システム マネージド ID がサポートされます。 アクセス境界を定義するために、ディザスター リカバリー オファリングで使用されるリソースにも同じ方法が適用されます。 

マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 

マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Recovery Services コンテナーでシステム マネージド ID を有効にする方法](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

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

**ガイダンス**:Microsoft Antimalware は、(Site Recovery などの) Azure サービスがサポートされる、基になるホストで有効になっていますが、コンテンツに対しては実行されません。 App Service、Data Lake Storage、Blob Storage などの非コンピューティング Azure リソースにアップロードされようとしているファイルはすべて、事前にスキャンしてください。

ストレージ アカウントにアップロードされたマルウェアを検出するには、Security Center のデータ サービス向け脅威検出を使用します。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について](../security/fundamentals/antimalware.md)

- [データ サービスに対する Azure Security Center の脅威検出について](../security-center/azure-defender.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**:Site Recovery では、顧客が自身のワークロードに対して構成したディザスター リカバリー ソリューションの状態を維持するために、Azure Storage アカウントを内部的に使用します。

Site Recovery サービス メタデータによって使用される、構成の種類が読み取りアクセス geo 冗長ストレージ (RA-GRS) のすべてのストレージ リソース。 GRS の上の種類 (RAGRS、RAG-ZRS など) のストレージ アカウントでは、セカンダリ リージョン (ソース データの主な拠点から数百マイル離れた場所) にデータがレプリケートされて、停止時に顧客のディザスター リカバリーに引き続きサービスが提供されます。

これは顧客のスコープ外であり、Site Recovery チームによって内部で処理されます。 顧客は、Azure でキー コンテナーのキーをバックアップできます。

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:バックアップされたカスタマー マネージド キーの復元を定期的にテストします。

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**:データは、Azure の Infrastructure as a Service (IaaS) ベースの仮想マシンで Storage Service Encryption (SSE) を使用して保存時に暗号化されます。 Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

- [Key Vault で論理的な削除を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 

要員のすべてのロール、および検出からインシデント後のレビューまでのインシデント処理または管理のフェーズを定義するインシデント対応計画が記述されていることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center によって割り当てられたアラートの重大度に基づいて、最初に調査する必要があるアラートの優先順位を設定します。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

サブスクリプション (運用、非運用など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物『IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド』をご覧ください](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 

インシデントを発生後にレビューして、問題が解決されていることを確認するためのプロセスを作成します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 

必要に応じて、Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングします。
- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Security Center のワークフローの自動化機能を使用します。
- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、確実に次の Microsoft の活動規則に従ってください。 https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらを参照してください。](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する