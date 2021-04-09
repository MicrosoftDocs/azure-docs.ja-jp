---
title: Azure Data Share 用の Azure セキュリティ ベースライン
description: Azure Data Share セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: data-share
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d423a8aaf3ace78ccb4b2eb1d8424c6a1dfb61bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705978"
---
# <a name="azure-security-baseline-for-azure-data-share"></a>Azure Data Share 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Microsoft Azure Data Share に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Data Share に適用できる関連ガイダンスによって定義されています。 Azure Data Share に適用できない **制御** は、除外されています。

 
Azure Data Share を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Data Share セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:Azure Data Share に関連するログを Azure Monitor を介して取り込み、エンドポイント デバイス、ネットワーク リソース、その他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

または、Azure Sentinel かサード パーティの SIEM に対してこのデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](/azure/azure-monitor/platform/diagnostic-settings) 

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:自動的に利用できるアクティビティ ログには、読み取り操作 (GET) を除く、Azure Data Share リソースに対するすべての書き込み操作 (PUT、POST、DELETE) が含まれています。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。

Azure Data Share の診断ログ (具体的には、MicrosoftDataShareSentShareSnapshotsLog &amp; MicrosoftDataShareReceivedShareSnapshotsLog の診断ログ) を有効にします。 これらのログを使用すると、同期の開始時刻、終了時刻、状態、その他の詳細など、重要な情報を取り込むことができます。 これらのログは、後でセキュリティ インシデントを調査し、フォレンジック演習を実行するために重要な場合があります。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](/azure/azure-monitor/platform/diagnostic-settings) 

- [Azure でのログ記録とログのさまざまな種類について](/azure/azure-monitor/platform/platform-logs-overview)

- [Azure アクティビティ ログ用に診断設定を構成する方法](/azure/azure-monitor/platform/activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Data Share ログを格納するために使用されるすべてのストレージ アカウントまたは Log Analytics ワークスペースに、組織のコンプライアンス規則に従ってログの保有期間を確実に設定します。

- [Log Analytics ワークスペースの保有期間を構成する方法](/azure/azure-monitor/platform/manage-cost-storage)

- [ Azure ストレージ アカウントでのリソース ログの格納](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Azure Data Share リソースに関連するログを、異常な動作がないかどうか分析および監視し、その結果を定期的に確認します。 Azure Monitor と Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

- [Log Analytics クエリの使用方法](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Azure Monitor でカスタム クエリを実行する方法](/azure/azure-monitor/log-query/get-started-queries)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Log Analytics ワークスペースと共に Azure Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。 または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md) 

- [Log Analytics のログ データに関するアラートを送信する方法](/azure/azure-monitor/learn/tutorial-response)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: Azure Data Share では、Azure Active Directory (Azure AD) を使用した SSO 認証がサポートされています。 組織の既存の ID でサービスの SSO を有効にすることで、ユーザーが管理する必要がある ID と資格情報の数を減らします。

- [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure AD の多要素認証を有効にして、Azure Security Center ID とアクセスの推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:高度な特権を必要とする管理タスクには、セキュリティで保護された Azure マネージド ワークステーション (特権アクセス ワークステーション (PAW) とも呼ばれます) を使用します。

- [セキュリティで保護された Azure マネージド ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure AD 多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Azure Data Share で Azure 組み込みロールを操作する](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、Azure AD の ID およびアクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

- [Azure AD のレポートの概要](/azure/active-directory/reports-monitoring/) 

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md) 

- [Azure Data Share で Azure 組み込みロールを操作する](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM または監視ツールと統合することができます。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で必要なアラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor と統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) Identity Protection 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure Data Share リソースに関連するデータとリソースへのアクセスを管理するには、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。それ以外の場合は、サービス固有のアクセス制御方法を使用します。

- [Azure で RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure アクティビティ ログで Azure Monitor を使用して、重要な Azure リソースに変更が加えられたときの Azure Monitor アラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:Azure Data Share を使用する場合、そのリソースに対する脆弱性スキャン ツールをインストールすることはできません。

一般には、Azure 仮想マシン、コンテナー イメージ、および SQL サーバーに対して脆弱性評価を実行することに関する Azure Security Center の推奨事項に従います。

ネットワーク デバイスと Web アプリケーションで脆弱性評価を実行するためのサードパーティ ソリューションを使用します。 リモート スキャンを実施する場合は、1 つの永続的な管理者アカウントを使用しないでください。 スキャン アカウントには、JIT プロビジョニングの方法論を実装することを検討してください。 スキャン アカウントの資格情報は保護と監視の対象とし、脆弱性のスキャンのためにのみ使用する必要があります。

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure リソース、リソース グループ、サブスクリプションにタグを適用して、論理的な分類に整理できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:Azure リソース、リソース グループ、サブスクリプションにタグを適用して、それらを論理的に分類できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。
Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:Azure リソースが不要になった場合は削除します。Azure portal、PowerShell、または CLI を使用して、それを行うことができます。

- [Azure リソース グループとリソースの削除](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)

Azure Data Share によって、OS が公開されることもなければ、そのリソース上にサードパーティ製のソフトウェア アプリケーションをインストールすることが許可されることもありません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: Azure Policy を使用して、環境内でプロビジョニングできるサービスを制限します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure Policy 定義、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。 Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:"DataShare" 名前空間で Azure Policy のエイリアスを使用して、システム構成のアラート、監査、適用を行うためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [エイリアスを使用する方法](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [VM で Azure リソース用のマネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
