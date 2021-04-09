---
title: Azure Active Directory の新着情報のアーカイブ | Microsoft Docs
description: このコンテンツ セットの概要セクションにある新着情報リリース ノートには、6 か月間のアクティビティが含まれています。 6 か月後、項目はメイン記事から削除され、このアーカイブ記事に配置されます。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e820194d36d2b80fcd28de7972dd5abe8a746345
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592950"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報のアーカイブ

主要な [Azure Active Directory の新着情報のリリース ノート](whats-new.md)の記事には過去 6 か月間の更新が含まれていますが、この記事には古い情報がすべて含まれています。

Azure Active Directory の新着情報のリリース ノートでは、次の情報が提供されています。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

---
## <a name="august-2020"></a>2020 年 8 月 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication Server ファイアウォールの要件に対する更新

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
2020 年 10 月 1 日以降、Azure MFA Server のファイアウォール要件には、追加の IP 範囲が必要になります。

組織に送信ファイアウォール規則がある場合は、MFA サーバーが必要なすべての IP 範囲と通信できるように規則を更新します。 IP 範囲については、「[Azure Multi-Factor Authentication Server ファイアウォールの要件](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)」を参照してください。

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>ID セキュリティ スコアのユーザー エクスペリエンスに対する今後の変更点

**種類:** 変更の計画  
**サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

ID セキュリティ スコア ポータルは、Microsoft セキュリティ スコアの[新しいリリース](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)で導入された変更に合わせて更新されています。 

変更を含むプレビュー バージョンは、9 月初めに利用可能になります。 プレビュー バージョンでの変更点は次のとおりです。
- ID セキュリティ スコアは、Microsoft セキュリティ スコアでのブランドに合わせて "ID のセキュリティ スコア" に名前が変更されます
- ポイントが標準スケールに正規化され、ポイントではなくパーセンテージで報告されます

このプレビューでは、お客様は既存のエクスペリエンスと新しいエクスペリエンスを切り替えることができます。 このプレビューは、2020 年 11 月末まで続きます。 プレビューの後、お客様は自動的に新しい UX エクスペリエンスに誘導されます。

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Azure AD での新しい制限付きゲスト アクセス許可 - パブリック プレビュー

**種類:** 新機能  
**サービス カテゴリ:** アクセス制御   
**製品の機能:** [ユーザー管理]

ゲスト ユーザーに対するディレクトリ レベルのアクセス許可が更新されました。 これらのアクセス許可により、管理者は外部のゲスト ユーザーのアクセスに対して追加の制限と制御を要求できます。 管理者は、外部のゲストによるユーザーとグループのプロファイルおよびメンバーシップ情報へのアクセスに対して制限を追加できるようになりました。 このパブリック プレビュー機能を使用すると、お客様は、グループ メンバーシップを難読化することで、外部ユーザーのアクセスを大規模に管理できます。これには、ゲスト ユーザーが属するグループのメンバーシップの表示の制限が含まれます。

詳細については、[制限されたゲスト アクセス許可](../enterprise-users/users-restrict-guest-permissions.md)および[ユーザーの既定のアクセス許可](./users-default-permissions.md)に関する記事を参照してください。
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>サービス プリンシパルに対するデルタ クエリの一般提供

**種類:** 新機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** 開発者エクスペリエンス
 
Microsoft Graph デルタ クエリで、v1.0 のリソースの種類がサポートされるようになりました。
- サービス プリンシパル

クライアントは、これらのリソースへの変更を効率的に追跡し、ローカル データ ストアでのそれらのリソースに対する変更を同期するための最適なソリューションを提供できます。 クエリでこれらのリソースを構成する方法については、「[デルタ クエリを使用して、Microsoft Graph データの変更を追跡する](/graph/delta-query-overview)」を参照してください。
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>oAuth2PermissionGrant に対するデルタ クエリの一般提供

**種類:** 新機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** 開発者エクスペリエンス

Microsoft Graph デルタ クエリで、v1.0 のリソースの種類がサポートされるようになりました。
- OAuth2PermissionGrant

クライアントは、これらのリソースへの変更を効率的に追跡し、ローカル データ ストアでのそれらのリソースに対する変更を同期するための最適なソリューションを提供できます。 クエリでこれらのリソースを構成する方法については、「[デルタ クエリを使用して、Microsoft Graph データの変更を追跡する](/graph/delta-query-overview)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 8 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2020 年 8 月には、フェデレーションをサポートする次の 25 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Backup365](https://portal.backup365.io/login)、[Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2)、[Alma SIS](https://almau.getalma.com/)、[Enlyft Dynamics 365 Connector](http://enlyft.com/)、[Serraview Space Utilization Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md)、[Uniq](https://web.uniq.app/)、[Visibly](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md)、[Edmentum - Courseware Assessments Exact Path](https://auth.edmentum.com/elf/login)、[CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome)、[Altamira HRM](../saas-apps/altamira-hrm-tutorial.md)、[WireWheel](../saas-apps/wirewheel-tutorial.md)、[Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common)、[Greenlight Enterprise Business Controls Platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md)、[Genetec Clearance](https://www.clearance.network/)、[iSAMS](../saas-apps/isams-tutorial.md)、[VeraSMART](../saas-apps/verasmart-tutorial.md)、[Amiko](https://amiko.web.rivero.app/)、[Twingate](https://auth.twingate.com/signup)、[Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/)、[Scalefusion](https://scalefusion.com/users/sign_in/)、[Bpanda](https://goto.bpanda.com/login)、[Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect)、[FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)、[Wandera End User](https://www.wandera.com/)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Azure AD DS でリソース フォレストを使用できるようになりました 

**種類:** 新機能 **サービス カテゴリ:** Azure AD Domain Services   
**製品の機能:** Azure AD Domain Services
 
Azure AD Domain Services でのリソース フォレストの機能が一般提供されました。 スマートカード認証を含め、Azure AD Domain Services を使用するために、パスワード ハッシュ同期なしで承認を有効にできるようになりました。 詳細については、「[Azure Active Directory Domain Services (プレビュー) のレプリカ セットの概念と機能](../../active-directory-domain-services/concepts-replica-sets.md)」を参照してください。
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Azure AD DS マネージド ドメインのリージョン レプリカのサポートを使用できるようになりました

**種類:** 新機能   
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services
 
マネージド ドメインを拡張して、Azure AD テナントごとに複数のレプリカ セットを使用できます。 レプリカ セットは、Azure AD Domain Services がサポートされている任意の Azure リージョンの、ピアリングされた任意の仮想ネットワークに追加できます。 異なる Azure リージョンにレプリカ セットを追加することで、1 つの Azure リージョンがオフラインになった場合に、レガシ アプリケーションの地理的なディザスター リカバリーが実現されます。 詳細については、「[Azure Active Directory Domain Services (プレビュー) のレプリカ セットの概念と機能](../../active-directory-domain-services/concepts-replica-sets.md)」を参照してください。

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD の自分のサインインの一般提供

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** エンド ユーザー エクスペリエンス
 
Azure AD の [自分のサインイン] は、企業ユーザーがサインイン履歴を調べて異常なアクティビティを確認できるようにする新しい機能です。 また、この機能により、エンド ユーザーは疑わしいアクティビティについて "自分ではありません" または "自分の操作です" を報告できます。 この機能の使用方法の詳細については、「[[自分のサインイン] ページから最近のサインイン アクティビティを表示および検索する](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)」を参照してください。
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>SAP SuccessFactors HR 主導による Azure AD へのユーザー プロビジョニングが一般提供されるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
権限のある ID ソースとしての SAP SuccessFactors と Azure AD を統合できるようになり、新しい採用者や解雇などの HR イベントを使用してエンド ツー エンドの ID ライフサイクルを自動化し、Azure AD でのアカウントのプロビジョニングとプロビジョニング解除を促進できます。 

Azure AD への SAP SuccessFactors 受信プロビジョニングを構成する方法の詳細については、「[SAP SuccessFactors から Active Directory へのユーザー プロビジョニングを構成する](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)」チュートリアルを参照してください。
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Azure AD B2C に対するカスタム Open ID Connect MS Graph API のサポート

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
以前のカスタム Open ID Connect プロバイダーは、Azure portal によってのみ追加または管理できました。 Azure AD B2C のお客様は、Microsoft Graph API のベータ版を使用してそれらを追加および管理することもできるようになりました。 API でこのリソースを構成する方法については、「[identityProvider リソース タイプ](/graph/api/resources/identityprovider?view=graph-rest-beta)」を参照してください。
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Azure AD の組み込みロールをクラウド グループに割り当てる

**種類:** 新機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御

この新機能を使用して、Azure AD の組み込みロールをクラウド グループに割り当てることができるようになりました。 たとえば、SharePoint 管理者ロールを Contoso_SharePoint_Admins グループに割り当てることができます。 また、PIM を使用すると、継続的なアクセス権を付与するのではなく、グループをロールの適格なメンバーにすることもできます。 この機能の構成方法については、「[クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する (プレビュー)](../roles/groups-concept.md)」を参照してください。
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Insights ビジネス リーダーの組み込みロールを利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御
 
Insights ビジネス リーダー ロールのユーザーは、[M365 Insights アプリケーション](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)を使用して、一連のダッシュボードと分析情報にアクセスできます。 これには、すべてのダッシュボード、表示される分析情報、およびデータ探索機能へのフル アクセスが含まれます。 ただし、このロールのユーザーには、製品の構成設定へのアクセス権がありません (これは Insights 管理者ロールの責任範囲です)。 このロールの詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../roles/permissions-reference.md#insights-business-leader)」を参照してください
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Insights 管理者の組み込みロールを利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御
 
Insights 管理者ロールのユーザーは、[M365 Insights アプリケーション](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)の管理機能の完全なセットにアクセスできます。 このロールのユーザーは、ディレクトリ情報の読み取り、サービスの正常性の監視、サポート チケットの提出、Insights 管理者設定の側面へのアクセスを行うことができます。 このロールの詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../roles/permissions-reference.md#insights-administrator)」を参照してください
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>アプリケーション管理者とクラウド アプリケーション管理者は、アプリケーションの拡張機能プロパティを管理できます

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御
 
以前は、[拡張機能プロパティ](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)を管理できるのは全体管理者だけでした。 アプリケーション管理者とクラウド アプリケーション管理者もこの機能を利用できるようになりました。
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 修正プログラム 4.6.263.0 とコネクタ 1.1.1301.0

**種類:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理

Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) の[修正プログラム ロールアップ パッケージ (ビルド 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) の提供が開始されました。 このロールアップ パッケージには、MIM CM、MIM 同期マネージャー、PAM コンポーネントの更新プログラムが含まれています。 さらに、MIM 汎用コネクタ ビルド 1.1.1301.0 には、Graph コネクタの更新プログラムが含まれています。

---

## <a name="july-2020"></a>2020 年 7 月

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>IT 管理者は、条件付きアクセスを使用してクライアント アプリを対象にする必要があります

**種類:** 変更の計画   
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
条件付きアクセスでのクライアント アプリ条件の GA リリースにより、新しいポリシーが既定で、すべてのクライアント アプリケーションに適用されるようになりました。 これには、レガシ認証クライアントが含まれます。 既存のポリシーは変更されませんが、ポリシーによって提供されるクライアント アプリを簡単に確認できるように、"*構成の "はい、いいえ"* " の切り替えは既存のポリシーから削除されます。 

新しいポリシーを作成するときは、従来の認証をまだ使用しているユーザーとサービス アカウントを除外してください。そうしないと、ブロックされます。 [詳細については、こちらを参照してください](../conditional-access/concept-conditional-access-conditions.md)。
 
---

### <a name="upcoming-scim-compliance-fixes"></a>今後の SCIM コンプライアンスの修正

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Azure AD プロビジョニング サービスでは、アプリケーションとの統合のために SCIM 標準が利用されています。 SCIM 標準の実装は進化しており、PATCH 操作の実行方法に関する変更や、リソースでのプロパティ "アクティブ" の設定が想定されています。 [詳細については、こちらを参照してください](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)。
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure 管理者ポータルのグループ所有者の設定が変更される

**種類:** 変更の計画  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

グループの全般設定ページの所有者の設定は、Azure 管理者ポータルとアクセス パネルで制限されたユーザー グループに対して所有者割り当て特権を制限するように構成できます。 間もなく、この 2 つの UX ポータルでグループ所有者特権を割り当てることができるだけでなく、PowerShell や Microsoft Graph など、バックエンドでポリシーを適用してエンドポイント間で一貫性のある動作を実現することもできるようになります。 

現在の設定を使用していないお客様については現在の設定を無効にする作業が始められており、今後数か月の間に、グループ所有者特権に対してユーザーを対象にするオプションが提供されます。 グループ設定の更新に関するガイダンスについては、[Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) を使用したグループ情報の編集に関するページを参照してください。

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 登録サービスでの TLS 1.0 および 1.1 のサポートが終了します

**種類:** 変更の計画  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** プラットフォーム

トランスポート層セキュリティ (TLS) 1.2 およびサーバーとクライアントの更新は、間もなく Azure Active Directory Device Registration サービスと通信するようになります。 Azure AD Device Registration サービスとの通信用の TLS 1.0 および 1.1 のサポートは廃止されます。
- 2020 年 8 月 31 日、すべてのソブリン クラウド (GCC High、DoD など)
- 2020 年 10 月 30 日、すべての商用クラウド

Azure AD 登録サービスに対する TLS 1.2 について[詳細を確認してください](../devices/reference-device-registration-tls-1-2.md)。

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD サインイン ログで確認できる Windows Hello for Business のサインイン

**種類:** 固定  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
Windows Hello for Business を使用すると、エンド ユーザーはジェスチャ (PIN や生体認証など) を使用して Windows コンピューターにサインインできます。 Azure AD 管理者は、組織のパスワードレス認証への移行の過程として、Windows Hello for Business のサインインを他の Windows サインインと区別することができます。 

管理者は、Azure portal の Azure AD サインイン ブレードで Windows サインイン イベントに対する [認証の詳細] タブを調べることにより、Windows 認証で Windows Hello for Business が使用されたかどうかを確認できます。 Windows Hello for Business 認証では、[認証方法] フィールドに "WindowsHelloForBusiness" が含まれます。 サインイン ログの解釈の詳細については、[サインイン ログのドキュメント](../reports-monitoring/concept-sign-ins.md)を参照してください。
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>グループ削除の動作の修正とパフォーマンスの向上

**種類:** 固定  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
以前は、グループを "スコープ内" から "スコープ外" に変更し、変更が完了する前に管理者が再起動をクリックすると、グループ オブジェクトは削除されませんでした。 現在は、グループ オブジェクトがスコープ外になった (無効、削除済み、未割り当て、またはスコープ フィルターを通過しなかった) 時点で、ターゲット アプリケーションから削除されます。 [詳細については、こちらを参照してください](../app-provisioning/how-provisioning-works.md#incremental-cycles)。
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>パブリック プレビュー:管理者は、アクセス レビューを作成するときに、レビュー担当者宛のメールにカスタム コンテンツを追加できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
新しいアクセス レビューが作成されると、レビュー担当者は、アクセス レビューの完了を求めるメールを受け取ります。 多くのお客様から、連絡先情報などのカスタム コンテンツ、またはレビュー担当者に指示するための他のサポート コンテンツをメールに追加する機能の要望がありました。 

パブリック プレビューになった機能を使用すると、管理者は、Azure AD アクセス レビューの [詳細設定] セクションでコンテンツを追加することにより、レビュー担当者に送信されるメールでカスタム コンテンツを指定できます。 アクセス レビューの作成に関するガイダンスについては、「[グループとアプリケーションのアクセス レビューを Azure AD アクセス レビューで作成する](../governance/create-access-review.md)」を参照してください。
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>シングルページ アプリの承認コード フローを使用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 開発者エクスペリエンス
 
最新のブラウザーのサード パーティ Cookie の制限 (Safari ITP など) により、SPA では、SSO を維持するために、暗黙的フローではなく、承認コード フローを使用する必要があります。MSAL.js v 2.x では、認証コード フローをサポートするようになりました。 

Azure portal の対応する更新により、ご自分の SPA の種類を "spa" に更新し、認証コード フローを使用することができます。 詳細については、「[承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する](../develop/quickstart-v2-javascript-auth-code.md)」を参照してください。
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD アプリケーション プロキシでリモート デスクトップ サービス Web クライアントがサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御

Azure AD アプリケーション プロキシでリモート デスクトップ サービス (RDS) Web クライアントがサポートされるようになりました。 RDS Web クライアントを使用すると、ユーザーは、Microsoft Edge、Internet Explorer 11、Google Chrome などの任意の HTML5 対応ブラウザーを使用して、リモート デスクトップ インフラストラクチャにアクセスできます。ユーザーは、どこからでもローカル デバイスと同様にリモート アプリやデスクトップと対話できます。 Azure AD アプリケーション プロキシを使用すると、すべての種類のリッチ クライアント アプリに対して事前認証と条件付きアクセス ポリシーを適用することにより、RDS デプロイのセキュリティを強化できます。 詳細については、「[Azure AD アプリケーション プロキシを使用したリモート デスクトップの発行](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)」を参照してください。
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>次世代の Azure AD B2C ユーザー フローのパブリック プレビュー

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
簡素化されたユーザー フロー エクスペリエンスは、プレビュー機能と同等の機能を備えており、すべての新機能のホームとなります。 ユーザーは、同じユーザー フロー内で新機能を有効にすることができるため、新しい機能をリリースするたびに複数のバージョンを作成する必要が少なくなります。 最後に、新しいユーザー フレンドリな UX により、ユーザー フローの選択と作成が簡単になります。 今すぐ[ユーザー フローを作成](../../active-directory-b2c/tutorial-create-user-flows.md)して試してください。 

ユーザー フローの詳細については、「[Azure Active Directory B2C のユーザー フロー バージョン](../../active-directory-b2c/user-flow-versions.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 7 月には、フェデレーションをサポートする次の 55 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Clap Your Hands](http://www.rmit.com.ar/)、[Appreiz](https://microsoftteams.appreiz.com/)、[Inextor Vault](https://inexto.com/inexto-suite/inextor)、[Beekast](https://my.beekast.com/)、[Templafy OpenID Connect](https://app.templafy.com/)、[PeterConnects receptionist](https://msteams.peterconnects.com/)、[AlohaCloud](https://appfusions.alohacloud.com/auth)、[Control Tower](https://bpm.tnxcorp.com/sso/microsoft)、[Cocoom](https://start.cocoom.com/)、[COINS Construction Cloud](https://sso.coinsconstructioncloud.com/#login/)、[Medxnote MT](https://task.teamsmain.medx.im/authorization)、[Reflekt](https://reflekt.konsolute.com/login)、[Rever](https://app.reverscore.net/access)、[MyCompanyArchive](https://login.mycompanyarchive.com/)、[GReminders](https://app.greminders.com/o365-oauth)、[Titanfile](../saas-apps/titanfile-tutorial.md)、[Wootric](../saas-apps/wootric-tutorial.md)、[SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US)、[OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md)、[Datasite](../saas-apps/datasite-tutorial.md)、[BlogIn](../saas-apps/blogin-tutorial.md)、[IntSights](../saas-apps/intsights-tutorial.md)、[kpifire](../saas-apps/kpifire-tutorial.md)、[Textline](../saas-apps/textline-tutorial.md)、[Cloud Academy - SSO](../saas-apps/cloud-academy-sso-tutorial.md)、[Community Spark](../saas-apps/community-spark-tutorial.md)、[Chatwork](../saas-apps/chatwork-tutorial.md)、[CloudSign](../saas-apps/cloudsign-tutorial.md)、[C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md)、[SmartHR](https://smarthr.jp/)、[NumlyEngage™](../saas-apps/numlyengage-tutorial.md)、[Michigan Data Hub Single Sign-On](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md)、[Egress](../saas-apps/egress-tutorial.md)、[SendSafely](../saas-apps/sendsafely-tutorial.md)、[Eletive](https://app.eletive.com/)、[Right-Hand Cybersecurity ADI](https://right-hand.ai/)、[Fyde Enterprise Authentication](https://enterprise.fyde.com/)、[Verme](../saas-apps/verme-tutorial.md)、[Lenses.io](../saas-apps/lensesio-tutorial.md)、[Momenta](../saas-apps/momenta-tutorial.md)、[Uprise](https://app.uprise.co/sign-in)、[Q](https://q.moduleq.com/login)、[CloudCords](../saas-apps/cloudcords-tutorial.md)、[TellMe Bot](https://tellme365liteweb.azurewebsites.net/)、[Inspire](https://app.inspiresoftware.com/)、[Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/)、[Smartschool (School Management System)](https://smartschoolz.com/login)、[Zepto - Intelligent timekeeping](https://user.zepto-ai.com/signin)、[Studi.ly](https://studi.ly/)、[Trackplan](http://www.trackplanfm.com/)、[Skedda](../saas-apps/skedda-tutorial.md)、[WhosOnLocation](../saas-apps/whos-on-location-tutorial.md)、[Coggle](../saas-apps/coggle-tutorial.md)、[Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/)、[BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>すべてのスコープでロールの割り当てを表示し、それらを csv ファイルにダウンロードする機能

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD ロール  
**製品の機能:** アクセス制御
 
Azure AD ポータルの [ロールと管理者] タブで、ロールのすべてのスコープについてロールの割り当てを表示できるようになりました。 また、ロールごとにそれらのロール割り当てを CSV ファイルにダウンロードすることもできます。 ロール割り当ての表示と追加に関するガイダンスについては、「[Azure Active Directory で管理者ロールを表示して割り当てる](../roles/manage-roles-portal.md)」を参照してください。
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>非推奨になった Azure Multi-Factor Authentication ソフトウェア開発 (Azure MFA SDK)

**種類:** 非推奨  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
2017 年 11 月に最初に発表されたように、2018 年 11 月 14 日をもって Azure Multi-Factor Authentication ソフトウェア開発 (Azure MFA SDK) の提供は終了しました。 Microsoft は、2020 年 9 月 30 日に有効な SDK サービスを終了します。 SDK に対するすべての呼び出しは失敗します。

組織で Azure MFA SDK を使用している場合は、2020 年 9 月 30 日までに移行する必要があります。
- MIM に対する Azure MFA SDK: MIM で SDK を使用している場合は、Azure MFA Server に移行し、次の[の手順](/microsoft-identity-manager/working-with-mfaserver-for-mim)に従って Privileged Access Management (PAM) をアクティブ化する必要があります。   
- カスタマイズされたアプリに対する Azure MFA SDK: アプリを Azure AD に統合し、条件付きアクセスを使用して MFA を適用することを検討してください。 始めるには、こちらの[ページ](../manage-apps/plan-an-application-integration.md)を確認してください。 

---

## <a name="june-2020"></a>2020 年 6 月 

### <a name="user-risk-condition-in-conditional-access-policy"></a>条件付きアクセス ポリシーのユーザー リスク条件

**種類:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 

Azure AD 条件付きアクセス ポリシーのユーザー リスク サポートを使用すると、複数のユーザー リスクベースのポリシーを作成できます。 ユーザーやアプリごとに、必要な最小ユーザー リスク レベルが異なる可能性があります。 ユーザー リスクに基づいて、アクセスをブロックしたり、多要素認証を要求したり、パスワードの変更をセキュリティで保護したり、Microsoft Cloud App Security にリダイレクトして追加監査などのセッション ポリシーを強制したりするポリシーを作成できます。

ユーザー リスク条件には、Azure AD Premium P2 が必要です。P2 オファリングである Azure Identity Protection が使用されるためです。 条件付きアクセスの詳細については、[Azure AD 条件付きアクセスのドキュメント](../conditional-access/index.yml)を参照してください。

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>要求時に SPNameQualifier を設定する必要があるアプリが SAML SSOでサポートされるようになりました

**種類:** 固定  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
一部の SAML アプリケーションでは、要求時にアサーション サブジェクトで SPNameQualifier が返される必要があります。 要求 NameID ポリシーで SPNameQualifier が要求されたときに、Azure AD が正しく応答するようになりました。 これは、SP initiated サインインでも機能します。また、IdP initiated サインインでも機能するようになる予定です。  Azure Active Directory での SAML プロトコルの詳細については、「[シングル サインオンの SAML プロトコル](../develop/single-sign-on-saml-protocol.md)」を参照してください。

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B Collaboration により Azure Government テナントでの MSA および Google ユーザーの招待がサポートされます

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

B2B Collaboration 機能を使用する Azure Government テナントは、Microsoft または Google アカウントを持つユーザーを招待できるようになりました。 お使いのテナントでこれらの機能を使用できるかどうかを確認するには、「[B2B コラボレーションが Azure US Government テナントで利用可能かどうかを確認するにはどうすればよいですか？](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)」の手順に従ってください。

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 のユーザー オブジェクトに externalUserState と externalUserStateChangedDateTime プロパティが含まれるようになりました

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

externalUserState と externalUserStateChangedDateTime プロパティを使用すると、招待したがまだ招待を受諾していない B2B ゲストを検索できます。また、数日間経過した後にまだ招待を受諾していないユーザーを削除するなどのビルド自動化を行うことができます。 これらのプロパティが、MS Graph v1 で使用できるようになりました。 これらのプロパティの使用方法については、[ユーザー リソースの種類](/graph/api/resources/user)に関するページを参照してください。
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD 条件付きアクセスでの認証セッションの管理が一般提供されるようになりました

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
認証セッションの管理機能を使用すると、ユーザーがサインイン資格情報を提供する必要がある頻度と、ブラウザーを閉じて再び開いた後に資格情報を提供する必要があるかどうかを構成することで、環境内でより高いセキュリティと柔軟性を提供できます。
 
また、認証セッション管理は以前、Azure AD 参加済み、Hybrid Azure AD 参加済み、および Azure AD 登録済みのデバイスでの第一要素認証のみに適用されていました。 現在、認証セッション管理は MFA にも適用されるようになりました。 詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 6 月、フェデレーションをサポートする次の 29 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md)、[Ekarda](../saas-apps/ekarda-tutorial.md)、[MailGates](../saas-apps/mailgates-tutorial.md)、[BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md)、[Raketa](../saas-apps/raketa-tutorial.md)、[Segment](../saas-apps/segment-tutorial.md)、[Ai Auditor](https://www.mindbridge.ai/products/ai-auditor/)、[Pobuca Connect](https://app.pobu.ca/)、[Proto.io](../saas-apps/proto.io-tutorial.md)、[Gatekeeper](https://www.gatekeeperhq.com/)、[Hub Planner](../saas-apps/hub-planner-tutorial.md)、[Ansira-Partner Go-to-Market Toolbox](https://ansira.com/technology/channel-engagement)、[IBM Digital Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md)、[Kisi Physical Security](../saas-apps/kisi-physical-security-tutorial.md)、[ViewpointOne](https://team.viewpoint.com/)、[IntelligenceBank](../saas-apps/intelligencebank-tutorial.md)、[pymetrics](../saas-apps/pymetrics-tutorial.md)、[Zero](https://www.teamzero.com/)、[InStation](https://instation.invillia.com/)、[edX for Business SAML 2.0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md)、[MOOC Office 365](https://mooc.office365-training.com/en/)、[SmartKargo](../saas-apps/smartkargo-tutorial.md)、[PKIsigning platform](https://platform.pkisigning.nl/)、[SiteIntel](../saas-apps/siteintel-tutorial.md)、[Field iD](../saas-apps/field-id-tutorial.md)、[Curricula SAML](../saas-apps/curricula-saml-tutorial.md)、[Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md)、[MyCompliance Cloud](https://cloud.metacompliance.com/)、[Smallstep SSH](https://smallstep.com/sso-ssh/)  

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。 Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください。

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>External Identities セルフサービス サインアップ用の API コネクターは、現在パブリック プレビュー中です

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
External Identities API コネクタを使用すると、Web API を利用してセルフサービス サインアップを外部クラウド システムと統合できます。 つまり、Web API をサインアップ フローの特定の手順として呼び出して、クラウドベースのカスタム ワークフローをトリガーできるようになりました。 たとえば、API コネクタを使用して次のことを行うことができます。

- カスタム承認ワークフローと統合する
- ID 証明を実行する
- ユーザー入力データを検証する
- ユーザー属性を上書する
- カスタム ビジネス ロジックを実行する

API コネクタで可能なすべてのエクスペリエンスの詳細については、「[API コネクタを使用してセルフサービス サインアップをカスタマイズおよび拡張する](../external-identities/api-connectors-overview.md)」、または[Web API 統合を使用した External Identities セルフサービス サインアップのカスタマイズ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)に関する記事を参照してください。
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>オンデマンドで数秒でユーザーをアプリにプロビジョニング

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Azure AD プロビジョニング サービスは、現在、周期的に動作しています。 このサービスは 40 分ごとに実行されます。 [オンデマンド プロビジョニング機能](https://aka.ms/provisionondemand)を使用すると、ユーザーを選択し、数秒でプロビジョニングできます。 この機能を使用すると、プロビジョニング サイクルを強制的に再起動しなくても、プロビジョニングの問題を迅速にトラブルシューティングできます。 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Graph で Azure AD エンタイトルメント管理を使用するための新しいアクセス許可

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** エンタイトルメント管理
 
新しい委任されたアクセス許可 EntitlementManagement.Read.All を Microsoft Graph ベータ版のエンタイトルメント管理 API で使用できるようになりました。 使用可能な API の詳細については、[Azure AD エンタイトルメント管理 API の操作](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)に関する記事を参照してください。

---

### <a name="identity-protection-apis-available-in-v10"></a>Identity Protection API を v1.0 で利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
riskyUsers および riskDetections Microsoft Graph API が一般提供されるようになりました。 v1.0 エンドポイントで利用できるようになったので、運用環境で使用することをお勧めします。 詳細については、[Microsoft Graph のドキュメント](/graph/api/resources/identityprotectionroot)を参照してください。
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Microsoft 365 グループにポリシーを適用するための秘密度ラベルが一般提供されるようになりました

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション
 

秘密度ラベルを作成し、そのラベルの設定を使用して、プライバシー (パブリックまたはプライベート) と外部ユーザー アクセス ポリシーを含むポリシーを Microsoft 365 グループに適用できるようになりました。 プライバシー ポリシーをプライベートに設定し、外部ユーザー アクセス ポリシーを、ゲスト ユーザーの追加を許可しないように設定したラベルを作成できます。 ユーザーがこのラベルをグループに適用すると、そのグループはプライベートになり、ゲスト ユーザーをグループに追加することは許可されません。 

秘密度ラベルは、ビジネスに不可欠なデータを保護するうえで重要であり、これを使用することにより、コンプライアンスに準拠しセキュリティで保護された方法で、大規模なグループを管理できます。 秘密度ラベルの使用のガイダンスについては、「[Azure Active Directory で Microsoft 365 グループに秘密度ラベルを割り当てる (プレビュー)](../enterprise-users/groups-assign-sensitivity-labels.md)」を参照してください。
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Azure AD Premium のお客様を対象とする Microsoft Identity Manager のサポートに対する更新

**種類:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理
 
Microsoft Identity Manager 2016 の延長サポートの終了により、Microsoft Identity Manager 2016 の Azure AD 統合コンポーネントに対して Azure サポートが利用できるようになりました。 詳細については、「[Microsoft Identity Manager を利用している Azure AD Premium のお客様向けの更新のサポート](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)」をお読みください。

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>SSO 要求構成でのグループ メンバーシップ条件の使用が増加しています

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
以前は、1 つのアプリケーション構成内で、グループ メンバーシップに基づいて条件付きで要求を変更するときに使用できるグループの数は、10 個に制限されていました。 SSO 要求構成でのグループ メンバーシップ条件の使用が、最大 50 グループに増加しました。 要求を構成する方法の詳細については、[エンタープライズ アプリケーションの SSO 要求の構成](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions)に関する記事を参照してください。 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>会社のブランドのサインイン ページ テキスト コンポーネントにおける基本的な書式設定の有効化

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD/Microsoft 365 ログイン エクスペリエンスでの会社のブランド機能が更新され、ハイパーリンクや、太字、下線、斜体などの書式設定をお客様が追加できるようになりました。 この機能の使用方法については、「[組織の Azure Active Directory のサインイン ページにブランドを追加する](./customize-branding.md)」を参照してください。

---

### <a name="provisioning-performance-improvements"></a>プロビジョニング パフォーマンスの向上

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
[増分サイクル](../app-provisioning/how-provisioning-works.md#incremental-cycles)が完了するまでの時間を短縮するようにプロビジョニング サービスが更新されました。 これは、ユーザーとグループが、以前よりも迅速にアプリケーションにプロビジョニングされることを意味します。 2020 年 6 月 10 日より後に作成されたすべての新しいプロビジョニング ジョブは、自動的にパフォーマンス向上の恩恵を受けます。 2020 年 6 月 10 日までにプロビジョニング用に構成されたアプリケーションでこのパフォーマンス向上を活用するためには、2020 年 6 月 10 日より後に 1 回再起動する必要があります。 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>ADAL と MS Graph パリティの非推奨の発表

**種類:** 非推奨  
**サービス カテゴリ:** 該当なし  
**製品の機能:** デバイスのライフサイクル管理

Microsoft 認証ライブラリ (MSAL) が利用できるようになったため、Azure Active Directory 認証ライブラリ (ADAL) への新機能の追加は行われなくなり、セキュリティ更新プログラムは 2022 年 6 月 30 日に終了となります。 MSAL への移行方法については、「[Microsoft Authentication Library (MSAL) へのアプリケーションの移行](../develop/msal-migration.md)」を参照してください。

さらに、MS Graph からすべての Azure AD Graph の機能を利用できるようにする作業を終了しました。 したがって、Azure AD Graph API がバグ修正とセキュリティ修正を受け取るのは、2022 年 6 月 30 日までのみとなります。 詳細については、[Microsoft 認証ライブラリと Microsoft Graph API を使用するようにアプリケーションを更新する](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)ことに関する記事を参照してください。
 
---
## <a name="may-2020"></a>2020 年 5 月

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>signIns、riskyUsers、および riskDetections API でのプロパティの提供終了

**種類:** 変更の計画  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

現在、riskDetections API と riskyUserHistoryItem (プレビュー段階) の両方で、riskType プロパティを表すために列挙型が使用されています。 signIns API の riskEventTypes プロパティにも列挙型が使用されています。 今後、これらのプロパティは文字列として表します。 

お客様は、ベータ版 riskDetections と riskyUserHistoryItem API の riskEventType プロパティに、そして 2020 年 9 月 9 日までにはベータ版 signIns API の riskEventTypes_v2 プロパティに移行する必要があります。 その日付の時点で、現在の riskType と riskEventTypes プロパティは廃止されます。 詳細については、[Microsoft Graph でのリスク イベント プロパティと ID 保護 API に対する変更](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)に関する記事を参照してください。

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Microsoft Graph での signIns v1.0 API の riskEventTypes プロパティの非推奨

**種類:** 変更の計画  
**サービス カテゴリ:** レポーティング  
**製品の機能:** ID のセキュリティ & 保護

2020 年 9 月より、Microsoft Graph でリスク イベント プロパティを表すときに、列挙型から文字列型に切り替えられます。 この変更は、プレビュー API に影響を与えるだけでなく、運用中の signIns API にも影響します。

signIns v1.0 API に新しい riskEventsTypes_v2 (string) プロパティが導入されました。 Microsoft Graph の非推奨ポリシーに従って、現在の riskEventTypes (enum) プロパティは 2022 年 6 月 11 日に廃止されます。 お客様は、2022 年 6 月 11 日までに v1.0 signIns API の riskEventTypes_v2 プロパティに移行する必要があります。 詳細については、「[Microsoft Graph での signIns v1.0 API の riskEventTypes プロパティの非推奨](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)」を参照してください。

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>MFA のメール通知の予定されている変更点

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 

クラウドの MFA の MFA メール通知には、次の変更があります。

メール通知は次のアドレスから送信されるようになります: azure-noreply@microsoft.com と msonlineservicesteam@microsoftonline.com。 不正アクセスのアラート メールの内容を更新して、使用のブロックを解除するために必要な手順をわかりやすく示します。

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Azure Active Directory に同期されていないため、Microsoft Teams にアクセスできないフェデレーション ドメイン内のユーザーのための新しいセルフサービス サインアップ。

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 

現時点では、Azure AD のフェデレーション ドメインに属していても、テナントに同期されていないユーザーは、Teams にアクセスできません。 6 月末から、この新機能により、既存のメール確認済みサインアップ機能を拡張することで、そうすることができるようになります。 これにより、フェデレーション IdP にサインインできても、まだ Azure ID 内にまだユーザー オブジェクトがないユーザーは、ユーザー オブジェクトが自動的に作成され、Teams に対して認証することができます。 ユーザー オブジェクトは、"セルフサービス サインアップ" としてマークされます。 これは、マネージド ドメイン内のユーザーが実行でき、同じフラグを使用して制御できる、メール確認済みセルフサインアップを実行するための、既存機能の拡張機能です。 この変更は、今後 2 か月の間にロールアウトが完了する予定です。 [こちら](../enterprise-users/directory-self-service-signup.md)のドキュメントの更新にご期待ください。
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>予定されている修正点: Azure Government クラウドの OIDC 検出ドキュメントが正しい Graph エンドポイントを参照するように更新されます。

**種類:** 変更の計画  
**サービス カテゴリ:** ソブリン クラウド  
**製品の機能:** ユーザー認証
 
6 月以降、[Azure Government クラウド](../develop/authentication-national-cloud.md) エンドポイント (login.microsoftonline.us) の OIDC 検出ドキュメント [Microsoft ID プラットフォームと OpenID Connect プロトコル](../develop/v2-protocols-oidc.md)では、指定されたテナントに基づいて正しい[国内クラウド Graph](/graph/deployments) エンドポイント (https://graph.microsoft.us または https://dod-graph.microsoft.us) ) が返されるようになります。  現在は、正しくない Graph エンドポイント (graph.microsoft.com) の "msgraph_host" フィールドが返されています。  

このバグ修正は約 2 か月の間に段階的にロールアウトされます。  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government ユーザーが login.microsoftonline.com にサインインできなくなります

**種類:** 変更の計画  
**サービス カテゴリ:** ソブリン クラウド  
**製品の機能:** ユーザー認証
 
2018 年 6 月 1 日、Azure Government に対する Azure Active Directory (Azure AD) の公式な機関が、 https://login-us.microsoftonline.com から https://login.microsoftonline.us に変更されました。 Azure Government テナント内でアプリケーションを所有している場合は、.us エンドポイント上でユーザーをサインインさせるようにご使用のアプリケーションを更新する必要があります。

5 月 5 日以降、Azure AD でエンドポイントの変更の適用が開始され、Azure Government ユーザーはパブリック エンドポイント (microsoftonline.com) を使用して Azure Government テナント内でホストされているアプリにサインインできなくなります。 影響を受けるアプリでは、エラー AADSTS900439 - USGClientNotSupportedOnPublicEndpoint が表示されます。 

この変更は段階的にロールアウトされ、2020 年 6 月にすべてのアプリに対して適用が完了することが想定されています。 詳細については、[Azure Government ブログ記事](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)を参照してください。

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML シングル ログアウト要求で NameID が正しい形式で送信されるようになりました

**種類:** 固定  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
ユーザーが (MyApps ポータルなどで) サインアウトをクリックすると、Azure AD は、ユーザー セッション内でアクティブになっていて、ログアウト URL が構成されている各アプリに SAML シングル ログアウト メッセージを送信します。 これらのメッセージには、永続的な形式で NameID が含まれています。

元の SAML サインイン トークンで NameID (メールや UPN など) に異なる形式が使用されていた場合、SAML アプリではログアウト メッセージ内の NameID を既存のセッションに関連付けることができません (両方のメッセージ内で使用されている NameID が異なるため)。それにより、SAML アプリによってログアウト メッセージが破棄され、ユーザーがログインしたままになります。 この修正により、アプリケーション用に構成された NameID とのサインアウト メッセージの整合性が確保されます。

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>クラウド プロビジョニングでハイブリッド ID 管理者の役割を使用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Azure AD のクラウド プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
IT 管理者は、Azure AD Connect クラウド プロビジョニングを設定するための最小特権ロールとして新しい "ハイブリッド管理" ロールの使用を開始できます。 この新しいロールにより、クラウド プロビジョニングの設定と構成に全体管理者ロールを使用する必要がなくなります。 [詳細については、こちらを参照してください](../roles/delegate-by-task.md#connect)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD アプリケーション ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 5 月、フェデレーションをサポートする次の 36 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Moula](https://moula.com.au/pay/merchants)、[Surveypal](https://www.surveypal.com/app)、[Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/)、[TackleBox](http://www.tacklebox.app/)、[Powell Teams](https://powell-software.com/en/powell-teams-en/)、[Talentsoft Assistant](https://msteams.talent-soft.com/)、[ASC Recording Insights](https://teams.asc-recording.app/product)、[GO1](https://www.go1.com/)、[B-Engaged](https://b-engaged.se/)、[Competella Contact Center Workgroup](http://www.competella.com/)、[Asite](http://www.asite.com/)、[ImageSoft Identity](https://identity.imagesoftinc.com/)、[My IBISWorld](https://identity.imagesoftinc.com/)、[insuite](../saas-apps/insuite-tutorial.md)、[Change Process Management](../saas-apps/change-process-management-tutorial.md)、[Cyara CX Assurance Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md)、[Smart Global Governance](../saas-apps/smart-global-governance-tutorial.md)、[Prezi](../saas-apps/prezi-tutorial.md)、[Mapbox](../saas-apps/mapbox-tutorial.md)、[Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md)、[Whimsical](../saas-apps/whimsical-tutorial.md)、[Trelica](../saas-apps/trelica-tutorial.md)、[EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md)、[EasySSO for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md)、[EasySSO for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md)、[Torii](../saas-apps/torii-tutorial.md)、[Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md)、[Humanage](../saas-apps/humanage-tutorial.md)、[ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md)、[CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md)、[ShareVault](../saas-apps/sharevault-tutorial.md)、[Vyond](../saas-apps/vyond-tutorial.md)、[TextExpander](../saas-apps/textexpander-tutorial.md)、[Anyone Home CRM](../saas-apps/anyone-home-crm-tutorial.md)、[askSpoke](../saas-apps/askspoke-tutorial.md)、[ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリー内でご自分のアプリケーションを公開する場合、詳細については、こちら (https://aka.ms/AzureADAppRequest ) をご覧ください。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件付きアクセスのレポート専用モードの一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

[Azure AD 条件付きアクセスのレポート専用モード](../conditional-access/concept-conditional-access-report-only.md)を使用すると、アクセス制御を実施せずにポリシーの結果を評価できます。 組織全体でレポート専用ポリシーをテストし、その影響について理解してから有効にすることで、展開がより安全で簡単になります。 これまで数か月にわたって、私たちはレポート専用モードを広範に導入しており、既に 2,600 万を超えるユーザーがレポート専用ポリシーのスコープ内に存在しています。 本日のこの発表では、新しい Azure AD 条件付きアクセス ポリシーが、既定でレポート専用モードで作成されます。 これは、ポリシーが作成された時点からその影響を監視できることを意味します。 また、MS Graph API シリーズを使用しているユーザーは、[プログラムによってレポート専用ポリシーを管理する](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true)こともできます。 

---

### <a name="self-service-sign-up-for-guest-users"></a>ゲスト ユーザーのセルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD 内で外部 ID を使用すると、ご自身の組織外のユーザーに組織のアプリとリソースへのアクセスを許可することができ、その際、外部ユーザーは希望する任意の ID を使用してサインインできます。 アプリケーションを外部ユーザーと共有する場合、アプリケーションにアクセスする必要があるユーザーが常に事前にわかっているとは限りません。 [セルフサービス サインアップ](../external-identities/self-service-sign-up-overview.md)を使用すると、ゲスト ユーザーがサインアップして、あなたの基幹業務 (LOB) アプリのゲスト アカウントを取得できるようになります。 サインアップ フローを作成して、Azure AD とソーシャル ID をサポートするようにカスタマイズできます。 サインアップ時にユーザーに関する追加情報を収集することもできます。

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件付きアクセスに関する分析情報とレポートのブックの一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

[分析情報とレポートのブック](../conditional-access/howto-conditional-access-insights-reporting.md)によって、管理者が各自のテナントの Azure AD 条件付きアクセスの概要ビューを得られます。 個々のポリシーを選択する機能によって、管理者が各ポリシーの動作をよりよく理解し、変更をリアルタイムで監視できます。 このブックにより、Azure Monitor に格納されているデータがストリームされます。これは、[こちらの手順に従って](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)数分で設定可能です。 ダッシュボードは、見つけやすくするために、Azure AD 条件付きアクセス メニュー内の新しい分析情報とレポートのタブに移動されました。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>条件付きアクセスのポリシー詳細ブレードのパブリック プレビュー開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

新しい[ポリシーの詳細ブレード](../conditional-access/troubleshoot-conditional-access.md)には、条件付きアクセス ポリシーの評価中に満たされた割り当て、条件、および制御が表示されます。 このブレードにアクセスするには、サインインの詳細の [条件付きアクセス] タブまたは [レポート専用] タブにある行を選択します。

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Microsoft Graph 内のディレクトリ オブジェクトに対する新しいクエリ機能のパブリック プレビュー開始

**種類:** 新機能  
**サービス カテゴリ:** MS Graph **製品の機能:** 開発者エクスペリエンス

Microsoft Graph ディレクトリ オブジェクト API シリーズに新しい機能が導入され、カウント、検索、フィルター処理、並べ替え操作が可能になります。 これにより、開発者は、インメモリのフィルター処理や並べ替えなどの回避策を使用せずに、ご自分のディレクトリ オブジェクトに対してすばやくクエリを実行できるようになります。 詳細については、この[ブログ記事](https://aka.ms/CountFilterMSGraphAAD)をご覧ください。

現在、パブリック プレビュー中で、フィードバックをお待ちしております。 この[簡単なアンケート](https://aka.ms/MsGraphAADSurveyDocs)でお客様のご意見をお送りください。

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Microsoft Graph API (ベータ版) を使用した SAML ベースのシングル サインオンの構成

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
ベータ版の MS Graph API シリーズを使用した Azure AD ギャラリーからのアプリケーションの作成と構成がサポートされるようになりました。 アプリケーションの複数のインスタンスに対して SAML ベースのシングル サインオンを設定する必要がある場合に、Microsoft Graph API シリーズを使用して [SAML ベースのシングル サインオンの構成を自動化](/graph/application-saml-sso-configure-api)することで、時間を節約できます。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic by Organization](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML トークン暗号化の一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
[SAML トークン暗号化](../manage-apps/howto-saml-token-encryption.md)を使用すると、暗号化された SAML アサーションを受信するようにアプリケーションを構成できます。 この機能は、すべてのクラウドで一般提供が開始されています。
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>アプリケーション トークン内のグループ名要求の一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
トークンで発行されたグループ要求が、アプリケーションに割り当てられたグループのみに制限されるようになりました。  これは、ユーザーが属しているグループのメンバーが多数いて、トークン サイズの制限を超えるリスクがある場合に特に重要です。 この新機能の導入により、[トークンにグループ名を追加する](../hybrid/how-to-connect-fed-group-claims.md)機能の一般提供が開始されました。
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback で勤務先電話番号属性の設定がサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Workday Writeback プロビジョニング アプリを強化し、勤務先電話番号と携帯電話番号の属性の書き戻しがサポートされるようになりました。 メールとユーザー名に加えて、電話番号の値が Azure AD から Workday にフローするように Workday Writeback プロビジョニング アプリを構成できるようになりました。 電話番号の書き戻しを構成する方法の詳細については、[Workday Writeback](../saas-apps/workday-writeback-tutorial.md) アプリのチュートリアルを参照してください。 

---

### <a name="publisher-verification-preview"></a>発行者の確認 (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** 開発者エクスペリエンス
 
発行者の確認 (プレビュー) は、管理者とエンド ユーザーが、Microsoft ID プラットフォームと統合するアプリケーション開発者の信頼性を理解するのに役立ちます。 詳細については、「[発行者の確認 (プレビュー)](../develop/publisher-verification-overview.md)」を参照してください。
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>シングルページ アプリの承認コード フロー

**種類:** 変更された機能 **サービス カテゴリ:** 認証 **製品の機能:** 開発者エクスペリエンス

最新のブラウザーの[サード パーティ Cookie の制限 (Safari ITP など)](../develop/reference-third-party-cookies-spas.md) により、SPA では、SSO を維持するために、暗黙的フローではなく、承認コード フローを使用する必要があります。MSAL.js v 2.x では、認証コード フローをサポートするようになりました。 Azure portal の対応する更新により、ご自分の SPA の種類を "spa" に更新し、認証コード フローを使用することができます。 ガイダンスについては、「[クイックスタート: 承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する](../develop/quickstart-v2-javascript-auth-code.md)」を参照してください。

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>デバイスの強化されたフィルター処理のパブリック プレビュー開始

**種類:** 変更された機能   
**サービス カテゴリ:** デバイス管理 **製品の機能:** デバイスのライフサイクル管理
 
以前は、使用できるフィルターは "有効" と "アクティビティの日付" のみでした。 OS の種類、結合の種類、コンプライアンスなど、[より多くのプロパティでデバイスの一覧をフィルター処理](../devices/device-management-azure-portal.md#device-list-filtering-preview)できるようになりました。 これらの追加により、特定のデバイスを簡単に見つけることができます。

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Azure AD B2C の新しいアプリの登録エクスペリエンスの一般提供開始

**種類:** 変更された機能   
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** ID ライフサイクル管理
 
Azure AD B2C の新しいアプリの登録エクスペリエンスの一般提供が開始されました。 

以前は、従来の "アプリケーション" エクスペリエンスを使用して、コンシューマー向けの B2C アプリケーションを他のアプリとは別に管理する必要がありました。 これは、Azure 内のさまざまな場所で、さまざまなアプリ作成エクスペリエンスが存在していたことを意味します。

新しいエクスペリエンスでは、すべての B2C アプリの登録と Azure AD アプリの登録を 1 か所で表示し、一貫した方法で管理することができます。 顧客向けアプリや、Microsoft Graph にアクセスして Azure AD B2C リソースをプログラムで管理できるアプリを管理する必要がある場合も、1 つの管理方法を学ぶだけで済みます。

新しいエクスペリエンスを利用するには、Azure AD B2C サービスに移動し、[アプリの登録] ブレードを選択します。 このエクスペリエンスには、Azure Active Directory サービスからアクセスすることもできます。

Azure AD B2C のアプリの登録エクスペリエンスは、Azure AD テナント向けの一般的な[アプリの登録エクスペリエンス](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)に基づいていますが、Azure AD B2C 向けにカスタマイズされています。 従来の "アプリケーション" エクスペリエンスは今後非推奨となる予定です。

詳細については、[Azure AD B2C の新しいアプリの登録エクスペリエンス](../../active-directory-b2c/app-registrations-training-guide.md)に関するページを参照してください。

---
## <a name="april-2020"></a>2020 年 4 月

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>統合されたセキュリティ情報登録エクスペリエンスの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** 認証 (ログイン)

**製品の機能:** ID のセキュリティ & 保護

多要素認証 (MFA) とセルフサービス パスワード リセット (SSPR) 用の統合された登録エクスペリエンスの一般提供が開始されました。 この新しい登録エクスペリエンスによって、ユーザーは 1 つの順を追ったプロセスで MFA と SSPR に登録できるようになります。 ご自身の組織に向けて新しいエクスペリエンスを展開すると、ユーザーはより短時間で、より少ない作業で登録できるようになります。 [こちら](https://bit.ly/3etiRyQ)のブログ記事をご覧ください。

---

### <a name="continuous-access-evaluation"></a>継続的アクセス評価

**種類:** 新機能

**サービス カテゴリ:** 認証 (ログイン)

**製品の機能:** ID のセキュリティ & 保護

継続的アクセス評価は新しいセキュリティ機能です。これを使うと、Azure AD でイベント (ユーザー アカウントの削除など) が発生した場合に、Azure AD アクセス トークンを利用する証明書利用者に対してほぼリアルタイムでポリシーを適用できるようになります。 この機能は、最初に Teams と Outlook のクライアント向けにロールアウトされます。 詳細については、Microsoft の[ブログ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)と[ドキュメント](../conditional-access/concept-continuous-access-evaluation.md)をご覧ください。

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS サインイン:現場担当者による、電話番号を使用したパスワードなしの Azure AD 対応アプリケーションへのサインイン

**種類:** 新機能

**サービス カテゴリ:** 認証 (ログイン)

**製品の機能:** ユーザー認証

Office では、非従来型の組織と、主要なコミュニケーション方法として電子メールを使用しない大規模な組織の従業員に対応した、一連のモバイルファースト ビジネス アプリが発表されています。 これらのアプリは、雇用者からメール アドレスを提供されない場合がある現場の従業員、デスクレス ワーカー、フィールド エージェント、または小売従業員が、コンピューターまたは IT にアクセスできるようにすることを目的としています。 このプロジェクトにより、このような従業員が、電話番号を入力してコードを送り返すことによってビジネス アプリケーションにサインインできます。 詳細については、[管理者向けのドキュメント](../authentication/howto-authentication-sms-signin.md)と[エンド ユーザー向けのドキュメント](../user-help/sms-sign-in-explainer.md)を参照してください。

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>内部ユーザーを B2B コラボレーションの使用に招待する

**種類:** 新機能

**サービス カテゴリ:** B2B

**製品の機能:**

B2B の招待機能は拡張中であり、今後、B2B コラボレーションの資格情報を使用するように既存の内部アカウントを招待できるようにする予定です。 これを実行するために、招待 API に対して、招待されたメール アドレスなどの通常のパラメーターに加えてユーザー オブジェクトが渡されます。 そのユーザーのオブジェクト ID、UPN、グループ メンバーシップ、アプリの割り当てなどは変更されませんが、今後は、招待前に使用していた内部資格情報ではなく、B2B を使用してホーム テナントの資格情報で認証を行うようになります。 詳細については、こちらの[ドキュメント](../external-identities/invite-internal-users.md)をご覧ください。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件付きアクセスのレポート専用モードの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

[Azure AD 条件付きアクセスのレポート専用モード](../conditional-access/concept-conditional-access-report-only.md)を使用すると、アクセス制御を実施せずにポリシーの結果を評価できます。 組織全体でレポート専用ポリシーをテストし、その影響について理解してから有効にすることで、展開がより安全で簡単になります。 これまで数か月にわたって、私たちはレポート専用モードを広範に導入しており、既に 26M のユーザーがレポート専用ポリシーのスコープ内に存在しています。 この発表では、新しい Azure AD 条件付きアクセス ポリシーが、既定でレポート専用モードで作成されます。 これは、ポリシーが作成された時点からその影響を監視できることを意味します。 また、MS Graph API を使用しているユーザーは、[プログラムによってレポート専用ポリシーを管理する](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true)こともできます。 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件付きアクセスに関する分析情報とレポートのブックの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

条件付きアクセスに関する[分析情報とレポートのブック](../conditional-access/howto-conditional-access-insights-reporting.md)によって、管理者が各自のテナントの Azure AD 条件付きアクセスの概要ビューを得られます。 個々のポリシーを選択する機能によって、管理者が各ポリシーの動作をよりよく理解し、変更をリアルタイムで監視できます。 このブックにより、Azure Monitor に格納されているデータがストリームされます。これは、[こちらの手順に従って](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)数分で設定可能です。 ダッシュボードは、見つけやすくするために、Azure AD 条件付きアクセス メニュー内の新しい分析情報とレポートのタブに移動されました。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>条件付きアクセスのポリシー詳細ブレードのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

新しい[ポリシーの詳細ブレード](../conditional-access/troubleshoot-conditional-access.md)には、条件付きアクセス ポリシーの評価中に満たされた割り当て、条件、および制御が表示されます。 このブレードにアクセスするには、サインインの詳細の **[条件付きアクセス]** タブまたは **[レポート専用]** タブにある行を選択します。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 4 月

**種類:** 新機能

**サービス カテゴリ:** エンタープライズ アプリケーション

**製品の機能:** サード パーティ統合

2020 年 4 月に、フェデレーションを使用した以下の 31 の新規アプリのサポートが、アプリ ギャラリーに追加されました。 

[SincroPool Apps](https://www.sincropool.com/)、[SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)、[Float](../saas-apps/float-tutorial.md)、[LMS365](https://lms.365.systems/)、[IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md)、[Lunni](https://lunni.fi/)、[EasySSO for Jira](../saas-apps/easysso-for-jira-tutorial.md)、[Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)、[Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md)、[Microsoft 365 Mover](https://app.mover.io/login)、[Speaker Engage](https://speakerengage.com/login.php)、[Honestly](../saas-apps/honestly-tutorial.md)、[Ally](../saas-apps/ally-tutorial.md)、[DutyFlow](https://app.dutyflow.nl/)、[AlertMedia](../saas-apps/alertmedia-tutorial.md)、[gr8 People](../saas-apps/gr8-people-tutorial.md)、[Pendo](../saas-apps/pendo-tutorial.md)、[HighGround](../saas-apps/highground-tutorial.md)、[Harmony](../saas-apps/harmony-tutorial.md)、[Timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md)、[SynchroNet CLICK](../saas-apps/synchronet-click-tutorial.md)、[empower](https://www.made-in-office.com/en/)、[Fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md)、[Litmus](../saas-apps/litmus-tutorial.md)、[GroupTalk](https://recorder.grouptalk.com/)、[Frontify](../saas-apps/frontify-tutorial.md)、[MongoDB Cloud](../saas-apps/mongodb-cloud-tutorial.md)、[TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md)、[COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/)、[Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md)、[Trend Micro Web Security(TMWS)](../saas-apps/trend-micro-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>oAuth2PermissionGrant に対する Microsoft Graph デルタ クエリのサポートのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

oAuth2PermissionGrant に対するデルタ クエリがパブリック プレビューで利用可能になりました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、変更を追跡できるようになりました。 [詳細情報。](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>組織の連絡先に対する Microsoft Graph デルタ クエリのサポートの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

組織の連絡先に対するデルタ クエリが一般提供されました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、実稼働アプリの変更を追跡できるようになりました。 パフォーマンスを大幅に改善するために、orgContact データに継続的にポーリングする既存のコードをデルタ クエリで置き換えてください。 [詳細情報。](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>アプリケーションに対する Microsoft Graph デルタ クエリのサポートの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

アプリケーションに対するデルタ クエリが一般提供されました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、実稼働アプリの変更を追跡できるようになりました。 パフォーマンスを大幅に改善するために、アプリケーション データに継続的にポーリングする既存のコードをデルタ クエリで置き換えてください。 [詳細情報。](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>管理単位に対する Microsoft Graph デルタ クエリのサポートのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 管理単位に対する開発者エクスペリエンスのデルタ クエリがパブリック プレビューで利用可能になりました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、変更を追跡できるようになりました。 [詳細情報。](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>新しい Microsoft Graph ベータ API で認証用電話番号などを管理する

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

これらの API は、ユーザーの認証方法を管理するための主要なツールです。 MFA とセルフサービス パスワード リセット (SSPR) に使用される認証子を、プログラムで事前登録および管理できるようになりました。 これは、Azure AD MFA、SSPR、および Microsoft Graph の領域で最も要求の多い機能の 1 つでした。 このウェーブでリリースされた新しい API により、次のことが可能になります。

- ユーザーの認証用電話を読み取り、追加、更新、削除する
- ユーザーのパスワードをリセットする
- SMS サインインを有効または無効にする

詳細については、「[Azure AD 認証方法の API の概要](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)」をご覧ください。

---

### <a name="administrative-units-public-preview"></a>管理単位のパブリック プレビュー

**種類:** 新機能

**サービス カテゴリ:** Azure AD ロール

**製品の機能:** アクセス制御

管理単位を使用すると、定義した組織の部門、リージョン、またはその他のセグメントに制限されている管理者アクセス許可を付与できます。 管理単位を使用して、各地域の管理者に権限を委任したり、ポリシーを細かく設定したりできます。 たとえば、ユーザー アカウント管理者は、管理単位内のユーザーに対してのみ、プロファイル情報の更新、パスワードのリセット、およびライセンスの割り当てを実行できます。

全体管理者は、管理単位を使用して以下を行うことができます。

- リソースの分散管理のために管理単位を作成する
- 管理単位内の Azure AD ユーザーのみに対して管理アクセス許可を含むロールを割り当てる
- 必要に応じてユーザーとグループで管理単位を設定する

詳細については、「[Azure Active Directory での管理単位の管理 (プレビュー)](../roles/administrative-units.md)」をご覧ください。

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>プリンター管理者とプリンター技術者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** Azure AD ロール

**製品の機能:** アクセス制御

**プリンター管理者**:このロールを持つユーザーは、プリンターを登録して、ユニバーサル印刷コネクタの設定など、Microsoft ユニバーサル印刷ソリューションのすべてのプリンター構成のすべての側面を管理できます。 すべての委任された印刷アクセス許可要求に同意することができます。 プリンター管理者は、印刷レポートにアクセスすることもできます。 

**プリンター技術者**:このロールを持つユーザーは、プリンターを登録して、Microsoft ユニバーサル印刷ソリューションでプリンターの状態を管理できます。 すべてのコネクタ情報を読み取ることもできます。 プリンター技術者が行うことができない主要なタスクは、プリンターに対するユーザー アクセス許可の設定と、プリンターの共有です。 [詳細情報。](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>ハイブリッド ID 管理者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** Azure AD ロール

**製品の機能:** アクセス制御

このロールのユーザーは、Azure AD でのハイブリッド ID の有効化に関連するサービスと設定を有効化、構成、管理できます。 このロールでは、サポートされている 3 つの認証方法 &#8212; パスワード ハッシュ同期 (PHS)、パススルー認証 (PTA)、フェデレーション (AD FS またはサード パーティのフェデレーション プロバイダー) &#8212; のいずれかに Azure AD を構成し、関連するオンプレミス インフラストラクチャを展開してそれらを有効にすることが許可されます。 オンプレミス インフラストラクチャには、プロビジョニング エージェントと PTA エージェントが含まれます。 このロールでは、Windows 10 以外のデバイスまたは Windows Server 2016 以外のコンピューターでシームレス シングル サインオン (S-SSO) を有効にして、シームレスな認証を有効にすることが許可されます。 さらに、このロールでは、監視とトラブルシューティングのために、サインイン ログを表示し、正常性と分析にアクセスすることが許可されます。 [詳細情報。](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>ネットワーク管理者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** Azure AD ロール

**製品の機能:** アクセス制御

このロールを持つユーザーは、ユーザーの場所からのネットワーク テレメトリに基づいて、Microsoft によるネットワーク境界アーキテクチャに関する推奨事項を確認できます。 Microsoft 365 のネットワーク パフォーマンスは、通常はユーザーの場所に固有である、企業の顧客の慎重なネットワーク境界アーキテクチャに依存します。 このロールを使用では、検出されたユーザーの場所と、それらの場所に対するネットワーク パラメーターの構成を編集して、テレメトリの測定と設計に関する推奨事項の改善できます。 [詳細情報。](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 管理ポータル エクスペリエンスでの一括アクティビティとダウンロード

**種類:** 新機能

**サービス カテゴリ:** [ユーザー管理]

**製品の機能:** ディレクトリ

Azure AD 管理ポータルのエクスペリエンスにおいて、CSV ファイルをアップロードすることで Azure AD のユーザーとグループに対して一括アクティビティを実行できるようになりました。 ユーザーの作成、ユーザーの削除、ゲスト ユーザーの招待を行うことができます。 また、グループのメンバーを追加および削除することもできます。

また、Azure AD 管理ポータルのエクスペリエンスから Azure AD リソースの一覧をダウンロードすることもできます。 ディレクトリ内のユーザーの一覧、ディレクトリ内のグループの一覧、および特定のグループのメンバーをダウンロードできます。

詳細については、以下を参照してください。

- [ユーザーの作成](../enterprise-users/users-bulk-add.md)または[ゲスト ユーザーの招待](../external-identities/tutorial-bulk-invite.md)に関する記事
- [ユーザーの削除](../enterprise-users/users-bulk-delete.md)または[削除済みユーザーの復元](../enterprise-users/users-bulk-restore.md)に関する記事
- [ユーザーの一覧のダウンロード](../enterprise-users/users-bulk-download.md)または[グループの一覧のダウンロード](../enterprise-users/groups-bulk-download.md)に関する記事
- グループに対する[メンバーの追加 (インポート)](../enterprise-users/groups-bulk-import-members.md)、[メンバーの削除](../enterprise-users/groups-bulk-remove-members.md)、または[メンバーの一覧のダウンロード](../enterprise-users/groups-bulk-download-members.md)に関する記事

---

### <a name="my-staff-delegated-user-management"></a>マイ スタッフによって委任されたユーザー管理

**種類:** 新機能

**サービス カテゴリ:** [ユーザー管理]

**製品の機能:**

ストア マネージャーなどの現場のマネージャーが、マイ スタッフを使用して、自分のスタッフ メンバーが Azure AD アカウントにアクセスできるようにすることができます。 組織では、パスワードのリセットや電話番号の変更などの一般的なタスクを、中央のヘルプデスクに頼る代わりに現場のマネージャーに委任することができます。 マイ スタッフを使用すると、自分のアカウントにアクセスできないユーザーが数回のクリックでアクセスを回復することができ、ヘルプデスクや IT スタッフは必要ありません。 詳細については、「[マイ スタッフを使用してユーザーを管理する (プレビュー)](../roles/my-staff-configure.md)」および「[マイ スタッフでユーザーの管理を委任する (プレビュー)](../user-help/my-staff-team-manager.md)」をご覧ください。

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>アクセス レビューにおけるアップグレードされたエンド ユーザー エクスペリエンス

**種類:** 変更された機能

**サービス カテゴリ:** アクセス レビュー

**製品の機能:** Identity Governance

マイ アプリ ポータルでの Azure AD アクセス レビューにおけるレビュー担当者エクスペリエンスが更新されました。 4 月の終わりに、Azure AD アクセス レビューのレビュー担当者エクスペリエンスにログインしたレビュー担当者に、マイ アクセスでの更新されたエクスペリエンスを試すことができるバナーが表示されます。 更新されたアクセス レビュー エクスペリエンスでは、現在のエクスペリエンスと同じ機能が提供されますが、ユーザーの生産性を向上させる新機能に加えてユーザー インターフェイスが改善されることにご注意ください。 [更新されたエクスペリエンスの詳細については、こちらをご覧ください](../governance/perform-access-review.md)。 このパブリック プレビューは、2020 年 7 月の終わりまで続きます。 7 月の終わりに、このプレビュー エクスペリエンスをオプトインしていないレビュー担当者が、アクセス レビューを実行するためのマイ アクセスに自動的にリダイレクトされます。 レビュー担当者が今すぐマイ アクセスのプレビュー エクスペリエンスに完全に切り替えられるようにしたい場合は、[こちらからリクエストを作成してください](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 受信ユーザー プロビジョニングおよび書き戻しアプリでの最新バージョンの Workday Web Services API サポートの開始

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** 

お客様からのフィードバックに基づいて、エンタープライズ アプリ ギャラリーの Workday 受信ユーザー プロビジョニングおよび書き戻しアプリが更新され、最新バージョンの Workday Web Services (WWS) API がサポートされるようになりました。 この変更により、お客様は、接続文字列内で使用したい WWS API バージョンを指定できるようになります。 これにより、お客様は Workday のリリースで使用可能なさらなる HR 属性を取得できるようになります。 Workday Writeback アプリでは、Maintain_Contact_Info の制限を克服するために、推奨される Change_Work_Contact_Info Workday Web サービスが使用されるようになりました。

接続文字列内でバージョンが指定されなかった場合、既定では、Workday 受信プロビジョニング アプリでは引き続き WWS v21.1 が使用されます。受信ユーザー プロビジョニングを最新の Workday API に切り替えるには、[こちらのチュートリアル](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)の記述に従って接続文字列を更新し、また、[Workday 属性リファレンス ガイド](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)の記述に従って Workday 属性で使用される XPATH を更新する必要があります。 

書き戻しに新しい API を使用する場合、Workday Writeback プロビジョニング アプリを変更する必要はありません。 Workday 側では、チュートリアルのセクション「[ビジネス プロセス セキュリティ ポリシーのアクセス許可の構成](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions)」に記載されているように、Workday 統合システム ユーザー (ISU) アカウントに Change_Work_Contact ビジネス プロセスを呼び出すためのアクセス許可が付与されていることを確認してください。 

新しい API バージョンのサポートを反映するために、[チュートリアル ガイド](../saas-apps/workday-inbound-tutorial.md)が更新されています。

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>既定のアクセス ロールを持つユーザーのプロビジョニング対象範囲への追加

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** ID ライフサイクル管理

従来、既定のアクセス ロールのユーザーはプロビジョニングの対象外でした。 このロールのユーザーはプロビジョニングの対象にする必要があるというフィードバックが寄せられています。 2020 年 4 月 16 日以降、すべての新しいプロビジョニング構成で既定のアクセス ロールを持つユーザーのプロビジョニングが許可されます。 既存のプロビジョニング構成の動作は、このロールを持つユーザーのプロビジョニングをサポートするために段階的に変更されます。 [詳細情報。](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>プロビジョニング UI の更新

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** ID ライフサイクル管理

より焦点を絞った管理ビューを作成するために、プロビジョニング エクスペリエンスが更新されました。 既に構成済みのエンタープライズ アプリケーションのプロビジョニング ブレードに移動したときに、簡単にプロビジョニングの進行状況を監視し、プロビジョニングの開始、停止、再開などの操作を管理できるようになります。 [詳細情報。](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>動的グループ ルールの検証のパブリック プレビュー開始

**種類:** 変更された機能

**サービス カテゴリ:** グループ管理

**製品の機能:** コラボレーション

Azure Active Directory (Azure AD) で、動的グループ ルールを検証する手段が提供されるようになりました。 **[ルールの検証]** タブで、サンプル グループ メンバーに対して動的ルールを検証し、ルールが想定どおりに機能していることを確認できます。 動的グループ ルールの作成時または更新時に、管理者は、ユーザーまたはデバイスがグループのメンバーであるかどうかを確認できます。 これは、ユーザーまたはデバイスがルールの条件を満たしているかどうかを評価し、メンバーシップが想定されていない場合のトラブルシューティングを支援するのに役立ちます。

詳細については、[動的グループ メンバーシップ ルールの検証 (プレビュー)](../enterprise-users/groups-dynamic-rule-validation.md) に関する記事をご覧ください。

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>ID セキュリティ スコア - セキュリティの既定値群と MFA の改善アクションの更新

**種類:** 変更された機能

**サービス カテゴリ:** 該当なし

**製品の機能:** ID のセキュリティ & 保護

**Azure AD 改善アクションのセキュリティの既定値群のサポート:** Microsoft セキュリティ スコアでは、[Azure AD のセキュリティの既定値群](./concept-fundamentals-security-defaults.md)をサポートするように改善アクションが更新されます。これにより、一般的な攻撃に対する事前構成済みのセキュリティ設定を使用して組織を保護しやすくなります。 これは、次の改善アクションに影響します。

- すべてのユーザーがセキュリティで保護されたアクセスのために多要素認証を完了できるようにする
- 管理者ロールに対して MFA を必須にする
- レガシ認証をブロックするポリシーを有効にする
 
**MFA の改善アクションの更新:** ビジネスに適したポリシーを適用しつつ、最高のセキュリティを保証するという企業のニーズを反映するために、Microsoft セキュリティ スコアでは多要素認証を中心とした 3 つの改善アクションが削除され、新たに 2 つが追加されました。

削除された改善アクション:

- すべてのユーザーを多要素認証に登録する
- すべてのユーザーに対して MFA を必須にする
- Azure AD 特権ロールに対して MFA を必須にする

追加された改善アクション:

- すべてのユーザーがセキュリティで保護されたアクセスのために多要素認証を完了できるようにする
- 管理者ロールに対して MFA を必須にする

これらの新しい改善アクションでは、ディレクトリ全体で多要素認証 (MFA) のためにユーザーまたは管理者を登録し、組織のニーズに合った適切な一連のポリシーを確立する必要があります。 主な目的は、すべてのユーザーと管理者が多要素またはリスクベースの本人確認プロンプトで認証できるようにしながら、柔軟性を高めることです。 それには、スコープに基づく決定を適用する複数のポリシーの使用や、ユーザーに MFA を要求するタイミングが Microsoft によって決定されるようにするためのセキュリティの既定値群の設定 (3 月 16 日以降) などの形態があります。 [Microsoft セキュリティ スコアの新機能をご覧ください](/microsoft-365/security/mtp/microsoft-secure-score#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>2021 年 3 月の B2B 更新プログラムでのアンマネージド Azure Active Directory アカウント

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
**2021 年 3 月 31 日以降**、Microsoft では、B2B コラボレーション シナリオ用にアンマネージド Azure Active Directory (Azure AD) アカウントおよびテナントを作成することによって招待の引き換えをサポートしなくなります。 これに備えて、[電子メール ワンタイム パスコード認証](../external-identities/one-time-passcode.md)を選択することをお勧めします。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>既定のアクセス ロールのユーザーは、プロビジョニングの対象ユーザーとなります

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
従来、既定のアクセス ロールのユーザーはプロビジョニングの対象外でした。 このロールのユーザーはプロビジョニングの対象にする必要があるというフィードバックが寄せられています。 すべての新しいプロビジョニング構成で既定のアクセス ロールのユーザーがプロビジョニングできるように、変更の展開に取り組んでいます。 段階的に、このロールを使用したユーザーのプロビジョニングをサポートするために、既存のプロビジョニング構成の動作を変更していきます。 お客様による対応は必要ありません。 この変更の準備が整ったら、[ドキュメント](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)に更新プログラムを投稿します。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B コラボレーションは、21Vianet (Azure China 21Vianet) テナントで運用される Microsoft Azure で利用できるようになります

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD B2B コラボレーション機能は、21Vianet (Azure China 21Vianet) テナントが運用している Microsoft Azure で利用できるようになります。これにより、Azure China 21Vianet テナントのユーザーは、他の Azure China 21Vianet テナントのユーザーとシームレスに共同作業を行えるようになります。 [詳細については、Azure AD B2B コラボレーションを参照してください](/azure/active-directory/b2b/)。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Collaboration の招待メールの再設計

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
ユーザーをディレクトリに招待するために Azure AD B2B コラボレーションの招待サービスによって送信される[メール](../external-identities/invitation-email-elements.md)は、招待情報とユーザーの次の手順がわかりやすくなるよう再設計されます。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery ポリシーの変更は、監査ログに表示されるようになります

**種類:** 固定  
**サービス カテゴリ:** Audit  
**製品の機能:** 監視とレポート
 
[HomeRealmDiscovery ポリシー](../manage-apps/configure-authentication-for-federated-users-portal.md)の変更が監査ログに含まれていないというバグを修正しました。 これで、ポリシーがいつどのように、だれによって変更されたかを確認できるようになります。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 3 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 3 月に、フェデレーションがサポートされる次の 51 の新しいアプリがアプリ ギャラリーに追加されました。 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)、[Zoho One China](../saas-apps/zoho-one-china-tutorial.md)、[PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/)、[Profit.co SAML App](../saas-apps/profitco-saml-app-tutorial.md)、[iPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md)、[contexxt.ai SPHERE](https://contexxt-sphere.com/login)、[Wisdom By Invictus](../saas-apps/wisdom-by-invictus-tutorial.md)、[Flare Digital Signage](https://spark-dev.pixelnebula.com/login)、[Logz.io - Cloud Observability for Engineers](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md)、[SpectrumU](../saas-apps/spectrumu-tutorial.md)、[BizzContact](https://bizzcontact.app/)、[Elqano SSO](../saas-apps/elqano-sso-tutorial.md)、[MarketSignShare](http://www.signshare.com/)、[CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md)、[Netvision Compas](../saas-apps/netvision-compas-tutorial.md)、[FCM HUB](../saas-apps/fcm-hub-tutorial.md)、[RIB A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757)、[GoLinks](../saas-apps/golinks-tutorial.md)、[Datadog](../saas-apps/datadog-tutorial.md)、[Zscaler B2B User Portal](../saas-apps/zscaler-b2b-user-portal-tutorial.md)、[LIFT](../saas-apps/lift-tutorial.md)、[Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md)、[WatchTeams](https://www.devfinition.com/)、[Aster](https://demo.asterapp.io/login)、[Skills Workflow](../saas-apps/skills-workflow-tutorial.md)、[Node Insight](https://admin.nodeinsight.com/AADLogin.aspx)、[IP Platform](../saas-apps/ip-platform-tutorial.md)、[InVision](../saas-apps/invision-tutorial.md)、[Pipedrive](../saas-apps/pipedrive-tutorial.md)、[Showcase Workshop](https://app.showcaseworkshop.com/)、[Greenlight Integration Platform](../saas-apps/greenlight-integration-platform-tutorial.md)、[Greenlight Compliant Access Management](../saas-apps/greenlight-compliant-access-management-tutorial.md)、[Grok Learning](../saas-apps/grok-learning-tutorial.md)、[Miradore Online](https://login.online.miradore.com/)、[Khoros Care](../saas-apps/khoros-care-tutorial.md)、[AskYourTeam](../saas-apps/askyourteam-tutorial.md)、[TruNarrative](../saas-apps/trunarrative-tutorial.md)、[Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)、[Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md)、[insuiteX](https://www.insuite.jp/)、[sybo](https://www.systexsoftware.com.tw/)、[Britive](../saas-apps/britive-tutorial.md)、[WhosOffice](../saas-apps/whosoffice-tutorial.md)、[E-days](../saas-apps/e-days-tutorial.md)、[Kollective SDN](https://portal.kollective.app/login)、[Witivio](https://app.witivio.com/)、[Playvox](https://my.playvox.com/login)、[Korn Ferry 360](../saas-apps/korn-ferry-360-tutorial.md)、[Campus Café](../saas-apps/campus-cafe-tutorial.md)、[Catchpoint](../saas-apps/catchpoint-tutorial.md)、[Code42](../saas-apps/code42-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B Collaboration が Azure Government テナントで利用可能になりました

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD B2B コラボレーション機能が Azure Government テナント間で利用可能になりました。  お使いのテナントでこれらの機能を使用できるかどうかを確認するには、「[B2B コラボレーションが Azure US Government テナントで利用可能かどうかを確認するにはどうすればよいですか？](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)」の手順に従ってください。

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure ログの Azure Monitor 統合が Azure Government で利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
Azure AD ログとの Azure Monitor 統合が Azure Government で利用できるようになりました。 Azure AD ログ (監査ログとサインイン ログ) をストレージ アカウント、イベント ハブ、Log Analytics にルーティングすることができます。 Azure AD のシナリオについては、[詳細なドキュメント](../reports-monitoring/concept-activity-logs-azure-monitor.md)と[レポートと監視のデプロイ計画](../reports-monitoring/plan-monitoring-and-reporting.md)に関するページを確認してください。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Government での Identity Protection の更新

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

[Microsoft Azure Government ポータル](https://portal.azure.us/)で、更新された [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)  エクスペリエンスをロールアウトしました。 詳しくは、[お知らせブログの投稿](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)をご覧ください。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>ディザスター リカバリー:プロビジョニング構成のダウンロードと保存

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Azure AD プロビジョニング サービスには、豊富な構成機能が用意されています。 お客様は、後で参照したり、既知の適切なバージョンにロールバックしたりするために、構成を保存できる必要があります。 プロビジョニング構成を JSON ファイルとしてダウンロードし、必要に応じてアップロードする機能が追加されました。 [詳細については、こちらを参照してください](../app-provisioning/export-import-provisioning-configuration.md)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (セルフサービス パスワード リセット) で、21Vianet (Azure China 21Vianet) によって運営される Microsoft Azure の管理者に 2 つのゲートが必要になりました 

**種類:** 変更された機能  
**サービス カテゴリ:** セルフサービス パスワード リセット  
**製品の機能:** ID のセキュリティ & 保護
 
以前は、21Vianet (Azure China 21Vianet) によって運営されている Microsoft Azure で、セルフサービス パスワード リセット (SSPR) を使用して自分のパスワードをリセットする管理者は、ID を証明するために必要な "ゲート" (チャレンジ) は 1 つだけでした。 パブリックおよびその他の国内のクラウドでは、通常、管理者は SSPR を使用するときに 2 つのゲートを使用して ID を証明する必要があります。 しかし、Azure China 21Vianet では SMS や電話をサポートしていなかったため、管理者による 1 ゲートのパスワード リセットを許可していました。

Azure China 21Vianet とパブリック クラウドの間に SSPR 機能パリティを作成中です。 今後、管理者は SSPR を使用するときに 2 つのゲートを使用する必要があります。 SMS、電話、認証アプリの通知とコードがサポートされるようになります。 [詳細については、こちらを参照してください](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>パスワードの長さは 256 文字に制限されています

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD サービスの信頼性を確保するために、ユーザーのパスワードの長さが 256 文字までに制限されるようになりました。 これよりも長いパスワードを持つユーザーは、管理者に連絡するか、セルフサービス パスワード リセット機能を使用して、以降のログインでパスワードを変更するように求められます。

この変更は、2020 年 3 月 13 日、午前 10 時 PST (UTC - 18 時) に有効になりました。エラーは AADSTS 50052、InvalidPasswordExceedsMaxLength です。 詳細については、[重大な変更に関する通知](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters)を参照してください。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD サインイン ログが Azure portal を通じてすべての無料テナントで利用できるようになりました

**種類:** 変更された機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
今後、無料のテナントを所有しているお客様は、最大 7 日間、[Azure portal から Azure AD サインイン ログ](../reports-monitoring/concept-sign-ins.md)にアクセスできます。 以前は、サインイン ログを利用できるのは、Azure Active Directory Premium ライセンスをお持ちのお客様でした。 この変更により、すべてのテナントがポータル経由でこれらのログにアクセスできるようになります。

> [!NOTE]
> また、Microsoft Graph API と Azure Monitor を通じてサインイン ログにアクセスするには、Premium ライセンス (Azure Active Directory Premium P1 または P2) が必要です。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Azure portal のグループの全般設定からのディレクトリ全体のグループ オプションの廃止

**種類:** 非推奨  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

お客様がニーズに最も合ったディレクトリ全体のグループを作成するためのより柔軟な方法を提供するために、Azure portal の **[グループ]**  >  **[全般]** 設定の **[ディレクトリ全体のグループ]** オプションを [動的グループのドキュメント](../enterprise-users/groups-dynamic-membership.md)へのリンクに置き換えました。 管理者がゲスト ユーザーを含めたり除外したりするすべてのユーザー グループを作成できるように、ドキュメントを改良しました。

---

## <a name="february-2020"></a>2020 年 2 月

### <a name="upcoming-changes-to-custom-controls"></a>カスタム コントロールへの今後の変更

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
現在のカスタム コントロールのプレビューを、パートナーが提供する認証機能で Azure Active Directory 管理者およびエンド ユーザー エクスペリエンスとシームレスに連携させることができる方法に置き換える予定です。 現在、パートナー MFA ソリューションは次の制約に直面しています。パスワードが入力された後にのみ機能すること、他の主要なシナリオではステップアップ認証用の MFA としては機能しないこと、およびエンド ユーザーまたは管理者の資格情報管理機能と統合されていないことです。 新しい実装では、パートナーが提供する認証要素を、登録、使用状況、MFA 要求、ステップアップ認証、レポート、ログ記録などの主要なシナリオに対して組み込みの要素と共に使用できます。 

カスタム コントロールは、一般提供が開始されるまで、新しい設計と共にプレビューで引き続きサポートされます。 その時点で、お客様に新しい設計に移行する猶予を与えます。 現在のアプローチには制約があるため、新しい設計を利用できるようになるまで新しいプロバイダーをオンボードしません。 Microsoft はお客様やプロバイダーと密接に協力しており、近づいてきたらスケジュールをお知らせします。 [詳細については、こちらを参照してください](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)。

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>ID セキュリティ スコア - MFA の改善アクションの更新

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
ビジネスに適したポリシーを適用しつつ、セキュリティを強化するという企業のニーズを反映するために、Microsoft のセキュリティ スコアは、多要素認証 (MFA) を中心とした 3 つの改善アクションを削除し、新たに 2 つ追加します。

次の改善アクションが削除されます。

- すべてのユーザーを MFA に登録する
- すべてのユーザーに対して MFA を必須にする
- Azure AD 特権ロールに対して MFA を必須にする

次の改善アクションが追加されます。

- すべてのユーザーが安全にアクセスできるよう MFA を完了できることを確認する
- 管理者ロールに対して MFA を必須にする

これらの新しい改善アクションでは、ユーザーまたは管理者をディレクトリ全体での MFA のために登録し、組織のニーズに合った適切な一連のポリシーを確立する必要があります。 主な目的は、すべてのユーザーと管理者が多要素またはリスクベースの本人確認プロンプトで認証できるようにしながら、柔軟性を高めることです。 これには、Microsoft がユーザーに MFA を要求するタイミングを決定するセキュリティの既定値群の設定や、スコープに基づく決定を適用する複数のポリシーの使用などの形態があります。 これらの改善アクションの更新の一環として、ベースライン保護ポリシーはスコアの計算に含まれなくなります。 [Microsoft セキュリティ スコアに関する最新情報をご覧ください](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming)。

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services の SKU の選択

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services
 
Microsoft では、Azure AD Domain Services をご利用のお客さまより、インスタンスのパフォーマンス レベルをより柔軟に選択したいというフィードバックをいただいておりました。 2020 年 2 月 1 日より、動的モデル (オブジェクト数に基づいて Azure AD のパフォーマンスと価格を決定するモデル) から自己選択モデルへの切り替えを行いました。 これにより、お客様は環境に合ったパフォーマンス レベルを選択できるようになりました。 この変更により、リソース フォレストなどの新しいシナリオや、毎日のバックアップなどの Premium 機能をも有効にすることができます。 すべての SKU でオブジェクト数は無制限になりましたが、各レベルでのオブジェクト数の提案は引き続き提供されます。

**お客様による早急な対応は必要はありません。** 既存のお客様については、2020 年 2 月 1 日時点で使用していた動的レベルによって、新しい既定のレベルが決まります。 この変更による、価格やパフォーマンスへの影響はありません。 今後、Azure AD DS をご利用のお客様は、ディレクトリのサイズとワークロードの特性の変化に応じて、パフォーマンス要件を評価する必要があります。 サービス レベル間の切り替え操作は引き続きダウンタイムなしで行われ、お客様がディレクトリの拡張に応じて新しいレベルに自動的に移動されることはなくなります。 さらに、価格の引き上げは行われず、新しい価格は現在の課金モデルに従います。 詳細については、[Azure AD DS SKU のドキュメント](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) および [Azure AD Domain Services の価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory-ds/)を参照してください。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 2 月に、フェデレーションがサポートされる次の 31 の新しいアプリがアプリ ギャラリーに追加されました。 

[IamIP Patent Platform](../saas-apps/iamip-patent-platform-tutorial.md)、[Experience Cloud](../saas-apps/experience-cloud-tutorial.md)、[NS1 SSO For Azure](../saas-apps/ns1-sso-azure-tutorial.md)、[Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure)、[ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad)、[In Case of Crisis - Online Portal](../saas-apps/in-case-of-crisis-online-portal-tutorial.md)、[BIC Cloud Design](../saas-apps/bic-cloud-design-tutorial.md)、[Beekeeper Azure AD Data Connector](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md)、[Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess)、[Verkada Command](../saas-apps/verkada-command-tutorial.md)、[Splashtop](../saas-apps/splashtop-tutorial.md)、[Syxsense](../saas-apps/syxsense-tutorial.md)、[EAB Navigate](../saas-apps/eab-navigate-tutorial.md)、[New Relic (Limited Release)](../saas-apps/new-relic-limited-release-tutorial.md)、[Thulium](https://admin.thulium.com/login/instance)、[Ticket Manager](../saas-apps/ticketmanager-tutorial.md)、[Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams)、[Beesy](https://www.beesy.me/index.php/site/login)、[Health Support System](../saas-apps/health-support-system-tutorial.md)、[MURAL](https://app.mural.co/signup)、[Hive](../saas-apps/hive-tutorial.md)、[LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)、[Wakelet](https://wakelet.com/login)、[Firmex VDR](../saas-apps/firmex-vdr-tutorial.md)、[ThingLink for Teachers and Schools](https://www.thinglink.com/)、[Coda](../saas-apps/coda-tutorial.md)、[NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product)、[WEDO](../saas-apps/wedo-tutorial.md)、[InvitePeople](https://invitepeople.com/login)、[Reprints Desk - Article Galaxy](../saas-apps/reprints-desk-article-galaxy-tutorial.md)、[TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2020 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>ハイブリッド環境での FIDO2 セキュリティ キーの Azure AD サポート

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Microsoft は、ハイブリッド環境での FIDO2 セキュリティ キーの Azure AD サポートのパブリック プレビューを発表しました。 ユーザーは、FIDO2 セキュリティ キーを使用して Hybrid Azure AD 参加済み Windows 10 デバイスにサインインし、オンプレミスとクラウドのリソースにシームレスにサインオンできるようになりました。 Azure AD 参加済みデバイスの FIDO2 サポートのパブリック プレビューを最初にローンチして以来、ハイブリッド環境のサポートは、パスワードレス認証をご利用のお客様からのご要望に最も多い機能でした。 生体認証や公開キーおよび秘密キーの暗号化などの高度なテクノロジーを使用したパスワードレス認証は、セキュリティを担保しつつ、利便性と使いやすさを提供します。 このパブリック プレビューにより、FIDO2 セキュリティ キーのような最新の認証を使用して、従来の Active Directory リソースにアクセスできるようになりました。 詳細については、「[オンプレミスのリソースへの SSO](../authentication/howto-authentication-passwordless-security-key-on-premises.md)」を参照してください。 

使用を開始するには、「[テナントの FIDO2 セキュリティ キーを有効にする](../authentication/howto-authentication-passwordless-security-key.md)」で詳しい手順を参照してください。 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>新しいマイ アカウントのエクスペリエンスの一般公開

**種類:** 変更された機能  
**サービス カテゴリ:** マイ プロファイル/アカウント  
**製品の機能:** エンド ユーザー エクスペリエンス
 
すべてのエンド ユーザー アカウント管理のニーズに対応する機能を 1 か所に統合するマイ アカウントが一般公開されました。 エンド ユーザーは、URL から、または新しいマイ アプリ エクスペリエンスのヘッダーから、この新しいサイトにアクセスできます。 新しいエクスペリエンスで提供されるすべてのセルフサービス機能の詳細については、「[マイ アカウント ポータルの概要](../user-help/my-account-portal-overview.md)」を参照してください。

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>マイ アカウントのサイトの URL が myaccount.microsoft.com に更新

**種類:** 変更された機能  
**サービス カテゴリ:** マイ プロファイル/アカウント  
**製品の機能:** エンド ユーザー エクスペリエンス
 
新しいマイ アカウントのエンド ユーザーエクスペリエンスの URL は、来月中に `https://myaccount.microsoft.com` に更新されます。 エクスペリエンスと、エンド ユーザーに提供されるアカウントのすべてのセルフサービス機能の詳細については「[マイ アカウント ポータルのヘルプ](../user-help/my-account-portal-overview.md)」を参照してください。

---

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>新しいマイ アプリ ポータルの一般提供が開始されました

**種類:** 変更の計画  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 
一般提供が開始された新しいマイ アプリ ポータルに組織をアップグレードしてください。 この新しいポータルとコレクションの詳細については、「[マイ アプリ ポータルでコレクションを作成する](../manage-apps/access-panel-collections.md)」を参照してください。

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD のワークスペースの名前がコレクションに変更されています

**種類:** 変更された機能  
**サービス カテゴリ:** マイ アプリ   
**製品の機能:** エンド ユーザー エクスペリエンス
 
今後、フィルター管理者がユーザーのアプリを整理するように構成できるワークスペースは、コレクションと呼ばれます。 それらの構成方法の詳細については、「[マイ アプリ ポータルでコレクションを作成する](../manage-apps/access-panel-collections.md)」を参照してください。

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Phone のカスタム ポリシーを使用した サインアップとサインイン (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
開発者や企業は、電話番号を使用したサインアップとサインインを使用して、SMS 経由でユーザーの電話番号に送信されるワンタイム パスワードを使用したユーザーのサインアップとサインインを許可できます。 この機能を使用すると、ユーザーが電話にアクセスできなくなった場合に自分の電話番号を変更することもできます。 カスタム ポリシー機能および電話でのサインアップとサインインによって、開発者や企業は、ページのカスタマイズを通して、自社のブランドを広めることができます。 「[Azure AD B2C でカスタム ポリシーを使用した電話のサインアップとサインインを設定する](../../active-directory-b2c/phone-authentication.md)」でその実行方法を確認してください。
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2020 年 1 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 1 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 1 月に、フェデレーションを使用した以下の 33 の新規アプリのサポートがアプリ ギャラリーに追加されました。 

[JOSA](../saas-apps/josa-tutorial.md)、[Fastly Edge Cloud](../saas-apps/fastly-edge-cloud-tutorial.md)、[Terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md)、[Spintr SSO](../saas-apps/spintr-sso-tutorial.md)、[Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik)、[SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)、[Upshotly](../saas-apps/upshotly-tutorial.md)、[LeaveBot](https://appsource.microsoft.com/en-us/product/office/WA200001175)、[DataCamp](../saas-apps/datacamp-tutorial.md)、[TripActions](../saas-apps/tripactions-tutorial.md)、[SmartWork](https://www.intumit.com/teams-smartwork/)、[Dotcom-Monitor](../saas-apps/dotcom-monitor-tutorial.md)、[SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE](../saas-apps/ssogen-tutorial.md)、[Hosted MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md)、[Yuhu Property Management Platform](../saas-apps/yuhu-property-management-platform-tutorial.md)、[LumApps](https://sites.lumapps.com/login)、[Upwork Enterprise](../saas-apps/upwork-enterprise-tutorial.md)、[Talentsoft](../saas-apps/talentsoft-tutorial.md)、[SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/)、[PressPage](../saas-apps/presspage-tutorial.md)、[ContractSafe Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md)、[Maxient Conduct Manager Software](../saas-apps/maxient-conduct-manager-software-tutorial.md)、[Helpshift](../saas-apps/helpshift-tutorial.md)、[PortalTalk 365](https://www.portaltalk.com/)、[CoreView](https://portal.coreview.com/)、Squelch Cloud Office365 Connector、[PingFlow Authentication](https://app-staging.pingview.io/)、[ PrinterLogic SaaS](../saas-apps/printerlogic-saas-tutorial.md)、[Taskize Connect](../saas-apps/taskize-connect-tutorial.md)、[Sandwai](https://app.sandwai.com/)、[EZRentOut](../saas-apps/ezrentout-tutorial.md)、[AssetSonar](../saas-apps/assetsonar-tutorial.md)、[Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="two-new-identity-protection-detections"></a>2 つの新しい Identity Protection の検出

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
2 つの新しいサインインにリンクされた検出の種類が Identity Protection に追加されました。不審な受信トレイ操作ルールとあり得ない移動です。 これらのオフライン検出は Microsoft Cloud App Security (MCAS) によって検出され、Identity Protection のユーザーとサインインのリスクに影響を及ぼします。 これらの検出の詳細については、[サインインのリスクの種類](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)に関する記事を参照してください。
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>重要な変更: URI フラグメントがログイン リダイレクトを通じて実行されることはありません

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
2020 年 2 月 8 日以降、ユーザーがサインインするための要求が login.microsoftonline.com に送信されると、サービスによって空のフラグメントが要求に追加されます。  これにより、ブラウザーで要求内の既存のフラグメントが消去されることで、リダイレクト クラスの攻撃を防止できます。 アプリはこの動作に依存しないようにしてください。 詳細については、Microsoft ID プラットフォームのドキュメントの[重大な変更](../develop/reference-breaking-changes.md#february-2020)に関する記事を参照してください。

---

## <a name="december-2019"></a>2019 年 12 月

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP SuccessFactors のプロビジョニングを Azure AD およびオンプレミス AD に統合する (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

Azure AD で、信頼できる ID ソースとして SAP SuccessFactors を統合できるようになりました。 この統合により、ID ライフサイクル全体の自動化が促進されます。たとえば、新規採用や退職などの人事イベントに基づいて Azure AD アカウントのプロビジョニングを制御できるようになります。

Azure AD への SAP SuccessFactors の受信プロビジョニングを設定する方法については、[SAP SuccessFactors の自動プロビジョニングを構成する](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)チュートリアルを参照してください。

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Azure AD B2C でのカスタマイズされた電子メールのサポート (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

ユーザーがアプリを使用するための新規登録をするときに、Azure AD B2C を使用して、カスタマイズされた電子メールを作成できるようになりました。 DisplayControls (現在プレビュー段階) とサード パーティの電子メール プロバイダー ([SendGrid](https://sendgrid.com/)、[SparkPost](https://sparkpost.com/)、カスタム REST API など) を使用することで、独自の電子メール テンプレート、**From** アドレス、件名テキストを使用できるだけでなく、ローカライズやカスタムのワンタイム パスワード (OTP) 設定をサポートできます。

詳細については、[Azure Active Directory B2C でのカスタム電子メール検証](../../active-directory-b2c/custom-email-sendgrid.md)に関する記事を参照してください。

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>セキュリティ既定値によるベースライン ポリシーの置き換え

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** ID のセキュリティと保護

"既定でセキュリティ保護" の認証モデルの一環として、すべてのテナントから既存のベースライン保護ポリシーを削除する取り組みが進められています。 この削除は 2 月末の完了を目標としています。 これらのベースライン保護ポリシーは、セキュリティの既定値に置き換えられます。 ベースライン保護ポリシーを使用している場合は、新しいセキュリティの既定ポリシーまたは条件付きアクセスへの移行を計画する必要があります。 これらのポリシーを使用していない場合は、特に対処の必要はありません。

新しいセキュリティの既定値の詳細については、[セキュリティの既定値](./concept-fundamentals-security-defaults.md)に関する記事を参照してください。 条件付きアクセス ポリシーの詳細については、「[一般的な条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-policy-common.md)」を参照してください。

---

## <a name="november-2019"></a>2019 年 11 月

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>SameSite 属性と Chrome 80 のサポート

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Cookie の "既定でセキュリティ保護" モデルの一環として、Chrome 80 ブラウザーでは、`SameSite` 属性を使用せずに Cookie を扱う方法の変更が進められています。 `SameSite` 属性を指定しない Cookie は、この属性が `SameSite=Lax` に設定されたものとして扱われるようになります。これにより、アプリが依存している可能性がある一部のクロスドメイン Cookie 共有シナリオが Chrome でブロックされるようになります。 従来の Chrome の動作を維持するために、HTTPS 接続でしかクロスサイト Cookie にアクセスできないよう、`SameSite=None` 属性を使用し、さらに `Secure` 属性を追加することができます。 Chrome は 2020 年 2 月 4 日までにこの変更を完了する予定です。

すべての開発者が、次のガイダンスを参考にしてアプリをテストすることを推奨します。

- **[セキュリティで保護された Cookie を使用します]** 設定の既定値を **[はい]** に設定します。

- **SameSite** 属性の既定値を **None** に設定します。

- **Secure** の `SameSite` 属性を追加します。

詳細については、[ASP.NET と ASP.NET Core で予定されている SameSite Cookie の変更](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)および [Chrome バージョン 79 以降で顧客 Web サイトおよび Microsoft の製品とサービスが中断される可能性](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)に関する記事を参照してください。

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) の新しい修正プログラム

**種類:** 固定  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理

Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) の修正プログラム ロールアップ パッケージ (ビルド 4.6.34.0) の提供が開始されました。 このロールアップ パッケージは、「この更新プログラムで修正された問題と追加された機能強化」で説明されているように、問題を解決し、機能強化を追加します。

修正プログラム パッケージの詳細およびダウンロード方法については、[Microsoft Identity Manager 2016 Service Pack 2 (ビルド 4.6.34.0) 更新プログラムのロールアップの提供開始](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)に関する記事を参照してください。

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Azure AD へのアプリの移行に役立つ新しい AD FS アプリ アクティビティ レポート (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

Azure portal で、新しい Active Directory フェデレーション サービス (AD FS) アプリ アクティビティ レポートを使用して、どのアプリが Azure AD に移行できるかを特定します。 このレポートは、Azure AD との互換性に関してすべての AD FS アプリを評価し、問題がないかどうか確認し、個々のアプリの移行の準備に関してガイダンスを提供します。

詳細については、「[AD FS アプリケーション アクティビティ レポート (プレビュー) を使用してアプリケーションを Azure AD に移行する](../manage-apps/migrate-adfs-application-activity.md)」を参照してください。

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>ユーザーが管理者の同意を要求するための新しいワークフロー (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** アクセス制御

新しい管理者の同意ワークフローによって、管理者は、管理者の承認を必要とするアプリへのアクセス権を付与することができます。 ユーザーがアプリにアクセスしようとしているものの同意ができない場合に、ユーザーは管理者の承認の要求を送信できるようになりました。 要求は、レビュー担当者として指定されているすべての管理者宛てに電子メールで送信され、Azure portal からアクセスできるキューに配置されます。 保留中の要求に対してレビュー担当者がアクションを実行すると、要求元のユーザーにアクションが通知されます。

詳細については、「[管理者の同意ワークフローの構成 (プレビュー)](../manage-apps/configure-admin-consent-workflow.md)」を参照してください。

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>オプションの要求を管理するための新しい Azure AD アプリ登録トークンの構成エクスペリエンス (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** 開発者エクスペリエンス

Azure portal の新しい **[Azure AD App Registrations Token configuration]\(Azure AD アプリ登録トークンの構成\)** ブレードには、アプリのオプション要求の動的な一覧がアプリ開発者に表示されます。 この新しいエクスペリエンスにより、Azure AD アプリの移行を効率化し、オプションの要求の構成ミスを最小限に抑えることができます。

詳細については、「[Azure AD アプリに省略可能な要求を提供する](../develop/active-directory-optional-claims.md)」を参照してください。

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD エンタイトルメント管理の新しい 2 段階承認ワークフロー (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** エンタイトルメント管理

新しく導入された 2 段階の承認ワークフローでは、アクセス パッケージへのユーザーの要求を 2 人の承認者が承認することを義務付けることができます。 たとえば、要求元ユーザーの上司が最初の承認を与える必要があるように設定し、その後、リソース所有者にも承認を求めることができます。 いずれかの承認者が承認しない場合、アクセス権は付与されません。

詳細については、「[Azure AD エンタイトルメント管理でアクセス パッケージの要求と承認の設定を変更する](../governance/entitlement-management-access-package-request-policy.md)」を参照してください。

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>[マイ アプリ] ページと新しいワークスペースに対する更新 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** サード パーティ統合

組織のユーザーによる、更新された [マイ アプリ] エクスペリエンスの表示とアクセスの方法をカスタマイズできるようになりました。 この新しいエクスペリエンスには、ユーザーがアプリを簡単に見つけて整理できるようにする新しいワークスペース機能も含まれています。

新しい [マイ アプリ] エクスペリエンスとワークスペースの作成の詳細については、「[マイ アプリ (プレビュー) ポータルでワークスペースを作成する](../manage-apps/access-panel-collections.md)」を参照してください。

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B コラボレーションの Google ソーシャル ID サポート (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** ユーザー認証

Azure AD で Google ソーシャル ID (Gmail アカウント) を使用するための新しいサポートにより、ユーザーとパートナーのコラボレーションがより簡単になります。 パートナーが新しい Microsoft 固有のアカウントを作成および管理する必要がなくなりました。 Microsoft Teams は、すべてのクライアントで、また、一般的な認証エンドポイントとテナント関連の認証エンドポイントにまたがって Google ユーザーを完全にサポートするようになりました。

詳細については、「[Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する](../external-identities/google-federation.md)」を参照してください。

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile での条件付きアクセスとシングル サインオンのサポート (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

iOS および Android 版 Microsoft Edge 向けの Azure AD で、Azure AD シングル サインオンと条件付きアクセスがサポートされるようになりました。

- **Microsoft Edge シングル サインオン (SSO):** Azure AD で接続されるすべてのアプリのネイティブ クライアント (Microsoft Outlook や Microsoft Edge など) を横断して、シングル サインオンが利用できるようになりました。

- **Microsoft Edge 条件付きアクセス:** アプリケーション ベースの条件付きアクセス ポリシーでは、ユーザーは Microsoft Edge などの Microsoft Intune で保護されたブラウザーを使用する必要があります。

Microsoft Edge での条件付きアクセスと SSO の詳細については、[Microsoft Edge Mobile での条件付きアクセスとシングル サインオンのサポートの一般提供開始](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179)に関するブログ記事を参照してください。 [アプリ ベースの条件付きアクセス](../conditional-access/app-based-conditional-access.md)または[デバイス ベースの条件付きアクセス](../conditional-access/require-managed-devices.md)を使用してクライアント アプリを設定する方法については、「[Microsoft Intune のポリシーで保護されたブラウザーを使用して Web アクセスを管理する](/intune/apps/app-configuration-managed-browser)」を参照してください。

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD エンタイトルメント管理 (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** エンタイトルメント管理

Azure AD のエンタイトルメント管理は、組織で ID とアクセスのライフサイクルを大規模に管理するために役立つ新しい ID ガバナンス機能です。 この新しい機能は、グループ、アプリ、および SharePoint Online サイトにまたがって、アクセス要求ワークフロー、アクセスの割り当て、レビュー、および有効期限を自動化するために役立ちます。

Azure AD エンタイトルメント管理を使用すると、従業員だけでなく、組織外のユーザーがこれらのリソースにアクセスする必要がある場合も、より効率的にアクセスを管理できます。

詳細については、「[Azure AD エンタイトルメント管理とは](../governance/entitlement-management-overview.md#license-requirements)」を参照してください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合  

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

[SAP Cloud Platform Identity Authentication Service](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md)、[RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md)、[SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md)、[Miro](../saas-apps/miro-provisioning-tutorial.md)、[Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md)、[Infor CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md)、[OfficeSpace Software](../saas-apps/officespace-software-provisioning-tutorial.md)、[Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 11 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 11 月に、フェデレーションを使用した以下の 21 の新規アプリのサポートが、アプリ ギャラリーに追加されました:

[Airtable](../saas-apps/airtable-tutorial.md)、[Hootsuite](../saas-apps/hootsuite-tutorial.md)、[Blue Access for Members (BAM)](../saas-apps/blue-access-for-members-tutorial.md)、[Bitly](../saas-apps/bitly-tutorial.md)、[Riva](../saas-apps/riva-tutorial.md)、[ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)、[NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md)、[TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279)、[Motus](../saas-apps/motus-tutorial.md)、[MyAryaka](../saas-apps/myaryaka-tutorial.md)、[BlueMail](https://loginself1.bluemail.me/)、[Beedle](https://teams-web.beedle.co/#/)、[Visma](../saas-apps/visma-tutorial.md)、[OneDesk](../saas-apps/onedesk-tutorial.md)、[Foko Retail](../saas-apps/foko-retail-tutorial.md)、[Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md)、[Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md)、[uniFLOW Online](../saas-apps/uniflow-online-tutorial.md)、[Claromentis](../saas-apps/claromentis-tutorial.md)、[Jisc Student Voter Registration](../saas-apps/jisc-student-voter-registration-tutorial.md)、[e4enable](https://portal.e4enable.com/)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>改良された新しい Azure AD アプリケーション ギャラリー

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

Azure AD アプリケーション ギャラリーが更新され、プロビジョニング、OpenID Connect、および SAML を Azure Active Directory テナントでサポートする事前統合アプリを簡単に見つけられるようになりました。

詳細については、「[Azure Active Directory テナントにアプリケーションを追加する](../manage-apps/add-application-portal.md)」を参照してください。

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>アプリ ロール定義の長さの上限が 120 文字から 240 文字に増加

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

一部のアプリやサービスで、アプリ ロール定義値の長さ制限が 120 文字では短すぎるとの意見がお客様から寄せられました。 ご要望に応え、ロール値定義の長さの上限を 240 文字に引き上げました。

アプリケーション固有のロール定義の使用方法については、「[アプリケーションにアプリ ロールを追加してトークンで受け取る](../develop/howto-add-app-roles-in-azure-ad-apps.md)」を参照してください。

---

## <a name="october-2019"></a>2019 年 10 月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Azure AD Identity Protection リスク検出用の identityRiskEvent API の廃止

**種類:** 変更の計画 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

開発者からのフィードバックに応え、Azure AD Premium P2 サブスクライバーは Microsoft Graph 用の新しい riskDetection API を使用して Azure AD Identity Protection のリスク検出データに対して複雑なクエリを実行できるようになりました。 既存の [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API ベータ版では、**2020 年 1 月 10 日** 頃からデータを戻すことを停止します。 組織で identityRiskEvent API を使用している場合は、新しい riskDetection API に移行する必要があります。

新しい riskDetection API の詳細については、[リスク検出 API のリファレンス ドキュメント](/graph/api/resources/riskdetection)をご覧ください。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>アプリケーション プロキシによる SameSite 属性と Chrome 80 のサポート

**種類:** 変更の計画 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

Chrome 80 ブラウザーがリリースされる数週間前に、アプリケーション プロキシの Cookie が **SameSite** 属性を処理する方法を更新する予定です。 Chrome 80 のリリースでは、**SameSite** 属性を指定していない Cookie は `SameSite=Lax` に設定されているかのように扱われます。

この変更によって悪影響を及ぼす可能性を回避するために、次の方法でアプリケーション プロキシのアクセスとセッション Cookie を更新しています。

- **[セキュリティで保護された Cookie を使用します]** 設定の既定値を **[はい]** に設定する。

- **SameSite** 属性の既定値を **None** に設定する。

    >[!NOTE]
    > アプリケーション プロキシ アクセス Cookie は、常にセキュリティで保護されたチャネルを介して排他的に送信されています。 これらの変更は、セッション Cookie にのみ適用されます。

アプリケーション プロキシ Cookie の設定の詳細については、「[Azure Active Directory でオンプレミスのアプリケーションにアクセスするための Cookie 設定](../manage-apps/application-proxy-configure-cookie-settings.md)」を参照してください。

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>アプリケーション登録ポータル (apps.dev.microsoft.com) のアプリ登録 (レガシ) とアプリ管理は使用不可となる

**種類:** 変更の計画 **サービス カテゴリ:** 該当なし **製品の機能:** 開発者エクスペリエンス

Azure AD アカウントを持つユーザーはアプリケーション登録ポータル (apps.dev.microsoft.com) を使用してアプリケーションを登録したり、管理したりできなくなります。また、Azure portal のアプリの登録 (レガシ) エクスペリエンスでアプリケーションを登録したり、管理したりできなくなります。

新しいアプリの登録エクスペリエンスの詳細については、「[トレーニング ガイド: Azure portal でのアプリの登録](../develop/quickstart-register-app.md)」を参照してください。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>ユーザーは今後、ユーザーごとの MFA から条件付きアクセス ベースの MFA への移行中に再登録をする必要がない

**種類:** 修正済み **サービス カテゴリ:** MFA **製品の機能:** ID のセキュリティ & 保護

ユーザーがユーザーごとの Multi-Factor Authentication (MFA) に対して無効であったところ、条件付きアクセス ポリシーを介して MFA が有効となった場合、ユーザーは再登録する必要がある、という既知の問題が修正されました。

ユーザーに再登録を要求するには、Azure AD ポータルでユーザーの認証方法から **[Required re-register MFA]\(MFA の再登録が必要\)** オプションを選択します。 ユーザーごとの MFA から条件付きアクセス ベースの MFA にユーザーを移行する方法の詳細については、「[ユーザーをユーザーごとの MFA から条件付きアクセス ベースの MFA に変換する](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)」を参照してください。

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>SAML トークン内の要求を変換および送信するための新機能

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

SAML トークンで要求をカスタマイズして送信するための機能が追加されました。 これらの新機能は次のとおりです。

- 追加の要求変換関数。要求で送信する値を変更するのに役立ちます。

- 1 つの要求に複数の変換を適用する機能。

- ユーザーの種類とユーザーが属するグループに基づいて、要求のソースを指定する機能。

これらの機能の詳細と使用方法については、「[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)」を参照してください。

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD のエンド ユーザー向けの新しい [自分のサインイン] ページ

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** 監視とレポート

新しい **[自分のサインイン]** ページ (https://mysignins.microsoft.com) ) を追加しました。これにより、組織のユーザーは、最新のサインイン履歴を表示して、異常なアクティビティを確認できます。 この新しいページでは、ユーザーは次のことを確認できます。

- だれかがパスワードを推測しようとしているか。

- 攻撃者が自分のアカウントへのサインインに成功したか、またその場所。

- 攻撃者がアクセスしようとしたアプリ。

詳細については、「[Users can now check their sign-in history for unusual activity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)」 (ユーザーはサインイン履歴で異常なアクティビティを確認できるようになった) ブログを参照してください。

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>クラシック仮想ネットワークから Azure Resource Manager 仮想ネットワークへの Azure AD Domain Services (Azure AD DS) の移行

**種類:** 新機能 **サービス カテゴリ:** Azure AD Domain Services **製品の機能:** Azure AD Domain Services

クラシック仮想ネットワークでスタックしていたお客様にとって、すばらしいニュースがあります。 クラシック仮想ネットワークから既存の Resource Manager 仮想ネットワークへ 1 回に限り移行することができるようになりました。 Resource Manager 仮想ネットワークに移行すると、詳細なパスワード ポリシー、電子メール通知、監査ログなど、追加およびアップグレードされた機能を利用できるようになります。

詳細については、「[プレビュー - クラシック仮想ネットワーク モデルから Resource Manager への Azure AD Domain Services の移行](../../active-directory-domain-services/migrate-from-classic-vnet.md)」を参照してください。

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C ページ コントラクトのレイアウトの更新

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

Azure AD B2C のページ コントラクトのバージョン 1.2.0 に対して新しい変更点がいくつか導入されました。 この更新されたバージョンでは、要素の読み込み順序を制御できるようになりました。これにより、スタイルシート (CSS) が読み込まれたときに発生するちらつきを防ぐことができます。

ページ コントラクトに加えられた変更の完全な一覧については、[バージョンの変更ログ](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd)を参照してください。

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>[マイ アプリ] ページと新しいワークスペースに対する更新 (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** マイ アプリ **製品の機能:** アクセス制御

新しいワークスペース機能を使用してアプリを簡単に見つけられるようにするなど、組織のユーザーが最新の [マイ アプリ] エクスペリエンスを表示してアクセスする方法をカスタマイズできるようになりました。 新しいワークスペース機能は、組織のユーザーが既にアクセス権を持っているアプリのフィルターとして機能します。

新しい [マイ アプリ] エクスペリエンスのロール アウトとワークスペースの作成の詳細については、「[マイ アプリ (プレビュー) ポータルでワークスペースを作成する](../manage-apps/access-panel-collections.md)」を参照してください。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>月間アクティブ ユーザー ベースの課金モデルのサポート (一般提供)

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

Azure AD B2C では、月間アクティブ ユーザー (MAU) の課金がサポートされるようになりました。 MAU の課金は、1 つのカレンダー月の間に認証アクティビティを行った一意のユーザーの数に基づいて行われます。 既存のお客様はいつでも、この新しい課金方法に切り替えることができます。

2019 年 11 月 1 日以降、すべての新しいお客様は自動的にこの方法で課金されます。 この課金方法はお客様にとって、コスト面でのメリットと、事前に計画できるというメリットがあります。

詳細については、「[Upgrade to monthly active users billing model](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)」 (月間アクティブ ユーザー課金モデルにアップグレードする) を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 10 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019 年 10 月に、フェデレーションを使用した以下の 35 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[In Case of Crisis – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md)、[Juno Journey](../saas-apps/juno-journey-tutorial.md)、[ExponentHR](../saas-apps/exponenthr-tutorial.md)、[Tact](https://www.tact.ai/products/tact-assistant)、[OpusCapita Cash Management](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm)、[Salestim](https://www.salestim.com/)、[Learnster](../saas-apps/learnster-tutorial.md)、[Dynatrace](../saas-apps/dynatrace-tutorial.md)、[HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process)、[Freshworks](../saas-apps/freshworks-tutorial.md)、[eCornell](../saas-apps/ecornell-tutorial.md)、[ShipHazmat](../saas-apps/shiphazmat-tutorial.md)、[Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md)、[Contentful](../saas-apps/contentful-tutorial.md)、[Bindtuning](https://bindtuning.com/login)、[HireVue Coordinate – Europe](https://www.hirevue.com/)、[HireVue Coordinate - USOnly](https://www.hirevue.com/)、[HireVue Coordinate - US](https://www.hirevue.com/)、[WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)、[Cloudmore](../saas-apps/cloudmore-tutorial.md)、[Visit.org](../saas-apps/visitorg-tutorial.md)、[Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup)、[Paylocity](../saas-apps/paylocity-tutorial.md)、[Mail Luck!](../saas-apps/mail-luck-tutorial.md)、[Teamie](https://theteamie.com/)、[Velocity for Teams](https://velocity.peakup.org/teams/login)、[SIGNL4](https://account.signl4.com/manage)、[EAB Navigate IMPL](../saas-apps/eab-navigate-impl-tutorial.md)、[ScreenMeet](https://console.screenmeet.com/)、[Omega Point](https://pi.ompnt.com/)、[Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)、[Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)、[ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md)、[iHealthHome Care Navigation System](https://ihealthnav.com/account/signin)、[Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD ポータルの [Consolidated Security]\(統合セキュリティ\) メニュー項目

**種類:** 変更された機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

Azure portal の新しい **[セキュリティ]** メニュー項目と **[検索]** バーから、Azure AD の利用可能なセキュリティ機能すべてにアクセスできるようになりました。 さらに、 **[Security - Getting started]\(セキュリティ - はじめに\)** という新しい **[セキュリティ]** ランディング ページには、パブリック ドキュメント、セキュリティ ガイダンス、デプロイ ガイドへのリンクが掲載されています。

新しい **[セキュリティ]** メニューには次のものが含まれます。

- 条件付きアクセス
- Identity Protection
- Security Center
- ID セキュリティ スコア
- 認証方法
- MFA
- リスク レポート - 危険なユーザー、危険なサインイン、リスクの検出
- その他...

詳細については、[[Security - Getting started]\(セキュリティ - はじめに\)](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted) を参照してください。

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365 グループの有効期限ポリシーの自動更新に関する拡張

**種類:** 変更された機能 **サービス カテゴリ:** グループ管理 **製品の機能:** ID ライフサイクル管理

Office 365 グループの有効期限ポリシーが拡張され、メンバーによってアクティブに使用されているグループは自動的に更新されるようになりました。 グループは、Outlook、SharePoint、および Teams を含むすべての Office 365 アプリでのユーザー アクティビティに基づいて自動更新されます。

この拡張により、グループの有効期限の通知が減り、アクティブなグループを引き続き使用できるようになります。 Office 365 グループのアクティブな有効期限ポリシーが既に設定されている場合は、この新機能を有効にするために何もする必要はありません。

詳細については、「[Office 365 グループの有効期限ポリシーの構成](../enterprise-users/groups-lifecycle.md)」を参照してください。

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Azure AD Domain Services (Azure AD DS) 作成エクスペリエンスの更新

**種類:** 変更された機能 **サービス カテゴリ:** Azure AD Domain Services **製品の機能:** Azure AD Domain Services

Azure AD Domain Services (Azure AD DS) を更新し、新しく強化された作成エクスペリエンスを提供することで、3 回クリックするだけでマネージド ドメインを作成できるようになりました。 さらに、テンプレートから Azure AD DS をアップロードしてデプロイできるようになりました。

詳細については、[Azure Active Directory Domain Services インスタンスを作成して構成する](../../active-directory-domain-services/tutorial-create-instance.md)」を参照してください。

---

## <a name="september-2019"></a>2019 年 9 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>変更の計画: Power BI コンテンツ パックの廃止

**種類:** 変更の計画 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

2019 年 10 月 1 日より、Power BI では、Azure AD Power BI コンテンツ パックを含む、すべてのコンテンツ パックの廃止が開始します。 このコンテンツ パックに代わり、Azure AD ブックを使用して、Azure AD 関連のサービスに関する分析情報を取得できます。 レポート専用モードの条件付きアクセス ポリシーに関するブック、アプリ同意に基づく分析情報など、その他のブックも表示されます。

ブックに関する詳細については、[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](../reports-monitoring/howto-use-azure-monitor-workbooks.md)に関する記事を参照してください。 コンテンツ パックの廃止に関する詳細は、「[Announcing Power BI template apps general availability](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)」 (Power BI テンプレート アプリの一般提供に関する告知) というブログ投稿をご覧ください。

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>マイ プロファイルの名前が変更され、Microsoft Office アカウント ページに統合されます

**種類:** 変更の計画 **サービス カテゴリ:** マイ プロファイルとマイ アカウント **製品の機能:** コラボレーション

10月から、「マイ プロファイル」エクスペリエンスは、「マイ アカウント」になります。 その変更の一環として、現在「**マイ プロファイル**」となっているところはすべて、「**マイ アカウント**」に変更されます。 名前の変更といくつかの設計上の改善に加え、更新されたエクスペリエンスでは、Microsoft Office アカウント ページとの統合が強化されます。 具体的には、Office のインストール済み環境とサブスクリプションに **[アカウントの概要]** ページからアクセスできるようになります。また、Office に関連した連絡先の設定に **[プライバシー]** ページからアクセスできます。

「マイ プロファイル」(プレビュー) エクスペリエンスの詳細については、「[マイ プロファイル (プレビュー) ポータルの概要](../user-help/my-account-portal-overview.md)」を参照してください。

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Azure AD ポータルで CSV ファイルを使用してグループとメンバーを一括管理します (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

一括グループ管理エクスペリエンスのパブリック プレビューが Azure AD ポータルでご利用いただけるようになったことを発表いたします。 CSV ファイルと Azure AD ポータルを使用して、以下を含むグループとメンバーの一覧の管理を行えるようになりました。

- グループのメンバーを追加または削除する。

- ディレクトリからグループの一覧をダウンロードする。

- 特定グループのグループ メンバーの一覧をダウンロードする。

詳細については、[メンバーの一括追加](../enterprise-users/groups-bulk-import-members.md)、[メンバーの一括削除](../enterprise-users/groups-bulk-remove-members.md)、[メンバー一覧の一括ダウンロード](../enterprise-users/groups-bulk-download-members.md)、[グループ一覧の一括ダウンロード](../enterprise-users/groups-bulk-download.md)に関する記事を参照してください。

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>新しい管理者の同意エンドポイントを使用した動的な同意がサポートされるようになりました

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

動的な同意をサポートするために、新しい管理者の同意エンドポイントを作成しました。これは、Microsoft ID プラットフォームで動的な同意モデルを使用する必要があるアプリに役立ちます。

この新しいエンドポイントを使用する方法について詳しくは、[管理者の同意エンドポイントの使用](../develop/v2-admin-consent.md)に関する記事を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 9 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019 年 9 月に、フェデレーションを使用した以下の 29 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[ScheduleLook](https://schedulelook.bbsonlineservices.net/)、[MS Azure SSO Access for Ethidex Compliance Office&trade; - Single  sign-on](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md)、[iServer Portal](../saas-apps/iserver-portal-tutorial.md)、[SKYSITE](../saas-apps/skysite-tutorial.md)、[Concur Travel and Expense](../saas-apps/concur-travel-and-expense-tutorial.md)、[WorkBoard](../saas-apps/workboard-tutorial.md)、`https://apps.yeeflow.com/`, [ARC Facilities](../saas-apps/arc-facilities-tutorial.md)、[Luware Stratus Team](https://stratus.emea.luware.cloud/login)、[Wide Ideas](https://wideideas.online/wideideas/)、[Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md)、[JDLT Client Hub](https://clients.jdlt.co.uk/login)、[RENRAKU](../saas-apps/renraku-tutorial.md)、[SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive)、[Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md)、`https://app.penneo.com/`、`https://app.testhtm.com/settings/email-integration`、[Cintoo Cloud](https://aec.cintoo.com/login)、[Whitesource](../saas-apps/whitesource-tutorial.md)、[Hosted Heritage Online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md)、[IDC](../saas-apps/idc-tutorial.md)、[CakeHR](../saas-apps/cakehr-tutorial.md)、[BIS](../saas-apps/bis-tutorial.md)、[Coo Kai Team Build](https://ms-contacts.coo-kai.jp/)、[Sonarqube](../saas-apps/sonarqube-tutorial.md)、[Adobe Identity Management](../saas-apps/tutorial-list.md)、[Discovery Benefits SSO](../saas-apps/discovery-benefits-sso-tutorial.md)、[Amelio](https://app.amelio.co/)、`https://itask.yipinapp.com/`

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="new-azure-ad-global-reader-role"></a>新しい Azure AD の全体閲覧者ロール

**種類:** 新機能 **サービス カテゴリ:** Azure AD ロールの **製品の機能:** アクセス制御

2019 年 9 月 24 日から、全体閲覧者という新しい Azure Active Directory (AD) ロールをロールアウトします。 このロールアウトは、運用とグローバル クラウド カスタマー (GCC) から始まり、10 月に全世界で完了します。

全体閲覧者ロールは、全体管理者に対応する読み取り専用のロールです。 このロールのユーザーは、Microsoft 365 の各サービスにわたって設定と管理情報を読み取ることができますが、管理アクションを実行することはできません。 全体閲覧者ロールは、組織内の全体管理者の数を削減するのを支援するために作成されました。 全体管理者アカウントは強力であり、攻撃の対象になりやすいため、全体管理者の数は 5 人未満にすることをお勧めします。 計画、監査、または調査には、全体閲覧者ロールを使用することをお勧めします。 また、全体管理者ロールを必要とせずに作業を行うことができるように、全体閲覧者ロールを Exchange 管理者などの制限付き管理者ロールと組み合わせて使用することをお勧めします。

全体閲覧者ロールは、新しい Microsoft 365 管理センター、Exchange 管理センター、Teams 管理センター、セキュリティ センター、コンプライアンス センター、Azure AD 管理センター、デバイス管理の管理センターと連携します。

>[!NOTE]
> パブリック プレビューの開始時、全体閲覧者ロールは、以下とは連携しません。SharePoint、Privileged Access Management、カスタマー ロックボックス、機密ラベル、Teams ライフサイクル、Teams レポートと通話分析、Teams IP 電話デバイス管理、および Teams アプリ カタログ。

詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../roles/permissions-reference.md)」を参照してください。

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシを使用して、オンプレミスのレポート サーバーに Power BI Mobile アプリからアクセスします

**種類:** 新機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

Power BI モバイル アプリと Azure AD アプリケーション プロキシの間の新しい統合により、Power BI モバイルアプリに安全にサインインし、オンプレミスの Power BI Report Server でホストされている組織のレポートを表示することができます。

Power BI Mobile アプリの詳細 (アプリのダウンロード場所など) については、[Power BI サイト](https://powerbi.microsoft.com/mobile/)を参照してください。 Azure AD アプリケーション プロキシを使用して Power BI モバイル アプリをセットアップする方法の詳細については、「[Azure AD アプリケーション プロキシを使用して Power BI Mobile へのリモート アクセスを有効にする](../manage-apps/application-proxy-integrate-with-power-bi.md)」を参照してください。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>AzureADPreview PowerShell モジュールの新しいバージョンが利用可能になりました

**種類:** 変更された機能 **サービス カテゴリ:** その他 **製品の機能:** ディレクトリ

次の新しいコマンドレットが AzureADPreview モジュールに追加され、Azure AD でカスタム ロールを定義し、割り当てることができるようになりました。

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>新しいバージョンの Azure AD Connect

**種類:** 変更された機能 **サービス カテゴリ:** その他 **製品の機能:** ディレクトリ

自動アップグレードのお客様を対象とした Azure AD Connect の更新バージョンをリリースしました。 この新しいバージョンには、いくつかの新機能、機能強化、およびバグ修正が含まれています。 この新しいバージョンの詳細については、「[Azure AD Connect:バージョンのリリース履歴](../hybrid/reference-connect-version-history.md#14250)」を参照してください。

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA) Server バージョン 8.0.2 が利用可能になりました

**種類:** 修正済み **サービス カテゴリ:** MFA **製品の機能:** ID のセキュリティ & 保護

2019 年 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、MFA Server の最新バージョン (バージョン 8.0.2) をダウンロードできるようになりました。 この新しいバージョンでは、次のことを行いました。

- Azure AD 同期によってユーザーが無効から有効に変更されると、電子メールがユーザーに送信されるように問題を修正しました。

- Tags の機能の使用を継続しながら、お客様が正常にアップグレードできるように問題を修正しました。

- Kosovo (+383) の国番号を追加しました。

- MultiFactorAuthSvc.log へのワンタイム バイパスの監査ログ記録を追加しました。

- Web サービス SDK のパフォーマンスが向上しました。

- その他の軽微なバグを修正しました。

2019 年 7 月 1 日以降、Microsoft は新しいデプロイに対する MFA Server の提供を停止しました。 多要素認証が必要な新しいお客様は、クラウドベースの Azure AD Multi-Factor Authentication を使用する必要があります。 詳細については、[クラウド ベースの Azure AD Multi-Factor Authentication のデプロイの計画](../authentication/howto-mfa-getstarted.md)に関するページを参照してください。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>グループの検索、フィルター処理、および並べ替えの機能強化は、Azure AD ポータル (パブリック プレビュー) で利用できます。

**種類:** 新機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

グループに関連する強化されたエクスペリエンスのパブリック プレビューが、Azure AD ポータルでご利用いただけるようになったことを発表いたします。 次を提供することで、これらの拡張機能がグループとメンバー リストをより適切に管理するのに役立ちます。

- グループ リストでの部分文字列検索などの高度な検索機能。
- メンバーと所有者のリストに対する高度なフィルター処理と並べ替えのオプション。
- メンバーと所有者のリストの新しい検索機能。
- 大規模なグループのより正確なグループ数。

詳細については、[Azure portal でのグループの管理](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)に関する記事をご覧ください。

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>アプリ登録管理に新しいカスタム ロールが使用できる (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** Azure AD ロールの **製品の機能:** アクセス制御

(Azure AD P1 または P2 サブスクリプションで使用できる) カスタム ロールで、特定のアクセス許可を持つロール定義を作成し、そのロールを特定のリソースに割り当てて、詳細なアクセスを提供できるようになりました。 現在は、アプリの登録を管理するためのアクセス許可を使用し、ロールを特定のアプリに割り当てて、カスタム ロールを作成します。 カスタム ロールの詳細については、[Azure Active Directory でのカスタム管理者ロール](../roles/custom-overview.md)に関する記事を参照してください。

追加のアクセス許可やリソースのサポートが必要だが、現在表示されていない場合は、[Azure フィードバック サイト](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)にフィードバックをお送りください。ご要望は Microsoft の更新予定に追加されます。

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>新しいプロビジョニング ログを利用して、アプリのプロビジョニング デプロイの監視とトラブルシューティングができる (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** アプリのプロビジョニング **製品の機能:** ID ライフサイクル管理

新しいプロビジョニング ログは、ユーザーとグループのプロビジョニング デプロイの監視とトラブルシューティングに役立ちます。 これらの新しいログ ファイルには、次の情報が含まれます。

- [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) で正常に作成されたグループ
- [AWS Single-Account Access](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access) からインポートされたロール
- [Workday](../saas-apps/workday-inbound-tutorial.md) からインポートされなかった従業員

詳細については、[Azure Active Directory ポータルのプロビジョニング レポート (プレビュー)](../reports-monitoring/concept-provisioning-logs.md) に関する記事を参照してください。

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>すべての Azure AD 管理者のための新しいセキュリティ レポート (一般提供)

**種類:** 新機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

すべての Azure AD 管理者は間もなく、既定で Azure AD 内の最新のセキュリティ レポートにアクセスできるようになります。 9 月末までは、最新のセキュリティ レポートの上部にあるバナーを使用し、前のレポートに戻ることができます。

最新のセキュリティ レポートでは、前のバージョンから次の追加機能が提供されます。

- 高度なフィルター処理と並べ替え
- ユーザー リスクの無視などの一括操作
- 侵害されたエンティティまたは安全なエンティティの確認
- 次のようなリスクの状態: 危険、無視、修復済み、セキュリティ侵害の確認済み
- 新しいリスク関連の検出 (Azure AD Premium のサブスクライバーが利用可能)

詳細については、[危険なユーザー](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users)、[リスクの高いサインイン](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)、[リスク検出](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections)に関する記事をご覧ください。

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>ユーザー割り当てマネージド ID が Virtual Machines と Virtual Machine Scale Sets で利用できる (一般提供)

**種類:** 新機能 **サービス カテゴリ:** Azure リソースのマネージド ID **製品の機能:** 開発者エクスペリエンス

ユーザー割り当てマネージド ID が Virtual Machines と Virtual Machine Scale Sets で一般提供されました。 この一環として、Azure では、使用中のサブスクリプションで信頼され、1 つまたは複数の Azure サービス インスタンスに割り当てることができる ID を Azure AD テナントで作成できます。 ユーザー割り当てマネージド ID の詳細については、[Azure リソースのマネージド ID とは](../managed-identities-azure-resources/overview.md)に関する記事を参照してください。

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>ユーザーはモバイル アプリまたはハードウェア トークンを利用してパスワードをリセットできる (一般提供)

**種類:** 変更された機能 **サービス カテゴリ:** セルフサービス パスワード リセット **製品の機能:** ユーザー認証

モバイル アプリを組織に登録しているユーザーは、Microsoft Authenticator アプリからの通知に同意するか、モバイル アプリまたはハードウェア トークンからのコードを入力すると、自分のパスワードをリセットできるようになりました。

詳細については、[動作のしくみ:Azure AD のセルフサービス パスワード リセット のクイック スタート](../authentication/concept-sspr-howitworks.md)に関する記事をご覧ください。 ユーザー エクスペリエンスの詳細については、[職場または学校のパスワードのリセットの概要](../user-help/active-directory-passwords-reset-register.md)に関する記事をご覧ください。

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET が on-behalf-of シナリオで MSAL.NET 共有キャッシュを無視する

**種類:** 修正済み **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

Azure AD Authentication ライブラリ (ADAL.NET) バージョン 5.0.0-preview 以降では、アプリの開発者が、[Web アプリおよび Web API のアカウントごとに 1 つのキャッシュをシリアル化](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)する必要があります。 そうしないと、`UserAssertion` の特定のユース ケースと共に Java 用 [on-behalf-of フロー](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java)を使用する一部のシナリオで、特権の昇格が生じることがあります。 この脆弱性を回避するために、on-behalf-of のシナリオでは、.NET 用 Microsoft Authentication Library (MSAL.NET) の共有キャッシュが ADAL.NET で無視されるようになりました。

この問題の詳細については、[Azure Active Directory 認証ライブラリの特権の昇格の脆弱性](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)に関するページを参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 8 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019 年 8 月に、フェデレーションを使用した以下の 26 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Civic Platform](../saas-apps/civic-platform-tutorial.md)、[Amazon Business](../saas-apps/amazon-business-tutorial.md)、[ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md)、[Cognidox](../saas-apps/cognidox-tutorial.md)、[Viareport's Inativ Portal (Europe)](../saas-apps/viareports-inativ-portal-europe-tutorial.md)、[Azure Databricks](https://azure.microsoft.com/services/databricks)、[Robin](../saas-apps/robin-tutorial.md)、[Academy Attendance](../saas-apps/academy-attendance-tutorial.md)、[Priority Matrix](https://sync.appfluence.com/pmwebng/)、[Cousto MySpace](https://cousto.platformers.be/account/login)、[Uploadcare](https://uploadcare.com/accounts/signup/)、[Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md)、[CPQSync by Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md)、[Chargebee](../saas-apps/chargebee-tutorial.md)、[deliver.media&trade; Portal](https://portal.deliver.media)、[Frontline Education](../saas-apps/frontline-education-tutorial.md)、[F5](https://www.f5.com/products/security/access-policy-manager)、[stashcat AD connect](https://www.stashcat.com)、[Blink](../saas-apps/blink-tutorial.md)、[Vocoli](../saas-apps/vocoli-tutorial.md)、[ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md)、[Sigstr](../saas-apps/sigstr-tutorial.md)、[Darwinbox](../saas-apps/darwinbox-tutorial.md)、[Watch by Colors](../saas-apps/watch-by-colors-tutorial.md)、[Harness](../saas-apps/harness-tutorial.md)、[EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>AzureAD PowerShell モジュールと AzureAD Preview PowerShell モジュールの新しいバージョンを利用できる

**種類:** 変更された機能 **サービス カテゴリ:** その他 **製品の機能:** ディレクトリ

AzureAD モジュールと AzureAD Preview PowerShell モジュールの新しい更新プログラムを利用できます。

- 新しい `-Filter` パラメーターが、AzureAD モジュールの `Get-AzureADDirectoryRole` パラメーターに追加されました。 このパラメーターは、コマンドレットから返されるディレクトリ ロールのフィルター処理に役立ちます。
- 次の新しいコマンドレットが AzureADPreview モジュールに追加され、Azure AD でカスタム ロールを定義し、割り当てることができるようになりました。

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure portal の動的グループ ルール ビルダーの UI の機能強化

**種類:** 変更された機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

Azure portal で利用できる動的グループ ルール ビルダーの UI をいくつか機能強化し、新しいルールの設定や既存のルールの変更が簡単になりました。 設計のこの機能強化により、1 つだけではなく、最大 5 つの式を使用してルールを作成できます。 また、デバイス プロパティ リストを更新し、非推奨のデバイス プロパティを削除しました。

詳細については、[動的メンバーシップ ルールの管理](../enterprise-users/groups-dynamic-membership.md)に関する記事を参照してください。

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>アクセス レビューで新しい Microsoft Graph アプリのアクセス許可が利用できるようになった

**種類:** 変更された機能 **サービス カテゴリ:** アクセス レビュー **製品の機能:** Identity Governance

Microsoft Graph アプリのアクセス許可に新しい `AccessReview.ReadWrite.Membership` を導入しました。これにより、アプリで、アクセス レビューを自動的に作成および取得して、グループ メンバーシップとアプリの割り当てができるようになりました。 このアクセス許可は、スケジュールを設定したジョブで使用したり、自動化の一部として使用したりでき、ログイン ユーザー コンテキストは必要ありません。

詳細については、「[Example how to create Azure AD access reviews using Microsoft Graph app permissions with PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)」 (PowerShell で Microsoft Graph アプリのアクセス許可を使用して Azure AD アクセス レビューを作成する方法の例) というブログをご覧ください。

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD アクティビティ ログが Azure Monitor の政府クラウド インスタンスで利用できるようになった

**種類:** 変更された機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure AD アクティビティ ログが Azure Monitor の政府クラウド インスタンスで利用できるようになったことをお知らせします。 [Sumologic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)、[Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)、[ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md) などの SIEM ツールと統合する目的で、ストレージ アカウントやイベント ハブに Azure AD ログを送信できるようになりました。

Azure Monitor を設定する方法については、[Azure Monitor の Azure AD アクティビティ ログ](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations)に関する記事を参照してください。

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>セキュリティ情報の機能強化された新しいエクスペリエンスをユーザーに提供する

**種類:** 変更された機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

2019 年 9 月 25 日に、ユーザー セキュリティ情報の登録と管理のためのセキュリティ情報エクスペリエンスのうち、機能強化されていない前のバージョンを無効にし、新しい[機能強化されたバージョン](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)のみを有効にします。 つまり、ユーザーは以前のエクスペリエンスを使用できなくなります。

機能強化されたセキュリティ情報エクスペリエンスについては、[管理者ドキュメント](../authentication/concept-registration-mfa-sspr-combined.md) と [ユーザー ドキュメント](../user-help/security-info-setup-signin.md)を参照してください。

#### <a name="to-turn-on-this-new-experience-you-must"></a>この新しいエクスペリエンスを有効にするには、次の操作を行う必要があります。

1. Azure portal にグローバル管理者またはユーザー管理者としてサインインします。

2. **[Azure Active Directory]、[ユーザー設定]、[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。

3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます - 拡張]** 領域で **[選択済み]** を選択し、ユーザーのグループを選択するか、 **[すべて]** を選択してテナントのすべてのユーザーに対してこの機能をオンにします。

4. [ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます] 領域で **[なし]** を選択します。

5. 設定を保存します。

    設定を保存すると、以前のセキュリティ情報エクスペリエンスにアクセスできなくなります。

>[!Important]
>2019 年 9 月 25 日より前にこれらの手順を完了しないと、機能強化されたエクスペリエンスについて、Azure Active Directory テナントが自動的に有効になります。 ご質問がございましたら、registrationpreview@microsoft.com にお問い合わせください。

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>POST ログインを使用した認証要求の検証がさらに厳密になる

**種類:** 変更された機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** 標準

2019 年 9 月 2 日より、POST メソッドを使用した認証要求は、HTTP 標準に対してさらに厳密に検証されます。 具体的には、スペースと二重引用符 (") が要求フォームの値から削除されなくなります。 これらの変更によって、既存のクライアントが中断されることがなく、Azure AD に送信された要求は毎回確実に処理されます。

詳細については、[Azure AD の破壊的変更の告知](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)に関するページを参照してください。

---

## <a name="july-2019"></a>2019 年 7 月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>変更の計画: アプリケーション プロキシ サービスが、TLS 1.2 のみをサポートするように更新される

**種類:** 変更の計画 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

最強の暗号化を提供できるように、アプリケーション プロキシ サービスのアクセスを TLS 1.2 プロトコルのみに制限する作業が開始される予定です。 この制限は、最初のうちは TLS 1.2 プロトコルを既に利用されているお客様にロールアウトされるため、影響はありません。 TLS 1.0 と TLS 1.1 のプロトコルは、2019 年 8 月 31 日に完全に非推奨になります。 まだ TLS 1.0 および TLS 1.1 を利用されているお客様には、この変更に備えていただけるよう、事前に通知されます。

この変更期間中もアプリケーション プロキシ サービスへの接続を維持するには、クライアントとサーバーおよびブラウザーとサーバーの組み合わせを TLS 1.2 を使用するように更新することをお勧めします。 また、アプリケーション プロキシ サービスから公開されるアプリに従業員がアクセスする際に使用するすべてのクライアント システムを含めることもお勧めします。

詳しくは、「[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](../manage-apps/application-proxy-add-on-premises-application.md)」を参照してください。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>変更の計画: アプリケーション ギャラリーのデザインの更新が予定されている

**種類:** 変更の計画 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

**[アプリケーションの追加]** ブレードの **[ギャラリーから追加する]** 領域のデザインが、新しいユーザー インターフェイスに変更されます。 この変更により、自動プロビジョニング、OpenID Connect、Security Assertion Markup Language (SAML)、パスワード シングル サインオン (SSO) がサポートされているアプリをより簡単に見つけることができるようになります。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>変更の計画: Office 365 IP アドレスから MFA サーバーの IP アドレスを削除する

**種類:** 変更の計画 **サービス カテゴリ:** MFA **製品の機能:** ID のセキュリティ & 保護

[Office 365 の IP アドレスと URL Web サービス](/office365/enterprise/office-365-ip-web-service)から、MFA サーバーの IP アドレスを削除する予定です。 現在これらのページに依存してファイアウォールの設定を更新している場合は、「[Azure Multi-Factor Authentication Server の概要](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)」の「**Azure Multi-Factor Authentication Server ファイアウォールの要件**」セクションに記載されている IP アドレスの一覧も含まれていることを確認する必要があります。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>アプリ専用トークンで、クライアント アプリがリソース テナントに存在することが必要になった

**種類:** 修正済み **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

2019 年 7 月 26 日に、[クライアント資格情報付与](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)によるアプリ専用トークンの提供方法を変更しました。 以前は、クライアント アプリがテナント内に存在するかどうかに関係なく、アプリでは他のアプリを呼び出すためのトークンを取得できました。 この動作が更新され、シングル テナント リソース (Web API とも呼ばれます) は、リソース テナントに存在するクライアント アプリによってのみ呼び出すことができるようになりました。

ご利用のアプリがリソース テナント内に存在しない場合、`The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` のようなエラー メッセージが表示されます。この問題を解決するには、[管理者の同意エンドポイント](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)または [PowerShell](../develop/howto-authenticate-service-principal-powershell.md) を使用して、テナント内にクライアント アプリ サービス プリンシパルを作成する必要があります。これにより、テナントではテナント内で動作するアクセス許可がアプリに付与されます。

詳細については、[認証の新機能](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)に関する記事をご覧ください。

> [!NOTE]
> クライアントと API の間の既存の同意は引き続き必要ありません。 アプリでは、引き続き独自の承認チェックを実行する必要があります。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>FIDO2 セキュリティ キーを使用した Azure AD への新しいパスワードなしのサインイン

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

Azure AD のお客様は、組織のユーザーとグループに対する FIDO2 セキュリティ キーを管理するためのポリシーを設定できるようになりました。 また、エンド ユーザーは、セキュリティ キーを自分で登録したり、FIDO 対応デバイスを使っているときにキーを使って Web サイト上の Microsoft アカウントにサインインしたり、Azure AD 参加済みの Windows 10 デバイスにサインインしたりできます。

管理者関連の情報の詳細については、[Azure AD へのパスワードなしのサインインを有効にする (プレビュー)](../authentication/concept-authentication-passwordless.md)、エンド ユーザー関連の情報については、[セキュリティ キーを使用するようにセキュリティ情報を設定する (プレビュー)](../user-help/security-info-setup-security-key.md) に関する記事をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 7 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019 年 7 月に、フェデレーションを使用した以下の 18 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md)、[Bright Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md)、[Clever Nelly](../saas-apps/clever-nelly-tutorial.md)、[AcquireIO](../saas-apps/acquireio-tutorial.md)、[Looop](https://www.looop.co/schedule-a-demo/)、[productboard](../saas-apps/productboard-tutorial.md)、[MS Azure SSO Access for Ethidex Compliance Office&trade;](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md)、[Hype](../saas-apps/hype-tutorial.md)、[Abstract](../saas-apps/abstract-tutorial.md)、[Ascentis](../saas-apps/ascentis-tutorial.md)、[Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)、[Wandera](../saas-apps/wandera-tutorial.md)、[TwineSocial](https://twinesocial.com/)、[Kallidus](../saas-apps/kallidus-tutorial.md)、[HyperAnna](../saas-apps/hyperanna-tutorial.md)、[PharmID WasteWitness](https://pharmid.com/)、[i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)、[JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関するページを参照してください

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>ネットワーク セキュリティ グループ用の新しい Azure AD Domain Services サービス タグ

**種類:** 新機能 **サービス カテゴリ:** Azure AD Domain Services **製品の機能:** Azure AD Domain Services

IP アドレスと範囲の長いリストの管理に苦労している場合は、Azure ネットワークのセキュリティ グループで新しい **AzureActiveDirectoryDomainServices** ネットワーク サービス タグを使うと、Azure AD Domain Services 仮想ネットワーク サブネットへの受信トラフィックをセキュリティで保護するのに役立ちます。

この新しいサービス タグの詳細については、[Azure AD Domain Services 用のネットワーク セキュリティ グループ](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)に関する記事をご覧ください。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 用の新しいセキュリティ監査 (プレビュー)

**種類:** 新機能 **サービス カテゴリ:** Azure AD Domain Services **製品の機能:** Azure AD Domain Services

Azure AD Domain Services のセキュリティ監査がパブリック プレビューにリリースされたことをお知らせします。 セキュリティ監査では、Azure AD Domain Services ポータルを使用して、Azure Storage、Azure Log Analytics ワークスペース、Azure Event Hub などのターゲット リソースにセキュリティ監査イベントをストリーミングして、認証サービスに関する重要な分析情報が提供されます。

詳細については、[Azure AD ドメイン サービスでセキュリティ監査を有効にする (プレビュー)](../../active-directory-domain-services/security-audit-events.md) に関する記事をご覧ください。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新しい認証方法の使用状況と分析情報 (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** セルフサービス パスワード リセット **製品の機能:** 監視とレポート

新しい認証方法の使用状況と分析情報のレポートは、Azure AD Multi-Factor Authentication やセルフサービス パスワード リセットなどの機能が組織内でどのように登録されて使用されているかを理解するのに役立ちます (各機能の登録済みユーザー数、パスワードのリセットでセルフサービス パスワード リセット が使用される頻度、リセットが行われた方法など)。

詳細については、[認証方法の使用状況と分析情報 (プレビュー)](../authentication/howto-authentication-methods-activity.md) に関する記事をご覧ください。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>新しいセキュリティ レポートは、すべての Azure AD 管理者が利用できます (パブリックプレビュー)

**種類:** 新機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

すべての Azure AD 管理者が、**リスクのフラグ付きユーザー** レポートなど、既存のセキュリティ レポートの上部にあるバナーを選択して、**危険なユーザー** レポートや **危険なサインイン** レポートに表示される新しいセキュリティ エクスペリエンスを使い始めることができるようになりました。 時間をかけて、すべてのセキュリティ レポートが以前のバージョンから新しいバージョンに移行され、新しいレポートでは次の追加機能が提供されます。

- 高度なフィルター処理と並べ替え

- ユーザー リスクの無視などの一括操作

- 侵害されたエンティティまたは安全なエンティティの確認

- 次のようなリスクの状態: 危険、無視、修復済み、セキュリティ侵害の確認済み

詳細については、[危険なユーザー レポート](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users)と[リスクの高いサインイン レポート](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)に関する記事をご覧ください。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 用の新しいセキュリティ監査 (プレビュー)

**種類:** 新機能 **サービス カテゴリ:** Azure AD Domain Services **製品の機能:** Azure AD Domain Services

Azure AD Domain Services のセキュリティ監査がパブリック プレビューにリリースされたことをお知らせします。 セキュリティ監査では、Azure AD Domain Services ポータルを使用して、Azure Storage、Azure Log Analytics ワークスペース、Azure Event Hub などのターゲット リソースにセキュリティ監査イベントをストリーミングして、認証サービスに関する重要な分析情報が提供されます。

詳細については、[Azure AD ドメイン サービスでセキュリティ監査を有効にする (プレビュー)](../../active-directory-domain-services/security-audit-events.md) に関する記事をご覧ください。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>SAML/WS フィードを使用した新しい B2B 直接フェデレーション (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** B2B **製品の機能:** B2B/B2C

直接フェデレーションは、SAML または WS フィード標準をサポートする ID システムを使用すると、IT 管理対象の ID ソリューションが Azure AD ではないパートナーとの連携を容易にするのに役立ちます。 パートナーとの直接フェデレーション関係を設定した後、そのドメインから招待するすべての新しいゲスト ユーザーと、既存の組織アカウントを使って共同作業することができ、ゲストに対するユーザー エクスペリエンスがよりシームレスになります。

詳細については、[ゲスト ユーザーのための AD FS およびサード パーティ プロバイダーとの直接フェデレーション (プレビュー)](../external-identities/direct-federation.md) に関する記事をご覧ください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Azure AD ポータルでの重複するグループ名の新しい確認

**種類:** 新機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

Azure AD ポータルからグループ名を作成または更新するときは、リソース内の既存のグループ名を複製しているどうかの確認が行われます。 名前が既に別のグループによって使用されていると判断された場合は、名前を変更するように求められます。

詳細については、[Azure AD ポータルでのグループの管](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)に関する記事をご覧ください。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>応答 (リダイレクト) URI での静的クエリ パラメーターの Azure AD による新しいサポート

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

Azure AD アプリでは、OAuth 2.0 要求に対する静的クエリ パラメーター (`https://contoso.com/oauth2?idp=microsoft` など) で応答 (リダイレクト) URI を登録して使用できるようになりました。 静的クエリ パラメーターは、応答 URI の他の部分と同様に、応答 URI に対する文字列照合の対象になります。 URL でデコードされたリダイレクト URI に一致する登録文字列がない場合、要求は拒否されます。 応答 URI が見つかった場合は、静的なクエリ パラメーターを含む文字列全体が、ユーザーをリダイレクトするために使われます。

動的応答 URI はセキュリティ上のリスクがあり、認証要求全体で状態情報を保持するために使用できないため、引き続き許可されません。 この目的には、`state` パラメーターを使います。

現在、Azure portal のアプリ登録画面では、クエリ パラメーターは引き続きブロックされます。 ただし、アプリ マニフェストを手動で編集して、アプリにクエリ パラメーターを追加し、テストすることはできます。 詳細については、[認証の新機能](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters)に関する記事をご覧ください。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>PowerShell コマンドレットで使用できるようになった Azure AD のアクティビティログ (MS Graph API)

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure AD PowerShell モジュールを通じて Azure AD のアクティビティ ログ (監査レポートとサインイン レポート) を利用できるようになったことをお知らせします。 以前は、MS Graph API エンドポイントを使って独自のスクリプトを作成できましたが、その機能が PowerShell コマンドレットに拡張されました。

これらのコマンドレットの使い方の詳細については、[レポート用の Azure AD PowerShell コマンドレット](../reports-monitoring/reference-powershell-reporting.md)に関する記事をご覧ください。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Azure AD での監査ログとサインイン ログに関するフィルター コントロールを更新

**種類:** 変更された機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

監査ログ レポートとサインイン ログ レポートが更新され、レポート画面で列として追加しなくても、さまざまなフィルターを適用できるようになりました。 さらに、画面に表示するフィルターの数を決定できるようになりました。 これらの更新すべてが連携して、レポートがいっそう読みやすく、ニーズに合うようになります。

これらの更新の詳細については、[監査ログのフィルター処理](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs)および[サインイン アクティビティのフィルター処理](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)に関する記事をご覧ください。

---

## <a name="june-2019"></a>2019 年 6 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Microsoft Graph 用の新しい riskDetections API (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

Microsoft Graph 用の新しい riskDetections API のパブリック プレビューが開始されました。 この新しい API を使用すると、組織の Identity Protection に関連したユーザーおよびサインインのリスク検出の一覧を表示できます。 また、この API を使用して、検出の種類、状態、レベルなどの詳細を含むリスク検出のクエリをさらに効率良く行うことができます。

詳細については、[リスク検出 API のリファレンス ドキュメント](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 6 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019年 6 月に、フェデレーションを使用した以下の 22 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md)、[Otsuka Shokai](../saas-apps/otsuka-shokai-tutorial.md)、[ANAQUA](../saas-apps/anaqua-tutorial.md)、[Azure VPN Client](https://portal.azure.com/)、[ExpenseIn](../saas-apps/expensein-tutorial.md)、[Helper Helper](../saas-apps/helper-helper-tutorial.md)、[Costpoint](../saas-apps/costpoint-tutorial.md)、[GlobalOne](../saas-apps/globalone-tutorial.md)、[Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/)、[Skore](https://app.justskore.it/)、[Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md)、[CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md)、[Scrible Edu](https://www.scrible.com/sign-in/#/create-account)、[PandaDoc](../saas-apps/pandadoc-tutorial.md)、[Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial)、[Proptimise OS](https://proptimise.co.uk/)、[Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md)、Oracle Access Manager for Oracle Retail Merchandising、Oracle Access Manager for Oracle E-Business Suite、Oracle IDCS for E-Business Suite、Oracle IDCS for PeopleSoft、Oracle IDCS for JD Edwards

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [[ズーム]](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関するページを参照してください

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Azure AD プロビジョニング サービスのリアルタイムの進行状況を表示する

**種類:** 変更された機能 **サービス カテゴリ:** アプリのプロビジョニング **製品の機能:** ID ライフサイクル管理

Azure AD プロビジョニング エクスペリエンスが更新されて、ユーザーのプロビジョニング プロセスがどのくらい進んでいるかを示す新しい進行状況バーが組み込まれました。 この更新されたエクスペリエンスでは、現在までにプロビジョニングされたユーザー数だけでなく、現在のサイクル中にプロビジョニングされたユーザー数に関する情報も提供されます。

詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>会社のブランドがサインアウト画面とエラー画面に表示される

**種類:** 変更された機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

Azure AD が更新されて、会社のブランドがサインイン ページだけでなくサインアウト画面やエラー画面にも表示されるようになりました。 この機能を有効にするための操作は一切不要で、Azure portal の **[会社のブランド]** 領域で既に設定した資産が Azure AD でそのまま使用されます。

会社のブランドの設定について詳しくは、[組織の Azure Active Directory ページへのブランドの追加](./customize-branding.md)に関する記事を参照してください。

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>新しいデプロイでの Azure Multi-Factor Authentication (MFA) Server の廃止

**種類:** 非推奨 **サービス カテゴリ:** MFA **製品の機能:** ID のセキュリティ & 保護

2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなります。 組織内で多要素認証が必要な新しいお客様は、今後はクラウドベースの Azure AD Multi-Factor Authentication を使用する必要があります。 7 月 1 日より前に MFA Server をアクティブ化したお客様には、変更が表示されません。 引き続き、最新バージョンをダウンロードしたり、今後の更新プログラムを取得したり、アクティブ化資格情報を生成したりすることができます。

詳細については、「[Azure Multi-Factor Authentication Server の概要](../authentication/howto-mfaserver-deploy.md)」を参照してください。 クラウドベースの Azure AD Multi-factor Authentication の詳細については、[クラウド ベースの Azure AD Multi-Factor Authentication のデプロイの計画](../authentication/howto-mfa-getstarted.md)に関するページを参照してください。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>サービスの変更:アプリケーション プロキシ サービスでの TLS 1.2 プロトコルのみの今後のサポート

**種類:** 変更の計画 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

お客様にクラス最高の暗号化を提供するために、Application Proxy サービスでは TLS 1.2 プロトコルのみにアクセスを制限しています。 この変更は、TLS 1.2 プロトコルのみを既に使用しているお客様に徐々に適用されているため、変更を意識することはありません。

2019 年 8 月 31日で TLS 1.0 と TLS 1.1 が廃止されますが、改めて事前通知を行いますので、この変更に備える時間が与えられます。 この変更に備えるために、Application Proxy を通じて発行されたアプリへのアクセスにユーザーが使用するすべてのクライアントを含む、クライアントとサーバーおよびブラウザーとサーバーの組み合わせが、TLS 1.2 プロトコルを使用して Application Proxy サービスへの接続を維持するように更新されていることを確認してください。 詳しくは、「[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites)」を参照してください。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用状況および分析情報レポートを使用して、アプリに関連するサインイン データを表示する

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** 監視とレポート

これで、Azure portal の **[エンタープライズ アプリケーション]** 領域にある使用状況および分析情報レポートを使用して、以下に関する情報を含んだサインイン データをアプリケーションを中心にして表示できます。

- 組織内で最もよく使われるアプリ

- サインインの失敗が最も多いアプリ

- アプリごとの上位のサインイン エラー

この機能の詳細については、「[Azure Active Directory ポータルの使用状況と分析情報のレポート](../reports-monitoring/concept-usage-insights-report.md)」を参照してください

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Azure AD を使用してクラウド アプリへのユーザー プロビジョニングを自動化する

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** 監視とレポート

これらの新しいチュートリアルに従って、Azure AD プロビジョニング サービスを使用して、次のクラウド ベース アプリのユーザー アカウントの作成、削除、および更新を自動化します。

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

グループ オブジェクトのプロビジョニング方法に関する情報が記載されている、この新しい [Dropbox チュートリアル](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)に従うこともできます。

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD での SaaS アプリケーションへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関するページを参照してください。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Azure AD で ID セキュリティ スコアが利用可能 (一般提供)

**種類:** 新機能 **サービス カテゴリ:** 該当なし **製品の機能:** ID のセキュリティ & 保護

これで、Azure AD で ID セキュリティ スコア機能を使用することにより、ID セキュリティ体制を監視し改善できます。 ID セキュリティ スコア機能では、次の操作に役立つ単一のダッシュボードを使用します。

- 1 から 223 のスコアに基づいて ID セキュリティ体制を客観的に測定する。

- ID セキュリティの強化を計画する

- セキュリティの強化の成功を確認する

ID セキュリティ スコア機能の詳細については、「[Azure Active Directory の ID セキュリティ スコアとは](./identity-secure-score.md)」を参照してください。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>新しいアプリの登録エクスペリエンスが利用可能 (一般提供)

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** 開発者エクスペリエンス

新しい[アプリの登録](https://aka.ms/appregistrations)エクスペリエンスが一般提供され利用可能になりました。 この新しいエクスペリエンスには、Azure portal とアプリケーション登録ポータルの使い慣れているすべての主要機能が含まれ、これらの機能は以下を通じて向上しています。

- **アプリの管理の向上。** さまざまなポータルにわたってアプリを表示する代わりに、1 か所ですべてのアプリを表示できるようになりました。

- **アプリの登録の簡略化。** 改善されたナビゲーション エクスペリエンスから、刷新されたアクセス許可選択エクスペリエンスまで、アプリの登録および管理が簡単になりました。

- **詳細情報。** クイック スタート ガイドなど、アプリに関する詳細が表示されます。

詳細については、[Microsoft ID プラットフォーム](../develop/index.yml)と[新しいアプリの登録エクスペリエンスが一般に利用可能になった](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)ことに関するページを参照してください。 ブログでのお知らせ。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Identity Protection の Risky Users API で使用できる新機能

**種類:** 新機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

Risky Users API を使用して、ユーザーのリスク履歴を取得し、危険なユーザーを無視し、ユーザーを侵害対象として確認できるようになったことをお知らせします。 この変更により、より効率的にユーザーのリスク状態を更新し、リスク履歴を理解できるようになりました。

詳細については、[Risky Users API のリファレンス ドキュメント](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 5 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019年 5 月に、フェデレーションを使用した以下の 21 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Freedcamp](../saas-apps/freedcamp-tutorial.md)、[Real Links](../saas-apps/real-links-tutorial.md)、[Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)、[Simple Sign](../saas-apps/simple-sign-tutorial.md)、[Braze](../saas-apps/braze-tutorial.md)、[Displayr](../saas-apps/displayr-tutorial.md)、[Templafy](../saas-apps/templafy-tutorial.md)、[Marketo Sales Engage](https://toutapp.com/login)、[ACLP](../saas-apps/aclp-tutorial.md)、[OutSystems](../saas-apps/outsystems-tutorial.md)、[Meta4 Global HR](../saas-apps/meta4-global-hr-tutorial.md)、[Quantum Workplace](../saas-apps/quantum-workplace-tutorial.md)、[Cobalt](../saas-apps/cobalt-tutorial.md)、[webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md)、[RedFlag](https://pocketstop.com/redflag/)、[Whatfix](../saas-apps/whatfix-tutorial.md)、[Control](../saas-apps/control-tutorial.md)、[JOBHUB](../saas-apps/jobhub-tutorial.md)、[NEOGOV](../saas-apps/neogov-tutorial.md)、[Foodee](../saas-apps/foodee-tutorial.md)、[MyVR](../saas-apps/myvr-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Azure AD ポータルの強化されたグループ作成および管理エクスペリエンス

**種類:** 新機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

Azure AD ポータルでグループに関連するエクスペリエンスの機能強化を行いました。 これらの機能強化により、管理者は、グループの一覧やメンバーの一覧をより適切に管理し、追加の作成オプションを提供できます。

機能強化は次のとおりです。

- メンバーシップの種類およびグループの種類による基本フィルター処理。

- ソースやメール アドレスなどの新しい列の追加。

- 簡単に選択解除するためのグループ、メンバー、および所有者の一覧を複数選択する機能。

- グループの作成中にメール アドレスを選択し、所有者を追加する機能。

詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](./active-directory-groups-create-azure-portal.md)」を参照してください。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Azure AD ポータルで Office 365 グループの名前付けポリシーを構成する (一般提供)

**種類:** 変更された機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

管理者は、Azure AD ポータルを使用して、Office 365 グループの名前付けポリシーを構成できるようになりました。 この変更は、組織内のユーザーによって作成または編集される Office 365 グループに対して一貫性のある名前付け規則を適用するのに役立ちます。

Office 365 グループの名前付けポリシーは、次の 2 通りの方法で構成できます。

- グループ名に自動的に追加されるプレフィックスまたはサフィックスを定義します。

- グループ名で許可されない、組織の禁止されている単語のカスタマイズ セットをアップロードします (たとえば、"CEO、Payroll、HR")。

詳細情報については、「[Azure Active Directory での Office 365 グループの名前付けポリシーの強制](../enterprise-users/groups-naming-policy.md)」をご覧ください。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API エンドポイントが Azure AD アクティビティ ログで利用可能(一般提供)

**種類:** 変更された機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure AD アクティビティ ログで Microsoft Graph API エンドポイントのサポートが一般的に利用できるようになったことをお知らせします。 このリリースでは、バージョン 1.0 の Azure AD 監査ログとサインイン ログ API を使用できるようになりました。

詳細については、[Azure AD 監査ログ API の概要](/graph/api/resources/azure-ad-auditlog-overview)に関するページを参照してください。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>管理者が統合登録プロセスに条件付きアクセスを使用可能 (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティ & 保護

管理者は、統合登録ページで使用する条件付きアクセス ポリシーを作成できるようになりました。 これには、次の場合に登録を許可するポリシーの適用が含まれます。

- ユーザーが信頼できるネットワーク上にいる場合。

- ユーザーのサインイン リスクが低い場合。

- ユーザーがマネージド デバイス上にある場合。

- ユーザーが組織の利用規約 (TOU) に同意した場合。

条件付きアクセスとパスワードのリセットの詳細については、[「Azure AD の MFA とパスワードのリセットの統合登録エクスペリエンスの条件付きアクセス」のブログ記事](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)をご覧ください。 統合登録プロセスの条件付きアクセス ポリシーの詳細については、「[統合登録の条件付きアクセス ポリシー](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration)」を参照してください。 Azure AD Terms of Use 機能の詳細については、「[Azure Active Directory Terms of Use 機能](../conditional-access/terms-of-use.md)」を参照してください。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>新しい Azure AD 脅威インテリジェンス検出が、Azure AD Identity Protection で利用可能

**種類:** 新機能 **サービス カテゴリ:** Azure AD ID 保護 **製品の機能:** ID のセキュリティ & 保護

Azure AD 脅威インテリジェンス検出が、Azure AD Identity Protection 機能の一部として利用できるようになりました。 この新しい機能は、Microsoft の内部および外部の脅威インテリジェンス ソースに基づいて、特定のユーザーにとって異常なユーザー アクティビティや、既知の攻撃パターンに一致するアクティビティを示す場合に役立ちます。

更新されたバージョンの Azure AD Identity Protection の詳細については、[Azure AD Identity Protection の 4 つの主要な機能強化がパブリック ビューに示されるようになった](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935)ことに関するブログと「[Azure Active Directory Identity Protection (更新版) とは](../identity-protection/overview-identity-protection.md)」 の記事を参照してください。 Azure AD 脅威インテリジェンス検出の詳細については、[Azure Active Directory Identity Protection のリスク検出](../identity-protection/concept-identity-protection-risks.md)に関する記事を参照してください。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD エンタイトルメント管理が利用可能 (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** ID ガバナンス **製品の機能:** Identity Governance

Azure AD エンタイトルメント管理 (現在パブリック プレビュー段階) は、従業員とビジネス パートナーがアクセスを要求する方法、承認を行うユーザー、アクセスできる時間を定義するアクセス パッケージの管理をお客様が委任できるようにします。 アクセス パッケージは、Azure AD と Office 365 の各グループでのメンバーシップ、エンタープライズ アプリケーションでのロールの割り当て、および SharePoint Online サイトのロールの割り当てを管理できます。 エンタイトルメント管理の詳細情報については、[Azure AD エンタイトルメント管理の概要](../governance/entitlement-management-overview.md)に関する記事をご覧ください。 Privileged Identity Management、アクセス レビュー、および利用規約などの Azure AD Identity Governance 機能の広がりについて詳しくは、「[Azure AD Identity Governance とは](../governance/identity-governance-overview.md)」をご覧ください。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Azure AD ポータルで Office 365 グループの名前付けポリシーを構成する (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

管理者は、Azure AD ポータルを使用して、Office 365 グループの名前付けポリシーを構成できるようになりました。 この変更は、組織内のユーザーによって作成または編集される Office 365 グループに対して一貫性のある名前付け規則を適用するのに役立ちます。

Office 365 グループの名前付けポリシーは、次の 2 通りの方法で構成できます。

- グループ名に自動的に追加されるプレフィックスまたはサフィックスを定義します。

- グループ名で許可されない、組織の禁止されている単語のカスタマイズ セットをアップロードします (たとえば、"CEO、Payroll、HR")。

詳細情報については、「[Azure Active Directory での Office 365 グループの名前付けポリシーの強制](../enterprise-users/groups-naming-policy.md)」をご覧ください。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD アクティビティ ログを Azure Monitor で使用できるようになりました (一般提供)。

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure AD アクティビティ ログの視覚化に関するフィードバックに対応するために、Log Analytics に新しい分析情報機能を導入します。 この機能により、Workbooks という対話型テンプレートを使用して Azure AD リソースに関する分析情報を得ることができます。 これらの既製の Workbooks は、アプリまたはユーザーの詳細情報を提供することができます。これには以下が含まれます。

- **サインイン。** サインインの場所、使用中のオペレーティング システムまたはブラウザーのクライアントとバージョン、成功または失敗したサインインの回数などのアプリおよびユーザーの詳細情報を提供します。

- **レガシ認証と条件付きアクセス。** 条件付きアクセス ポリシーによってトリガーされる Multi-Factor Authentication の使用、条件付きアクセス ポリシーを使用するアプリなど、レガシ認証を使用するアプリおよびユーザーの詳細情報を提供します。

- **サインイン エラー分析。** サインイン エラーの発生原因がユーザー アクションか、ポリシーの問題か、またはインフラストラクチャかを判別するのを支援します。

- **カスタム レポート。** 新規の Workbooks を作成したり、既存の Workbooks を編集したりして、組織の Insights 機能のカスタマイズに役立てることができます。

詳細情報については、「[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](../reports-monitoring/howto-use-azure-monitor-workbooks.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 4 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019 年 4 月に、フェデレーションを使用した以下の 21 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md)、[HRworks Single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md)、[Percolate](../saas-apps/percolate-tutorial.md)、[MobiControl](../saas-apps/mobicontrol-tutorial.md)、[Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md)、[Shibumi](../saas-apps/shibumi-tutorial.md)、[Benchling](../saas-apps/benchling-tutorial.md)、[MileIQ](https://mileiq.onelink.me/991934284/7e980085)、[PageDNA](../saas-apps/pagedna-tutorial.md)、[EduBrite LMS](../saas-apps/edubrite-lms-tutorial.md)、[RStudio Connect](../saas-apps/rstudio-connect-tutorial.md)、[AMMS](../saas-apps/amms-tutorial.md)、[Mitel Connect](../saas-apps/mitel-connect-tutorial.md)、[Alibaba Cloud (ロールベースの SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md)、[Certent Equity Management](../saas-apps/certent-equity-management-tutorial.md)、[Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md)、[GreenOrbit](../saas-apps/greenorbit-tutorial.md)、[Workgrid](../saas-apps/workgrid-tutorial.md)、[monday.com](../saas-apps/mondaycom-tutorial.md)、[SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md)、[Indiggo](https://indiggolead.com/)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新規アクセス レビューの頻度オプションと複数のロールの選択

**種類:** 新機能 **サービス カテゴリ:** アクセス レビュー **製品の機能:** Identity Governance

Azure AD アクセス レビューの新しい更新プログラムにより、次のことが可能になります。

- アクセス レビューの頻度を、以前からある、週に 1 度、月に 1 度、4 半期に 1 度、および 1 年に 1 度のオプションに加え、「**半年に 1 度**」に変更します。

- 1 つのアクセス レビューを作成するときに、複数の Azure AD と Azure リソースのロールを選択します。 この場合は、すべてのロールが同じ設定値で設定され、すべてのレビュー担当者に同時に通知が送られます。

アクセス レビューを作成する方法の詳細については、[Azure AD アクセス レビューでのグループまたはアプリケーションのアクセス レビューの作成](../governance/create-access-review.md)に関するページを参照してください

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect の電子メール アラート システムの切り替えが行われており、一部のお客様に対して新規電子メール送信者情報が送信される

**種類:** 変更された機能 **サービス カテゴリ:** AD Sync **製品の機能:** プラットフォーム

Azure AD Connect で電子メール アラート システムの切り替えが行われており、一部のお客様に新規電子メール送信者が表示される可能性があります。 これに対処するには、`azure-noreply@microsoft.com` を組織の許可リストに追加する必要があります。さもないと、Office 365、Azure、またはお使いの同期サービスから重要なアラートを引き続き受信できなくなります。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Azure AD Connect でフェデレーション ドメイン間の UPN サフィックスの変更が正常に行われるようになりました

**種類:** 修正済み **サービス カテゴリ:** AD Sync **製品の機能:** プラットフォーム

Azure AD Connect で、あるフェデレーション ドメインから別のフェデレーション ドメインに、ユーザーの UPN サフィックスを正しく変更できるようになりました。 この修正により、同期サイクル中に FederatedDomainChangeError エラー メッセージを受け取ったり、"Azure Active Directory でこのオブジェクトを更新できません。属性 [FederatedUser.UserPrincipalName] が無効です。 ローカルのディレクトリ サービスでこの値を更新してください" という通知メールを受け取ったりすることがなくなります。

詳しくは、[同期中のエラーのトラブルシューティング](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror)に関するページをご覧ください。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Azure AD でアプリ保護ベースの条件付きアクセス ポリシーを使用してセキュリティを強化 (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティ & 保護

**[アプリの保護ポリシーが必要]** を使用することで、アプリ保護ベースの条件付きアクセスを使用できるようになりました。 この新しいポリシーは、次のことを防止することで組織のセキュリティ向上を支援します。

- ユーザーが Microsoft Intune ライセンスなしでアプリにアクセスできるようにすること。

- ユーザーが Microsoft Intune アプリ保護ポリシーを取得できないようにすること。

- ユーザーが Microsoft Intune アプリ保護ポリシーなしでアプリにアクセスできるようにすること。

詳細については、[条件付きアクセスを使用してクラウド アプリへのアクセスにアプリ保護ポリシーを要求する方法](../conditional-access/app-protection-based-conditional-access.md)に関するページを参照してください。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge での Azure AD シングル サインオンと条件付きアクセスの新しいサポート (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティ & 保護

Microsoft Edge に対する Azure AD のサポートが強化されました。これには、Azure AD シングル サインオンと条件付きアクセスの新しいサポートの提供が含まれます。 以前に Microsoft Intune Managed Browser を使用していた場合は、代わりに Microsoft Edge を使用できるようになりました。

条件付きアクセスを使用したデバイスとアプリの設定と管理の詳細については、「[条件付きアクセスを使用してクラウド アプリへのアクセスにマネージド デバイスを要求する](../conditional-access/require-managed-devices.md)」および「[条件付きアクセスを使用してクラウド アプリへのアクセスに承認されたクライアント アプリを要求する](../conditional-access/app-based-conditional-access.md)」を参照してください。 Microsoft Intune ポリシーと Microsoft Edge を使用してアクセスを管理する方法の詳細については、「[Microsoft Intune のポリシーで保護されたブラウザーを使用してインターネット アクセスを管理する](/intune/app-configuration-managed-browser)」を参照してください。

---

## <a name="march-2019"></a>2019 年 3 月

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C での Identity Experience Framework とカスタム ポリシーのサポートが使用可能になりました (GA)

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

Azure AD B2C でカスタム ポリシーを作成できるようになりました。これには、Azure SLA のもとで大規模にサポートされる次のタスクが含まれます。

- カスタム ポリシーを使用して、カスタム認証のユーザー体験を作成およびアップロードする。

- 要求プロバイダー間のやり取りとしてユーザー体験を順を追って記述する。

- ユーザー体験の条件分岐を定義する。

- リアルタイムの意思決定および通信で使用するために、要求を変換およびマップする。

- カスタム認証のユーザー体験で REST API 対応サービスを使用する。 たとえば、電子メール プロバイダー、CRM、および独自の承認システムで。

- OpenIDConnect プロトコルに準拠している ID プロバイダーとフェデレーションする。 たとえば、マルチテナント Azure AD、ソーシャル アカウント プロバイダー、または 2 要素検証プロバイダーと。

カスタム ポリシーの作成の詳細については、[Azure Active Directory B2C でのカスタム ポリシーの開発者向けメモ](../../active-directory-b2c/custom-policy-developer-notes.md)に関するページ、および [Alex Simon のブログ記事 (ケース スタディを含む)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791) を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 3 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019 年 3 月に、フェデレーションがサポートされる次の 14 の新しいアプリがアプリ ギャラリーに追加されました。

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/)、[MediusFlow](https://office365.cloudapp.mediusflow.com/)、[ePlatform](../saas-apps/eplatform-tutorial.md)、[Fulcrum](../saas-apps/fulcrum-tutorial.md)、[ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md)、[Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md)、[Lean](../saas-apps/lean-tutorial.md)、[Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md)、[Cinode](https://cinode.com/)、[Iris Intranet](../saas-apps/iris-intranet-tutorial.md)、[Empactis](../saas-apps/empactis-tutorial.md)、[SmartDraw](../saas-apps/smartdraw-tutorial.md)、[Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md)、[TAS](../saas-apps/tas-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD ギャラリー内の新しい Zscaler および Atlassian プロビジョニング コネクタ - 2019 年 3 月

**種類:** 新機能 **サービス カテゴリ:** アプリのプロビジョニング **製品の機能:** サード パーティ統合

次のアプリでのユーザー アカウントの作成、更新、および削除を自動化します。

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md)、[Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md)、[Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md)、[Zscaler 2](../saas-apps/zscaler-two-provisioning-tutorial.md)、[Zscaler 3](../saas-apps/zscaler-three-provisioning-tutorial.md)、[Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md)、[Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD での SaaS アプリケーションへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関するページを参照してください。

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Azure AD ポータルで削除された Office 365 グループの復元および管理

**種類:** 新機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

Azure AD ポータルから削除された Office 365 グループを表示および管理できるようになりました。 この変更により、どのグループが復元できるかを確認したり、組織で必要がなくなったすべてのグループを完全に削除したりできるようになります。

詳細については、[有効期限が切れたグループや削除されたグループの復元](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore)に関するページを参照してください。

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Azure AD SAML で保護されたオンプレミス アプリで、アプリケーション プロキシ経由でシングル サインオンを使用できるようになりました (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

SAML で認証されたオンプレミス アプリで、これらのアプリへのリモート アクセスと共に、アプリケーション プロキシ経由でシングル サインオン (SSO) エクスペリエンスを使用できるようになりました。 オンプレミス アプリで SAML SSO を設定する方法の詳細については、「[アプリケーション プロキシ (プレビュー) を使用したオンプレミスのアプリケーションに対する SAML シングル サインオン](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md)」を参照してください。

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>信頼性とユーザー エクスペリエンスを向上させるために要求ループ内のクライアント アプリが中断されます

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

クライアント アプリが、短期間に数百の同じログイン要求を誤って発行する場合があります。 これらの要求は、成功するかどうかにかかわらず、そのすべてがユーザー エクスペリエンスの低下や IDP のワークロードの増加につながるため、すべてのユーザーの待ち時間が長くなり、IDP の可用性が低下します。

この更新プログラムは、通常の動作の範囲を超えて、重複した要求を短期間に複数回発行するクライアント アプリに `invalid_grant` エラー: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` を送信します。 この問題が発生したクライアント アプリは対話型プロンプトを表示し、ユーザーに再度サインインすることを要求する必要があります。 この変更や、このエラーが発生した場合にアプリを修正する方法の詳細については、「[認証の新機能](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted)」を参照してください。

---

### <a name="new-audit-logs-user-experience-now-available"></a>[監査ログ] の新しいユーザー エクスペリエンスが使用可能になりました

**種類:** 変更された機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

読みやすさと情報の検索方法の両方を向上させるのに役立つ新しい Azure AD **[監査ログ]** ページが作成されました。 新しい **[監査ログ]** ページを表示するには、Azure AD の **[アクティビティ]** セクションで **[監査ログ]** を選択します。

![サンプル情報を含む新しい [監査ログ] ページ](media/whats-new/audit-logs-page.png)

新しい **[監査ログ]** ページの詳細については、「[Azure Active Directory ポータルの監査アクティビティ レポート](../reports-monitoring/concept-audit-logs.md#audit-logs)」を参照してください。

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>正しく構成されていない条件付きアクセス ポリシーからの誤った管理者ロックアウトの防止に役立つ新しい警告およびガイダンス

**種類:** 変更された機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティ & 保護

管理者が、正しく構成されていない条件付きアクセス ポリシーにより自分のテナントから誤って自身をロックすることを防止するのに役立つように、Azure Portal に新しい警告および更新されたガイダンスが作成されました。 新しいガイダンスの詳細については、「[Azure Active Directory 条件付きアクセスのサービス依存関係の概要](../conditional-access/service-dependencies.md)」を参照してください。

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>モバイル デバイスでのエンドユーザーの利用規約エクスペリエンスの改善

**種類:** 変更された機能 **サービス カテゴリ:** 使用条件 **製品の機能:** ガバナンス

モバイル デバイスで利用規約を確認して同意する方法の改善に役立つように、既存の利用規約エクスペリエンスが更新されました。 情報のズームイン/ズームアウト、前の画面への戻り、ダウンロード、およびハイパーリンクの選択が可能になりました。 更新された利用規約の詳細については、[Azure Active Directory Terms of Use 機能](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users)に関するページを参照してください。

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>新しい Azure AD アクティビティ ログのダウンロード エクスペリエンスが使用可能になりました

**種類:** 変更された機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

大量のアクティビティ ログを Azure Portal から直接ダウンロードできるようになりました。 この更新プログラムでは、次のことが可能になります。

- 最大 250,000 行をダウンロードする。

- ダウンロードが完了したら通知を受け取る。

- ファイル名をカスタマイズする。

- 出力形式 (JSON または CSV のどちらか) を決定する。

この機能の詳細については、「[クイック スタート: Azure portal を使用して監査レポートをダウンロードする](../reports-monitoring/quickstart-download-audit-report.md)」を参照してください。

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>重大な変更:Exchange ActiveSync (EAS) による条件評価の更新

**種類:** 変更の計画 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** アクセス制御

現在、Exchange ActiveSync (EAS) が次の条件を評価する方法を更新しているところです。

- 国、地域、または IP アドレスに基づいたユーザーの場所

- サインイン リスク

- デバイスのプラットフォーム

以前に条件付きアクセス ポリシーでこれらの条件を使用している場合は、条件動作が変更される可能性があることに注意してください。 たとえば、以前にあるポリシーでユーザーの場所の条件を使用している場合は、そのポリシーが、ユーザーの場所に基づいて省略されるようになったことに気付く可能性があります。

---

## <a name="february-2019"></a>2019 年 2 月

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>構成可能な Azure AD SAML トークン暗号化 (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

サポートされている SAML アプリを、暗号化された SAML トークンを受信するように構成できるようになりました。 アプリで構成および使用する場合、Azure AD は、Azure AD に格納されている証明書から取得した公開キーを使用して、出力された SAML アサーションを暗号化します。

SAML トークン暗号化を構成する方法の詳細については、「[Azure AD SAML トークン暗号化の構成](../manage-apps/howto-saml-token-encryption.md)」を参照してください。

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Azure AD アクセス レビューを使用してグループやアプリのアクセス レビューを作成する

**種類:** 新機能 **サービス カテゴリ:** アクセス レビュー **製品の機能:** ガバナンス

グループのメンバーシップまたはアプリの割り当てについて、1 つの Azure AD アクセス レビューに複数のグループまたはアプリを含められるようになりました。 複数のグループまたはアプリを含むアクセス レビューは同じ設定を使用して設定され、含められたすべてのレビュー担当者に同時に通知されます。

Azure AD アクセス レビューを使用してアクセス レビューを作成する方法の詳細については、[Azure AD アクセス レビューでのグループまたはアプリケーションのアクセス レビューの作成](../governance/create-access-review.md)に関するページを参照してください

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 2 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019 年 2 月に、フェデレーションがサポートされる次の 27 の新しいアプリがアプリ ギャラリーに追加されました。

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md)、[MindTickle](../saas-apps/mindtickle-tutorial.md)、[FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)、[AirStack](../saas-apps/airstack-tutorial.md)、[Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md)、[IDrive](../saas-apps/idrive-tutorial.md)、[Skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md)、[Brightidea](../saas-apps/brightidea-tutorial.md)、[AlertOps](../saas-apps/alertops-tutorial.md)、[Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md)、Permission Click、[Brandfolder](../saas-apps/brandfolder-tutorial.md)、[StoregateSmartFile](../saas-apps/smartfile-tutorial.md)、[Pexip](../saas-apps/pexip-tutorial.md)、[Stormboard](../saas-apps/stormboard-tutorial.md)、[Seismic](../saas-apps/seismic-tutorial.md)、[Share A Dream](https://www.shareadream.org/how-it-works)、[Bugsnag](../saas-apps/bugsnag-tutorial.md)、[webMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md)、[Knowledge Anywhere LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md)、[OU Campus](../saas-apps/ou-campus-tutorial.md)、[Periscope Data](../saas-apps/periscope-data-tutorial.md)、[Netop Portal](../saas-apps/netop-portal-tutorial.md)、[smartvid.io](../saas-apps/smartvid.io-tutorial.md)、[PureCloud by Genesys](../saas-apps/purecloud-by-genesys-tutorial.md)、[ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="enhanced-combined-mfasspr-registration"></a>拡張された MFA と SSPR の統合登録

**種類:** 変更された機能 **サービス カテゴリ:** セルフサービス パスワード リセット **製品の機能:** ユーザー認証

お客様からのフィードバックに基づき、MFA と SSPR の統合登録のプレビュー版の機能を強化して、ユーザーが MFA と SSPR の両方のセキュリティ情報をより迅速に登録できるようにしました。

**ユーザーがこの強化された機能を今すぐ利用できるようにするには、以下の手順を実行してください。**

1. グローバル管理者またはユーザー管理者として Azure portal にサインインし、 **[Azure Active Directory] > [ユーザー設定] > [アクセス パネル プレビュー機能の設定を管理]** の順に移動します。

2. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます – 更新]** オプションで、この機能を有効にする対象として **[選択したユーザー グループ]** または **[すべてのユーザー]** を選択します。

今後数週間のうちに、現時点で有効にされていない MFA/SSPR の古い統合登録 のプレビュー版の機能を有効にできないようにする予定です。

**ご使用のテナントで対象コントロールが削除されるかどうかを確認するには、次の手順を実行します。**

1. グローバル管理者またはユーザー管理者として Azure portal にサインインし、 **[Azure Active Directory] > [ユーザー設定] > [アクセス パネル プレビュー機能の設定を管理]** の順に移動します。

2. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます]** オプションを **[なし]** に設定すると、このオプションがテナントから削除されます。

以前に MFA/SSPR の古い統合登録のプレビュー版の機能をユーザーに対して有効にしたかどうかに関係なく、古い機能は今後無効になる予定です。 そのため、可能なかぎり早急に、強化された新しい機能に移行することをお勧めします。

強化された登録機能の詳細については、[Azure AD の MFA とパスワードのリセットの統合登録エクスペリエンスに関する優れた拡張内容](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)に関するページを参照してください。

---

### <a name="updated-policy-management-experience-for-user-flows"></a>ユーザー フローのポリシー管理エクスペリエンスが更新されました

**種類:** 変更された機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

ユーザー フローに対するポリシーの作成と管理のプロセス (従来の組み込みポリシー) が簡単になるように更新しました。 この新しいエクスペリエンスがすべての Azure AD テナントに関する新しい既定になりました。

ポータル画面上部にある **[フィードバックの送信]** 領域にあるお気に入りや問題点、改善点のためのアイコンを使用して、フィードバックや提案をお送りください。

新しいポリシー管理エクスペリエンスについて詳しくは、関連するブログ記事「[Azure AD B2C に JavaScript のカスタマイズおよび多数の新機能が追加されました](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)」をご覧ください。

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Azure AD B2C によって提供される特定のページ要素のバージョンを選択する

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

Azure AD B2C が提供する特定のバージョンのページ要素を選択できるようになりました。 特定のバージョンを選択することで、ページに表示される前に更新をテストし、動作を予測できるようになります。 さらに、JavaScript のカスタマイズを可能にするために特定のページ バージョンを強制するように選択できるようになりました。 この機能を有効にするには、ユーザー フローの **[プロパティ]** ページにアクセスします。

特定のバージョンのページ要素を選択する方法の詳細については、関連するブログ記事「[Azure AD B2C に JavaScript のカスタマイズおよび多数の新機能が追加されました](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)」をご覧ください。

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C の構成可能なエンドユーザーのパスワード要件 (GA)

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

ネイティブな Azure AD パスワード ポリシーを使用しなくても、エンド ユーザー向けの組織のパスワードの複雑さを設定できるようになりました。 ユーザー フローの **[プロパティ]** ブレード (以前の組み込みポリシー) から、パスワードの複雑度を **[簡易]** または **[強い]** のいずれかに選択できます。または、 **[カスタム]** の要件セットを作成することもできます。

パスワードの複雑度の要件構成については、「[Azure Active Directory B2C でパスワードの複雑度要件を構成する](../../active-directory-b2c/password-complexity.md)」をご覧ください。

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>カスタムのブランド化された認証エクスペリエンスの新しい既定のテンプレート

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

ユーザー フローの **[ページ レイアウト]** ブレードにある新しい既定のテンプレート (以前の組み込みポリシー) を使用して、ユーザー用にカスタムのブランド化された認証エクスペリエンスを作成できます。

テンプレートの使用方法については、「[Azure AD B2C に JavaScript のカスタマイズと多くの新機能が追加されました](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)」をご覧ください。

---

## <a name="january-2019"></a>2019 年 1 月

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>ワンタイム パスコード認証を使用した Active Directory B2B コラボレーション (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** B2B **製品の機能:** B2B/B2C

Azure AD、Microsoft アカウント (MSA)、Google フェデレーションなどの他の手段を使用して認証できない B2B ゲスト ユーザーのために、ワンタイム パスコード認証 (OTP) が導入されました。 この新しい認証方法により、ゲスト ユーザーは新しい Microsoft アカウントを作成せずに済みます。 代わりに、ゲスト ユーザーは、招待の利用時、または共有リソースへのアクセス時に、メール アドレスに一時コードを送信するように要求できます。 ゲスト ユーザーは、この一時コードを使用してサインインを続行できます。

詳細については、[メールによるワンタイム パスコード認証 (プレビュー)](../external-identities/one-time-passcode.md) に関するページ、および [Azure AD でアカウントを持つユーザーがシームレスに共有およびコラボレーションを行う方法](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)に関するブログを参照してください。

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>新しい Azure AD アプリケーション プロキシの Cookie の設定

**種類:** 新機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

アプリケーション プロキシを使用して発行されたアプリで使用可能な、次の 3 つの新しい Cookie の設定を導入しました。

- **HTTP 専用 Cookie を使用する。** アプリケーション プロキシのアクセスとセッションの Cookie に **HTTPOnly** フラグを設定します。 この設定をオンにすると、クライアント側のスクリプトによる Cookie のコピーや変更の防止に役立つといった、セキュリティに関する追加の利点が提供されます。 追加の利点を得るために、このフラグをオンにする ( **[はい]** を選択する) ことをお勧めします。

- **セキュリティで保護された Cookie を使用する。** アプリケーション プロキシのアクセスとセッションの Cookie に **Secure** フラグを設定します。 この設定をオンにすると、Cookie が HTTPS などの TLS セキュア チャネル経由でのみ送信されるようにすることで、セキュリティに関する追加の利点が提供されます。 追加の利点を得るために、このフラグをオンにする ( **[はい]** を選択する) ことをお勧めします。

- **永続的な Cookie を使用する。** Web ブラウザーが閉じているときにアクセスの Cookie が期限切れにならないようにします。 これらの Cookie は、アクセス トークンの有効期間の間、持続します。 ただし、有効期限に達した場合、またはユーザーが手動で Cookie を削除した場合、これらの Cookie はリセットされます。 既定の設定 **[いいえ]** のままにして、プロセス間で Cookie を共有しない古いアプリの設定だけをオンにすることをお勧めします。

新しい Cookie の詳細については、「[Azure Active Directory でオンプレミスのアプリケーションにアクセスするための Cookie 設定](../manage-apps/application-proxy-configure-cookie-settings.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 1 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2019 年 1 月に、フェデレーションを使用した以下の 35 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Firstbird](../saas-apps/firstbird-tutorial.md)、[Folloze](../saas-apps/folloze-tutorial.md)、[Talent Palette](../saas-apps/talent-palette-tutorial.md)、[Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md)、[Cisco Umbrella](../saas-apps/cisco-umbrella-tutorial.md)、[Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md)、[Expiration Reminder](../saas-apps/expiration-reminder-tutorial.md)、[InstaVR Viewer](../saas-apps/instavr-viewer-tutorial.md)、[CorpTax](../saas-apps/corptax-tutorial.md)、[Verb](https://app.verb.net/login)、[OpenLattice](https://openlattice.com/agora)、[TheOrgWiki](https://www.theorgwiki.com/signup)、[Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md)、[GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md)、[Cloud Service PICCO](../saas-apps/cloud-service-picco-tutorial.md)、[AuditBoard](../saas-apps/auditboard-tutorial.md)、[iProva](../saas-apps/iprova-tutorial.md)、[Workable](../saas-apps/workable-tutorial.md)、[CallPlease](https://webapp.callplease.com/create-account/create-account.html)、[GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md)、[CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md)、[Deskradar](../saas-apps/deskradar-tutorial.md)、[Coralogixv](../saas-apps/coralogix-tutorial.md)、[Signagelive](../saas-apps/signagelive-tutorial.md)、[ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md)、[K2 for Office 365](https://www.k2.com/O365)、[Xledger](https://www.xledger.net/)、[iDiD Manager](../saas-apps/idid-manager-tutorial.md)、[HighGear](../saas-apps/highgear-tutorial.md)、[Visitly](../saas-apps/visitly-tutorial.md)、[Korn Ferry ALP](../saas-apps/korn-ferry-alp-tutorial.md)、[Acadia](../saas-apps/acadia-tutorial.md)、[Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>新しい Azure AD Identity Protection の機能強化 (パブリック プレビュー)

**種類:** 変更された機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

Azure AD Identity Protection のパブリック プレビュー オファリングに、以下の機能強化が追加されたことを発表いたします。

- ユーザー インターフェイスが更新され、さらに統合されました

- 追加の API

- 機械学習によるリスク評価が強化されました

- リスクの高いユーザーとリスクの高いサインインの間での製品全体の配置

機能強化の詳細については、[Azure Active Directory Identity Protection (更新済み) の概要](../identity-protection/overview-identity-protection.md)に関するページを参照してください。 製品内のプロンプトを介して、詳しく学習することや自分の考えを共有することができます。

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>iOS および Android デバイスの Microsoft Authenticator アプリのアプリ ロックの新機能

**種類:** 新機能 **サービス カテゴリ:** Microsoft Authenticator アプリ **製品の機能:** ID のセキュリティ & 保護

ワンタイム パスコード、アプリの情報、アプリの設定のセキュリティを強化するには、Microsoft Authenticator アプリのアプリ ロック機能をオンにします。 アプリ ロック機能をオンにすることは、Microsoft Authenticator アプリを開くたびに、PIN または生体認証を使用した認証を求められることを意味します。

詳細については、「[Microsoft Authenticator アプリに関する FAQ](../user-help/user-help-auth-app-faq.md)」を参照してください。

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Azure AD Privileged Identity Management (PIM) のエクスポート機能の強化

**種類:** 新機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

Privileged Identity Management (PIM) の管理者が、すべての子リソースのロールの割り当てを含む、特定のリソースのすべてのアクティブで適格なロールの割り当てをエクスポートできるようになりました。 以前は、管理者がサブスクリプションに対するロールの割り当ての完全な一覧を取得するのは困難で、特定のリソースごとにロールの割り当てをエクスポートする必要がありました。

詳細については、[PIM での Azure リソース ロールのアクティビティおよび監査履歴の表示](../privileged-identity-management/azure-pim-resource-rbac.md)に関するページを参照してください。

---

## <a name="novemberdecember-2018"></a>2018 年 11 月/12 月

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>同期範囲から削除されたユーザーがクラウド専用アカウントに切り替わらない

**種類:** 修正済み **サービス カテゴリ:** ユーザー管理 **製品の機能:** ディレクトリ

>[!Important]
>この修正についてご意見をいただき、ご迷惑をおかけしていることをよく理解しております。 このため、お客様の組織に変更を簡単に導入できるようになるまで、この変更は元に戻しました。

Active Directory Domain Services (AD DS) オブジェクトが同期範囲から除外され、後続の同期周期で Azure AD のごみ箱に移動されると、ユーザーの DirSyncEnabled フラグが間違って **False** に切り替わるバグを修正しました。 この修正の結果、ユーザーが同期範囲から除外され、その後、Azure AD のごみ箱から復元された場合、予想どおり、ユーザー アカウントはオンプレミス AD との同期を維持します。また、その権限ソース (SoA) がオンプレミス AD のままなので、クラウドで管理できません。

この修正の前は、DirSyncEnabled フラグが False に切り替わるという問題がありました。 これらのアカウントがクラウド専用オブジェクトに変換された、アカウントをクラウドで管理できるという間違った印象を与えました。 しかしながら、アカウントの SoA はオンプレミスのままであり、同期されるプロパティ (シャドウ属性) はすべてオンプレミス AD から届きます。 この状態は Azure AD とその他のクラウド ワークロードでさまざまな問題を引き起こしました。これらのアカウントは AD と同期しているものとして処理されるはずですが、クラウド専用アカウントのように動作しました。

現時点では、AD 同期アカウントをクラウド専用アカウントに実際に変換する唯一の方法はテナント レベルで DirSync を無効にし、SoA を転送するバックエンド操作をトリガーすることです。 この種の SoA 変更では、オンプレミス関連の属性 (LastDirSyncTime やシャドウ属性など) をすべて消去する必要があり、また、他のクラウド ワークロードに信号を送り、個々のオブジェクトもクラウド専用アカウントに変換させる必要があります (ただし、これらに限定されません)。

結果的に、今回の修正によって、一部のシナリオでは過去に必須でしたが、AD と同期しているユーザーの ImmutableID 属性を直接更新することが回避されます。 仕様では、Azure AD のオブジェクトの ImmutableID はその名前が示すように不変であることを意図しています。 Azure AD Connect Health と Azure AD Connect Synchronization クライアントで導入された新機能で次のようなシナリオに対処できます。

- **多数のユーザーを対象に大規模な ImmutableID 更新を段階的に行う**

  たとえば、時間のかかる AD DS フォレスト間移行を行う必要があります。 解決方法:Azure AD Connect を使用し、**ソース アンカーを構成** します。また、ユーザーが移行するとき、Azure AD からローカル AD DS ユーザーの新しいフォレストの ms-DS-Consistency-Guid 属性に既存の ImmutableID 値をコピーします。 詳細については、「[sourceAnchor としての ms-DS-ConsistencyGuid の使用](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)」を参照してください。

- **多数のユーザーを対象に大規模な ImmutableID 更新を一度に行う**

  たとえば、Azure AD Connect の導入中、設定を間違え、SourceAnchor 属性を変更しなければなりません。 解決方法:テナント レベルで DirSync を無効にし、無効な ImmutableID 値をすべて消去します。 詳しくは、「[Office 365 のディレクトリ同期を無効にする](/office365/enterprise/turn-off-directory-synchronization)」をご覧ください。

- **オンプレミス ユーザーと Azure AD の既存ユーザーを再度組み合わせる** たとえば、AD DS でユーザーが再作成された場合、Azure AD の既存アカウントと再度組み合わされず、重複する Azure AD アカウントが生成されます (孤立オブジェクト)。 解決方法:Azure portal の Azure AD Connect Health を使用し、ソース アンカー/ImmutableID のマッピングをやり直します。 詳細については、「[孤立したオブジェクトのシナリオ](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario)」を参照してください。

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>重要な変更: Azure Monitor を通じた監査およびサインイン ログ スキーマの更新

**種類:** 変更された機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

現在、監査とサインインのログ ストリームはどちらも Azure Monitor を通じて公開していますので、お使いの SIEM ツールや Log Analytics とログ ファイルをシームレスに統合できます。 お客様のフィードバックに基づき、またこの機能の一般提供の発表に備えて、次の変更がスキーマに加えられます。 これらのスキーマ変更と関連ドキュメントの更新は 1 月の第 1 週までに行われる予定です。

#### <a name="new-fields-in-the-audit-schema"></a>監査スキーマの新しいフィールド
リソースに対して実行される操作の種類を提供するために、新しい **[操作の種類]** フィールドが追加されます。 たとえば、 **[追加]** 、 **[更新]** 、 **[削除]** などです。

#### <a name="changed-fields-in-the-audit-schema"></a>監査スキーマの変更されるフィールド
監査スキーマの次のフィールドが変更されます:

|フィールド名|変更箇所|古い値|新しい値|
|----------|------------|----------|----------|
|カテゴリ|以前は **[サービス名]** フィールドでした。 現在は **[監査カテゴリ]** フィールドです。 **[サービス名]** は **loggedByService** フィールドに名前が変更されました。|<ul><li>アカウント プロビジョニング</li><li>Core Directory (コア ディレクトリ)</li><li>セルフサービス パスワード リセット</li></ul>|<ul><li>[ユーザー管理]</li><li>グループ管理</li><li>アプリの管理</li></ul>|
|targetResources|**TargetResourceType** を最上位レベルに含みます。|&nbsp;|<ul><li>ポリシー</li><li>アプリ</li><li>User</li><li>グループ</li></ul>|
|loggedByService|監査ログを生成したサービスの名前を提供します。|[Null]|<ul><li>アカウント プロビジョニング</li><li>Core Directory (コア ディレクトリ)</li><li>セルフサービス パスワード リセット</li></ul>|
|結果|監査ログの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>障害</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>サインイン スキーマの変更されるフィールド
サインイン スキーマの次のフィールドが変更されます:

|フィールド名|変更箇所|古い値|新しい値|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|これは **conditionalaccessPolicies** フィールドでした。 現在は **appliedConditionalAccessPolicies** フィールドです。|変更なし|変更なし|
|conditionalAccessStatus|サインイン時点の条件付きアクセス ポリシー ステータスの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>障害</li><li>未適用</li><li>無効</li></ul>|
|appliedConditionalAccessPolicies: 結果|サインイン時点の個別の条件付きアクセス ポリシー ステータスの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>障害</li><li>未適用</li><li>無効</li></ul>|

スキーマの詳細については、「[Azure Monitor の Azure AD 監査ログ スキーマを解釈する (プレビュー)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)」を参照してください。

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Identity Protection の教師あり機械学習モデルとリスク スコア エンジンの機能強化

**種類:** 変更された機能 **サービス カテゴリ:** ID 保護 **製品の機能:** リスク スコア

Identity Protection に関連した、ユーザーおよびサインインのリスク評価エンジンの機能強化により、ユーザー リスクの精度とカバレッジを向上させることができます。 管理者にとっての注目点は、ユーザーのリスク レベルが特定の検出のリスク レベルに直接リンクされなくなったことと、リスクの高いサインイン イベントの数とレベルが増加していることです。

ユーザーのサインインと検出のパターンの追加機能を使用してユーザーのリスクを計算する、教師ありの機械学習モデルによってリスクの検出が評価されるようになりました。 ユーザーに関連付けられた検出のリスクが低または中の場合でも、このモデルに基づいて、管理者はリスク スコアの高いユーザーを見つけることができます。

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>管理者が Microsoft Authenticator アプリ (パブリック プレビュー) を使用して自分自身のパスワードをリセット可能

**種類:** 変更された機能 **サービス カテゴリ:** セルフサービス パスワード リセット **製品の機能:** ユーザー認証

Azure AD 管理者は、Microsoft Authenticator アプリの通知や、任意のモバイル認証アプリまたはハードウェア トークンのコードを使用して、自分自身のパスワードをリセットできるようになりました。 自分自身のパスワードをリセットするために、管理者は次の方法のうちの 2 つを使用できるようになります:

- Microsoft Authenticator アプリの通知

- その他のモバイル認証アプリ/ハードウェア トークンのコード

- Email

- 音声通話

- テキスト メッセージ

Microsoft Authenticator アプリを使用したパスワードのリセットの詳細については、[Azure AD のセルフサービス パスワード リセット - モバイル アプリと SSPR (プレビュー)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr) に関するページを参照してください。

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>新しい Azure AD Cloud Device Administrator ロール (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** デバイスの登録と管理 **製品の機能:** アクセス制御

クラウド デバイス管理タスクを実行するために、管理者はユーザーを新しい Cloud Device Administrator ロールに割り当てることができます。 Cloud Device Administrators ロールを割り当てられたユーザーは、Azure AD でデバイスを有効化、無効化、削除できることに加えて、Azure portal で Windows 10 の BitLocker キー (ある場合) を読み取ることができます。

ロールとアクセス許可の詳細については、「[Azure Active Directory の管理者ロールの割り当て](../roles/permissions-reference.md)」を参照してください

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Azure AD で新しいアクティビティ タイムスタンプを使用してデバイスを管理する (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** デバイスの登録と管理 **製品の機能:** デバイスのライフサイクル管理

Azure AD では、古くなったデバイスを環境から排除するために、定期的に組織のデバイスを更新したり、インベントリから削除したりする必要があります。 このプロセスを支援するために、Azure AD では、新しいアクティビティ タイムスタンプによってデバイスが更新されることで、デバイスのライフサイクルが管理しやすくなりました。

このタイムスタンプを取得および使用する方法については、「[方法:Azure AD で古いデバイスを管理する](../devices/manage-stale-devices.md)」を参照してください

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>管理者が各デバイスで利用規約の承諾をユーザーに要求可能

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** ガバナンス

管理者は **[各デバイスでユーザーによる同意が必要]** オプションを有効にすることで、テナントでユーザーが使用しているすべてのデバイスで利用規約の承諾をユーザーに要求できるようになりました。

詳細については、[Azure Active Directory Terms of Use 機能の、デバイスごとの利用規約に関するセクション](../conditional-access/terms-of-use.md#per-device-terms-of-use)を参照してください。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>管理者が定期的なスケジュールに基づいて利用規約の有効期限を設定可能

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** ガバナンス


管理者は **[期限切れの同意]** オプションを有効にすることで、すべてのユーザーを対象に、利用規約の有効期限を指定の定期スケジュールに基づいて設定できるようになりました。 年に 1 回、半年に 1 回、3 か月に 1 回、または月に 1 回のスケジュールを指定できます。 利用規約が期限切れになったら、ユーザーは再度受け入れる必要があります。

詳細については、[Azure Active Directory Terms of Use 機能の、利用規約の追加に関するセクション](../conditional-access/terms-of-use.md#add-terms-of-use)を参照してください。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>管理者が各ユーザーのスケジュールに基づいて利用規約の有効期限を設定可能

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** ガバナンス

管理者は、ユーザーが利用規約を再承諾しなければならない期間を指定できるようになりました。 たとえば、管理者は、ユーザーが 90 日おきに利用規約を再承諾しなければならないことを指定できます。

詳細については、[Azure Active Directory Terms of Use 機能の、利用規約の追加に関するセクション](../conditional-access/terms-of-use.md#add-terms-of-use)を参照してください。

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory のロールに関する新しい Azure AD Privileged Identity Management (PIM) の電子メール

**種類:** 新機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

Azure AD Privileged Identity Management (PIM) を使用しているお客様は、過去 7 日間の次の情報を含むダイジェスト電子メールを毎週受信できるようになりました:

- 最上位の有資格かつ永続的なロール割り当ての概要

- ロールをアクティブ化しているユーザー数

- PIM でロールに割り当てられたユーザー数

- PIM 外でロールに割り当てられたユーザー数

- PIM で "永続化" されたユーザー数

PIM と利用可能な電子メール通知の詳細については、「[PIM での電子メール通知](../privileged-identity-management/pim-email-notifications.md)」を参照してください。

---

### <a name="group-based-licensing-is-now-generally-available"></a>グループベースのライセンスの一般提供開始

**種類:** 変更された機能 **サービス カテゴリ:** その他 **製品の機能:** ディレクトリ

グループベースのライセンスのパブリック プレビューが終了し、一般提供が開始されました。 この一般向けリリースの一環として、この機能のスケーラビリティの向上に加えて、単一ユーザーへのグループベースのライセンス割り当てを再処理できるようになり、Office 365 E3/A3 ライセンスでグループベースのライセンスが使用できるようになりました。

グループベースのライセンスの詳細については、「[Azure Active Directory のグループベースのライセンスとは](./active-directory-licensing-whatis-azure-portal.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 11 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018 年 11 月に、フェデレーションを使用した以下の 26 の新規アプリのサポートが、アプリ ギャラリーに追加されました:

[CoreStack](https://cloud.corestack.io/site/login)、[HubSpot](../saas-apps/hubspot-tutorial.md)、[GetThere](../saas-apps/getthere-tutorial.md)、[Gra-Pe](../saas-apps/grape-tutorial.md)、[eHour](https://getehour.com/try-now)、[Consent2Go](../saas-apps/consent2go-tutorial.md)、[Appinux](../saas-apps/appinux-tutorial.md)、[DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview)、[Useall](../saas-apps/useall-tutorial.md)、[Infinite Campus](../saas-apps/infinitecampus-tutorial.md)、[Alaya](https://alayagood.com)、[HeyBuddy](../saas-apps/heybuddy-tutorial.md)、[Wrike SAML](../saas-apps/wrike-tutorial.md)、[Drift](../saas-apps/drift-tutorial.md)、[Zenegy for Business Central 365](https://accounting.zenegy.com/)、[Everbridge Member Portal](../saas-apps/everbridge-tutorial.md)、[IDEO](https://profile.ideo.com/users/sign_up)、[Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md)、[Peakon](../saas-apps/peakon-tutorial.md)、[Allbound SSO](../saas-apps/allbound-sso-tutorial.md)、[Plex Apps - Classic Test](https://test.plexonline.com/signon)、[Plex Apps – Classic](https://www.plexonline.com/signon)、[Plex Apps - UX Test](https://test.cloud.plex.com/sso)、[Plex Apps – UX](https://cloud.plex.com/sso)、[Plex Apps – IAM](https://accounts.plex.com/)、[CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

## <a name="october-2018"></a>2018 年 10 月

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD のログを Azure Log Analytics (パブリック プレビュー) と連携させることが可能になりました

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure AD のログを Azure Log Analytics に転送できるようになりました。 要望の特に多かったこの機能が追加されたことで、ビジネス、運営、セキュリティに関する分析データへのアクセスがこれまで以上に改善され、インフラストラクチャの監視機能も強化されました。 詳しくは、関連のブログ記事 (「[Azure Active Directory Activity logs in Azure Log Analytics now available (Azure Active Directory のアクティビティ ログが Azure Log Analytics で利用できるようになりました)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)」) をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 10 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018 年 10 月に、フェデレーションを使用した以下の 14 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[My Award Points](../saas-apps/myawardpoints-tutorial.md)、[Vibe HCM](../saas-apps/vibehcm-tutorial.md)、ambyint、[MyWorkDrive](../saas-apps/myworkdrive-tutorial.md)、[BorrowBox](../saas-apps/borrowbox-tutorial.md)、Dialpad、[ON24 Virtual Environment](../saas-apps/on24-tutorial.md)、[RingCentral](../saas-apps/ringcentral-tutorial.md)、[Zscaler Three](../saas-apps/zscaler-three-tutorial.md)、[Phraseanet](../saas-apps/phraseanet-tutorial.md)、[Appraisd](../saas-apps/appraisd-tutorial.md)、[Workspot Control](../saas-apps/workspotcontrol-tutorial.md)、[Shuccho Navi](../saas-apps/shucchonavi-tutorial.md)、[Glassfrog](../saas-apps/glassfrog-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services の電子メール通知

**種類:** 新機能 **サービス カテゴリ:** Azure AD Domain Services **製品の機能:** Azure AD Domain Services

Azure AD Domain Services では、マネージド ドメインの構成ミスや問題に関するアラートが、Azure portal を通じて提供されます。 これらのアラートにはステップ バイ ステップ ガイドが含まれているので、サポートへの連絡を必要とすることなく、問題の解決に着手できます。

10 月からは、マネージド ドメインの通知設定をカスタマイズできるようになるので、新しいアラートの発生時に、指定のユーザー グループへ電子メールを自動送信できるようになります。これにより、ポータルで更新プログラムを常時確認する必要がなくなります。

詳しくは、「[Azure AD Domain Services の通知設定](../../active-directory-domain-services/notifications.md)」をご覧ください。

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD ポータルで、ForceDelete ドメイン API を使用してカスタム ドメインを削除できるようになりました

**種類:** 変更された機能 **サービス カテゴリ:** ディレクトリ管理 **製品の機能:** ディレクトリ

ForceDelete ドメイン API を使用して、ユーザー、グループ、アプリなどの参照を、カスタム ドメイン名 (contoso.com) から既定の初期ドメイン名 (contoso.onmicrosoft.com) へと非同期的に名前変更することにより、カスタム ドメイン名を削除できるようになりました。

これにより、カスタム ドメイン名が使用されなくなった場合や、そのドメイン名を別の Azure AD で使用する必要が生じた場合に、ドメイン名をよりすばやく削除できるようになりました。

詳しくは、「[カスタム ドメイン名を削除する](../enterprise-users/domains-manage.md#delete-a-custom-domain-name)」をご覧ください。

---

## <a name="september-2018"></a>2018 年 9 月

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>動的グループの管理者ロールのアクセス許可が更新された

**種類:** 修正済み **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

特定の管理者ロールが、グループの所有者になる必要なく、動的メンバーシップ ルールの作成と更新を行えるように問題を修正しました。

これらのロールを次に示します。

- 全体管理者

- Intune 管理者

- ユーザー管理者

詳細については、「[動的グループの作成と状態チェックを行う](../enterprise-users/groups-create-rule.md)」を参照してください

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>一部のサード パーティ製アプリケーションのシングル サインオン (SSO) 構成設定を簡素化

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

各アプリケーション構成の固有の性質が原因で、サービスとしてのソフトウェア (SaaS) アプリケーションに向けたシングル サインオン (SSO) の設定が困難であることは、Microsoft でも認識しています。 以下のサードパーティ製 SaaS アプリケーションの SSO 構成設定を自動入力するため、簡素化した構成エクスペリエンスを構築しました。

- Zendesk

- ArcGis Online

- Jamf Pro

このワンクリック エクスペリエンスの使用を開始するには、**Azure portal** > アプリケーションの **SSO 構成** ページにアクセスします。 詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory のデータの保管場所に関するページ

**種類:** 新機能 **サービス カテゴリ:** その他 **製品の機能:** GoLocal

**Azure Active Directory のデータの保管場所** に関するページから会社のリージョンを選択すると、Azure AD のすべてのサービスについて、どの Azure データ センターに Azure AD の保存データが格納されているかが表示されます。 この情報は、会社のリージョンに関して、特定の Azure AD サービスに基づいてフィルター処理できます。

この機能へのアクセス方法と機能の詳細については、[Azure Active Directory のデータの保管場所](https://aka.ms/AADDataMap)に関するページを参照してください。

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>[マイ アプリ] アクセス パネルで新しいデプロイ計画を使用可能

**種類:** 新機能 **サービス カテゴリ:** マイ アプリ **製品の機能:** SSO

[マイ アプリ] アクセス パネルで使用できる新しいデプロイ計画をご確認ください (https://aka.ms/deploymentplans) 。
[マイ アプリ] アクセス パネルによって、ユーザーは 1 つの場所でアプリケーションの検索とアクセスを行えます。 このポータルでは、ユーザーはセルフ サービスで、アプリケーションとグループへのアクセスを要求したり、他のユーザーに代わってこれらのリソースへのアクセスを管理したりすることなどが可能です。

詳細については、「[マイ アプリ ポータルとは](../user-help/my-apps-portal-end-user-access.md)」を参照してください

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure portal の [サインイン] ログ ページに [トラブルシューティングおよびサポート] タブを新設

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure portal の **[サインイン]** ページの **[トラブルシューティングおよびサポート]** タブは、管理者やサポート エンジニアが、Azure AD のサインインに関連する問題のトラブルシューティングを行う助けとなるように設計されています。この新しいタブには、問題の解決に役立つように、エラー コード、エラー メッセージ、および修復の推奨事項 (ある場合) が表示されます。 問題を解決できない場合は、**クリップボードへのコピー** 操作によってサポート チケットを作成する新しい方法も用意されています。この方法で、サポート チケット内のログ ファイルの **要求 ID** と **日付 (UTC)** のフィールドが設定されます。

![新しいタブが表示されている サインイン ログ](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>動的メンバーシップ ルールを作成するために使用されるカスタム拡張機能プロパティのサポートの強化

**種類:** 変更された機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

この更新プログラムでは、動的ユーザー グループのルール ビルダーにある **[Get custom extension properties] (カスタム拡張機能プロパティを取得する)** リンクをクリックし、一意のアプリ ID を入力すると、ユーザーの動的メンバーシップ ルールの作成時に使用するカスタム拡張機能プロパティの完全な一覧が表示されるようになりました。 この一覧を最新の情報に更新して、そのアプリの新しいカスタム拡張機能プロパティを取得することもできます。

動的メンバーシップ ルール用のカスタム拡張機能プロパティの使用に関する詳細については、[拡張機能プロパティおよびカスタム拡張機能プロパティ](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)についてのページを参照してください

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

**種類:** 変更の計画 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティと保護

以下のアプリが、[承認されたクライアント アプリ](../conditional-access/concept-conditional-access-conditions.md#client-apps)の一覧に示されます。

- Microsoft To-Do

- Microsoft Stream

詳細については、次を参照してください。

- [Azure AD のアプリベースの条件付きアクセス](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 のロック画面からのセルフサービス パスワード リセットを新たにサポート

**種類:** 新機能 **サービス カテゴリ:** SSPR **製品の機能:** ユーザー認証

この新しい機能を設定すると、Windows 7、Windows 8、または Windows 8.1 を実行しているデバイスの **ロック** 画面から自分のパスワードをリセットするためのリンクがユーザーに表示されます。 そのリンクをクリックすると、ユーザーには、Web ブラウザーを介する場合と同じパスワードのリセット フローが示されます。

詳細については、[Windows 7、8、8.1 からパスワードのリセットを有効にする方法](../authentication/howto-sspr-windows.md)に関するページを参照してください

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>変更通知:承認コードを再利用できなくなる

**種類:** 変更の計画 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

2018 年 11 月 15 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの間にアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

このことと、その他のプロトコル関連の変更については、[認証についての新機能の完全な一覧](../develop/reference-breaking-changes.md)を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 9 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018 年 9 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Uberflip](../saas-apps/uberflip-tutorial.md)、[Comeet Recruiting Software](../saas-apps/comeetrecruitingsoftware-tutorial.md)、[Workteam](../saas-apps/workteam-tutorial.md)、[ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md)、[Nuclino](../saas-apps/nuclino-tutorial.md)、[JDA Cloud](../saas-apps/jdacloud-tutorial.md)、[Snowflake](../saas-apps/snowflake-tutorial.md)、NavigoCloud、[Figma](../saas-apps/figma-tutorial.md)、join.me、[ZephyrSSO](../saas-apps/zephyrsso-tutorial.md)、[Silverback](../saas-apps/silverback-tutorial.md)、Riverbed Xirrus EasyPass、[Rackspace SSO](../saas-apps/rackspacesso-tutorial.md)、Enlyft SSO for Azure、SurveyMonkey、[Convene](../saas-apps/convene-tutorial.md)、[dmarcian](../saas-apps/dmarcian-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="support-for-additional-claims-transformations-methods"></a>要求変換メソッドの追加サポート

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

新しい要求変換メソッドの ToLower() と ToUpper() を導入しました。これらは、SAML ベースの **[シングル サインオンの構成]** ページから SAML トークンに適用できます。

詳細については、[Azure AD のエンタープライズ アプリケーションの SAML トークンで発行された要求をカスタマイズする方法](../develop/active-directory-saml-claims-customization.md)についてのページを参照してください

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>SAML ベースのアプリの構成 UI を更新 (プレビュー)

**種類:** 変更された機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

SAML ベースのアプリの構成 UI が更新された一部として、以下が提供されます。

- SAML ベースのアプリを構成するためのチュートリアル エクスペリエンスの更新。

- 構成において不足している、または正しくないものに関する可視性の向上。

- 証明書の有効期限通知のために複数のメール アドレスを追加する機能。

- 新しい要求変換メソッドの ToLower() と ToUpper() など。

- エンタープライズ アプリのために独自のトークン署名証明書をアップロードする方法。

- SAML アプリの NameID 形式を設定する方法と、ディレクトリ拡張機能として NameID 値を設定する方法。

この更新されたビューをオンにするには、 **[シングル サインオン]** ページ上部の **[Try out our new experience] (新しいエクスペリエンスを試す)** リンクをクリックします。 詳細については、[Azure Active Directory による SAML ベースのシングル サインオンをアプリケーションに構成する](../manage-apps/view-applications-portal.md)」を参照してください。

---

## <a name="august-2018"></a>2018 年 8 月

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Azure Active Directory の IP アドレス範囲の変更

**種類:** 変更の計画 **サービス カテゴリ:** その他 **製品の機能:** プラットフォーム

Microsoft では、Azure AD に対してより大規模な IP 範囲の導入を進めています。つまり、ファイアウォール、ルーター、またはネットワーク セキュリティ グループの Azure AD IP アドレス範囲を構成している場合はそれらを更新する必要があります。 Microsoft では、Azure AD で新しいエンドポイントが追加されたときにファイアウォール、ルーター、またはネットワーク セキュリティ グループの IP アドレス範囲の構成を再び変更する必要がないように、この更新を行っています。

ネットワーク トラフィックは、今後 2 か月にわたってこれらの新しい範囲に移行されます。 中断のないサービスを継続するには、2018 年 9 月 10 日までに、次に示す更新された値を IP アドレスに追加する必要があります。

- 20.190.128.0/18

- 40.126.0.0/18

すべてのネットワーク トラフィックが新しい範囲に移行されるまでは古い IP アドレス範囲を削除しないことを強くお勧めします。 移行に関する最新情報や古い範囲を削除できるタイミングについては、「[Office 365 URLs and IP address ranges (Office 365 の URL と IP アドレス範囲)](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」を参照してください。

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>変更通知:承認コードを再利用できなくなる

**種類:** 変更の計画 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

2018 年 11 月 15 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの間にアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

このことと、その他のプロトコル関連の変更については、[認証についての新機能の完全な一覧](../develop/reference-breaking-changes.md)を参照してください。

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>セルフサービスのパスワード (SSPR) と Multi-Factor Authentication (MFA) の集中型セキュリティ情報管理

**種類:** 新機能 **サービス カテゴリ:** SSPR **製品の機能:** ユーザー認証

この新しい機能を使用すると、ユーザーは SSPR および MFA に対するセキュリティ情報 (電話番号、モバイル アプリなど) を 1 つの場所とエクスペリエンス管理できます。以前は、2 つの異なる場所で行われていました。

この集中型エクスペリエンスは、SSPR または MFA のどちらかを使用しているユーザーでも機能します。 さらに、MFA または SSPR の登録を強制していない組織の場合でも、ユーザーは、組織で許可されている MFA または SSPR のセキュリティ情報の方法に、マイ アプリ ポータルから登録できます。

これは、オプトイン パブリック プレビューです。 管理者は、新しいエクスペリエンスを (必要に応じて) テナント内の特定のグループまたはすべてのユーザーに有効にできます。 集中型エクスペリエンスについて詳しくは、[集中型エクスペリエンスのブログ](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)をご覧ください。

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD アプリケーション プロキシ アプリでの新しい HTTP 専用 Cookie の設定

**種類:** 新機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

アプリケーション プロキシ アプリには、**HTTP 専用 Cookie** と呼ばれる新しい設定があります。 この設定では、アプリケーション プロキシ アクセス Cookie とセッション Cookie の両方に対する HTTP 応答ヘッダーに HTTPOnly フラグを追加して、クライアント側スクリプトから Cookie へのアクセスを停止し、さらに Cookie のコピーや変更などのアクションを防止することにより、セキュリティを強化します。 このフラグは以前は使用されていませんでしたが、Cookie は常に暗号化され、TLS 接続を使用して送信されることで、不適切な変更から保護されていました。

この設定は、リモート デスクトップなどの ActiveX コントロールを使用するアプリとの互換性はありません。 このような状況の場合は、この設定をオフにすることをお勧めします。

HTTP 専用 Cookie の設定について詳しくは、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](../manage-apps/application-proxy-add-on-premises-application.md)」をご覧ください。

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure リソースに対する Privileged Identity Management (PIM) での管理グループのリソースの種類のサポート

**種類:** 新機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

サブスクリプション、リソース グループ、リソース (VM、App Services など) に対して既に行われているのと同じように、管理グループのリソースの種類に対しても Just-In-Time アクティブ化と割り当ての設定を適用できるようになります。 さらに、管理グループに対して管理者アクセスできるロールを持つすべてのユーザーは、PIM でそのリソースを検出して管理できます。

PIM と Azure リソースについて詳しくは、「[Privileged Identity Management を使用した Azure リソースの探索と管理](../privileged-identity-management/pim-resource-roles-discover-resources.md)」をご覧ください。

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>アプリケーション アクセス (プレビュー) では、Azure AD ポータルへのより高速なアクセスが提供される

**種類:** 新機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

現在、PIM を使用してロールをアクティブ化すると、アクセス許可が有効になるまでに 10 分以上かかることがあります。 アプリケーション アクセス (現在パブリック プレビュー段階) を使用した場合、管理者はアクティブ化要求が完了するとすぐに Azure AD ポータルにアクセスすることができます。

現時点では、アプリケーション アクセスでサポートされているのは、Azure AD ポータル エクスペリエンスと Azure リソースのみです。 PIM とアプリケーション アクセスについて詳しくは、「[Azure AD Privileged Identity Management とは](../privileged-identity-management/pim-configure.md)」をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 8 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018年 8 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Hornbill](../saas-apps/hornbill-tutorial.md)、[Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md)、[Sauce Labs - Mobile and Web Testing](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md)、[Meta Networks Connector](../saas-apps/metanetworksconnector-tutorial.md)、[Way We Do](../saas-apps/waywedo-tutorial.md)、[Spotinst](../saas-apps/spotinst-tutorial.md)、[ProMaster (by Inlogik)](../saas-apps/promaster-tutorial.md)、SchoolBooking、[4me](../saas-apps/4me-tutorial.md)、[Dossier](../saas-apps/dossier-tutorial.md)、[N2F - Expense reports](../saas-apps/n2f-expensereports-tutorial.md)、[Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md)、[SafeConnect](../saas-apps/safeconnect-tutorial.md)、[ZenQMS](../saas-apps/zenqms-tutorial.md)、[eLuminate](../saas-apps/eluminate-tutorial.md)、[Dovetale](../saas-apps/dovetale-tutorial.md)。

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Tableau のネイティブ サポートが Azure AD アプリケーション プロキシで使用できるようになった

**種類:** 変更された機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

事前認証プロトコルについての OpenID Connect から OAuth 2.0 Code Grant プロトコルへの更新により、アプリケーション プロキシで Tableau を使用するために追加構成を行う必要がなくなりました。 このプロトコルの変更により、JavaScript と HTML のタグで一般的にサポートされる HTTP のみのリダイレクトを使用することで、アプリケーション プロキシによる最新のアプリのサポートも向上しました。

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Azure Active Directory で B2B ゲスト ユーザーに対する ID プロバイダーとして Google を追加する新しいサポート (プレビュー)

**種類:** 新機能 **サービス カテゴリ:** B2B **製品の機能:** B2B/B2C

組織において Google とのフェデレーションを設定することにより、招待された Gmail ユーザーは既存の Google アカウントを使用して共有アプリおよびリソースにサインインできます。個人の Microsoft アカウント (MSA) または Azure AD アカウントを作成する必要はありません。

これは、オプトイン パブリック プレビューです。 Google のフェデレーションについて詳しくは、「[Add Google as an identity provider for B2B guest users](../external-identities/google-federation.md)」(B2B ゲスト ユーザーの ID プロバイダーとして Google を追加する) をご覧ください。

---

## <a name="july-2018"></a>2018 年 7 月

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Azure Active Directory の電子メール通知の機能強化

**種類:** 変更された機能 **サービス カテゴリ:** その他 **製品の機能:** ID ライフサイクル管理

Azure Active Directory (Azure AD) の設計が更新され、以下のサービスから送信された場合、送信者メール アドレスと送信者表示名が変更されます。

- Azure AD アクセス レビュー
- Azure AD Connect Health
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- エンタープライズ アプリの証明書期限切れ通知
- エンタープライズ アプリのサービス プロビジョニング通知

メール通知は、次のメール アドレスと表示名から送信されます。

- メール アドレス: azure-noreply@microsoft.com
- 表示名:Microsoft Azure

新しい電子メール デザインのいくつかの例と詳細は、「[Azure AD PIM の電子メール通知](../privileged-identity-management/pim-email-notifications.md)」で参照してください。

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD アクティビティ ログは Azure Monitor から使用できるようになりました。

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure AD アクティビティ ログは、Azure Monitor (Azure のプラットフォーム全体の監視サービス) のパブリック プレビューで利用可能になりました。 Azure Monitor は、これらの機能強化に加え、長期保有とシームレスな統合を提供します。

- ログ ファイルを Azure ストレージ アカウントにルーティングすることによる長期保有。

- シームレスな SIEM 統合により、カスタム スクリプトを作成または管理する必要がありません。

- お客様独自のカスタム ソリューション、分析ツール、またはインシデント管理ソリューションとシームレスに統合。

これらの新機能の詳細については、マイクロソフト公式ブログ「[Azure AD activity logs in Azure Monitor diagnostics is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/)」(Azure Monitor 診断の Azure AD アクティビティ ログは現在パブリック プレビュー中です) や [Azure Monitor の Azure Active Directory アクティビティ ログ (プレビュー)](../reports-monitoring/concept-activity-logs-azure-monitor.md) に関するマイクロソフトのドキュメントを参照してください。

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Azure AD サインイン レポートに条件付きアクセス情報を追加

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** ID のセキュリティ & 保護

この更新により、ユーザーがサインインしたときにどのポリシーが評価されるか、また、そのポリシーの結果を確認できるようになりました。 さらに、ユーザーによって使用されるクライアント アプリの種類がレポートに含まれるようになったため、レガシ プロトコルのトラフィックを識別できます。 ユーザーに表示されるエラー メッセージに記載されている相関 ID を条件にレポート エントリ内を検索できるようにもなったため、対応するサインイン要求を特定してトラブルシューティングに利用できます。

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>サインイン アクティビティ ログでレガシ認証を表示

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

サインイン アクティビティ ログに **[クライアント アプリ]** フィールドを導入することで、お客様がレガシ認証を使用しているユーザーを確認できるようになりました。 この情報にアクセスするには、Sign-ins Microsoft Graph API を使用するか、Azure AD ポータルのサインイン アクティビティ ログで **[クライアント アプリ]** コントロールを使用してレガシー認証をフィルター処理します。 詳細については、ドキュメントをご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 7 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018年 7 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Innovation Hub](../saas-apps/innovationhub-tutorial.md)、[Leapsome](../saas-apps/leapsome-tutorial.md)、[Certain Admin SSO](../saas-apps/certainadminsso-tutorial.md)、PSUC Staging、[iPass SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md)、[Screencast-O-Matic](../saas-apps/screencast-tutorial.md)、PowerSchool Unified Classroom、[Eli Onboarding](../saas-apps/elionboarding-tutorial.md)、[Bomgar Remote Support](../saas-apps/bomgarremotesupport-tutorial.md)、[Nimblex](../saas-apps/nimblex-tutorial.md)、[Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md)、[Insight4GRC](../saas-apps/insight4grc-tutorial.md)、[SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md)、[Kanbanize](../saas-apps/kanbanize-tutorial.md)、[SmartLPA](../saas-apps/smartlpa-tutorial.md)、[Skills Base](../saas-apps/skillsbase-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>新しいユーザー プロビジョニング SaaS アプリの統合 - 2018 年 7 月

**種類:** 新機能 **サービス カテゴリ:** アプリのプロビジョニング **製品の機能:** サード パーティ統合

Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などの SaaS アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。 2018 年 7 月、Azure AD アプリ ギャラリーの次のアプリケーションに対して、ユーザー プロビジョニング サポートが追加されました。

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

ユーザー プロビジョニングをサポートする Azure AD ギャラリーのすべてのアプリケーションの一覧については、[Azure Active Directory と SaaS アプリケーションの統合](../saas-apps/tutorial-list.md)に関する記事を参照してください。

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync - 孤立および重複した属性の同期エラーを修正する簡単な方法

**種類:** 新機能 **サービス カテゴリ:** AD Connect **製品の機能:** 監視とレポート

Azure AD Connect Health は、同期エラーを特定し修正するためのセルフサービス修復機能を提供します。 この機能は、重複した属性同期エラーのトラブルシューティングを行い、Azure AD から孤立したオブジェクトを修正します。 この診断には、次のような利点があります。

- 重複した属性の同期エラーを絞り込み、特定の修正プログラムを提供します。

- 1 つの手順でエラーを解決する、Azure AD のシナリオ専用の修正プログラムを適用します。

- この機能を有効にするために、アップグレードや構成の必要はありません。

詳細については、「[重複する属性の同期エラーの診断](../hybrid/how-to-connect-health-diagnose-sync-errors.md)」を参照してください。

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Azure AD のビジュアルと MSA のサインイン エクスペリエンスを更新

**種類:** 変更された機能 **サービス カテゴリ:** Azure AD **製品の機能:** ユーザー認証

Office 365 や Azure などの Microsoft のオンライン サービス用のサインイン エクスペリエンスを更新しました。 この変更により、画面がすっきりし見やすくなりました。 この変更の詳細については、ブログ 「[Azure AD のサインイン画面に関するアップデートのお知らせ](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)」を参照してください。

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect の新しいリリース - 2018 年 7 月

**種類:** 変更された機能 **サービス カテゴリ:** アプリのプロビジョニング **製品の機能:** ID ライフサイクル管理

Azure AD Connect の最新リリースは次のとおりです。

- バグの修正とサポートの更新

- Ping フェデレーション統合の一般公開

- 最新の SQL 2012 クライアントに更新

この更新の詳細については、「[Azure AD Connect:バージョンのリリース履歴](../hybrid/reference-connect-version-history.md)」を参照してください

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>使用条件エンドユーザー UI の更新

**種類:** 変更された機能 **サービス カテゴリ:** 使用条件 **製品の機能:** ガバナンス

TOU エンドユーザー UI の同意の文字列を更新しています。

**現在のテキスト** [tenantName] リソースにアクセスするには、使用条件に同意する必要があります。<br>**新しいテキスト** [tenantName] リソースにアクセスするには、使用条件を読む必要があります。

**現在のテキスト:** 合意を選択すると上記の使用条件すべてに同意することを意味します。<br>**新しいテキスト:** 使用条件を読んで理解したことを確定するには、[同意する] をクリックしてください。

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>パススルー認証で、レガシのプロトコルとアプリケーションをサポート

**種類:** 変更された機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

パススルー認証で、レガシのプロトコルとアプリケーションをサポートできるようになりました。 次の制限事項は完全にサポートされています。

- レガシの Office クライアント アプリケーション、Office 2010、および Office 2013 へのユーザー サインイン (先進認証なし)。

- Office 2010 上でのみ、Exchange ハイブリッド環境で予定表の共有と空き時間情報にアクセスします。

- Skype for Business クライアント アプリケーションへのユーザー サインイン (先進認証なし)。

- PowerShell バージョン 1.0 へのユーザー サインイン。

- iOS 設定アシスタントを使用している Apple Device Enrollment Program (Apple DEP)。

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>セルフサービス パスワード リセットと多要素認証の集中型セキュリティ情報管理

**種類:** 新機能 **サービス カテゴリ:** SSPR **製品の機能:** ユーザー認証

この新しい機能を使用すれば、ユーザーはセキュリティ情報 (たとえば、電話番号、電子メール アドレス、モバイル アプリなど) のセルフサービス パスワード リセット (SSPR) と多要素認証 (MFA) を 1 つのエクスペリエンスで管理できます。 ユーザーが SSPR と MFA の 2 つの異なるエクスペリエンスのために同じセキュリティ情報を登録する必要がなくなります。 この新しいエクスペリエンスは、SSPR または MFA のいずれかを持つユーザーにも適用されます。

組織で MFA または SSPR 登録を使用していない場合は、ユーザーは **Myapps** ポータルを通してセキュリティ情報を登録できます。 そこから、ユーザーは MFA または SSPR に有効な任意の方法を登録できます。

これは、オプトイン パブリック プレビューです。 管理者は、新しいエクスペリエンス (必要に応じて) を特定のユーザー グループまたはテナント内のすべてのユーザーに有効にできます。

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Microsoft Authenticator アプリを使用して、パスワードをリセットしたときに本人確認する

**種類:** 変更された機能 **サービス カテゴリ:** SSPR **製品の機能:** ユーザー認証

この機能を使用すると、パスワードをリセットする際に、Microsoft Authenticator (または他の認証アプリ) からの通知またはコードを使用して、管理者以外のユーザーが本人確認できます。 管理者が、このセルフサービス パスワード リセット方法をオンにすると、aka.ms/mfasetup または aka.ms/setupsecurityinfo を通してモバイル アプリを登録したユーザーは、パスワードをリセットする際にモバイル アプリを本人確認の方法として使用できます。

モバイル アプリの通知は、パスワードのリセットに 2 つの認証方法を要求するポリシーの一部としてのみオンに設定できます。

---

## <a name="june-2018"></a>2018 年 6 月

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>変更通知:Azure AD アクティビティ ログ API を使用するアプリの委任された承認ワークフローに対するセキュリティの修正

**種類:** 変更の計画 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

セキュリティ強化のため、委任された承認ワークフローを使って [Azure AD アクティビティ ログ API](../reports-monitoring/concept-reporting-api.md) にアクセスするアプリのアクセス許可を変更する必要がありました。 この変更は、**2018 年 6 月 26日** までに行われます。

お使いのアプリに Azure AD アクティビティ ログ API を使っているものがある場合は、以下の手順に従って、変更後にアプリで問題が発生しないことを確認してください。

**アプリのアクセス許可を更新するには**

1. Azure Portal にサインインし、 **[Azure Active Directory]** を選択してから **[アプリの登録]** を選択します。
2. Azure AD アクティビティ ログ API を使用しているアプリを選択して **[設定]** を選択し、 **[必要なアクセス許可]** を選んでから、 **[Windows Azure Active Directory]** API を選択します。
3. **[アクセスの有効化]** ブレードの **[委任されたアクセス許可]** 領域で、 **[ディレクトリ データの読み取り]** チェック ボックスをオンにして、 **[保存]** を選択します。
4. **[アクセス許可の付与]** を選択し、 **[はい]** を選択します。

    >[!Note]
    >アプリへのアクセス許可の付与は、全体管理者が行う必要があります。

詳細については、「Azure AD レポート API にアクセスするための前提条件」記事の「[アクセス許可を付与する](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions)」セクションを参照してください。

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>PCI DSS コンプライアンスのために TLS 設定を構成して Azure AD サービスに接続する

**種類:** 新機能 **サービス カテゴリ:** 該当なし **製品の機能:** プラットフォーム

トランスポート層セキュリティ (TLS) は、通信する 2 つのアプリケーション間にプライバシーとデータ整合性を提供するプロトコルであり、今日、最も広く展開され、使用されているセキュリティ プロトコルです。

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) は、**2018 年 6 月 30 日** に開始されたコンプライアンスに沿って、新しいより安全なアプリ プロトコルを有効にして、初期バージョンの TLS および Secure Sockets Layer (SSL) を無効化する必要があることを決定しました。 この変更は、Azure AD サービスに接続し、PCI DSS コンプライアンスを必要とする場合には、TLS 1.0 を無効にしなければならないことを意味します。 複数のバージョンの TLS が使用可能ですが、TLS 1.2 は Azure Active Directory Services に利用できる最新バージョンです。 クライアント/サーバーおよびブラウザー/サーバーの両方の組み合わせにおいて、TLS 1.2 に直接移行することを強くお勧めします。

古いブラウザーでは、TLS 1.2 などの新しい TLS バージョンがサポートされない可能性があります。 お使いのブラウザーでどの TLS バージョンがサポートされるかを確認するには、[Qualys SSL Labs](https://www.ssllabs.com/) サイトへ移動して、 **[Test your browser]** をクリックします。 お使いの Web ブラウザーを最新バージョンにアップグレードして、可能であれば TLS 1.2 のみを有効にすることをお勧めします。

**TLS 1.2 を有効にするために、ブラウザーで実行する手順**

- **Microsoft Edge および Internet Explorer (いずれも、Internet Explorer を使用して設定される)**

    1. Internet Explorer を開き、 **[ツール]**  >  **[インターネット オプション]**  >  **[詳細]** の順に選択します。
    2. **[セキュリティ]** 領域で、 **[use TLS 1.2]\(TLS 1.2 を使用する\)** を選択して、 **[OK]** をクリックします。
    3. すべてのブラウザー ウィンドウを閉じて、Internet Explorer を再起動します。

- **Google Chrome**

    1. Google Chrome を開き、「*chrome://settings/* 」とアドレス バーに入力して、**Enter** キーを押します。
    2. **[詳細]** オプションを展開し、 **[システム]** 領域へ移動して、 **[プロキシ セットアップを開く]** を選択します。
    3. **[インターネットのプロパティ]** ボックスで、 **[詳細]** タブを選択し、 **[セキュリティ]** 領域へ移動して、 **[use TLS 1.2]\(TLS 1.2 を使用する\)** を選択してから、 **[OK]** をクリックします。
    4. すべてのブラウザー ウィンドウを閉じて、Google Chrome を再起動します。

- **Mozilla Firefox**

    1. Firefox を開き、アドレス バーに「*about:config*」と入力してから、**Enter** キーを押します。
    2. *TLS* という語句を検索して、**security.tls.version.max** エントリを選択します。
    3. 値を **3** に設定して、ブラウザーでバージョン TLS 1.2 までを使用するように設定し、 **[OK]** をクリックします。

        >[!NOTE]
        >Firefox バージョン 60.0 では TLS 1.3 をサポートしているため、security.tls.version.max の値を **4** に設定することもできます。

    4. すべてのブラウザー ウィンドウを閉じて、Mozilla Firefox を再起動します。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 6 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018年 6 月に、フェデレーションを使用した以下の 15 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Skytap](../saas-apps/skytap-tutorial.md)、[Settling music](../saas-apps/settlingmusic-tutorial.md)、[SAML 1.1 Token enabled LOB App](../saas-apps/saml-tutorial.md)、[Supermood](../saas-apps/supermood-tutorial.md)、[Autotask](../saas-apps/autotaskendpointbackup-tutorial.md)、[Endpoint Backup](../saas-apps/autotaskendpointbackup-tutorial.md)、[Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md)、Smartway2、[TonicDM](../saas-apps/tonicdm-tutorial.md)、[Moconavi](../saas-apps/moconavi-tutorial.md)、[Zoho One](../saas-apps/zohoone-tutorial.md)、[SharePoint on-premises](../saas-apps/sharepoint-on-premises-tutorial.md)、[ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md)、[Vidyard](../saas-apps/vidyard-tutorial.md)、[ChronicX](../saas-apps/chronicx-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>パブリック プレビューで使用可能な Azure AD Password Protection

**種類:** 新機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ユーザー認証

Azure AD Password Protection を使用して、簡単に推測できるパスワードをお使いの環境から排除できます。 これらのパスワードを排除すると、パスワード スプレー タイプの攻撃に侵害されるリスクが低下します。

具体的には、Azure AD Password Protection は次のような機能を提供します。

- Azure AD および Windows Server Active Directory (AD) の両方で組織のアカウントを保護する。
- 最も一般的に使用されている 500 を超えるパスワード一覧のパスワードと、それらのパスワードの 100 万を超える代替文字のバリエーションを、ユーザーが使用しないようにする。
- Azure AD およびオンプレミスの Windows Server AD の両方に対して、Azure AD ポータルの単一の場所から Azure AD Password Protection を管理する。

Azure AD Password Protection の詳細については、「[組織内の不適切なパスワードを排除する](../authentication/concept-password-ban-bad.md)」を参照してください。

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>使用条件の作成中に作成された、新しい "すべてのゲスト" 条件付きアクセス ポリシー テンプレート

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** ガバナンス

使用条件の作成中に、"すべてのゲスト" と "すべてのアプリ" のための新しい条件付きアクセス ポリシー テンプレートも作成されます。 この新しいポリシー テンプレートでは、新しく作成された ToU を適用し、ゲストの作成および強制プロセスを合理化します。

詳しくは、「[Azure Active Directory Terms of Use 機能](../conditional-access/terms-of-use.md)」を参照してください。

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>使用条件の作成中に作成された、新しい "カスタム" 条件付きアクセス ポリシー テンプレート

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** ガバナンス

使用条件の作成中に、新しい "カスタム" 条件付きアクセス ポリシー テンプレートも作成されます。 この新しいポリシー テンプレートでは、ToU を作成してから、すぐに条件付きアクセス ポリシーの作成ブレードに移動できます。ポータル経由で、手動で移動する必要はありません。

詳しくは、「[Azure Active Directory Terms of Use 機能](../conditional-access/terms-of-use.md)」を参照してください。

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication のデプロイに関する新しい包括的なガイダンス

**種類:** 新機能 **サービス カテゴリ:** その他 **製品の機能:** ID のセキュリティ & 保護

組織に Azure AD Multi-Factor Authentication (MFA) をデプロイする方法に関する新しいステップ バイ ステップ ガイドを公開しました。

MFA のデプロイ ガイドを表示するには、GitHub の [Identity Deployment Guides](./active-directory-deployment-plans.md) (ID デプロイ ガイド) リポジトリに移動します。 デプロイ ガイドに関するフィードバックを提出するには、[Deployment Plan Feedback (デプロイ プランに関するフィードバック) フォーム](https://aka.ms/deploymentplanfeedback)を使用してください。 デプロイ ガイドに関する質問がある場合は、[IDGitDeploy](mailto:idgitdeploy@microsoft.com) までお問い合わせください。

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>パブリック プレビューにある Azure AD の委任されたアプリ管理ロール

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** アクセス制御

管理者は、グローバル管理者ロールを割り当てずに、アプリ管理タスクを委任できるようになりました。 新しいロールと機能は次のとおりです。

- **新しい標準 Azure AD 管理者ロール:**

    - **アプリケーション管理者。** 登録、SSO 設定、アプリの割り当てとライセンス付与、アプリのプロキシ設定、および承認 (Azure AD リソースを除く) を含め、すべてのアプリの全側面を管理する権限が付与されます。

    - **クラウド アプリケーション管理者。** アプリ プロキシではオンプレミス アクセスを提供していないため、アプリ プロキシを除くすべてのアプリケーション管理者の権限が付与されます。

    - **アプリケーション開発者。** **[allow users to register apps]\(ユーザーによるアプリ登録を許可する\)** オプションが無効になっている場合でも、アプリ登録を作成する権限を付与します。

- **所有権 (アプリ登録ごと、および企業アプリごとに設定し、グループ所有権のプロセスとほぼ同じ):**

    - **アプリ登録の所有者。** アプリ マニフェストや別の所有者の追加など、所有するアプリ登録の全側面を管理する権限が付与されます。

    - **エンタープライズ アプリ所有者。** SSO 設定、アプリの割り当て、および承認 (Azure AD リソースを除く) を含め、所有するエンタープライズ アプリの多くの側面を管理する権限が付与されます。

パブリック プレビューの詳細については、ブログ「[Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/)」(Azure AD の委任されたアプリケーション管理ロールをパブリック プレビューで確認しよう!) を参照してください。 ロールとアクセス許可の詳細については、「[Azure Active Directory の管理者ロールの割り当て](../roles/permissions-reference.md)」を参照してください。

---

## <a name="may-2018"></a>2018 年 5 月

### <a name="expressroute-support-changes"></a>ExpressRoute のサポートの変更

**種類:** 変更の計画 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** プラットフォーム

Azure Active Directory (Azure AD) などのサービスとしてのソフトウェア オファーは、インターネット経由で直接アクセスすることで最適に動作するように設計されており、ExpressRoute または他のプライベート VPN トンネルは必要ありません。 このため、**2018 年 8 月 1日** をもって、Azure パブリック ピアリングおよび Microsoft ピアリングの Azure コミュニティを使用する Azure AD サービスに対する ExpressRoute のサポートは停止します。 この変更によって影響を受けるサービスでは、Azure AD トラフィックが ExpressRoute からインターネットに段階的に移行します。

サポートは変更されますが、認証トラフィック用に専用回線セットを使用する必要がある状況がまだ存在することも認識しています。 このため、Azure AD は、"他の Office 365 Online サービス" コミュニティとの Microsoft ピアリングでまだ ExpressRoute とサービスを使用しているテナント IP 範囲ごとの制限を引き続きサポートします。 お使いのサービスが影響を受け、ExpressRoute が必要な場合は、次の操作を行う必要があります。

- **Azure パブリック ピアリングの場合。** Microsoft ピアリングに移行し、**その他の Office 365 Online サービス (12076:5100)** コミュニティにサインアップします。 Azure パブリック ピアリングから Microsoft ピアリングに移行する方法について詳しくは、「[パブリック ピアリングを Microsoft ピアリングに移行する](../../expressroute/how-to-move-peering.md)」をご覧ください。

- **Microsoft ピアリングの場合。** **その他の Office 365 Online サービス (12076:5100)** コミュニティにサインアップします。 ルーティングの要件について詳しくは、「ExpressRoute のルーティングの要件」の「[BGP コミュニティのサポート](../../expressroute/expressroute-routing.md#bgp)」をご覧ください。

専用回線の使用を続ける必要がある場合は、Microsoft アカウント チームに連絡し、**その他の Office 365 Online サービス (12076:5100)** コミュニティを使用する承認を取得する方法を確認する必要があります。 MS Office 管理レビュー委員会は、回線が必要かどうかを検証し、回線を保持することによる技術的な影響をお客様が理解していることを確認します。 承認されていないサブスクリプションが Office 365 のルート フィルターを作成しようとすると、エラー メッセージが表示されます。

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>TOU の管理シナリオ用の Microsoft Graph API

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** 開発者エクスペリエンス

Azure AD Terms of Use の管理操作のための Microsoft Graph API が追加されました。 使用条件オブジェクトを作成、更新、削除できます。

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD のマルチ テナント エンドポイントを Azure AD B2C の ID プロバイダーとして追加

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

カスタム ポリシーを使用して、Azure AD の共通エンドポイントを ID プロバイダーとして Azure AD B2C に追加できるようになりました。 これにより、アプリケーションにサインインするすべての Azure AD ユーザー用のエントリ ポイントを 1 つにまとめることができます。 詳細については、[Azure Active Directory B2C で カスタム ポリシーを使用して、ユーザーがマルチテナント Azure AD ID プロバイダーにサインインできるようにする](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md)に関するページを参照してください。

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>My Apps Sign-in Extension と Azure AD アプリケーション プロキシを使い、どこからでも内部 URL を使用してアプリにアクセス可能

**種類:** 新機能 **サービス カテゴリ:** マイ アプリ **製品の機能:** SSO

ユーザーが企業ネットワークの外部にいる場合でも、Azure AD 向けの My Apps Sign-in Extension を使用して、内部 URL を使ってアプリケーションにアクセスできるようになりました。 これは、Azure AD アプリケーション プロキシを使用して発行されたすべてのアプリケーションで動作し、アクセス パネル ブラウザー拡張機能もインストールされているブラウザーで動作します。 URL リダイレクト機能は、ユーザーが拡張機能にログインすると自動的に有効になります。 この拡張機能は、[Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)、[Chrome](https://go.microsoft.com/fwlink/?linkid=866367)、[Firefox](https://go.microsoft.com/fwlink/?linkid=866366) でダウンロードして使用できます。

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - ヨーロッパ内のデータ (ヨーロッパのお客様向け)

**種類:** 新機能 **サービス カテゴリ:** その他 **製品の機能:** GoLocal

ヨーロッパのお客様は、プライバシー法とヨーロッパの法律を満たすために、各自のデータをヨーロッパ内にとどめる必要があり、ヨーロッパのデータセンターの外部にデータをレプリケートすることはできません。 こちらの[記事](./active-directory-data-storage-eu.md)に、ヨーロッパ内に保存される ID 情報の具体的詳細と、ヨーロッパのデータセンターの外部で保存される情報の詳細が説明されています。

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>新しいユーザー プロビジョニング SaaS アプリの統合 - 2018 年 5 月

**種類:** 新機能 **サービス カテゴリ:** アプリのプロビジョニング **製品の機能:** サード パーティ統合

Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などの SaaS アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。 2018 年 5 月、Azure AD アプリ ギャラリーの次のアプリケーションに対して、ユーザー プロビジョニング サポートが追加されました。

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Azure AD ギャラリーにある、ユーザー プロビジョニングをサポートするすべてのアプリケーションの一覧については、[https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) を参照してください。

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>グループとアプリの Azure AD アクセス レビューで定期レビューが可能

**種類:** 新機能 **サービス カテゴリ:** アクセス レビュー **製品の機能:** ガバナンス

グループとアプリのアクセス レビューが Azure AD Premium P2 の一部として一般公開されました。  管理者は、グループ メンバーシップとアプリケーション割り当てのアクセス レビューを、毎月または四半期などの定期的な間隔で自動的に繰り返すように構成できるようになります。

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD アクティビティ ログ (サインインと監査) を MS Graph から利用可能

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure AD アクティビティ ログ (サインイン ログと監査ログを含みます) を Microsoft Graph API から利用できるようになりました。 これらのログにアクセスするための Microsoft Graph API 経由の 2 つのエンドポイントが公開されています。 Azure AD Reporting API を開始するためのプログラムによるアクセスについては、こちらの[ドキュメント](../reports-monitoring/concept-reporting-api.md)を参照してください。

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>B2B のredemption エクスペリエンスと組織からの離脱

**種類:** 新機能 **サービス カテゴリ:** B2B **製品の機能:** B2B/B2C

**ジャスト イン タイム利用:** B2B API を使用してリソースをゲスト ユーザーと共有したら、特別な招待メールを送信する必要はありません。 ほとんどの場合、ゲスト ユーザーはリソースにアクセスでき、ジャスト イン タイムで利用エクスペリエンスを体験できます。 電子メールの見逃しによる影響は発生しなくなります。 ゲスト ユーザーに「システムが送信した利用リンクをクリックしましたか」と質問する必要はありません。 つまり、SPO が招待マネージャーを使用すると、利用状態に関係なく、すべてのユーザー (内部および外部) のクラウドの添付ファイルで同一の正規 URL を使用できます。

**最新の利用エクスペリエンス:** 画面分割された利用ランディング ページはなくなりました。 サードパーティのアプリと同じように、招待側組織のプライバシーに関する声明を含む最新の同意エクスペリエンスがユーザーに提供されます。

**ゲスト ユーザーは組織を離れることが可能:** ユーザーは、組織との関係が終了したら、その組織からの離脱を自分で実行できます。 招待側組織の管理者に連絡して "削除" を依頼する必要も、サポート チケットを提出する必要もありません。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 5 月

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018年 5 月に、フェデレーションを使用した以下の 18 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[AwardSpring](../saas-apps/awardspring-tutorial.md)、Infogix Data3Sixty Govern、[Yodeck](../saas-apps/infogix-tutorial.md)、[Jamf Pro](../saas-apps/jamfprosamlconnector-tutorial.md)、[KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md)、[Envi MMIS](../saas-apps/envimmis-tutorial.md)、[LaunchDarkly](../saas-apps/launchdarkly-tutorial.md)、[Adobe Captivate Prime](../saas-apps/adobecaptivateprime-tutorial.md)、[Montage Online](../saas-apps/montageonline-tutorial.md)、[まなびポケット](../saas-apps/manabipocket-tutorial.md)、OpenReel、[Arc Publishing - SSO](../saas-apps/arc-tutorial.md)、[PlanGrid](../saas-apps/plangrid-tutorial.md)、[iWellnessNow](../saas-apps/iwellnessnow-tutorial.md)、[Proxyclick](../saas-apps/proxyclick-tutorial.md)、[Riskware](../saas-apps/riskware-tutorial.md)、[Flock](../saas-apps/flock-tutorial.md)、[Reviewsnap](../saas-apps/reviewsnap-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Azure Active Directory のデプロイ手順に関する新しいガイダンス

**種類:** 新機能 **サービス カテゴリ:** その他 **製品の機能:** ディレクトリ

Azure Active Directory (Azure AD) のデプロイ方法に関する新しいステップ バイ ステップ ガイド。セルフサービス パスワード リセット (SSPR)、シングル サインオン (SSO)、条件付きアクセス、アプリ プロキシ、ユーザー プロビジョニング、パススルー認証 (PTA) に対する Active Directory フェデレーション サービス (AD FS)、およびパスワード ハッシュの同期 (PHS) に対する ADFS が含まれます。

デプロイ ガイドを表示するには、GitHub の [Identity Deployment Guides](./active-directory-deployment-plans.md) (ID デプロイ ガイド) リポジトリに移動します。 デプロイ ガイドに関するフィードバックを提出するには、[Deployment Plan Feedback (デプロイ プランに関するフィードバック) フォーム](https://aka.ms/deploymentplanfeedback)を使用してください。 デプロイ ガイドに関する質問がある場合は、[IDGitDeploy](mailto:idgitdeploy@microsoft.com) までお問い合わせください。

---

### <a name="enterprise-applications-search---load-more-apps"></a>エンタープライズ アプリケーション検索 - 読み込むアプリを増やす

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

アプリケーション/サービス プリンシパルが見つかりませんか? すべてのアプリケーションが表示されるように、エンタープライズ アプリケーションにより多くのアプリケーションを読み込む機能が追加されました。 既定では、20 のアプリケーションが表示されます。 **[さらに読み込む]** をクリックすると、さらにアプリケーションが表示されるようになりました。

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>AADConnect の 5 月のリリースには、PingFederate との統合のパブリック プレビュー、重要なセキュリティ更新プログラム、多くのバグ修正、新しい優れたトラブルシューティング ツールが含まれています。

**種類:** 変更された機能 **サービス カテゴリ:** AD Connect **製品の機能:** ID ライフサイクル管理

AADConnect の 5 月のリリースには、PingFederate との統合のパブリック プレビュー、重要なセキュリティ更新プログラム、多くのバグ修正、新しい優れたトラブルシューティング ツールが含まれています。 [こちら](../hybrid/reference-connect-version-history.md)でリリース ノートを確認できます。

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD アクセス レビュー: 自動適用

**種類:** 変更された機能 **サービス カテゴリ:** アクセス レビュー **製品の機能:** ガバナンス

グループとアプリのアクセス レビューが Azure AD Premium P2 の一部として一般公開されました。 管理者は、アクセス レビューの完了時にグループまたはアプリのレビュー担当者による変更が自動的に適用されるように構成できます。 さらに、管理者は、レビュー担当者が応答しなかった場合、アクセスが削除された場合、アクセスが維持された場合、またはシステムの推奨事項が採用された場合に、ユーザーの継続中のアクセスをどのように処理するかを指定できます。

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>新しいアプリでは query response_mode を使用して ID トークンを返すことはできません。

**種類:** 変更された機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

2018 年 4 月 25 日以降に作成されたアプリは、**query** response_mode を使用して **id_token** を要求することはできません。  これにより、OIDC 仕様による Azure AD のインラインが実現し、アプリの攻撃対象領域を削減できます。  2018 年 4 月 25 日より前に作成されたアプリによる **query** response_mode と **id_token** の response_type の使用はブロックされません。  Azure AD に id_token を要求した場合に返されるエラーは **AADSTS70007: 'query' is not a supported value of 'response_mode' when requesting a token** (AADSTS70007: トークン要求時の 'query' は 'response_mode' のサポートされている値ではありません) です。

**fragment** と **form_post** response_modes は引き続き動作します。新しいアプリケーション オブジェクトを作成する (たとえば、アプリ プロキシで使用する) 場合は、新しいアプリケーションが作成される前にいずれかの response_modes が使用されることを確認してください。

---

## <a name="april-2018"></a>2018 年 4 月

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C アクセス トークンが GA に

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

Azure AD B2C によってセキュリティで保護されている Web API にアクセス トークンを使用してアクセスできるようになりました。 この機能はパブリック プレビューから GA に移行します。 Azure AD B2C アプリケーションと Web API を構成するための UI エクスペリエンスが強化され、他の小規模な改善が加えられました。

詳細については、「[Azure AD B2C: アクセス トークンの要求](../../active-directory-b2c/access-tokens.md)」を参照してください。

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>SAML ベース アプリケーションのシングル サインオン構成をテストする

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

SAML ベースの SSO アプリケーションを構成するときに、構成ページで統合をテストできます。 サインイン時にエラーが発生した場合には、テスト エクスペリエンスにエラーを提供でき、特定の問題を解決するための解決手順が Azure AD から示されます。

詳細については、次を参照してください。

- [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../manage-apps/view-applications-portal.md)
- [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD Terms of Use にユーザーごとのレポートが設けられた

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** コンプライアンス

管理者が特定の使用条件 (ToU) を選択し、その ToU に同意したすべてのユーザーと、同意された日時を確認できるようになりました。

詳細については、[Azure AD Terms of Use 機能](../conditional-access/terms-of-use.md)に関するページを参照してください。

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: AD FS エクストラネット ロックアウト保護の危険な IP

**種類:** 新機能 **サービス カテゴリ:** その他 **製品の機能:** 監視とレポート

Connect Health で、U/P ログインの失敗のしきい値を超える IP アドレスを時間単位または日単位で検出する機能がサポートされました。 この機能によって次のことが可能になります。

- カスタマイズ可能なしきい値に基づいて時間単位または日単位で生成される、失敗したログインの IP アドレスと回数を示す包括的なレポート。
- 特定の IP アドレスが U/P ログイン失敗のしきい値 (時間単位または日単位) をいつ超えたかを示す、電子メール ベースのアラート。
- データの詳しい分析を実行するためのダウンロード オプション

詳細については、[危険な IP のレポート](../hybrid/how-to-connect-health-adfs.md)に関するページを参照してください。

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>メタデータ ファイルまたは URL を使用した簡単なアプリ構成

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

管理者は [エンタープライズ アプリケーション] ページで、Azure AD ギャラリーに含まれるアプリケーションとギャラリーに含まれないアプリケーション向けに SAML ベースのサインインを構成するための SAML メタデータ ファイルをアップロードできます。

さらに、Azure AD アプリケーションのフェデレーション メタデータ URL を使用して、対象となるアプリケーションで SSO を構成できます。

詳細については、「[Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../manage-apps/view-applications-portal.md)」を参照してください。

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Terms of Use が一般公開に

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** コンプライアンス


Azure AD Terms of Use がパブリック プレビューから一般公開に移動されました。

詳細については、[Azure AD Terms of Use 機能](../conditional-access/terms-of-use.md)に関するページを参照してください。

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>B2B ユーザーに対する特定組織からの招待を許可またはブロックする

**種類:** 新機能 **サービス カテゴリ:** B2B **製品の機能:** B2B/B2C


Azure AD B2B コラボレーションで共有とコラボレーションを行う相手の取引先組織を指定できるようになりました。 そのために、特定の許可ドメインまたは拒否ドメインを指定したリストを作成できます。 これらの機能を使用してドメインをブロックすると、従業員はそのドメイン内のユーザーに招待を送信できなくなります。

これは、承認済みユーザーのエクスペリエンスを円滑に保ちながら、リソースに対するアクセスを制御するのに役立ちます。

この B2B コラボレーション機能はすべての Azure Active Directory ユーザーが利用でき、条件付きアクセスや ID 保護などの Azure AD Premium 機能と連携させて、外部のビジネス ユーザーによるサインインやアクセス取得のタイミングと方法をより細かく制御することができます。

詳細については、「[B2B ユーザーに対する特定組織からの招待を許可またはブロックする](../external-identities/allow-deny-list.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018年 4 月に、フェデレーションを使用した以下の 13 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

Criterion HCM、[FiscalNote](../saas-apps/fiscalnote-tutorial.md)、[Secret Server (On-Premises)](../saas-apps/secretserver-on-premises-tutorial.md)、[Dynamic Signal](../saas-apps/dynamicsignal-tutorial.md)、[mindWireless](../saas-apps/mindwireless-tutorial.md)、[OrgChart Now](../saas-apps/orgchartnow-tutorial.md)、[Ziflow](../saas-apps/ziflow-tutorial.md)、[AppNeta Performance Monitor](../saas-apps/appneta-tutorial.md)、[Elium](../saas-apps/elium-tutorial.md)、[Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md)、[Cisco Cloud](../saas-apps/ciscocloud-tutorial.md)、Shelf、[SafetyNet](../saas-apps/safetynet-tutorial.md)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>オンプレミスのアプリケーションに対するアクセスを Azure AD の B2B ユーザーに付与する (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** B2B **製品の機能:** B2B/B2C

Azure Active Directory (Azure AD) B2B コラボレーション機能を使用して取引先組織のゲスト ユーザーを Azure AD に招待している組織が、これらの B2B ユーザーにオンプレミスのアプリケーションへのアクセスを提供できるようになりました。 これらのオンプレミスのアプリケーションでは、Kerberos の制約付き委任 (KCD) と共に SAML ベースの認証または統合 Windows 認証 (IWA) を使用できます。

詳細については、「[Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する](../external-identities/hybrid-cloud-to-on-premises.md)」を参照してください。

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Azure Marketplace から SSO 統合チュートリアルを入手する

**種類:** 変更された機能 **サービス カテゴリ:** その他 **製品の機能:** サード パーティ統合

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) にリストされているアプリケーションが SAML ベースのシングル サインオンをサポートしている場合、 **[今すぐ入手する]** をクリックすると、そのアプリケーションに関連する統合チュートリアルが表示されます。

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>SaaS アプリケーションへの Azure AD 自動ユーザー プロビジョニングのパフォーマンスが向上

**種類:** 変更された機能 **サービス カテゴリ:** アプリのプロビジョニング **製品の機能:** サード パーティ統合

以前は、(Salesforce、ServiceNow、Box などの) SaaS アプリケーション向けの Azure Active Directory ユーザー プロビジョニング コネクタを使用するお客様は、Azure AD テナントに含まれるユーザーとグループが 100,000 を超え、プロビジョニングが必要なユーザーを判別するためにユーザーとグループの割り当てを使用している場合に、パフォーマンスが低下することがありました。

2018 年 4 月 2 日に Azure AD プロビジョニング サービスに非常に重要性の高いパフォーマンス強化がデプロイされ、Azure Active Directory とターゲット SaaS アプリケーションの間における初期同期の実行に必要な時間が大幅に短縮されました。

その結果、これまではアプリの初期同期に多くの日数を要したり、完了に至らなかったりした多くのお客様が、わずか数分または数時間でこれを完了できるようになりました。

詳細については、「[プロビジョニング中の動作](../..//active-directory/app-provisioning/how-provisioning-works.md)」を参照してください。

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>ハイブリッド Azure AD 参加マシンの Windows 10 ロック画面からのセルフサービス パスワード リセット

**種類:** 変更された機能 **サービス カテゴリ:** セルフサービス パスワード リセット **製品の機能:** ユーザー認証

Windows 10 SSPR 機能が更新され、ハイブリッド Azure AD に参加しているマシンがサポートされるようになりました。 この機能は Windows 10 RS4 で利用でき、ユーザーは Windows 10 マシンのロック画面からパスワードをリセットできます。 この機能を利用できるのは、セルフサービス パスワード リセットを有効にして登録したユーザーです。

詳細については、「[ログイン画面からの Azure AD パスワード リセット](../authentication/howto-sspr-windows.md)」を参照してください。

---

## <a name="march-2018"></a>2018 年 3 月

### <a name="certificate-expire-notification"></a>証明書の期限切れ通知

**種類:** 修正済み **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

ギャラリーのアプリケーションまたはギャラリー以外のアプリケーションの証明書の有効期限が近づくと、Azure AD は通知を送信します。

SAML ベースのシングル サインオン対応に構成されたエンタープライズ アプリケーションを使う一部のユーザーは、通知を受け取りませんでした。 この問題が解決されました。 Azure AD は、証明書が期限切れになる 7 日前、30 日前、60 日前に通知を送信します。 このイベントは監査ログで確認できます。

詳細については、次を参照してください。

- [Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Azure Active Directory ポータルの監査アクティビティ レポート](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C での Twitter および GitHub の ID プロバイダー

**種類:** 新機能 **サービス カテゴリ:** B2C - コンシューマー ID 管理 **製品の機能:** B2B/B2C

Azure AD B2C の ID プロバイダーとして、Twitter または GitHub を追加できるようになりました。 Twitter はパブリック プレビューから GA に移行します。 GitHub はパブリック プレビューでリリースされます。

詳しくは、「[Azure AD B2B コラボレーションとは](../external-identities/what-is-b2b.md)」をご覧ください。

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>iOS および Android 用の、Azure AD アプリケーション ベースの条件付きアクセスを使用した、Intune Managed Browser によるブラウザー アクセスの制限

**種類:** 新機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティ & 保護

**パブリック プレビュー中です。**

**Intune Managed Browser SSO:** 従業員が (Microsoft Outlook などの) ネイティブ クライアント全体でシングル サインオンを使用できます。また、Azure AD に接続されたすべてのアプリで Intune Managed Browser を使用できます。

**Intune Managed Browser の条件付きアクセスのサポート:** アプリケーションベースの条件付きアクセス ポリシーを使用して、従業員に Intune Managed Browser の使用を求めることができます。

詳細については、この[ブログ記事](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)をご覧ください。

詳細については、次を参照してください。

- [アプリケーションベースの条件付きアクセスの設定](../conditional-access/app-based-conditional-access.md)

- [Managed Browser ポリシーの構成](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>PowerShell GA モジュールでのアプリ プロキシ コマンドレット

**種類:** 新機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** アクセス制御

アプリケーション プロキシ コマンドレットのサポートが、PowerShell GA モジュールに組み込まれました。 この機能を使うには、PowerShell モジュールを最新の状態にしておく必要があります。1 年以上古いモジュールでは、一部のコマンドレットが動作しない可能性があります。

詳しくは、「[AzureAD](/powershell/module/Azuread/)」をご覧ください。

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 のネイティブ クライアントが、非対話型のプロトコルを使ってシームレス SSO によりサポートされる

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

Office 365 ネイティブ クライアント (バージョン 16.0.8730.xxxx 以降) を使うユーザーは、シームレス SSO によるサイレント サインオン エクスペリエンスを利用できます。 このサポートは、Azure AD への非対話型プロトコル (WS-Trust) の追加によって提供されます。

詳しくは、「[ネイティブ クライアントでのシームレス SSO によるサインインのしくみ](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)」をご覧ください。

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>アプリケーションがサインイン要求を Azure AD のテナント エンドポイントに送信する場合、シームレス SSO によるサイレント サインオン エクスペリエンスがユーザーに提供される

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

アプリケーション (`https://contoso.sharepoint.com` など) がサインイン要求を Azure AD の共通エンドポイント (`https://login.microsoftonline.com/common/<...>`) ではなく、Azure AD のテナント エンドポイント (つまり、`https://login.microsoftonline.com/contoso.com/<..>` または `https://login.microsoftonline.com/<tenant_ID>/<..>`) に送信する場合、シームレス SSO でサイレント サインオン エクスペリエンスがユーザーに提供されます。

詳しくは、「[Azure Active Directory シームレス シングル サインオン](../hybrid/how-to-connect-sso.md)」をご覧ください。

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>シームレス SSO をロールアウトするには、以前のように 2 つの URL ではなく、1 つの Azure AD URL だけをユーザーのイントラネット ゾーンの設定に追加する必要がある

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

シームレス SSO をユーザーにロールアウトするには、Active Directory のグループ ポリシーを使って、ユーザーのイントラネット ゾーンの設定にただ 1 つの Azure AD URL `https://autologon.microsoftazuread-sso.com` を追加する必要があります。 以前は、2 つの URL を追加する必要がありました。

詳しくは、「[Azure Active Directory シームレス シングル サインオン](../hybrid/how-to-connect-sso.md)」をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018年 3 月に、フェデレーションを使用した以下の 15 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md)、[CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md)、Wrike、[SignalFx](../saas-apps/signalfx-tutorial.md)、Assistant by FirstAgenda、[YardiOne](../saas-apps/yardione-tutorial.md)、Vtiger CRM、inwink、[Amplitude](../saas-apps/amplitude-tutorial.md)、[Spacio](../saas-apps/spacio-tutorial.md)、[ContractWorks](../saas-apps/contractworks-tutorial.md)、[Bersin](../saas-apps/bersin-tutorial.md)、[Mercell](../saas-apps/mercell-tutorial.md)、[Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md)、[Qumu Cloud](../saas-apps/qumucloud-tutorial.md)。

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="pim-for-azure-resources-is-generally-available"></a>Azure リソース向け PIM の一般公開

**種類:** 新機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

ディレクトリ ロールに Azure AD Privileged Identity Management を使っている場合は、サブスクリプション、リソース グループ、仮想マシン、および Azure Resource Manager によってサポートされるその他のリソースなどの Azure リソース ロールに、PIM の期限付きアクセスと割り当ての機能を使うことができるようになりました。 Just-In-Time でロールをアクティブ化するときに多要素認証を適用し、承認済みの変更期間と連携してアクティブ化をスケジュールします。 さらに、このリリースでは、更新された UI、承認ワークフロー、まもなく期限切れになるロールの延長と期限が切れたロールの更新など、パブリック プレビュー期間中には使用できなかった機能強化が追加されています。

詳しくは、「[Azure リソース向けの PIM (プレビュー)](../privileged-identity-management/azure-pim-resource-rbac.md)」をご覧ください

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>アプリ トークンへのオプション要求の追加 (パブリック プレビュー)

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

Azure AD アプリでは、JWT トークンまたは SAML トークンでカスタム要求またはオプション要求を要求できるようになりました。  これらは、サイズまたは適用性の制約のために、既定ではトークンに含まれないユーザーまたはテナントに関する要求です。  この機能は現在、v1.0 および v2.0 エンドポイントの Azure AD アプリに対するパブリック プレビューです。  追加できる要求および要求を行うためのアプリケーション マニフェストの編集方法については、ドキュメントをご覧ください。

詳しくは、「[Optional claims in Azure AD](../develop/active-directory-optional-claims.md)」(Azure AD でのオプションの要求) をご覧ください。

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>より安全な OAuth フローのための Azure AD による PKCE のサポート

**種類:** 新機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

Azure AD のドキュメントが更新されて、PKCE のサポートが記述されるようになりました。この機能は、OAuth 2.0 承認コード付与フローでのより安全な通信に対応します。  S256 とプレーンテキストの両方の code_challenge が、v1.0 と v2.0 のエンドポイントでサポートされます。

詳しくは、「[承認コードを要求する](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code)」をご覧ください。

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Workday Get_Workers API で使用可能なすべてのユーザー属性値のプロビジョニングのサポート

**種類:** 新機能 **サービス カテゴリ:** アプリのプロビジョニング **製品の機能:** サード パーティ統合

Workday から Active Directory と Azure AD への受信プロビジョニングのパブリック プレビューで、Workday Get_Workers API で使用可能なすべての属性値の抽出とプロビジョニングがサポートされるようになりました。 これにより、Workday 受信プロビジョニング コネクタの初期バージョンに付属するもの以外に、何百もの標準とカスタム属性のサポートが追加されます。

詳細については、次を参照してください。[Workday のユーザー属性リストをカスタマイズする](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>動的から静的または静的から動的への、グループ メンバーシップの変更

**種類:** 新機能 **サービス カテゴリ:** グループ管理 **製品の機能:** コラボレーション

グループのメンバーシップの管理方法を変更することができます。 これは、システムで同じグループの名前と ID を保持する場合に便利です。グループへの既存の参照は有効のままであるため、新しいグループを作成する場合にそれらの参照を更新する必要がありません。
Azure AD 管理センターが更新されて、この機能がサポートされるようになりました。 現在では、ユーザーは、動的なメンバーシップから割り当て済みのメンバーシップに、またはその逆に、既存のグループを変換できます。 既存の PowerShell コマンドレットも引き続き使用できます。

詳細については、「[Azure Active Directory の動的グループ メンバーシップ ルール](../enterprise-users/groups-dynamic-membership.md)」を参照してください。

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>シームレス SSO でのサインアウト動作の向上

**種類:** 変更された機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

以前は、ユーザーは、Azure AD によってセキュリティ保護されたアプリケーションから明示的にサインアウトした場合であっても、ドメイン参加デバイスから企業ネットワーク内の Azure AD アプリケーションに再びアクセスしようとすると、シームレス SSO を使うサインインに自動的に戻りました。 この変更により、サインアウトがサポートされるようになります。  これにより、ユーザーは、シームレス SSO を使って自動的にサインインするのではなく、再度のサインインに使うものとして同じ Azure AD アカウントまたは異なる Azure AD アカウントを選ぶことができます。

詳しくは、「[Azure Active Directory シームレス シングル サインオン](../hybrid/how-to-connect-sso.md)」をご覧ください

---

### <a name="application-proxy-connector-version-154020-released"></a>アプリケーション プロキシ コネクタと バージョン 1.5.402.0 のリリース

**種類:** 変更された機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** ID のセキュリティ & 保護

このバージョンのコネクタは、11 月中に徐々にロールアウトされます。 この新しいバージョンのコネクタには、次の変更が含まれています。

- コネクタは、サブドメイン レベルではなくドメイン レベルで Cookie を設定するようになりました。 これにより、いっそう滑らかな SSO エクスペリエンスが提供され、冗長な認証プロンプトが表示されなくなります。
- チャンク エンコード要求のサポート
- 強化されたコネクタ正常性の監視
- 複数のバグ修正と安定性の向上

詳しくは、「[Azure AD アプリケーション プロキシ コネクタを理解する](../manage-apps/application-proxy-connectors.md)」をご覧ください。

---

## <a name="february-2018"></a>2018 年 2 月

### <a name="improved-navigation-for-managing-users-and-groups"></a>ユーザーとグループの管理に関するナビゲーションの改善

**種類:** 変更の計画 **サービス カテゴリ:** ディレクトリ管理 **製品の機能:** ディレクトリ

ユーザーとグループを管理する際のナビゲーションの操作性が向上しています。 ディレクトリの概要から全ユーザーの一覧に直接移動できるようになりました。削除済みのユーザーの一覧にもアクセスしやすくなっています。 また、全グループの一覧にもディレクトリの概要から直接移動できるようになり、グループ管理の設定にアクセスしやすくなりました。 さらに、ディレクトリの概要ページから、ユーザーやグループ、エンタープライズ アプリケーション、アプリの登録を検索することもできます。

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>21Vianet (Azure China 21Vianet) が運用する Microsoft Azure でサインイン レポートと監査レポートが利用可能に

**種類:** 新機能 **サービス カテゴリ:** Azure Stack **製品の機能:** 監視とレポート

21Vianet (Azure China 21Vianet) インスタンスで運用される Microsoft Azure で、Azure AD アクティビティ ログ レポートが利用できるようになりました。 次のログが対象となります。

- **サインイン アクティビティ ログ** - ご利用のテナントに関連付けられているすべてのサインイン ログが対象となります。

- **セルフサービス パスワード監査ログ** - すべての SSPR 監査ログが対象となります。

- **ディレクトリ管理監査ログ** - ディレクトリ管理に関連したすべての監査ログが対象となります (ユーザー管理、アプリ管理など)。

これらのログを通じて、自分が管理する環境がどのような状態にあるのかの分析情報を得ることができます。 レポートから得たデータによって次のことが可能となります。

- 管理下にあるユーザーがアプリとサービスをどのように利用しているかを明らかにする。

- ユーザーの作業を妨げている問題をトラブルシューティングする。

レポートの使い方について詳しくは、「[Azure Active Directory レポート](../reports-monitoring/overview-reports.md)」をご覧ください。

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>"レポート リーダー" ロール (非管理者ロール) を使用して Azure AD アクティビティ レポートを閲覧する

**種類:** 新機能 **サービス カテゴリ:** レポート **製品の機能:** 監視とレポート

Azure AD のアクティビティ ログに非管理者ロールでアクセスできるようにするというカスタマー フィードバックの一環として、"レポート リーダー" ロールのユーザーを対象に、Azure portal 内のサインイン アクティビティや監査アクティビティにアクセスしたり、Microsoft Graph API を使用したりする機能を用意しました。

レポートの使い方について詳しくは、「[Azure Active Directory レポート](../reports-monitoring/overview-reports.md)」をご覧ください。

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID の信頼性情報がユーザー属性やユーザー ID として利用可能に

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

SAML ベースのサインオンを使用したアプリケーションでは、メンバー ユーザーや B2B ゲストのユーザー識別子およびユーザー属性として **EmployeeID** をエンタープライズ アプリケーションの UI から構成することができます。

詳細については、「[Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)」を参照してください。

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Azure Active Directory アプリケーション プロキシでワイルドカードを使用してアプリケーション管理を単純化

**種類:** 新機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** ユーザー認証

アプリケーションをデプロイしやすくして管理オーバーヘッドを低減するために、ワイルドカードを使ってアプリケーションを発行する機能に対応しました。 ワイルドカード アプリケーションの発行は、標準的なアプリケーション発行フローに沿って行われますが、内部 URL と外部 URL にはワイルドカードが使用されます。

詳細については、「[Azure Active Directory アプリケーション プロキシのワイルドカード アプリケーション](../manage-apps/application-proxy-wildcard.md)」を参照してください。

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>アプリケーション プロキシの構成をサポートする新しいコマンドレット

**種類:** 新機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** プラットフォーム

AzureAD PowerShell Preview モジュールの最新リリースには、PowerShell を使ってユーザーがアプリケーション プロキシ アプリケーションを構成できる新しいコマンドレットが備わっています。

新たに追加されたコマンドレットは次のとおりです。

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>グループの構成をサポートする新しいコマンドレット

**種類:** 新機能 **サービス カテゴリ:** アプリ プロキシ **製品の機能:** プラットフォーム

AzureAD PowerShell モジュールの最新リリースには、Azure AD 内のグループを管理するためのコマンドレットが備わっています。 これらのコマンドレットは、AzureADPreview モジュールで従来から提供されていましたが、新たに AzureAD モジュールにも追加されました。

新たに一般提供向けにリリースされた Group のコマンドレットは次のとおりです。

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Azure AD Connect の新しいリリースが利用可能に

**種類:** 新機能 **サービス カテゴリ:** AD Sync **製品の機能:** プラットフォーム

Azure AD とオンプレミス データ ソース (Windows Server Active Directory や LDAP) との間でデータを同期するためのツールとしては Azure AD Connect が推奨されています。

>[!Important]
>このビルドでは、スキーマと同期ルールに変更が加えられています。 アップグレードの後、フル インポートの手順と完全同期の手順が Azure AD Connect 同期サービスによってトリガーされます。 この動作を変更する方法については、「[アップグレード後に完全な同期を保留にする方法](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)」を参照してください。

このリリースで行われた更新や変更を以下に示します。

**修正された問題**

- 次のページに切り替えるときの、パーティション フィルター処理ページのバック グラウンド タスクにおけるタイミング ウィンドウの修正。

- ConfigDB カスタム アクションの実行中にアクセス違反を引き起こしていたバグを修正しました

- SQL 接続のタイムアウトから復旧できるようにバグを修正しました。

- SAN ワイルドカードを含む証明書で前提条件の確認に失敗するバグを修正しました。

- Azure AD コネクタのエクスポート中に miiserver.exe がクラッシュするバグを修正しました。

- 実行時に誤ったパスワードで DC へのログオンを試行すると、Azure AD 接続ウィザードの構成が変更されてしまうバグを修正しました。

**新機能と機能強化**

- アプリケーション テレメトリ - このデータ クラスのオン/オフは、管理者が切り替えることができます。

- Azure AD の正常性データ - 正常性の設定を制御するには、管理者が正常性ポータルにアクセスする必要があります。 サービス ポリシーが変更された場合、エージェントがこれを読み取って強制します。

- デバイスの書き戻し構成アクションと、ページの初期化の進行状況バーを追加しました。

- HTML レポートでの一般的な診断と、ZIP テキスト/HTML レポートの完全なデータ コレクションが向上しました。

- 自動アップグレードの信頼性が向上しました。また、サーバーの正常性を確認できるように、テレメトリを追加しました。

- AD コネクタ アカウントの特権アカウントで使用できるアクセス許可が制限されています。 新規インストールの場合、MSOL アカウントの作成後に特権アカウントが MSOL アカウントに対して持つアクセス許可がウィザードによって制限されます。 この変更は、自動作成アカウントでの高速インストールとカスタム インストールに影響します。

- AADConnect のクリーン インストールで SA 特権が求められないようにインストーラーを変更しました。

- 特定のオブジェクトの同期に関する問題のトラブルシューティングを行う新しいユーティリティを追加しました。 現時点では、このユーティリティは以下の内容を確認します。

    - 同期されたユーザー オブジェクトと Azure AD テナントのユーザー アカウントの間の UserPrincipalName の不一致。

    - ドメインのフィルター処理によって、オブジェクトが同期からフィルター処理されているかどうか

    - 組織単位 (OU) のフィルター処理によって、オブジェクトが同期からフィルター処理されているかどうか

- 特定のユーザー アカウントを対象に、オンプレミス Active Directory に格納されている現在のパスワード ハッシュを同期する新しいユーティリティを追加しました。 このユーティリティでは、パスワードの変更は必要ありません。

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Azure AD アプリケーション ベースの条件付きアクセスで使用できるアプリケーションに Intune App Protection ポリシー対応アプリケーションを追加

**種類:** 変更された機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティ & 保護

アプリケーション ベースの条件付きアクセスをサポートするアプリケーションが増えました。 今後は、これらの承認されたクライアント アプリを使用して、Office 365 などの Azure AD 接続クラウド アプリにアクセスすることができます。

2 月末までに次のアプリケーションが追加される予定です。

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

詳細については、次を参照してください。

- [承認されたクライアント アプリケーションの要件](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD のアプリベースの条件付きアクセス](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>使用条件のモバイル エクスペリエンスへの更新

**種類:** 変更された機能 **サービス カテゴリ:** 使用条件 **製品の機能:** コンプライアンス

使用条件が表示されたときに **[表示に問題がある場合は、ここをクリックします]** をクリックできるようになりました。 このリンクをクリックすると、お使いのデバイスでネイティブに使用条件が表示されます。 ドキュメントのフォント サイズやデバイスの画面サイズに関係なく、必要に応じてズームしてドキュメントを読むことができます。

---

## <a name="january-2018"></a>2018 年 1 月

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2018 年 1 月に、フェデレーションをサポートする次の新しいアプリがアプリ ギャラリーに追加されました。

[IBM OpenPages](../saas-apps/ibmopenpages-tutorial.md)、[OneTrust Privacy Management Software](../saas-apps/onetrust-tutorial.md)、[Dealpath](../saas-apps/dealpath-tutorial.md)、IriusRisk Federated Directory、[Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md)。

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="sign-in-with-additional-risk-detected"></a>追加のリスクが検出されたサインイン

**種類:** 新機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティ & 保護

検出されたリスク検出に対して取得する洞察は、Azure AD サブスクリプションに関連付けられています。 Azure AD Premium P2 エディションでは、基になるすべての検出に関する最も詳細な情報を取得できます。

Azure AD Premium P1 エディションでは、ライセンスに含まれない検出は、追加のリスクが検出されたサインインというリスク検出として表示されます。

詳細については、「[Azure Active Directory リスク検出](../identity-protection/overview-identity-protection.md)」を参照してください。

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>エンド ユーザーのアクセス パネルでの Office 365 アプリケーションの非表示

**種類:** 新機能 **サービス カテゴリ:** マイ アプリ **製品の機能:** SSO

新しいユーザー設定を使用することで、ユーザーのアクセス パネルで Office 365 アプリケーションを表示する方法が管理しやすくなりました。 このオプションは、Office ポータルでのみ Office アプリを表示する場合に、ユーザーのアクセス パネル内のアプリ数を減らすのに役立ちます。 この設定は **[ユーザー設定]** 内にあり、 **[ユーザーは Office 365 ポータルでのみ Office 365 アプリを表示できる]** というラベルが付いています。

詳細については、「[Azure Active Directory でユーザーのエクスペリエンスからアプリケーションを非表示にする](../manage-apps/hide-application-from-user-portal.md)」をご覧ください。

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>アプリ URL からの直接のパスワード SSO が有効なアプリへのシームレスなサインイン

**種類:** 新機能 **サービス カテゴリ:** マイ アプリ **製品の機能:** SSO

マイ アプリのブラウザー拡張機能は、マイ アプリのシングル サインオン機能をブラウザーでショートカットとして使用できる、便利なツール上で利用できます。 インストール後、ユーザーのブラウザーにワッフル アイコンが表示され、そこからアプリにクイック アクセスできるようになります。 ユーザーは次のメリットを得ることができます。

- アプリのログイン ページから直接、パスワード SSO ベースのアプリにサインインできます
- クイック検索機能を使用して、すべてのアプリを起動できます
- 拡張機能で最近使用したアプリにショートカットできます
- この拡張機能は、Microsoft Edge、Chrome、Firefox で利用できます。

詳細については、「[マイ アプリによるセキュリティで保護されたサインイン拡張機能](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)」をご覧ください。

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure クラシック ポータルでの Azure AD 管理エクスペリエンスの廃止

**種類:** 非推奨 **サービス カテゴリ:** Azure AD **製品の機能:** ディレクトリ

2018 年 1 月 8 日に、Azure クラシック ポータルでの Azure AD 管理エクスペリエンスは廃止されました。 これは Azure クラシック ポータル自体の廃止と合わせて行われました。 今後は、Azure AD のポータル ベースの管理についてはすべて [Azure AD 管理センター](https://aad.portal.azure.com)をご利用ください。

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor Web ポータルは廃止されました

**種類:** 非推奨 **サービス カテゴリ:** Azure AD **製品の機能:** ディレクトリ

2018 年 1 月 8日に、PhoneFactor Web ポータルは廃止されました。 このポータルは、MFA サーバーの管理に使用されていましたが、これらの機能は portal.azure.com の Azure Portal に移行しました。

MFA の構成は、 **[Azure Active Directory] \> [MFA サーバー]** にあります。

---

### <a name="deprecate-azure-ad-reports"></a>Azure AD レポートの廃止

**種類:** 非推奨 **サービス カテゴリ:** レポート **製品の機能:** ID ライフサイクル管理


新しい Azure Active Directory 管理コンソールと、アクティビティおよびセキュリティ レポートに使用できる新しい API の一般公開に伴い、"/reports" エンドポイントにあるレポート API は 2017 年 12 月 31 日に廃止されました。

**利用可能な機能**

新しい管理コンソールへの移行の一環として、Azure AD アクティビティ ログの取得に使用できる新しい 2 つの API が作成されました。 この新しい API セットでは監査およびサインイン アクティビティが追加されているだけでなく、より充実したフィルター機能と並べ替え機能も備わっています。 以前はセキュリティ レポートを通じて提供されていたデータに、Microsoft Graph の Identity Protection リスク検出 API を通じてアクセスできるようになりました。

詳細については、次を参照してください。

- [Azure Active Directory Reporting API の概要](../reports-monitoring/concept-reporting-api.md)

- [Azure Active Directory Identity Protection と Microsoft Graph の基本](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>2017 年 12 月

### <a name="terms-of-use-in-the-access-panel"></a>アクセス パネルでの使用条件

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** コンプライアンス

アクセス パネルに移動して、以前承認した使用条件を確認できます。

次の手順に従います。

1. [MyApps ポータル](https://myapps.microsoft.com)に移動し、サインインします。

2. 右上隅に表示されるご自身の名前をクリックし、一覧から **[プロファイル]** を選択します。

3. **[プロファイル]** で、 **[使用条件の確認]** を選択します。

4. 承認した使用条件を確認できます。

詳細については、「[Azure Active Directory Terms of Use 機能 (プレビュー)](../conditional-access/terms-of-use.md)」をご覧ください。

---

### <a name="new-azure-ad-sign-in-experience"></a>新しい Azure AD サインイン エクスペリエンス

**種類:** 新機能 **サービス カテゴリ:** Azure AD **製品の機能:** ユーザー認証

Azure AD と Microsoft アカウント ID システムの UI は、外観と操作性が一貫したものになるよう再設計されました。 また、Azure AD サインイン ページは最初にユーザー名を収集し、2 番目の画面で資格情報を収集します。

詳細については、「[The new Azure AD sign-in experience is now in public preview (サインイン エクスペリエンスのパブリック プレビューを開始)](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)」をご覧ください。

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>回数が減ったサインイン プロンプト: Azure AD サインインの新しい "サインインしたままにする" エクスペリエンス

**種類:** 新機能 **サービス カテゴリ:** Azure AD **製品の機能:** ユーザー認証

Azure AD サインイン ページの **[サインインしたままにする]** チェック ボックスは、お客様が正常に認証された後に表示される新しいプロンプトに置き換えられました。

このプロンプトに **[はい]** と応答した場合、サービスにより永続的な更新トークンが付与されます。 この動作は、以前のエクスペリエンスで **[サインインしたままにする]** チェック ボックスをオンにした場合と同じになります。 フェデレーション テナントの場合、お客様がフェデレーション サービスで正常に認証された後にこのプロンプトが表示されます。

詳細については、「[Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview」 (回数が減ったサインイン プロンプト: Azure AD の新しい "サインインしたままにする" エクスペリエンスはプレビュー段階)](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)」を参照してください。

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>同意前に使用条件の展開を要求する構成の追加

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** コンプライアンス

条項に同意する前に使用条件を展開することをお客様のユーザーに要求する、管理者向けのオプションです。

[ユーザーは使用条件を展開する必要があります] を **[オン]** にするか **[オフ]** にするかを選択します。 **[オン]** に設定すると、同意前に使用条件を承認するようユーザーに要求します。

詳細については、「[Azure Active Directory Terms of Use 機能 (プレビュー)](../conditional-access/terms-of-use.md)」をご覧ください。

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>対象となるロール割り当ての範囲指定されたアクティブ化

**種類:** 新機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

範囲指定されたアクティブ化により、元の割り当ての既定値よりも低い自律性で、対象となる Azure リソースのロール割り当てをアクティブ化できます。 たとえば、ご利用のテナントでお客様がサブスクリプションの所有者として割り当てられているとします。 範囲指定されたアクティブ化では、サブスクリプションに含まれる最大 5 つのリソース (リソース グループや仮想マシンなど) に対して所有者のロールをアクティブ化できます。 アクティブ化を範囲指定すると、重要な Azure リソースに対して望ましくない変更が実行される可能性を低減できる場合があります。

詳しくは、「[Azure AD Privileged Identity Management とは](../privileged-identity-management/pim-configure.md)」をご覧ください。

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーの新しいフェデレーション アプリ

**種類:** 新機能 **サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** サード パーティ統合

2017 年 12 月に、フェデレーションを使用した以下の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Accredible](../saas-apps/accredible-tutorial.md)、Adobe Experience Manager、[EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md)、[Communifire](../saas-apps/communifire-tutorial.md) CybSafe、[FactSet](../saas-apps/factset-tutorial.md)、[IMAGE WORKS](../saas-apps/imageworks-tutorial.md)、[MOBI](../saas-apps/mobi-tutorial.md)、[MobileIron Azure AD integration](../saas-apps/mobileiron-tutorial.md)、[Reflektive](../saas-apps/reflektive-tutorial.md)、[SAML SSO for Bamboo by resolution GmbH](../saas-apps/bamboo-tutorial.md)、[SAML SSO for Bitbucket by resolution GmbH](../saas-apps/bitbucket-tutorial.md)、[Vodeclic](../saas-apps/vodeclic-tutorial.md)、WebHR、Zenegy Azure AD Integration。

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](../saas-apps/tutorial-list.md)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](../develop/v2-howto-app-gallery-listing.md)」を参照してください。

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD ディレクトリ ロールに対する承認ワークフロー

**種類:** 変更された機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

Azure AD ディレクトリ ロールの承認ワークフローが一般公開されました。

承認ワークフローでは、特権ロール管理者は特権ロール使用前に、対象となるロール メンバーに対してロールのアクティブ化を要求するよう要求できます。 複数のユーザーとグループに承認責任を委任できます。 対象となるロール メンバーは、承認が完了して各自のロールがアクティブ化されたときに通知を受け取ります。

---

### <a name="pass-through-authentication-skype-for-business-support"></a>パススルー認証: Skype for Business のサポート

**種類:** 変更された機能 **サービス カテゴリ:** 認証 (ログイン) **製品の機能:** ユーザー認証

パススルー認証では、先進認証 (オンライン トポロジとハイブリッド トポロジを含む) をサポートする Skype for Business クライアント アプリケーションへのユーザー サインインがサポートされるようになりました。

詳細については、「[先進認証でサポートされる Skype for Business トポロジ](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)」をご覧ください。

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Azure RBAC 用の Azure AD Privileged Identity Management の更新 (プレビュー)

**種類:** 変更された機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

Azure ロールベースのアクセス制御 (Azure RBAC) 用の Azure AD Privileged Identity Management (PIM) のパブリック プレビュー更新により、次のことが可能になりました。

* Just Enough Administration を使用する。
* リソース ロールのアクティブ化の前に承認を要求する。
* Azure AD と Azure の両方のロールの承認を要求するロールの、将来のアクティブ化をスケジュールする。

詳細については、「[Azure リソース向けの PIM (プレビュー)](../privileged-identity-management/azure-pim-resource-rbac.md)」をご覧ください。

---

## <a name="november-2017"></a>2017 年 11 月

### <a name="access-control-service-retirement"></a>Access Control Service の廃止

**種類:** 変更の計画 **サービス カテゴリ:** Access Control Service **製品の機能:** Access Control Service

Azure Active Directory Access Control (Access Control Service とも呼ばれます) は 2018 年後半に廃止されます。 詳しいスケジュールや大まかな移行ガイダンスなど、詳細については数週間以内にお知らせします。 Access Control Service に関する質問をこのページにコメントとして残していただければ、チーム メンバーがお答えします。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>ブラウザー アクセスを Intune Managed Browser に限定

**種類:** 変更の計画 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティと保護

Intune Managed Browser を承認されたアプリとして使用することで、Office 365 などの Azure AD 接続クラウド アプリへのブラウザー アクセスを制限できるようになります。

アプリケーションベースの条件付きアクセスで以下の条件を構成できるようになります。

**クライアント アプリ:** Browser

**この変更の影響**

現在、この条件を使用する場合、アクセスはブロックされます。 このプレビューが利用可能になると、すべてのアクセスで Managed Browser アプリケーションの使用が必須になります。

この機能の詳細については、今後のブログとリリース ノートをお待ちください。

詳細については、[Azure AD の条件付きアクセス](../conditional-access/overview.md)に関するページを参照してください。

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

**種類:** 変更の計画 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティと保護

以下のアプリが、[承認されたクライアント アプリ](../conditional-access/concept-conditional-access-conditions.md#client-apps)の一覧に示されます。

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

詳細については、次を参照してください。

- [承認されたクライアント アプリケーションの要件](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD のアプリベースの条件付きアクセス](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>使用条件での多言語のサポート

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** コンプライアンス

管理者は、PDF ドキュメントを複数含めた新しい使用条件を作成できるようになりました。 これらの PDF ドキュメントは、対応する言語にタグ付けできます。 PDF はユーザー設定に一致する言語で表示されます。 一致する言語がない場合は、既定の言語が表示されます。

---

### <a name="real-time-password-writeback-client-status"></a>パスワード ライトバック クライアントのリアルタイムの状態

**種類:** 新機能 **サービス カテゴリ:** セルフサービス パスワード リセット **製品の機能:** ユーザー認証

オンプレミスのパスワード ライトバック クライアントの状態を確認できるようになりました。 このオプションは、[[パスワードのリセット]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) ページの **[オンプレミスの統合]** セクションで使用できます。

オンプレミスのライトバック クライアントへの接続で問題が生じた場合、次の内容のエラー メッセージが表示されます。

- オンプレミスのライトバック クライアントへ接続できない理由の詳細
- 問題の解決に役立つドキュメントへのリンク

詳細については、「[オンプレミスの統合](../authentication/concept-sspr-howitworks.md#on-premises-integration)」をご覧ください。

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD のアプリベースの条件付きアクセス

**種類:** 新機能 **サービス カテゴリ:** Azure AD **製品の機能:** ID のセキュリティと保護

[Azure AD のアプリベースの条件付きアクセス](../conditional-access/app-based-conditional-access.md)を使用することで、Office 365 などの Azure AD 接続クラウド アプリへのアクセスを、Intune App Protection ポリシーがサポートされる[承認されたクライアント アプリ](../conditional-access/concept-conditional-access-conditions.md#client-apps)に限定できるようになりました。 Intune App Protection ポリシーは、これらのクライアント アプリケーション上にある企業データの構成と保護に使用されます。

[アプリベース](../conditional-access/app-based-conditional-access.md)の条件付きアクセス ポリシーを[デバイスベース](../conditional-access/require-managed-devices.md)の条件付きアクセス ポリシーと組み合わせることで、個人および会社のデバイスのデータを柔軟に保護できます。

現在、アプリベースの条件付きアクセスでは以下の条件とコントロールを使用できます。

**サポートされるプラットフォームの条件**

- iOS
- Android

**クライアント アプリの条件**

- モバイル アプリとデスクトップ クライアント

**アクセス制御**

- 承認済みクライアント アプリを必須にする

詳細については、[Azure AD のアプリベースの条件付きアクセス](../conditional-access/app-based-conditional-access.md)に関するページを参照してください。

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure Portal での Azure AD デバイスの管理

**種類:** 新機能 **サービス カテゴリ:** デバイスの登録と管理 **製品の機能:** ID のセキュリティと保護

Azure AD に接続されているすべてのデバイスと、それらのデバイスに関連したアクティビティを 1 か所で確認できるようになりました。 Azure Portal にすべてのデバイスの ID と設定を管理できる新しい管理機能が追加されました。 このリリースでは、次の操作を行うことができます。

- Azure AD の条件付きアクセスで使用可能なすべてのデバイスを表示する。
- プロパティを表示する (ハイブリッド Azure AD 参加済みデバイスも含む)。
- Azure AD 参加済みデバイスの BitLocker キーを確認し、Intune などでデバイスを管理する。
- Azure AD デバイス関連の設定を管理する。

詳細については、[Azure Portal によるデバイスの管理](../devices/device-management-azure-portal.md)に関するページをご覧ください。

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Azure AD の条件付きアクセス用デバイス プラットフォームとしての macOS のサポート

**種類:** 新機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティと保護

Azure AD 条件付きアクセス ポリシーのデバイス プラットフォームの条件に macOS を追加 (または除外) できるようになりました。 サポートされるデバイス プラットフォームに macOS が追加されたことで、以下の操作が可能になりました。

- **Intune を使用して macOS デバイスを登録、管理する。** iOS や Android などのプラットフォームと同様に、macOS でも登録を統合するポータル サイト アプリケーションを使用できるようになりました。 macOS 用の新しいポータル サイト アプリを使用すれば、Intune と Azure AD にデバイスを登録できます。
- **Intune で定義したご所属の組織のコンプライアンス ポリシーを厳守するよう macOS デバイスを設定する。** Azure Portal の Intune では、macOS デバイス用のコンプライアンス ポリシーを設定できます。
- **Azure AD のアプリケーションにアクセスできるデバイスを、ポリシーに準拠した macOS デバイスのみに制限する。** 条件付きアクセス ポリシーを作成する際、別のデバイス プラットフォーム オプションとして macOS を選択できます。 Azure で設定済みのターゲット アプリケーション向けに macOS 専用の条件付きアクセス ポリシーを作成できます。

詳細については、次を参照してください。

- [Intune で macOS デバイス用のデバイス コンプライアンス ポリシーを作成する](/mem/intune/protect/compliance-policy-create-mac-os)
- [Azure AD の条件付きアクセス](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication の ネットワーク ポリシー サーバー拡張機能

**種類:** 新機能 **サービス カテゴリ:** 多要素認証 **製品の機能:** ユーザー認証

Azure AD Multi-Factor Authentication のネットワーク ポリシー サーバー拡張機能は、既存のサーバーを使用してクラウド ベースの多要素認証機能をご利用の認証インフラストラクチャに追加します。 ネットワーク ポリシー サーバー拡張機能を使用すると、電話、テキスト メッセージ、またはモバイル アプリによる検証を、ご利用の既存の認証フローに追加できます。 新しいサーバーをインストール、構成、メンテナンスする必要はありません。

この拡張機能は、Azure Multi-Factor Authentication Server をデプロイせずに仮想プライベート ネットワーク接続を保護する必要がある組織を対象に作成されました。 ネットワーク ポリシー サーバー拡張機能は、RADIUS とクラウド ベース Azure AD Multi-Factor Authentication の間のアダプターとして機能し、認証の 2 番目の要素をフェデレーション ユーザーまたは同期済みユーザーに提供します。

詳細については、[Azure AD Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](../authentication/howto-mfa-nps-extension.md)に関するページをご覧ください。

---

### <a name="restore-or-permanently-remove-deleted-users"></a>削除済みユーザーの復元または完全な削除

**種類:** 新機能 **サービス カテゴリ:** ユーザー管理 **製品の機能:** ディレクトリ

Azure AD 管理センターで以下の操作が可能になりました。

- 削除済みユーザーを復元する。
- ユーザーを完全に削除する。

**実行方法:**

1. Azure AD 管理センターの **[管理]** セクションで [[すべてのユーザー]](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) を選択します。

2. **[表示]** リストで **[最近削除されたユーザー]** を選択します。

3. 最近削除されたユーザーを 1 名または複数名選択して、復元するか完全に削除します。

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

**種類:** 変更された機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティと保護

以下のアプリが、[承認されたクライアント アプリ](../conditional-access/concept-conditional-access-conditions.md#client-apps)の一覧に追加されました。

- Microsoft Planner
- Azure Information Protection

詳細については、次を参照してください。

- [承認されたクライアント アプリケーションの要件](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD のアプリベースの条件付きアクセス](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>条件付きアクセス ポリシーのコントロール間での "OR" の使用

**種類:** 変更された機能 **サービス カテゴリ:** 条件付きアクセス **製品の機能:** ID のセキュリティと保護

条件付きアクセス コントロールで (選択したコントロールのいずれかを必須にする) "OR" を使用できるようになりました。 この機能を使用すると、アクセス コントロール間を "OR" でつないでポリシーを作成できます。 たとえば、この機能を使用して、多要素認証を使用したサインイン "または" 準拠デバイスの使用をユーザーに求めるポリシーを作成できます。

詳細については、[Azure AD の条件付きアクセスのコントロール](../conditional-access/controls.md)に関するページを参照してください。

---

### <a name="aggregation-of-real-time-risk-detections"></a>リアルタイムのリスク検出の集計

**種類:** 変更された機能 **サービス カテゴリ:** ID 保護 **製品の機能:** ID のセキュリティと保護

Azure AD Identity Protection では、特定の日に同一の IP アドレスで発生したすべてのリアルタイム リスク検出が、リスク検出の種類ごとに集計されるようになりました。 この変更により、表示されるリスク検出の量が制限されますが、ユーザーのセキュリティが変更されることはありません。

根本となるリアルタイム検出は、ユーザーのサインインのたびに行われます。 多要素認証またはアクセスのブロックを行うサインインのリスク セキュリティ ポリシーを設定している場合、このポリシーは引き続きリスクのあるサインインのたびにトリガーされます。

---

## <a name="october-2017"></a>2017 年 10 月

### <a name="deprecate-azure-ad-reports"></a>Azure AD レポートの廃止

**種類:** 変更の計画 **サービス カテゴリ:** レポート **製品の機能:** ID ライフサイクル管理

Azure Portal では次の機能が提供されています。

- 新しい Azure AD 管理コンソール。
- アクティビティ レポートとセキュリティ レポートのための新しい API。

これらの新機能の導入に伴い、/reports エンドポイントのレポート API は 2017 年 12 月 10 日に廃止されました。

---

### <a name="automatic-sign-in-field-detection"></a>サインイン フィールドの自動検出

**種類:** 修正済み **サービス カテゴリ:** マイ アプリ **製品の機能:** シングル サインオン

Azure AD は、HTML ユーザー名とパスワード フィールドをレンダリングするアプリケーションのサインイン フィールドの自動検出をサポートしています。 この手順は「[アプリケーションのサインイン フィールドを自動的にキャプチャする方法](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app)」に示されています。 この機能は、[Azure Portal](https://aad.portal.azure.com) の **[エンタープライズ アプリケーション]** ページで *ギャラリー以外のアプリケーション* を追加することで表示されます。 さらに、Web URL を入力してページを保存することにより、この新しいアプリケーションの **シングル サインオン** モードを **パスワード ベースのシングル サインオン** を実行するように構成できます。

サービスの問題により、この機能は一時的に無効化されていました。 問題が解決されたため、サインイン フィールドの自動検出が再び利用可能になりました。

---

### <a name="new-multi-factor-authentication-features"></a>新しい多要素認証機能

**種類:** 新機能 **サービス カテゴリ:** 多要素認証 **製品の機能:** ID のセキュリティと保護

多要素認証 (MFA) は、組織を保護する上で欠かせない要素です。 資格情報の適応性を高めて多要素認証をよりシームレスに利用できるようにするため、以下の機能が追加されました。

- MFA 結果へのプログラムによるアクセスなどを含め、多要素認証結果が Azure AD サインイン レポートに直接統合されました。
- MFA 構成 が Azure Portal の Azure AD 構成エクスペリエンスに、より緊密に統合されました。

このパブリック プレビューでは、MFA の管理およびレポートが、コアとなる Azure AD の構成エクスペリエンスに組み込まれました。 Azure AD を使用しながら MFA の管理ポータル機能を管理できます。

詳細については、「[Azure Portal の多要素認証レポートのリファレンス](../authentication/howto-mfa-reporting.md)」をご覧ください。

---

### <a name="terms-of-use"></a>使用条件

**種類:** 新機能 **サービス カテゴリ:** 使用条件 **製品の機能:** コンプライアンス

Azure AD の使用条件を利用して、ユーザーに対して法律またはコンプライアンスの要件に関する免責事項を表示できます。

Azure AD Terms of Use は、次のシナリオで使用できます。

- ご所属の組織内のすべてのユーザーに対する一般的な使用条件
- ユーザーの属性 (たとえば、動的グループによって行われる、医師と看護師や、国内の従業員と国外の従業員などの区別) に基づいた特定の使用条件
- ビジネスへの影響が大きいアプリ (Salesforce など) へのアクセスのための特定の使用条件

詳細については、[Azure AD の使用条件](../conditional-access/terms-of-use.md)に関するページをご覧ください。

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management の機能強化

**種類:** 新機能 **サービス カテゴリ:** Privileged Identity Management **製品の機能:** Privileged Identity Management

Azure AD Privileged Identity Management で、組織内の以下の Azure リソース (プレビュー) へのアクセスを管理、制御、監視できるようになりました。

- サブスクリプション
- リソース グループ
- 仮想マシン

Azure RBAC 機能を利用する Azure Portal 内のすべてのリソースで、Azure AD Privileged Identity Management から提供されるセキュリティおよびライフサイクルの管理機能を使用できます。

詳細については、[Azure リソース向けの Privileged Identity Management](../privileged-identity-management/azure-pim-resource-rbac.md) に関するページをご覧ください。

---

### <a name="access-reviews"></a>アクセス レビュー

**種類:** 新機能 **サービス カテゴリ:** アクセス レビュー **製品の機能:** コンプライアンス

組織はアクセス レビュー (プレビュー) を使用して、グループ メンバーシップの管理とエンタープライズ アプリケーションへのアクセスを効率的に行うことができます。

- アプリケーションへのアクセスおよびグループのメンバーシップのアクセス レビューを使うと、ゲスト ユーザーによるアクセスを認定できます。 レビュー担当者は、アクセス レビューによって提供される分析情報に基づき、ゲストが引き続きアクセスすることを許可するかどうかを効率的に決定できます。
- アクセス レビューを使って従業員のアプリケーション アクセスとグループ メンバーシップを認定できます。

アクセス レビューの制御を組織に関連するプログラムに集めて、コンプライアンスまたはリスクを受けやすいアプリケーションのレビューを追跡できます。

詳細については、「[Azure AD アクセス レビュー](../governance/access-reviews-overview.md)」をご覧ください。

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>マイ アプリおよび Office 365 アプリ ランチャーでのサード パーティ製アプリケーションの非表示

**種類:** 新機能 **サービス カテゴリ:** マイ アプリ **製品の機能:** シングル サインオン

新しい **アプリの非表示** プロパティにより、ご利用のユーザー ポータルに表示されるアプリを管理しやすくなりました。 アプリの非表示は、バックエンド サービス用のアプリ タイルが表示されていたり、タイルが重複してユーザーのアプリ ランチャーが整理されていなかったりする場合に役立ちます。 切り替えボタンはサード パーティ製アプリの **[プロパティ]** セクションにあり、 **[Visible to user?]\(ユーザーに表示しますか?\)** というラベルが付いています。 PowerShell を介してプログラムでアプリを非表示にすることもできます。

詳細については、[Azure AD でユーザーのエクスペリエンスからサードパーティ製アプリケーションを非表示にする](../manage-apps/hide-application-from-user-portal.md)方法に関するページをご覧ください。


**利用可能な機能**

 新しい管理コンソールへの移行の一環として、Azure AD のアクティビティ ログを取得する新しい 2 つの API が利用可能になりました。 この新しい API セットでは監査およびサインイン アクティビティが追加されているだけでなく、より充実したフィルター機能と並べ替え機能も備わっています。 以前はセキュリティ レポートで提供されていたデータに、Microsoft Graph の Identity Protection リスク検出 API を使用してアクセスできまるようになりました。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-identity-manager"></a>Identity Manager の修正プログラム

**種類:** 変更された機能 **サービス カテゴリ:** Identity Manager **製品の機能:** ID ライフサイクル管理

2017 年 9 月 25 日に、Identity Manager 2016 Service Pack 1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供が開始されました。 このロールアップ パッケージの説明は以下のとおりです。

- 問題を解決し、機能を強化します。
- Identity Manager 2016 のビルド 4.4.1459.0 までのすべての Identity Manager 2016 Service Pack 1 更新プログラムに代わる、累積的な更新プログラムです。
- ご利用のコンピューターに Identity Manager 2016 ビルド 4.4.1302.0 がインストールされている必要があります。

詳細については、[Identity Manager 2016 Service Pack 1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0)](https://support.microsoft.com/help/4021562) に関するページをご覧ください。

---