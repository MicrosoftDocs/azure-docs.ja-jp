---
title: Azure Lighthouse 用の Azure セキュリティ ベースライン
description: Azure Lighthouse セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 69234d7c22f1725f6f21fe52a455e64d743f052e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709854"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Azure Lighthouse 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Azure Lighthouse に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Lighthouse に適用できる関連ガイダンスによって定義されています。 Azure Lighthouse に適用できない **制御** は除外されています。

Azure Lighthouse を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Lighthouse セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](../security/benchmarks/security-controls-v2-identity-management.md).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス**:Azure Lighthouse では、Azure Active Directory (Azure AD) を既定の ID およびアクセス管理サービスとして使用します。 Azure AD を標準化して、以下での組織の ID とアクセス管理を統制します。
- Azure portal、Azure Storage、Azure 仮想マシン (Linux と Windows)、Azure Key Vault、PaaS、SaaS アプリケーションなどの Microsoft Cloud リソース。
- Azure 上のアプリケーションや企業ネットワーク リソースなどの組織のリソース。

Azure Lighthouse を使用すると、管理するテナントの指定ユーザーには、Azure 組み込みロールがあります。これにより、顧客のテナント内の委任されたサブスクリプションやリソース グループにアクセスすることができます。 現在、組み込みロールは、所有者、または DataActions アクセス許可を持つ組み込みロールを除き、すべてサポートされています。 マネージド ID へのロールの割り当てにおいて、ユーザー アクセス管理者ロールは、限定された用途のみに対してサポートされています。 カスタム ロールと従来のサブスクリプション管理者ロールはサポートされていません。

- [Azure Active Directory のテナント](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [アプリケーションに外部 ID プロバイダーを使用する](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory の ID セキュリティ スコアとは](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2:アプリケーション ID を安全かつ自動的に管理する

**ガイダンス**:Azure マネージド ID によって、Azure AD 認証をサポートする Azure のサービスとリソースに対して認証を行うことができます。 認証は事前に定義されたアクセス許可規則によって有効になり、ソース コードまたは構成ファイル内でハードコーディングされた資格情報を使用せずに済みます。 Azure Lighthouse を使用すると、顧客のサブスクリプションに対するユーザー アクセス管理者ロールを持つユーザーは、その顧客のテナントにマネージド ID を作成できます。 このロールは、一般に Azure Lighthouse でサポートされているものではありませんが、今回のように、このアクセス許可が設定されているユーザーがマネージド ID に対していくつかの組み込みロールを割り当てられるようにするシナリオでは使用可能です。

マネージド ID をサポートしていないサービスに対しては、Azure AD を使用して、リソース レベルでアクセス許可が制限されたサービス プリンシパルを代わりに作成します。 Azure Lighthouse によりサービス プリンシパルは、オンボード プロセス中に付与されたロールに従って顧客リソースにアクセスできます。 証明書の資格情報を使用してサービス プリンシパルを構成し、クライアント シークレットにフォールバックすることが推奨されます。 どちらの場合も、Azure Key Vault を Azure マネージド ID と組み合わせて使用し、ランタイム環境 (Azure 関数など) でキー コンテナーから資格情報を取得できるようにすることができます。

- [Azure マネージド ID](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure サービス プリンシパル](/powershell/azure/create-azure-service-principal-azureps)

- [Azure Key Vault を使用してセキュリティ プリンシパルを登録する](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [顧客のテナント内でマネージド ID にロールを割り当てることができるユーザーを作成する](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス**:管理テナント内のすべてのユーザー (委任された顧客のリソースにアクセスできるユーザーを含む) について、Multi-Factor Authentication (MFA) を必須としてください。 顧客にも、そのテナントに MFA を導入するよう求めることをお勧めします。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

**ガイダンス**:Azure AD には、次のデータ ソースが用意されています。 

-   サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。

-   監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 ログに記録される変更の監査ログの例としては、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除などがあります。

-   リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

-   リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

これらのデータ ソースは、Azure Monitor、Azure Sentinel、またはサードパーティの SIEM システムと統合できます。

Azure Lighthouse を使用するサービス プロバイダーは、Azure AD のログを Azure Sentinel に転送し、テナント間でアラートを表示/設定して、アカウントの異常を監視してアラートを生成することができます。

- [Azure AD でアクティビティ レポートを監査する](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Sentinel ワークスペースの大規模な管理を行う](how-to/manage-sentinel-workspaces.md)

- [Azure AD から Azure Sentinel にデータを接続する](../sentinel/connect-azure-active-directory.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス**:Azure Lighthouse は、委任された顧客リソースに対する条件付きアクセス機能をサポートしていません。 管理するテナントで、特定の IP 範囲からのユーザー ログインに対する多要素認証 (MFA) の使用の要求など、ユーザー定義の条件に基づく、より詳細なアクセス制御のために、Azure AD の条件付きアクセスを使用します。 詳細な認証セッションの管理は、さまざまなユース ケースに対する Azure AD 条件付きアクセス ポリシーによって使用することもできます。 

管理テナント内のすべてのユーザー (委任された顧客のリソースにアクセスできるユーザーを含む) について、MFA を必須としてください。 顧客にも、そのテナントに MFA を導入するよう求めることをお勧めします。

- [Azure での条件付きアクセスの概要](../active-directory/conditional-access/overview.md)

- [一般的な条件付きアクセス ポリシー](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [条件付きアクセスを使用して認証セッション管理を構成する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)」を参照してください。*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1:高い特権を持つユーザーを保護および制限する

**ガイダンス**:高い特権を持つユーザー アカウントの数を制限し、これらのアカウントを昇格されたレベルで保護します。 Azure Lighthouse を有効にして使用するために、グローバル管理者アカウントは必要ありません。

テナントレベルのアクティビティ ログ データにアクセスするには、監視閲覧者 Azure 組み込みロールをルート スコープ (/) でアカウントに割り当てる必要があります。 ルート スコープでの閲覧者監視ロールは、広範なアクセス レベルであるため、個々のユーザーまたはグループではなく、サービス プリンシパル アカウントにこのロールを割り当てることをお勧めします。 この割り当てについては、全体管理者ロールと追加の昇格されたアクセス権を持つユーザーが実行する必要があります。 この昇格されたアクセスは、ロールの割り当てを行う直前に追加し、その後、割り当てが完了した時点で削除する必要があります。

- [Azure AD での管理者ロールのアクセス許可](../active-directory/roles/permissions-reference.md)

- [アクセス権の割り当てによるテナントレベル アクティビティ ログ データの監視](how-to/monitor-delegation-changes.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**:Azure Lighthouse では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、アカウントが属するサブスクリプションと管理グループへの特権アクセスを許可するアカウントを制限し、ビジネス クリティカルなシステムへのアクセスを分離します。

ユーザーが、特定のタスクの実行に必要なアクセス許可のみを持つように、必ず最小限の特権の原則に従ってください。

また、お使いのビジネス クリティカルなアクセス対象への管理アクセス権を持つ、管理システム、ID システム、セキュリティ システムへのアクセスも制限してください (Active Directory Domain Controller (DC)、セキュリティ ツール、システム管理ツール、ビジネス クリティカル システムにインストールされたエージェントなど)。 これらの管理システムおよびセキュリティ システムを侵害した攻撃者は、それらをすぐに悪用してビジネス クリティカルな資産を侵害することができます。

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。

- [Azure のコンポーネントと参照モデル](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理グループ アクセス](../governance/management-groups/overview.md#management-group-access)

- [Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Azure Lighthouse のユーザーとロールを定義するためのベスト プラクティス](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Lighthouse では、Azure Active Directory (Azure AD) アカウントを使用してリソースを管理し、ユーザー アカウントとアクセス割り当てを定期的にレビューして、アカウントとそのアクセスが有効であることを確認します。 Azure AD アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てをレビューすることができます。 Azure AD レポートによって、古いアカウントの検出に役立つログが提供されます。 また、Azure AD Privileged Identity Management を使用してアクセス レビュー レポート ワークフローを作成し、レビュー プロセスを容易にすることもできます。

顧客は、Azure portal で Azure Lighthouse を使用して、管理するテナントで、ユーザーに付与されたアクセスのレベルを確認できます。 顧客は、このアクセスをいつでも削除できます。

また、Azure Privileged Identity Management を構成して、過剰な数の管理者アカウントが作成された場合にアラートを生成したり、古い管理者アカウントまたは不適切に構成されている管理者アカウントを識別したりすることもできます。

注: 一部の Azure サービスでは、Azure AD で管理されていないローカル ユーザーとロールがサポートされています。 これらのユーザーは個別に管理する必要があります。

- [Privileged Identity Management (PIM) で Azure リソース ロールのアクセス レビューを作成する](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [委任へのアクセスを削除する](how-to/remove-delegation.md)

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Azure portal でサービス プロバイダー ページを表示する](how-to/view-manage-service-providers.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4:Azure AD で緊急アクセスを設定する

**ガイダンス**:Azure Lighthouse は、Azure Active Directory との統合により、そのリソースを管理します。 Azure AD 組織から誤ってロックアウトされるのを防ぐために、通常の管理者アカウントを使用できない場合にアクセスするための緊急アクセス用アカウントを設定します。 緊急アクセス用アカウントは高い特権を持っており、特定の個人に割り当てることはできません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" に制限されます。

緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は安全に保管し、緊急時にのみそれらを使うことを許可された個人のみに知らせる必要があります。

- [Azure AD で緊急アクセス用アカウントを管理する](../active-directory/roles/security-emergency-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5:エンタイトルメント管理を自動化する 

**ガイダンス**:Azure Lighthouse は、Azure Active Directory (Azure AD) との統合により、そのリソースを管理します。 Azure AD のエンタイトルメント管理機能を使用して、アクセスの割り当て、レビュー、有効期限など、アクセス要求のワークフローを自動化します。 2 段階または複数段階の承認もサポートされています。

- [Azure AD アクセス レビューとは](../active-directory/governance/access-reviews-overview.md) 

- [Azure AD エンタイトルメント管理とは](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

**ガイダンス**:セキュリティで保護された分離したワークステーションは、管理者、開発者、重要なサービス オペレーターのような機密性の高い役割のセキュリティには非常に重要です。 要件に応じて、高度に保護されたユーザー ワークステーションや Azure Bastion を使用して、運用環境の Azure Lighthouse で管理タスクを実行することができます。 Azure Active Directory、Microsoft Defender Advanced Threat Protection (ATP)、または Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを展開します。 セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。 

- [特権アクセス ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [特権アクセス ワークステーションを展開する](/security/compass/privileged-access-deployment)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7:Just Enough Administration (最小限の特権の原則) に従う 

**ガイダンス**:Azure Lighthouse は、Azure のロールベースのアクセス制御 (RBAC) との統合により、そのリソースを管理します。 Azure RBAC を使用すると、ロールの割り当てによって Azure リソースへのアクセスを管理できます。 これらの組み込みロールを、ユーザー、グループ、サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。 Azure RBAC を使用してリソースに割り当てる特権は、常に、ロールで必要なものに制限する必要があります。 これは、Azure Active Directory Privileged Identity Management (PIM) のジャスト イン タイム (JIT) アプローチを補完するものであり、定期的に見直す必要があります。 組み込みロールを使用してアクセス許可を割り当て、必要な場合にのみカスタム ロールを作成します。

Azure Lighthouse を使用すると、Azure 組み込みロールを使用して、委任された顧客リソースにアクセスできます。 ほとんどの場合、多くの個々のユーザー アカウントではなく、グループまたはサービス プリンシパルにこれらのロールを割り当てることになります。 これにより、実際のアクセス要件が変更されたときに、プランを更新して再発行することなく、個々のユーザーのアクセス権を追加または削除できます。

管理するテナントに顧客リソースを委任するには、ゲスト以外のアカウントが、オンボード対象のサブスクリプションで所有者の組み込みロールを持っている (またはオンボード対象のリソース グループを含む) 顧客のテナントでデプロイを実行する必要があります。

- [Azure Lighthouse のテナント、ユーザー、ロールを理解する](concepts/tenants-users-roles.md)

- [Azure Lighthouse に最小限の権限の原則を適用する方法](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md) 

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="asset-management"></a>アセット管理

*詳細については、[Azure セキュリティ ベンチマークの「アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)」を参照してください。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1:セキュリティ チームが資産のリスクを確実に可視化できるようにする

**ガイダンス**:セキュリティ チームに Azure テナントとサブスクリプションのセキュリティ閲覧者アクセス許可を付与して、セキュリティ チームが Azure Security Center を使用してセキュリティ上のリスクを監視できるようにします。 

セキュリティ チームの責任がどのように構造化されているかによって、セキュリティ リスクの監視は中央のセキュリティ チームまたはローカル チームの責任になります。 ただし、セキュリティ分析情報とリスクは、常に組織内で一元的に集計する必要があります。 

セキュリティ閲覧者のアクセス許可は、テナント全体 (ルート管理グループ) に幅広く適用することも、管理グループまたは特定のサブスクリプションにスコープ指定することもできます。 

注:ワークロードとサービスを可視化するには、追加のアクセス許可が必要になることがあります。 

- [セキュリティ閲覧者ロールの概要](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理グループの概要](../governance/management-groups/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2:セキュリティ チームが資産インベントリとメタデータに確実にアクセスできるようにする

**ガイダンス**:顧客のセキュリティ チームは、アクティビティログを確認して、Azure Lighthouse を使用するサービス プロバイダーが行ったアクティビティを確認できます。 

委任された顧客リソースのレビューをサービス プロバイダーがセキュリティ チームに許可する場合は、セキュリティ チームの認可には、閲覧者の組み込みロールが含まれている必要があります。

- [顧客がサービス プロバイダーのアクティビティを確認する方法](how-to/view-service-provider-activity.md)

- [Azure Lighthouse への顧客のオンボード時にロールを指定する方法](how-to/onboard-customer.md#define-roles-and-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

**ガイダンス**:Azure Policy を使用して、環境内でユーザーがプロビジョニングできるサービスを監査および制限します。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 また、Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成することもできます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general) 

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4:アセット ライフサイクル管理のセキュリティを確保する

**ガイダンス**:Azure Lighthouse によって委任されたリソースへのアクセス権が不要になったら、このアクセス権を削除します。これで、サービス プロバイダーはアクセスできなくなります。 アクセス権は、顧客またはサービス プロバイダーが削除できます。 

- [委任へのアクセスを削除する](how-to/remove-delegation.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:Azure Lighthouse は、ID と認証のために Azure Active Directory (Azure AD) と統合されています。 "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure 条件付きアクセスを使用して、Azure Resource Manager を操作するユーザーの機能を制限することができます。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)」を参照してください。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1:Azure リソースの脅威検出を有効にする

**ガイダンス**:Azure Lighthouse で顧客の Azure リソースを監視して、潜在的な脅威や異常を検知することができます。 アナリストが選別しやすいように、質の高いアラートを取得して誤検知を減らすことに専念します。 アラートは、ログ データ、エージェント、その他のデータを元に生成できます。

Azure Security Center の組み込みの脅威検出機能を使用します。これは、Azure サービス テレメトリの監視とサービス ログの分析に基づいています。 データは、Log Analytics エージェントを使用して収集されます。このエージェントは、セキュリティ関連のさまざまな構成とイベント ログをシステムから読み取り、分析のためにデータをワークスペースにコピーします。 

さらに、Azure Sentinel を使用して分析ルールを構築し、顧客の環境全体で特定の条件に一致する脅威を探します。 ルールにより、条件が一致したときにインシデントが生成されます。これにより、各インシデントを調査できます。 Azure Sentinel にサード パーティの脅威インテリジェンスをインポートして、脅威の検出機能を強化することもできます。 

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md)

- [Azure Security Center セキュリティ アラート リファレンス ガイド](../security-center/alerts-reference.md)

- [脅威を検出するためのカスタム分析規則を作成する](../sentinel/tutorial-detect-threats-custom.md)

- [Azure Sentinel ワークスペースの大規模な管理を行う](how-to/manage-sentinel-workspaces.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス**:Azure Lighthouse では、Azure Security Center を使用して、認証試行の失敗回数が多すぎるなど、管理する顧客テナントでの特定の不審なアクティビティや、サブスクリプションで使用すべきでないアカウントに関するアラートを生成することができます。

Azure Active Directory (Azure AD) では、次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、または他の SIEM/監視ツールと統合できます。
- サインイン - サインイン レポートでは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報が提供されます。
- 監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。
- リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によってサインインが試行された可能性があることを示す指標です。
- リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、非推奨のアカウントに対してアラートを生成することもできます。 Azure Security Center の脅威保護モジュールでは、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービス)、データ リソース (SQL DB、ストレージ)、Azure サービス レイヤーから、さらに詳細なセキュリティ アラートを収集することもできます。 この機能により、個々のリソース内でアカウントの異常を確認することができます。 

- [Azure Lighthouse により拡張されたサービスとシナリオ](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Azure Active Directory の監査アクティビティ レポート](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**:自動的に使用可能になるアクティビティ ログには、読み取り操作 (GET) を除く、Azure Lighthouse リソースに対するすべての書き込み操作 (PUT、POST、DELETE) が含まれています。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。

Azure Lighthouse では、管理下にある顧客テナント全体を対象として、スケーラブルな方法で Azure Monitor ログを使用することができます。 顧客テナントに直接、Log Analytics ワークスペースを作成します。これで、顧客データが自分のテナントにエクスポートされるのではなく、テナントに残ります。 また、Log Analytics でサポートされるリソースまたはサービスの監視を一元化し、監視対象となるデータの種類について、より柔軟な運用が可能となります。

Azure Lighthouse に委任されたサブスクリプションのあるお客様は、Azure アクティビティ ログ データを表示して、実行されたすべてのアクションを確認できます。 これにより顧客は、サービス プロバイダーが実行している操作と、顧客独自の Azure Active Directory (Azure AD) テナント内のユーザーが実行した操作を完全に可視化できます。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/essentials/platform-logs-overview.md)

- [委任されたリソースを大規模に監視する](how-to/monitor-at-scale.md)

- [サービス プロバイダーのアクティビティを表示する](how-to/view-service-provider-activity.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**:ログ記録のストレージと分析を一元化して、相関関係を有効にします。 ログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確認します。

Azure アクティビティ ログを一元的なログ記録に統合していることを確認します。 Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

さらに、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。

Azure Lighthouse では、管理下にある顧客テナント全体を対象として、スケーラブルな方法で Azure Monitor ログを使用することができます。 顧客テナントに直接、Log Analytics ワークスペースを作成します。これで、顧客データが自分のテナントにエクスポートされるのではなく、テナントに残ります。 また、Log Analytics でサポートされるリソースまたはサービスの監視を一元化し、監視対象となるデータの種類について、より柔軟な運用が可能となります。

Azure Lighthouse に委任されたサブスクリプションのあるお客様は、Azure アクティビティ ログ データを表示して、実行されたすべてのアクションを確認できます。 これにより顧客は、サービス プロバイダーが実行している操作と、顧客独自の Azure Active Directory (Azure AD) テナント内のユーザーが実行した操作を完全に可視化できます。

多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [委任されたリソースを大規模に監視する](how-to/monitor-at-scale.md)

- [顧客がサービス プロバイダーのアクティビティを表示する方法](how-to/view-service-provider-activity.md)

- [Azure Sentinel ワークスペースの大規模な管理を行う](how-to/manage-sentinel-workspaces.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6:ログの保持期間を構成する

**ガイダンス**:現在、Azure Lighthouse では、セキュリティ関連のログは生成されません。 サービス プロバイダーの利用状況を表示する場合は、コンプライアンス、規制、およびビジネス要件に応じてログの保有期間を構成できます。 

Azure Monitor で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定できます。 長期ストレージおよびアーカイブ ストレージには、Azure Storage、Data Lake、または Log Analytics ワークスペースのアカウントを使用します。

- [Log Analytics でデータ保持期間を変更する](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center のアラートと推奨事項のエクスポート](../security-center/continuous-export.md)。構成すると、顧客はログの保有期間を設定できません。

- [顧客がサービス プロバイダーのアクティビティ ログ データを確認する方法](how-to/view-service-provider-activity.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)」を参照してください。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 準備 – インシデント対応プロセスを Azure 用に更新する

**ガイダンス**:組織において、セキュリティ インシデントに対応するためのプロセスが用意されていること、Azure のこれらのプロセスが更新されていること、それらのプロセスを定期的に使用して準備されていることを確認します。

- [企業環境全体にセキュリティを実装する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [インシデント対応のリファレンス ガイド](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 準備 – インシデント通知をセットアップする

**ガイダンス**:Azure Security Center でセキュリティ インシデントの連絡先情報を設定します。 この連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 また、インシデント対応のニーズに応じて、異なる Azure サービスでインシデント アラートと通知をカスタマイズするオプションもあります。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 検出と分析 – 高品質のアラートに基づいてインシデントを作成する

**ガイダンス**:高品質のアラートを作成し、アラートの品質を測定するプロセスがあることを確認します。 これにより、過去のインシデントから教訓を学び、アナリストに対するアラートに優先順位を付けることができるので、擬陽性に時間を無駄にすることがありません。 

高品質のアラートは、過去のインシデントからの経験、検証されたコミュニティ ソース、およびさまざまなシグナル ソースの融合と関連付けによってアラートを生成してクリーンアップするように設計されたツールに基づいて構築できます。 

Azure Security Center では、多数の Azure 資産について高品質のアラートが提供されます。 ASC データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 Azure Sentinel を使用すると、高度なアラート ルールを作成し、調査のためにインシデントを自動的に生成できます。 

エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートし、Azure リソースへのリスクを特定します。 アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートします。

- [エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 検出と分析 – インシデントを調査する

**ガイダンス**:アナリストが潜在的なインシデントを調査するときに、さまざまなデータ ソースを照会して使用し、発生した内容の完全なビューを構築できることを確認します。 キル チェーン全体で潜在的な攻撃者のアクティビティを追跡して死角を回避するには、さまざまなログを収集する必要があります。  また、他のアナリストのため、および将来において履歴が参照される場合のために、確実に分析情報と学習がキャプチャされているようにします。  

調査のためのデータ ソースには、スコープ内のサービスおよび実行中のシステムから既に収集されている一元化されたログ ソースが含まれますが、次のものも含まれます。

- ネットワーク データ - ネットワーク セキュリティ グループのフロー ログ、Azure Network Watcher、Azure Monitor を使用して、ネットワーク フロー ログやその他の分析情報をキャプチャします。 

- 実行中のシステムのスナップショット: 

    - Azure 仮想マシンのスナップショット機能を使用して、実行中のシステムのディスクのスナップショットを作成します。 

    - オペレーティング システムのネイティブ メモリ ダンプ機能を使用して、実行中のシステムのメモリのスナップショットを作成します。

    - Azure サービスのスナップショット機能またはソフトウェア独自の機能を使用して、実行中のシステムのスナップショットを作成します。

Azure Sentinel により、事実上すべてのログソースに対して広範な Data Analytics と、インシデントのライフサイクル全体を管理するためのケース管理ポータルが提供されます。 調査中のインテリジェンス情報を、追跡とレポートのためにインシデントに関連付けることができます。 

- [Windows マシンのディスクのスナップショットを作成する](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux マシンのディスクのスナップショットを作成する](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure サポートの診断情報とメモリ ダンプ コレクション](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel でインシデントを調査します](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 検出と分析 – インシデントの優先順位を付ける

**ガイダンス**:アラートの重要度と資産の機密性に基づいて、最初に注目するインシデントについてのコンテキストをアナリストに提供します。 

Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してリソースをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 包含、根絶、復旧 – インシデントの処理を自動化する

**ガイダンス**:手動による反復タスクを自動化して、応答時間を短縮し、アナリストの負担を軽減します。 手動タスクの実行には時間がかかり、各インシデントの速度が低下し、アナリストが処理できるインシデントの数が減少します。 手動タスクではアナリストの疲労も増加します。これにより、遅延が発生する人的エラーのリスクが増加し、複雑なタスクに効果的に焦点を当てるアナリストの能力が低下します。 Azure Security Center と Azure Sentinel のワークフロー自動化機能を使用して、自動的にアクションをトリガーしたり、プレイブックを実行して受信したセキュリティ アラートに応答したりします。 プレイブックにより、通知の送信、アカウントの無効化、問題のあるネットワークの特定などのアクションが実行されます。 

- [Security Center でワークフロー自動化を構成する](../security-center/workflow-automation.md)

- [Azure Security Center で脅威への自動対応を設定する](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel で脅威への自動対応を設定します](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="posture-and-vulnerability-management"></a>体制と脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「体制と脆弱性の管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)」を参照してください。*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure サービスのセキュリティで保護された構成を確立する 

**ガイダンス**:Azure Lighthouse は、以下のサービス固有のポリシーをサポートしています。これは、Azure リソースの構成の監査と適用のために、Azure Security Center に用意されています。 これは、Azure Security Center または Azure Policy イニシアチブで構成できます。

- Azure Lighthouse を使用してオンボードするテナント ID の管理を許可する

- 管理テナントへのスコープの委任を監査する

Azure Blueprints を使用すると、1 つのブループリント定義で、Azure Resource Manager テンプレート、Azure RBAC コントロール、ポリシーなど、サービスとアプリケーション環境のデプロイと構成を自動化することができます。

- [Azure Lighthouse ポリシー](samples/policy-reference.md)

- [チュートリアル - コンプライアンスを適用するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure サービスのセキュリティで保護された構成を維持する

**ガイダンス**:Azure Lighthouse は、以下のサービス固有のポリシーをサポートしています。これは、Azure リソースの構成の監査と適用のために、Azure Security Center に用意されています。 これは、Azure Security Center または Azure Policy イニシアチブで構成できます。

- [Azure Lighthouse ポリシー](samples/policy-reference.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: コンピューティング リソースにセキュリティで保護された構成を確立する

**ガイダンス**:Azure Security Center と Azure Policy を使用して、VM、コンテナーなど、すべてのコンピューティング リソースにセキュリティで保護された構成を確立します。

- [Azure Security Center の推奨事項を監視する方法](../security-center/security-center-recommendations.md) 

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 定期的に攻撃シミュレーションを実施する

**ガイダンス**:必要に応じて、Azure リソースの侵入テストまたはレッド チーム アクティビティを実施し、セキュリティに関するすべての重大な調査結果が確実に修復されるようにします。
お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [Azure での侵入テスト](../security/fundamentals/pen-testing.md)

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="governance-and-strategy"></a>ガバナンスと戦略

*詳細については、[Azure セキュリティ ベンチマークの「ガバナンスと戦略](../security/benchmarks/security-controls-v2-governance-strategy.md)」を参照してください。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 資産の管理とデータ保護の戦略を定義する 

**ガイダンス**:システムとデータを継続的に監視および保護するための明確な戦略が文書化および伝達されるようにします。 ビジネスに不可欠なデータとシステムの検出、評価、保護、監視の優先順位を決定します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   ビジネス リスクに応じたデータ分類標準

-   リスクと資産のインベントリに対するセキュリティ組織の可視性 

-   Azure サービスを使用するためのセキュリティ組織の承認 

-   ライフサイクルを通じた資産のセキュリティ

-   組織のデータ分類に従った必要なアクセス制御戦略

-   Azure ネイティブおよびサードパーティのデータ保護機能の使用

-   転送中および保存中のユース ケースのデータ暗号化要件

-   適切な暗号化標準

詳細については、次のリファレンスを参照してください。
- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../security/fundamentals/encryption-overview.md)

- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure セキュリティ ベンチマーク - アセット管理](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure セキュリティ ベンチマーク - データ保護](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 企業のセグメント化戦略を定義する 

**ガイダンス**:ID、ネットワーク、アプリケーション、サブスクリプション、管理グループ、その他のコントロールを利用し、アセットへのアクセスをセグメント化する企業規模の戦略を確立します。

セキュリティ分離の必要性と、互いと通信し、データにアクセスする必要があるシステムの日常的操作を有効にするという必要性のバランスを慎重に取ります。

セグメント化戦略は、ネットワーク セキュリティ、ID とアクセス モデル、アプリケーション アクセス許可とアクセス モデル、人的プロセスの制御など、あらゆるコントロールの種類を対象として確実に一貫性をもって実装します。

- [Azure のセグメント化戦略に関するガイド (動画)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure のセグメント化戦略に関するガイド (ドキュメント)](/security/compass/governance#enterprise-segmentation-strategy)

- [ネットワークのセグメント化を企業のセグメント化戦略に合わせる](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: セキュリティ態勢管理の戦略を定義する

**ガイダンス**:個々の資産とそれらがホストされている環境に対するリスクを継続的に測定し、軽減します。 高い価値を持つ資産と、攻撃に晒される可能性の高い部分 (公開されたアプリケーション、ネットワークのイングレス ポイントとエグレス ポイント、ユーザーと管理者のエンドポイントなど) を優先します。

- [Azure セキュリティ ベンチマーク - 体制と脆弱性の管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 組織の役割、責任、責務を整合させる

**ガイダンス**:セキュリティ組織における役割と責任に関する明確な戦略が文書化されて伝えられるようにします。 セキュリティに関する決定についてわかりやすく説明すること、共有される責任モデルについて全員に教育すること、クラウドをセキュリティで保護するテクノロジについて技術チームに教育することを優先とします。

- [Azure のセキュリティのベスト プラクティス 1 – 人: クラウド セキュリティに関する取り組みについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure のセキュリティのベスト プラクティス 2 - 人: クラウド セキュリティ テクノロジについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure のセキュリティのベスト プラクティス 3 - プロセス: クラウドのセキュリティに関する意思決定のアカウンタビリティを割り当てる](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: ネットワーク セキュリティ戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure ネットワーク セキュリティ アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元的なネットワーク管理とセキュリティ責任

-   エンタープライズ セグメント化戦略と一致する仮想ネットワーク セグメント化モデル

-   さまざまな脅威と攻撃のシナリオにおける修復戦略

-   インターネット エッジとイングレスおよびエグレス戦略 /azure/security/benchmarks/security-controls-v2-logging-threat-detection
-   クラウドとオンプレミスのハイブリッド相互依存戦略

-   最新のネットワーク セキュリティ成果物 (例: ネットワーク図、参照ネットワーク アーキテクチャ)

詳細については、次のリファレンスを参照してください。
- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure のネットワーク セキュリティの概要](../security/fundamentals/network-overview.md)

- [エンタープライズ ネットワーク アーキテクチャ戦略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID と特権アクセス戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure の ID と特権アクセス アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元化された ID と認証システム、および他の内部および外部 ID システムとのその相互接続

-   さまざまなユース ケースおよび条件における強力な認証方法

-   高い特権を持つユーザーの保護

-   異常なユーザー アクティビティの監視と処理  

-   ユーザー ID とアクセスの確認と調整のプロセス

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ID 管理](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure セキュリティ ベンチマーク - 特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 管理のセキュリティの概要](../security/fundamentals/identity-management-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: ログ記録と脅威対応戦略を定義する

**ガイダンス**:コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 アナリストが、統合や手動による手順ではなく、脅威の対応に集中できるように、質の高いアラートとシームレスなエクスペリエンスを提供することを優先してください。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   セキュリティ運用 (SecOps) 組織の役割と責任 

-   NIST または他の業界フレームワークと一致する、明確に定義されたインシデント対応プロセス 

-   脅威の検出、インシデント対応、コンプライアンスのニーズに対応するためのログのキャプチャと保持

-   SIEM、Azure のネイティブ機能、その他のソースを使用した、脅威に関する情報の一元的な可視化と関連付け 

-   顧客、サプライヤー、および関心を持つパブリック パーティに関するコミュニケーションと通知の計画

-   ログ記録と脅威の検出、フォレンジクス、攻撃の修復と根絶など、インシデント処理に対する Azure ネイティブおよびサードパーティのプラットフォームの使用

-   インシデントとインシデント発生後のアクティビティを処理するためのプロセス (教訓や証拠の保持など)

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure セキュリティ ベンチマーク - インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure のセキュリティのベスト プラクティス 4 - プロセス: クラウドのインシデント対応プロセスを更新する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 導入フレームワーク、ログ、およびレポートの決定ガイド](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure のエンタープライズ スケーリング、管理、監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する