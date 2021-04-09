---
title: Azure Bastion 用の Azure セキュリティ ベースライン
description: Azure Bastion セキュリティ ベースラインは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースを示します。
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b8f0cfdf3ca6e3b0bb0b455a5690d6a2727786ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586754"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Azure Bastion 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Azure Bastion に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Bastion に適用できる関連ガイダンスによって定義されています。 Azure Bastion に適用できない **制御** は、除外されています。

Azure Bastion を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Bastion セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-controls-v2-network-security.md)」を参照してください。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 内部トラフィック用のセキュリティを実装する

**ガイダンス**:Azure Bastion リソースをデプロイする場合は、既存の仮想ネットワークを作成または使用する必要があります。 すべての Azure 仮想ネットワークが、ビジネス リスクに合わせたエンタープライズ セグメント化の原則に従っていることを確認します。 組織のリスクが高くなる可能性があるシステムは、独自の仮想ネットワーク内に隔離し、ネットワーク セキュリティ グループ (NSG) で十分に保護する必要があります。

Azure Bastion サービスの正常な動作を実現するには、次のポートを開く必要があります。

- イグレス トラフィック:
   - パブリック インターネットからのイグレス トラフィック: Azure Bastion によってパブリック IP が作成されます。このパブリック IP では、イグレス トラフィック用にポート 443 が有効になっている必要があります。 AzureBastionSubnet でポート 3389/22 が開かれている必要はありません。 

   - Azure Bastion からのイグレス トラフィックのコントロール プレーン: コントロール プレーン接続の場合は、GatewayManager サービス タグからのポート 443 受信を有効にします。 これにより、コントロール プレーン、つまりゲートウェイ マネージャーから Azure Bastion への通信が可能になります。

- エグレス トラフィック:

   - 仮想マシン (VM) へのエグレス トラフィック:Azure Bastion は、プライベート IP 経由でターゲット VM にリーチします。 NSG では、他のターゲット VM サブネットへのエグレス トラフィックをポート 3389 と 22 に許可する必要があります。

   - Azure の他のパブリックエンド ポイントへのエグレス トラフィック: Azure Bastion から Azure 内のさまざまなパブリック エンドポイントに接続できる必要があります (たとえば、診断ログや測定ログを格納するため)。 このため、Azure Bastion には AzureCloud サービス タグに対する 443 への送信が必要です。

ゲートウェイ マネージャーと Azure サービス タグへの接続は、Azure 証明書によって保護 (ロックダウン) されています。 それらのリソースの顧客を含む外部エンティティは、これらのエンドポイントで通信できません。 

- [セキュリティ規則を使用してネットワーク セキュリティ グループを作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

- [Bastion NSG の要件に関する詳細情報](bastion-nsg.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: プライベート ネットワークをまとめて接続する

**ガイダンス**:Azure Bastion は、プライベート ネットワーク経由でアクセスできるエンドポイントを公開しません。 Azure Bastion は、ピアリングされたネットワークへのデプロイをサポートしています。これにより、Bastion のデプロイメントを一元化してネットワーク間接続を有効できます。

- [Azure Bastion と仮想ネットワークのピアリング](vnet-peering.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](../security/benchmarks/security-controls-v2-identity-management.md).* 」を参照してください。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1:Azure Active Directory を中央 ID および認証システムとして標準化する

**ガイダンス**:Azure Bastion は、Azure の既定の ID およびアクセス管理サービスである Azure Active Directory (Azure AD) と統合されています。 ユーザーは、Azure AD Authentication を使用して Azure portal にアクセスし、Azure Bastion サービスを管理 (Bastion リソースを作成、更新、削除) できます。

Azure Bastion を使用した仮想マシンへの接続は、SSH キーか、ユーザー名とパスワードの組み合わせで行います。Azure AD の資格情報の使用は現在サポートされていません。 

SSH キーを Azure Key Vault シークレットとして格納し、これらのシークレットで、Azure Bastion を使用して仮想マシンに接続できます。 これらのシークレットへのユーザー アクセスを制御するには、個々のユーザーまたは Azure AD グループに [Key Vault アクセス ポリシーを割り当てます](../key-vault/general/assign-access-policy-portal.md)。 この方法を使用して仮想マシンに接続するには、ユーザーに次のアクセス許可が必要です。
- 選択した Azure Key Vault に格納されているシークレットに対する **取得** アクセス権
- 選択した Azure Key Vault に格納されているシークレットに対する **リスト** アクセス権

SSH キーまたはユーザー名とパスワードによる方法に加え、ユーザーが Azure Bastion を使用して仮想マシンに接続するときは、次のロールの割り当てが必要です。
- 対象の仮想マシンに対する閲覧者ロール
- 対象の仮想マシンのプライベート IP を持つ NIC に対する閲覧者ロール
- Azure Bastion リソースに対する閲覧者ロール

詳細については、次のリファレンスを参照してください。

- [Azure Bastion を使用して Linux 仮想マシンに接続する](bastion-connect-vm-ssh.md)

- [Azure Bastion を使用して Windows 仮想マシンに接続する](bastion-connect-vm-rdp.md)

- [Azure 組み込みロール](../role-based-access-control/built-in-roles.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3:アプリケーションのアクセスに Azure AD シングル サインオン (SSO) を使用する

**ガイダンス**:Azure Bastion は、仮想マシンのリソースに対して認証を行うときの方法として SSO をサポートしていません。サポートされるのは、SSH、またはユーザー名とパスワードの組み合わせのみです。 ただし、Azure Bastion は、Azure Active Directory (Azure AD) を使用して、サービス全体に対して ID とアクセスを管理します。 ユーザーは、Azure AD に対して認証を行い、Azure Bastion リソースにアクセスして管理できます。また、Azure AD Connect を介して同期された自分のエンタープライズ ID でシームレスにシングルサインオンできます。 

- [Azure AD を使用したアプリケーションの SSO について理解する](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Azure Bastion を使用して Linux 仮想マシンに接続する](bastion-connect-vm-ssh.md)

- [Azure Bastion を使用して Windows 仮想マシンに接続する](bastion-connect-vm-rdp.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス**:Azure Bastion は、サービスのアクセスと管理のために Azure Active Directory (Azure AD) と統合されています。 Azure AD テナント用に Azure Active Directory Multi-Factor Authentication を構成します。 Azure AD では、多要素認証 (MFA) と強力なパスワードレスの方法によって、強力な認証制御がサポートされています。
  
- 多要素認証:Azure AD MFA を有効にして、MFA のセットアップに対する Azure Security Center ID とアクセス管理の推奨事項に従います。 MFA は、サインインの条件とリスク要因に基づいて、すべてのユーザー、選択されたユーザー、またはユーザー単位のレベルで適用できます。 

- パスワードレスの認証:次の 3 つのパスワードレス認証オプションを利用できます: Windows Hello for Business、Microsoft Authenticator アプリ、スマート カードなどのオンプレミスの認証方法。 

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory のパスワードレス認証オプションの概要](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

**ガイダンス**:Azure Bastion リモート セッションの診断ログを有効にします。それらのログを使用して、どのユーザーが、どのワークロードに、いつ、どこから接続したかということや、他の関連するログ情報を確認できます。 Azure Monitor を使用して、ログに記録された特定の Bastion セッションについてのアラートを作成し、ログで異常が見つかった場合に通知を受け取ることもできます。

- [診断ログを有効にする方法の詳細情報](diagnostic-logs.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス**:Azure Bastion サービスには Azure portal 経由でのみアクセスできます。Azure portal へのアクセスは、Azure Active Directory (Azure AD) の条件付きアクセスを使用して制限できます。 特定の IP 範囲からのユーザー ログインに対する MFA の使用の要求など、ユーザー定義の条件に基づく、より詳細なアクセス制御のために、Azure AD の条件付きアクセスを使用します。

また、ユーザーは、ドメインに参加している仮想マシン レベルで、さまざまなロールベースのアクセス制御ポリシーを使用して、仮想マシンへのアクセスをさらに制限することもできます。

- [Azure AD の条件付きアクセスに関する詳細情報](../active-directory/conditional-access/overview.md)

- [Azure での条件付きアクセスの概要](../active-directory/conditional-access/overview.md)

- [一般的な条件付きアクセス ポリシー](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [条件付きアクセスを使用して認証セッション管理を構成する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)」を参照してください。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**:Azure Bastion は、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、特定の仮想マシンに接続するためのアクセスを許可するアカウントを制限することで、ビジネス クリティカルなシステムへのアクセスを分離します。 最小限の特権のみ付与するという原則に従い、ユーザーには、そのユーザーが実行する特定のタスクに必要なアクセス許可のみを付与してください。

また、お使いのビジネス クリティカルなアクセス対象への管理アクセス権を持つ、管理システム、ID システム、セキュリティ システムへのアクセスも制限してください (Active Directory Domain Controller (DC)、セキュリティ ツール、システム管理ツール、ビジネス クリティカル システムにインストールされたエージェントなど)。 これらの管理システムおよびセキュリティ システムを侵害した攻撃者は、それらをすぐに悪用してビジネス クリティカルな資産を侵害することができます。

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。

- [Azure Bastion を使用して仮想マシンにアクセスするために必要なロール](bastion-faq.md#roles)

- [Azure のコンポーネントと参照モデル](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理グループ アクセス](../governance/management-groups/overview.md#management-group-access)

- [Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Bastion は、Azure Active Directory (Azure AD) アカウントと Azure RBAC を使用してそのリソースを管理します。 ユーザー アカウントとアクセス割り当てを定期的に見直して、アカウントとそのアクセス権を適切に保ってください。 Azure AD アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てをレビューすることができます。 Azure AD レポートによって、古いアカウントの検出に役立つログが提供されます。 また、Azure AD Privileged Identity Management を使用してアクセス レビュー レポート ワークフローを作成し、レビュー プロセスを容易にすることもできます。

さらに、過剰な数の管理者アカウントが作成された場合にアラートを発行したり、古い管理者アカウントや不適切に構成されている管理者アカウントを特定するように Azure Privileged Identity Management を構成することもできます。

- [Privileged Identity Management (PIM) で Azure リソース ロールのアクセス レビューを作成する](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [委任へのアクセスを削除する](../lighthouse/how-to/remove-delegation.md)

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4:Azure AD で緊急アクセスを設定する

**ガイダンス**:Azure Bastion は、リソースを管理するため、Azure Active Directory および Azure RBAC と統合されています。 Azure AD 組織から誤ってロックアウトされるのを防ぐために、通常の管理者アカウントを使用できない場合にアクセスするための緊急アクセス用アカウントを設定します。 緊急アクセス用アカウントは高い特権を持っており、特定の個人に割り当てることはできません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" に制限されます。

緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は安全に保管し、緊急時にのみそれらを使うことを許可された個人のみに知らせる必要があります。

- [Azure AD で緊急アクセス用アカウントを管理する](../active-directory/roles/security-emergency-access.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5:エンタイトルメント管理を自動化する 

**ガイダンス**:Azure Bastion は、リソースを管理するため、Azure Active Directory (Azure AD) および Azure RBAC と統合されています。 Azure AD のエンタイトルメント管理機能を使用して、アクセスの割り当て、レビュー、有効期限など、アクセス要求のワークフローを自動化します。 2 段階または複数段階の承認もサポートされています。

- [Azure AD アクセス レビューとは](../active-directory/governance/access-reviews-overview.md)

- [Azure AD エンタイトルメント管理とは](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

**ガイダンス**:セキュリティで保護された分離したワークステーションは、管理者、開発者、重要なサービス オペレーターのような機密性の高い役割のセキュリティには非常に重要です。 要件に応じて、厳密にセキュリティ保護されたユーザー ワークステーションを使用して、運用環境の Azure Bastion リソースに対して管理タスクを実行できます。 Azure Active Directory、Microsoft Defender Advanced Threat Protection (ATP)、または Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを展開します。 セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。 

- [特権アクセス ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [特権アクセス ワークステーションを展開する](/security/compass/privileged-access-deployment)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7:Just Enough Administration (最小限の特権の原則) に従う 

**ガイダンス**:Azure Bastion は、リソースを管理するため、Azure のロールベースのアクセス制御 (RBAC) と統合されています。 Azure RBAC を使用すると、ロールの割り当てによって Azure リソースへのアクセスを管理できます。 これらの組み込みロールを、ユーザー、グループ、サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。 Azure RBAC を使用してリソースに割り当てる特権は、常に、ロールで必要なものに制限する必要があります。 これは、Azure Active Directory Privileged Identity Management (PIM) のジャスト イン タイム (JIT) アプローチを補完するものであり、定期的に見直す必要があります。 組み込みロールを使用してアクセス許可を割り当て、必要な場合にのみカスタム ロールを作成します。

ユーザーが Azure Bastion を使用して仮想マシンに接続するときは、次のロールの割り当てが必要です。
- 対象の仮想マシンに対する閲覧者ロール
- 対象の仮想マシンのプライベート IP を持つ NIC に対する閲覧者ロール
- Azure Bastion リソースに対する閲覧者ロール

詳細については、次のリファレンスを参照してください。

- [Azure Bastion を使用して Linux 仮想マシンに接続する](bastion-connect-vm-ssh.md)

- [Azure Bastion を使用して Windows 仮想マシンに接続する](bastion-connect-vm-rdp.md)

- [Azure 組み込みロール](../role-based-access-control/built-in-roles.md)

**Azure Security Center の監視**: 適用なし

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

**ガイダンス**:Azure Bastion リソース、リソース グループ、サブスクリプションにタグを適用して、それらを論理的に分類できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。 セキュリティ チームが、Azure 上の資産の継続的に更新されるインベントリに確実にアクセスできるようにします。 Azure Resource Graph を使用すると、クエリを実行してサブスクリプション内のすべてのリソース (Azure サービス、アプリケーション、ネットワーク リソースなど) を検出できます。

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center の資産インベントリ管理](../security-center/asset-inventory.md)

- 資産のタグ付けの詳細については、「[リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)」を参照

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

**ガイダンス**:環境内でユーザーがプロビジョニングできるサービスを監査、制限 (Azure Bastion リソースのデプロイメントの許可または拒否を含む) するには、Azure Policy を使用します。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 また、Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成することもできます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general)

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4:アセット ライフサイクル管理のセキュリティを確保する

**ガイダンス**:デプロイされている Azure Bastion リソースが不要になった場合は、攻撃面を減らすため、これらのリソースへのアクセスを解除します。 ユーザーは、Azure portal、CLI、または REST API を使用して Azure Bastion リソースを管理できます。 また、実行中のリモート セッションが不要になった場合や、潜在的な脅威になると特定された場合は、そのセッションの削除または強制切断を実行できます。

- [リモート セッションの削除または強制切断](session-monitoring.md#view)

- [Azure ネットワーク CLI](/powershell/module/az.network/?preserve-view=true&view=azps-5.1.0#networking)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:Azure Bastion は、ID と認証のために Azure Active Directory (Azure AD) と統合されています。 Azure の条件付きアクセスを使用すると、ユーザーによる Azure Resource Manager の操作を制限できます。これは、"Microsoft Azure Management" アプリに "アクセスのブロック" を構成することで行います。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](../security/benchmarks/security-controls-v2-data-protection.md)」を参照してください。*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2:Azure ID とアクセスの管理のために脅威検出を有効にする

**ガイダンス**:Azure Bastion は Azure Active Directory (Azure AD) と統合されており、サービスには Azure portal を介してアクセスします。 既定では、サービスに対する管理操作 (作成、更新、削除など) は、Azure のアクティビティ ログを使用して取得されます。 また、ユーザーは Azure Bastion リソースのログ (セッション BastionAuditLogs など) を有効にして Azure Bastion セッションを追跡する必要もあります。

Azure AD では、次のユーザー ログが記録され、これは Azure AD レポートで確認できます。また、より高度な監視と分析のユース ケースの場合は、Azure Monitor、Azure Sentinel、またはその他の SIEM/監視ツールと統合できます。 
-   サインイン - サインイン レポートは、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。

-   監査ログ - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。

-   リスクの高いサインイン - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

-   リスクのフラグ付きユーザー - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。

Azure Security Center で、認証試行の失敗回数が多すぎるなど、サブスクリプションにおける特定の不審なアクティビティや、使用すべきでないアカウントに対してアラートを生成することもできます。 Azure Security Center の脅威保護モジュールは、基本的なセキュリティ検疫監視に加えて、個々の Azure コンピューティング リソース (仮想マシン、コンテナー、アプリ サービスなど)、データ リソース (SQL DB、ストレージなど)、Azure サービス レイヤーから、さらに詳細なセキュリティ アラートを収集することもできます。 この機能を使用すると、個々のリソース内でアカウントの異常を確認できます。

- [Azure Bastion リソースのログ](diagnostic-logs.md)

- [Azure AD でアクティビティ レポートを監査する](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure Identity Protection を有効にする](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

**ガイダンス**:Azure Bastion リソースのログを有効にします。この診断ログを使用すると、どのユーザーが、どのワークロードに、いつ、どこから接続したかということや、他の関連するログ情報を確認できます。 ユーザーは、長期保存と監査のため、これらのログがストレージ アカウントに送信されるように構成することができます。

Azure Bastion リソースをデプロイした仮想ネットワークに適用されるネットワーク セキュリティ グループで、ネットワーク セキュリティ グループ (NSG) リソース ログと NSG フロー ログを有効にし、収集します。 これらのログは、ネットワーク セキュリティの分析に使用できるほか、インシデントの調査、脅威の特定、セキュリティ アラートの生成に役立ちます。 Azure Monitor Log Analytics ワークスペースにフロー ログを送信し、Traffic Analytics を使用して分析情報を提供できます。

- [Azure Bastion ログを有効にして使用する](diagnostic-logs.md)

- [ネットワーク セキュリティ グループのフローのログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Firewall のログとメトリック](../firewall/logs-and-metrics.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Network Watcher を使用した監視](../network-watcher/network-watcher-monitoring-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**:自動的に利用できるアクティビティ ログには、読み取り操作 (GET) を除く、Azure Bastion リソースに対するすべての書き込み操作 (PUT、POST、DELETE) が含まれています。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure でのログ記録とログのさまざまな種類について](../azure-monitor/essentials/platform-logs-overview.md)

- [Azure Bastion で Azure リソース ログを有効にする](diagnostic-logs.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**:ログ記録のストレージと分析を一元化して、相関関係を有効にします。 ログ ソースごとに、データ所有者、アクセス ガイダンス、ストレージの場所、データの処理とアクセスに使用するツール、データ保持の要件を割り当てたことを確認します。

Azure アクティビティ ログを一元的なログ記録に統合していることを確認します。 Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

さらに、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。

多くの組織では、頻繁に使用される "ホット" データに対しては Azure Sentinel を、使用頻度の低い "コールド" データに対しては Azure Storage を使用することを選択しています。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6:ログの保持期間を構成する

**ガイダンス**:Azure Bastion ログを格納するために使用されるすべてのストレージ アカウントまたは Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

Azure Monitor で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定できます。

- [Log Analytics ワークスペースの保有期間を構成する方法](../azure-monitor/logs/manage-cost-storage.md)

- [ Azure ストレージ アカウントでのリソース ログの格納](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

- [Azure Bastion ログを有効にして使用する](diagnostic-logs.md)

**Azure Security Center の監視**: 現在は使用できません

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

**ガイダンス**:Azure Policy を使用して、Azure Bastion の標準的なセキュリティ構成を定義して実装します。 Azure Bastion のネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Network" 名前空間内で Azure Policy エイリアスを使用します。 また、Azure Blueprints または ARM テンプレートを活用して Bastion リソースを安全かつ一貫してデプロイすることで、セキュリティで保護された構成を確立することもできます。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ARM テンプレートについて](../azure-resource-manager/templates/overview.md)

- [Azure Blueprints の概要](../governance/blueprints/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure サービスのセキュリティで保護された構成を維持する

**ガイダンス**:Azure Policy を使用して、Azure Bastion の標準的なセキュリティ構成を定義して実装します。 Bastion リソースのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Network" 名前空間内で Azure Policy エイリアスを使用します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

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

- [Azure セキュリティ ベンチマーク - アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)

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

-   インターネット エッジとイングレスおよびエグレス戦略

-   クラウドとオンプレミスのハイブリッド相互依存戦略

-   最新のネットワーク セキュリティ成果物 (例: ネットワーク図、参照ネットワーク アーキテクチャ)

詳細については、次のリファレンスを参照してください。
- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](../security/benchmarks/security-controls-v2-network-security.md)

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