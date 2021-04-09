---
title: 新機能 リリース ノート - Azure Active Directory | Microsoft Docs
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Active Directory の新着情報について説明します。
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/4/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d9769a2cfdbd5f552e97a6cd665263cbd488325
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592967"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報

>URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` をコピーして、お使いの ![RSS フィード リーダー アイコン](./media/whats-new/feed-icon-16x16.png) フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

Azure AD は随時改善されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[新着情報のアーカイブ - Azure Active Directory](whats-new-archive.md)」をご覧ください。

---
## <a name="february-2021"></a>2021 年 2 月

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>2021 年 10 月以降、既定でワンタイム パスコード認証が電子メールで送信される

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

2021 年 10 月 31 日以降、Microsoft Azure Active Directory の[電子メールによるワンタイム パスコード認証](../external-identities/one-time-passcode.md)が、B2B コラボレーションのシナリオでアカウントとテナントを招待する既定の方法になります。 今のところ、管理対象外の Azure Active Directory アカウントを使用した招待の引き換えは許可されなくなります。 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>要求されていない承認済みの権限が、条件付きアクセスをトリガーする場合、それらがトークンに追加されることはなくなります。

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** プラットフォーム
 
現在、[動的な権限](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)を使用するアプリケーションには、アクセスを承認したすべての権限が付与されます。 これには、要求されていないアプリケーションが含まれ、条件付きアクセスをトリガーする場合も該当します。 これにより、たとえば、`user.read` のみを要求し、`files.read` も承諾したアプリケーションが、`files.read` 権限に割り当てられた条件付きアクセスを受け入れるように強制されるということが起こります。 

不要な条件付きアクセスのプロンプトの数を減らすため、Azure AD は、要求されていないスコープがアプリケーションに提供される方法を変更しています。 アプリケーションは、明示的に要求した権限に対してのみ、条件付きアクセスをトリガーします。 詳細については、[認証の新機能](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)に関する記事を参照してください。
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>パブリック プレビュー - 一時アクセス パスを使用してパスワードレスの資格情報を登録する

**種類:** 新機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

一時アクセス パスは、強力な資格情報として機能する、時間限定のパスコードです。ユーザーが強力な認証要素 (FIDO2 セキュリティ キーや Microsoft Authenticator など) アプリを紛失したり忘れたりしたときに、サインインして新しい強力な認証方法を登録する必要がある場合に、パスワードレスの資格情報と復元を実現できます。 [詳細については、こちらを参照してください](../authentication/howto-authentication-temporary-access-pass.md)。

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>パブリック プレビュー - 次世代のユーザー フローでの KMSI (サインインしたままにする) 機能

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

次世代の B2C ユーザー フローでは、[KMSI (サインインしたままにする)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 機能がサポートされるようになりました。これにより、Web やネイティブ アプリケーションを使用しているユーザーのセッション期間を、永続的な Cookie を使用することで延長できます。  この機能は、ユーザーがブラウザーを終了して再び開いた後もセッションをアクティブなままにし、ユーザーがサインアウトしたときに取り消します。

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>パブリック プレビュー - MSA アカウントを使用した AAD での External Identities セルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
外部ユーザーは、Microsoft アカウントを使用して Azure AD ファースト パーティおよび LOB アプリにサインインできるようになりました。 [詳細については、こちらを参照してください](../external-identities/self-service-sign-up-overview.md)。

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>パブリック プレビュー - ゲスト ユーザーの引き換え状態をリセットする

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
引き換え状態をリセットするように、既存の外部ゲスト ユーザーを招待し直すことができるようになりました。これにより、ゲスト ユーザー アカウントをアクセス権を失うことなくそのまま残すことができます。 [詳細については、こちらを参照してください](../external-identities/reset-redemption-status.md)。
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>パブリックプレビュー - 同期 (プロビジョニング) API でアプリケーションのアクセス許可をサポート

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
同期 API を呼び出すため、application.readwrite.ownedby をアプリケーションのアクセス許可として使用できるようになりました。 これは、Azure AD からサードパーティ アプリケーション (AWS、Data Bricks など) にプロビジョニングする場合のみサポートされます。 HR プロビジョニング (Workday / Successfactors) や Cloud Sync (AD から Azure AD) については現在サポートされていません。 [詳細については、こちらを参照してください](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)。
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>一般提供 - 認証ポリシー管理者組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
このロールを持つユーザーは、認証方法のポリシー、テナント全体の MFA 設定、およびパスワード保護ポリシーを構成できます。 このロールは、パスワード保護設定を管理するためのアクセス許可を付与します (スマート ロックアウトの構成とカスタムの禁止パスワード リストの更新)。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#authentication-policy-administrator)。

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>一般提供 - マイ アプリのユーザー コレクションを使用可能

**種類:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** エンド ユーザー エクスペリエンス
 
ユーザーは、マイ アプリのアプリ ランチャーで独自のアプリ グループを作成できるようになりました。 共有されているコレクションを並べ替えたり、非表示にすることも管理者によって可能です。 [詳細については、こちらを参照してください](../user-help/my-apps-portal-user-collections.md)。

---

### <a name="general-availability---autofill-in-authenticator"></a>一般提供 - Authenticator のオートフィル

**種類:** 新機能  
**サービスカテゴリ:** Microsoft Authenticator アプリ  
**製品の機能:** ID のセキュリティ & 保護
 
Microsoft Authenticator は、多要素認証 (MFA) とアカウント管理機能を提供しているほか、ユーザーが自分のモバイル機器 (iOS および Android) でサイトやアプリにアクセスする際にパスワードのオートフィルを実現するようになりました。 

Authenticator でオートフィルを使用するには、ユーザーが自分の個人 Microsoft アカウントを Authenticator に追加し、それを使用してパスワードを同期する必要があります。 現時点では、職場や学校のアカウントを使用してパスワードを同期することはできません。 [詳細については、こちらを参照してください](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins)。

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>一般提供 - B2B コラボレーションへの内部ユーザーの招待

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
お客様は、既存の内部アカウントに招待状を送信する代わりに、内部ゲストを招待して B2B コラボレーションを使用できるようになりました。 これにより、ユーザーのオブジェクト ID、UPN、グループ メンバーシップ、アプリの割り当てを保持することができます。 [詳細については、こちらを参照してください](../external-identities/invite-internal-users.md)。

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>一般提供 - ドメイン名管理者組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
このロールのユーザーは、ドメイン名の管理 (読み取り、追加、検証、更新、および削除) を行うことができます。 また、ユーザー、グループ、およびアプリケーションに関するディレクトリ情報も読み取ることができます。これらのオブジェクトにはドメインの依存関係があるためです。 

オンプレミス環境の場合、このロールを持つユーザーは、関連付けられたユーザーが常にオンプレミスで認証されるように、フェデレーションのドメイン名を構成できます。 これらのユーザーは、シングル サインオンを介してオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインインできます。 フェデレーション設定を Azure AD Connect 経由で同期する必要があるため、ユーザーは Azure AD Connect を管理する権限も持っています。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#domain-name-administrator)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2021 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2021 年 2 月には、フェデレーションをサポートする次の 37 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Loop Messenger Extension](https://loopworks.com/loop-flow-messenger/)、[Silverfort Azure AD Adapter](http://www.silverfort.com/)、[Interplay Learning](https://skilledtrades.interplaylearning.com/#login)、[Nura Space](https://dashboard.nuraspace.com/login)、[Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft)、[UXPressia](https://uxpressia.com/users/sign-in)、[introDus Pre- and Onboarding Platform](http://app.introdus.dk/login)、[Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All)、[LeaksID](https://app.leaksid.com/)、[ShiftWizard](http://www.shiftwizard.com/)、[PingFlow SSO](https://app.pingview.io/)、[Swiftlane](https://admin.swiftlane.com/login)、[Quasydoc SSO](https://www.quasydoc.eu/login)、[Fenwick Gold Account](https://businesscentral.dynamics.com/)、[SeamlessDesk](https://www.seamlessdesk.com/login)、[Learnsoft LMS & TMS](http://www.learnsoft.com/)、[P-TH+](https://p-th.jp/)、[myViewBoard](https://api.myviewboard.com/auth/microsoft/)、[Tartabit IoT Bridge](https://bridge-us.tartabit.com/)、[AKASHI](../saas-apps/akashi-tutorial.md)、[Rewatch](../saas-apps/rewatch-tutorial.md)、[Zuddl](../saas-apps/zuddl-tutorial.md)、[Parkalot - Car park management](../saas-apps/parkalot-car-park-management-tutorial.md)、[HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md)、[IBMid](../saas-apps/ibmid-tutorial.md)、[SharingCloud](../saas-apps/sharingcloud-tutorial.md)、[PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md)、[GlobeSmart](../saas-apps/globesmart-tutorial.md)、[Samsung Knox and Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md)、[Penji](../saas-apps/penji-tutorial.md)、[Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md)、[Maptician](../saas-apps/maptician-tutorial.md)、[Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md)、[Sigma Computing](../saas-apps/sigma-computing-tutorial.md)、[CloudKnox Permissions Management Platform](../saas-apps/cloudknox-permissions-management-platform-tutorial.md)、[Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md)、[Enablon](../saas-apps/enablon-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、次の詳細をお読みください: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2021 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

詳細については、[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関する記事を参照してください。

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>一般提供 - 10 件の Azure Active Directory ロールの名前変更

**種類:** 変更された機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
10 件の Azure AD 組み込みロールの名前が変更され、[Microsoft 365 管理センター](/microsoft-365/admin/microsoft-365-admin-center-preview)、[Azure AD ポータル](https://portal.azure.com/)、[Microsoft Graph](https://developer.microsoft.com/graph/) で統一されました。 新しいロールの詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../roles/permissions-reference.md#all-roles)」を参照してください。

![MS Graph API と Azure portal のロール名、および API、Azure portal、Mac 全体で提案された最終的な名前を示す表。](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>MFA/SSPR の統合登録での新しい会社のブランディングの表示

**種類:** 変更された機能  
**サービスカテゴリ:** ユーザー エクスペリエンスと管理  
**製品の機能:** エンド ユーザー エクスペリエンス
 
これまで、Azure Active Directory サインイン ページで会社のロゴは使用されていませんでした。 このたび、会社のブランディングが、MFA/SSPR の統合登録の左上に配置されました。 会社のブランディングには、自分のサインインとセキュリティ情報ページにも表示されます。 [詳細については、こちらを参照してください](../fundamentals/customize-branding.md)。

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>一般提供 - 別の承認者として第 2 レベルのマネージャーを設定できる

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
エンタイトルメント管理で、承認者を選択するときに追加のオプションが使用できるようになりました。 [最初の承認者] として [承認者としてのマネージャー] を選択した場合は、別の承認者フィールドで選択できる追加オプション [別の承認者としての第 2 レベルのマネージャー] を使用できます。 このオプションを選択した場合は、システムが第 2 レベルのマネージャーを見つけられない場合に、要求の転送先のフォールバック承認者を追加する必要があります。 [詳細については、こちらを参照してください](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)。
 
---

### <a name="authentication-methods-activity-dashboard"></a>認証方法アクティビティのダッシュボード

**種類:** 変更された機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 

[認証方法の更新] アクティビティダッシュボードでは、管理者は、テナントの認証方法の登録と使用状況アクティビティの概要を管理できます。 レポートには、各方法に登録されているユーザー数と、サインインおよびパスワードのリセット時に使用される方法の概要が表示されます。 [詳細については、こちらを参照してください](../authentication/howto-authentication-methods-activity.md)。
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>構成可能なトークンの有効期間 (CTL) での更新とセッション トークンの有効期間の構成を廃止

**種類:** 非推奨  
**サービス カテゴリ:** その他  
**製品の機能:** ユーザー認証
 
CTL での更新とセッション トークンの有効期間の構成は廃止されました。 Azure Active Directory では、既存のポリシーの更新およびセッション トークンの構成が考慮されなくなくなりました。 [詳細については、こちらを参照してください](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens)。
 
---
 
## <a name="january-2021"></a>2021 年 1 月

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>シークレット トークンがプロビジョニングの構成時に必須フィールドになる

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

以前は、カスタム/BYOA アプリケーションでプロビジョニングを設定するときに、シークレット トークン フィールドを空のままにすることができました。 この機能は、テストに使用することのみを目的としていました。 このフィールドが必須になるように、UI が更新されます。 

お客様は、ブラウザーの URL で機能フラグを使用することで、このテスト目的用の要件を回避できます。 [詳細については、こちらを参照してください](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery)。
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>パブリック プレビュー - 現場担当者用の Android 共有デバイスを大規模にカスタマイズして構成する

**種類:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** ID のセキュリティ & 保護
 
Azure AD と Microsoft Endpoint Manager チームが合同で、現場担当者のデバイスをカスタマイズし、スケーリングし、セキュリティで保護するための機能を提供しました。

次のプレビュー機能を使用すると、以下を行うことができます。
- Microsoft Endpoint Manager を使用して Android 共有デバイスを大規模にプロビジョニングする
- デバイス ベースの条件付きアクセスを使用して、シフト作業者のアクセスをセキュリティで保護する
- 管理ホーム画面を使用してシフト作業者のサインイン エクスペリエンスをカスタマイズする

詳細については、「[現場担当者用の共有デバイスを大規模にカスタマイズして構成する](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)」を参照してください。

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>パブリック プレビュー - プロビジョニング ログを CSV または JSON としてダウンロードできるようになった

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

お客様は、UI と Graph API を使用して、プロビジョニング ログを CSV または JSON ファイルとしてダウンロードできます。 詳細については、[Azure Active Directory ポータルのプロビジョニング レポート](../reports-monitoring/concept-provisioning-logs.md)に関する記事を参照してください。

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>パブリック プレビュー - クラウド グループを Azure AD カスタム ロールと管理単位スコープ ロールに割り当てる

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
お客様は、クラウド グループを Azure AD カスタム ロールまたは管理単位スコープ ロールに割り当てることができます。 この機能の使用方法については、「[クラウド グループを使用して Azure Active Directory でロールの割り当てを管理する](../roles/groups-concept.md)」を参照してください。

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>一般提供 - Azure AD Connect クラウド同期 (以前はクラウド プロビジョニングと呼ばれていました)

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Connect クラウド同期  
**製品の機能:** ID ライフサイクル管理
 
Azure AD Connect クラウド同期が、すべてのお客様に一般提供されるようになりました。

Azure AD Connect クラウドにより、変換ロジックの大量の処理がクラウドに移行され、オンプレミスでの占有領域が削減されます。 さらに、同期の可用性を高めるために、複数の軽量エージェントを配置できます。 [詳細については、こちらを参照してください](https://aka.ms/cloudsyncGA)。
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>一般提供 - 攻撃シミュレーション管理者と攻撃ペイロード作成者の組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
ロール ベースのアクセス制御で、2 つの新しいロールである攻撃シミュレーション管理者と攻撃ペイロード作成者をユーザーに割り当てることができます。 

[攻撃シミュレーション管理者](../roles/permissions-reference.md#attack-simulation-administrator)ロールのユーザーは、テナント内のすべてのシミュレーションにアクセスでき、さらに以下を行うことができます。
- 攻撃のシミュレーション作成のすべての側面を作成および管理する
- シミュレーションの起動/スケジューリング
-  シミュレーションの結果を確認する 

[攻撃ペイロード作成者](../roles/permissions-reference.md#attack-payload-author)ロールのユーザーは、攻撃のペイロードを作成することはできますが、それらを実際に起動することやスケジュールすることはできません。 攻撃のペイロードは、それらを使用してシミュレーション作成できるテナントの管理者全員が利用できます。

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>一般提供 - 使用状況の概要のレポート閲覧者組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
使用状況の概要のレポート閲覧者ロールのユーザーは、使用状況と生産性スコアについて、Microsoft 365 管理センターでテナント レベルの集計データと関連する分析情報にアクセスできます。 ただし、ユーザー レベルの詳細や分析情報にはアクセスできません。 

2 つのレポートについて、Microsoft 365 管理センターでは、テナント レベルの集計データとユーザー レベルの詳細が区別されます。 このロールにより、個々のユーザーを特定できるデータに対する保護レイヤーが追加されます。 [詳細については、こちらを参照してください](../roles/permissions-reference.md#usage-summary-reports-reader)。

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>一般提供 - Azure AD 条件付きアクセスでの、アプリ保護ポリシーを必須にする許可

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
"アプリ保護ポリシーを必須にする" の Azure AD 条件付きアクセスの許可が GA になりました。 

このポリシーにより、以下の機能が提供されます。
- Intune App Protection をサポートするモバイル アプリケーションを使用している場合にだけアクセスを許可する
- ユーザーがモバイル アプリケーションに Intune App Protection ポリシーを配布している場合にだけアクセスを許可する

アプリ保護の条件付きアクセス ポリシーを設定する方法の詳細については、[こちら](../conditional-access/app-protection-based-conditional-access.md)を参照してください。
 
---

### <a name="general-availability---email-one-time-passcode"></a>一般提供 - ワンタイム パスコードをメールで送信する

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
メール OTP を使用すると、世界中の組織が、メールでリンクまたは招待状を送信することで、だれとでもコラボレーションすることができます。 招待されたユーザーは、自分のメールに送信されたワンタイム パスコードを使用して自分の身元を証明して、パートナーのリソースにアクセスできます。 [詳細については、こちらを参照してください](../external-identities/one-time-passcode.md)。 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2021 年 1 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

詳細については、「[Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化とは](../app-provisioning/user-provisioning.md)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2021 年 1 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2021 年 1 月には、フェデレーションをサポートする次の 29 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[mySCView](https://dev.myscview.com/)、[Talentech](https://talentech.com/contact/)、[Bipsync](https://www.bipsync.com/)、[OroTimesheet](https://app.orotimesheet.com/login.php)、[Mio](https://app.m.io/auth/install/microsoft?scopetype=hub)、[Sovelto Easy](https://login.soveltoeasy.fi/)、[Supportbench](https://account.supportbench.net/agent/login/)、[Bienvenue Formation](https://formation.bienvenue.pro/login)、[AIDA Healthcare SSO](https://aidaforparents.com/login/organizations)、[International SOS Assistance 製品](../saas-apps/international-sos-assistance-products-tutorial.md)、[NAVEX One](../saas-apps/navex-one-tutorial.md)、[LabLog](../saas-apps/lablog-tutorial.md)、[Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md)、[EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md)、[Notion](../saas-apps/notion-tutorial.md)、[Syndio](../saas-apps/syndio-tutorial.md)、[Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md)、[Timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md)、[Nalco E-data](https://www.ecolab.com/)、[Vacancy Filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login)、[Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md)、[Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md)、[Illusive Networks](../saas-apps/illusive-networks-tutorial.md)、[Proware](../saas-apps/proware-tutorial.md)、[Splan Visitor](../saas-apps/splan-visitor-tutorial.md)、[Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md)、[Contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md)、[Perimeter 81](../saas-apps/perimeter-81-tutorial.md)、[Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>パブリック プレビュー - 別の承認者として第 2 レベルのマネージャーを設定できる

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
エンタイトルメント管理で、承認者を選択するときに追加のオプションが使用できるようになりました。 [最初の承認者] として [承認者としてのマネージャー] を選択した場合は、別の承認者フィールドで選択できる追加オプション [別の承認者としての第 2 レベルのマネージャー] を使用できます。 このオプションを選択した場合は、システムが第 2 レベルのマネージャーを見つけられない場合に、要求の転送先のフォールバック承認者を追加する必要があります。 [詳細情報](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>一般提供 - マイ アクセス ポータルから直接 Teams に移動する

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
マイ アクセス ポータルから直接 Teams を起動できるようになりました。 

それを行うには、[マイ アクセス] (https://myaccess.microsoft.com/) ) にサインインし、[アクセス パッケージ] に移動します。次に、[アクティブ] タブに移動して、既にアクセス権があるすべてのアクセス パッケージを表示します。 選択したアクセス パッケージを展開して Teams をマウスでポイントすると、[開く] ボタンをクリックして起動できるようになります。 [詳細については、こちらを参照してください](../governance/entitlement-management-request-access.md)。
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>危険なゲスト ユーザーのログとエンドユーザー プロンプトが向上

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 

危険なゲスト ユーザーのログとエンドユーザー プロンプトが更新されました。 詳細については、「[Identity Protection と B2B ユーザー](../identity-protection/concept-identity-protection-b2b.md)」を参照してください。
 
---
 
## <a name="december-2020"></a>2020 年 12 月

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>パブリック プレビュー - 組み込みポリシーを使用した Azure AD B2C Phone のサインアップとサインイン

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
組み込みポリシーを使用した B2C Phone のサインアップとサインインを使用すると、組織の IT 管理者や開発者は、エンド ユーザーが電話番号を使用してサインインおよびサインアップできるユーザー フローを提供できます。 詳細については、「[ユーザー フローの電話でのサインアップとサインインを設定する (プレビュー)](../../active-directory-b2c/phone-authentication-user-flows.md)」を参照してください。

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>一般提供 - すべての新規テナントに対して既定で有効にされるようになったセキュリティの既定値群

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** ID のセキュリティ & 保護
 
ユーザー アカウントを保護するため、2020 年 11 月 12 日以降に作成されるすべての新しいテナントについては、セキュリティの既定値群が有効になります。 セキュリティの既定値群により、次のような複数のポリシーが適用します。
- すべてのユーザーと管理者は、Microsoft Authenticator アプリを使用して MFA に登録する必要があります
- クリティカルな管理者ロールは、サインインするたびに MFA を使用する必要があります。 他のすべてのユーザーは、必要に応じて MFA の入力を求められます。 
- レガシ認証は、テナント全体でブロックされます。 

詳細については、「[セキュリティの既定値群とは](../fundamentals/concept-fundamentals-security-defaults.md)」を参照してください。

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>一般提供 - AADConnect での最大 250,000 メンバーが含まれるグループのサポート

**種類:** 変更された機能  
**サービス カテゴリ:** AD Connect  
**製品の機能:** ID ライフサイクル管理
 
Microsoft がデプロイした Azure AD Connect 用の新しいエンドポイント (API) では、Azure Active Directory に対する同期サービス操作のパフォーマンスが向上しています。 新しい [V2 エンドポイント](../hybrid/how-to-connect-sync-endpoint-api-v2.md)を使用すると、Azure AD に対するエクスポートとインポートのパフォーマンスが明らかに向上していることがわかります。 この新しいエンドポイントでは、次のシナリオがサポートされています。

- メンバー数が最大 250,000 までのグループの同期
- Azure AD に対するエクスポートとインポートのパフォーマンスの向上

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>一般提供 - Azure China クラウドのテナントに利用できるエンタイトルメント管理

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 

Azure China クラウドのすべてのテナントで、エンタイトルメント管理の機能を使用できるようになりました。 詳細については、[ID ガバナンスのドキュメント](https://docs.azure.cn/zh-cn/active-directory/governance/)に関するサイトを参照してください。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 12 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 12 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 12 月には、フェデレーションをサポートする次の 18 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[AwareGo](../saas-apps/awarego-tutorial.md)、[HowNow SSO](https://gethownow.com/)、[ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold)、[Guider](http://www.guider-ai.com/)、[Softcrisis](https://www.softcrisis.se/sv/)、[Pims 365](http://www.omega365.com/pims)、[InformaCast](../saas-apps/informacast-tutorial.md)、[RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md)、[vonage](../saas-apps/vonage-tutorial.md)、[Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md)、[ProProfs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md)、[RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md)、[JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md)、[Shutterstock](../saas-apps/shutterstock-tutorial.md)、[FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md)、[LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md)、[Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md)、[KFAdvance](../saas-apps/kfadvance-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>マイ アクセス ポータルから直接 Teams に移動する

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理 **製品の機能:** エンタイトルメント管理

マイ アクセス ポータルから直接 Teams を起動できるようになりました。 それを行うには、[[マイ アクセス]](https://myaccess.microsoft.com/) にサインインし、 **[アクセス パッケージ]** に移動します。次に、 **[アクティブ]** タブに移動して、既にアクセス権があるすべてのアクセス パッケージを表示します。 アクセス パッケージを展開して Teams をマウスでポイントすると、 **[開く]** ボタンをクリックして起動できるようになります。 

マイ アクセス ポータルの使用の詳細については、[Azure AD エンタイトルメント管理でのアクセス パッケージへのアクセスの要求](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)に関するページに移動してください。

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>パブリック プレビュー - 別の承認者として第 2 レベルのマネージャーを設定できる

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理

エンタイトルメント管理の承認プロセスで追加オプションを使用できるようになりました。 [最初の承認者] として [承認者としてのマネージャー] を選択した場合は、別の承認者フィールドで選択できる追加オプション [別の承認者としての第 2 レベルのマネージャー] を使用できます。 このオプションを選択した場合は、システムが第 2 レベルのマネージャーを見つけられない場合に、要求の転送先のフォールバック承認者を追加する必要があります。

詳細については、[Azure AD エンタイトルメント管理でのアクセス パッケージの承認設定の変更](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)に関するページに移動してください。

--- 

## <a name="november-2020"></a>2020 年 11 月

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1.0、TLS 1.1、3DES の非推奨化

**種類:** 変更の計画  
**サービス カテゴリ:** すべての Azure AD アプリケーション  
**製品の機能:** 標準

Azure Active Directory では、2021 年 6 月 30 日までに、Azure Active Directory の世界各地のリージョンで次のプロトコルが非推奨になります。

- TLS 1.0
- TLS 1.1
- 3DES 暗号スイート (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

影響を受ける環境は次のとおりです。
- Azure 商用クラウド
- Office 365 GCC および WW

関連するお知らせ - Azure、Office 365、Microsoft 365 サービスの Azure Active Directory へのセキュリティで保護された接続を維持するために、クライアントとサーバーおよびブラウザーとサーバーのすべての組み合わせで、TLS 1.2 と最新の暗号スイートを使用する必要があります。 これは、「[US Gov クラウドでの Azure Active Directory TLS 1.0、TLS 1.1、3DES の非推奨化](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud)」に関連した変更です。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 11 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2020 年 11 月には、フェデレーションをサポートする次の 52 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Travel & Expense Management](https://app.expenseonce.com/Account/Login)、[Tribeloo](../saas-apps/tribeloo-tutorial.md)、[Itslearning File Picker](https://pmteam.itslearning.com/)、[Crises Control](../saas-apps/crises-control-tutorial.md)、[CourtAlert](https://www.courtalert.com/)、[StealthMail](https://stealthmail.com/)、[Edmentum - Study Island](https://app.studyisland.com/cfw/login/)、[Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md)、[TIMU](../saas-apps/timu-tutorial.md)、[Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md)、[Talview - Recruit, Real Time Translator](https://recruit.talview.com/login)、[Klaxoon](https://access.klaxoon.com/login)、[Podbean](../saas-apps/podbean-tutorial.md)、[zcal](https://zcal.co/signup)、[expensemanager](https://api.expense-manager.com/)、[Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md)、[En-trak Tenant Experience Platform](https://portal.en-trak.app/)、[Appian](../saas-apps/appian-tutorial.md)、[Panorays](../saas-apps/panorays-tutorial.md)、[Builterra](https://portal.builterra.com/)、[EVA Check-in](https://my.evacheckin.com/organization)、[HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md)、[Coupa Risk Assess](../saas-apps/coupa-risk-assess-tutorial.md)、[Lucid (すべての製品)](../saas-apps/lucid-tutorial.md)、[GoBright](https://portal.brightbooking.eu/)、[SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md)、[Resource Central](../saas-apps/resource-central-tutorial.md)、[UiPathStudioO365App](https://www.uipath.com/product/platform)、[Jedox](../saas-apps/jedox-tutorial.md)、[Cequence Application Security](../saas-apps/cequence-application-security-tutorial.md)、[PerimeterX](../saas-apps/perimeterx-tutorial.md)、[TrendMiner](../saas-apps/trendminer-tutorial.md)、[Lexion](../saas-apps/lexion-tutorial.md)、[WorkWare](../saas-apps/workware-tutorial.md)、[ProdPad](../saas-apps/prodpad-tutorial.md)、[AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md)、[AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md)、[Luum](../saas-apps/luum-tutorial.md)、[Freight Measure](https://www.gpcsl.com/freight.html)、[Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md)、[Nature Research](../saas-apps/nature-research-tutorial.md)、[Play Digital Signage](https://login.playsignage.com/login)、[RemotePC](../saas-apps/remotepc-tutorial.md)、[Prolorus](../saas-apps/prolorus-tutorial.md)、[Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md)、[Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure)、[Roadmunk](../saas-apps/roadmunk-tutorial.md)、[Sunrise Software Relations CRM](https://cloud.relations-crm.com/)、[Procaire](../saas-apps/procaire-tutorial.md)、[Mentor® by eDriving:Business](https://www.edriving.com/)、[Gradle Enterprise](https://gradle.com/)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>パブリック プレビュー - エンタープライズ アプリのカスタム ロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
 [委任されたエンタープライズ アプリケーション管理のためのカスタム RBAC ロール](../roles/custom-available-permissions.md)がパブリック プレビューになりました。 これらの新しいアクセス許可は、アプリ登録管理のためのカスタム ロールに付与されます。これにより、管理者が持つアクセス権をきめ細かく制御できます。 今後、Azure AD の管理を委任するための追加のアクセス許可がリリースされます。

いくつかの一般的な委任のシナリオを次に示します。
- SAML ベースのシングル サインオン アプリケーションにアクセスできるユーザーとグループの割り当て
- Azure AD ギャラリー アプリケーションの作成
- SAML ベースのシングル サインオン アプリケーションの基本的な SAML 構成の更新と読み取り
- SAML ベースのシングル サインオン アプリケーションに使用される署名証明書の管理
- SAML ベースのシングル サインオン アプリケーションの期限切れが迫っているサインイン証明書の通知メール アドレスの更新
- SAML ベースのシングル サインオン アプリケーションに使用される SAML トークン署名とサインイン アルゴリズムの更新
- SAML ベースのシングル サインオン アプリケーションに使用されるユーザー属性と要求の作成、削除、更新
- プロビジョニング ジョブを有効化、無効化、再開する権限
- 属性マッピングの更新
- オブジェクトに関連付けられているプロビジョニング設定を読み取る権限
- サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る権限
- プロビジョニングのためのアプリケーション アクセスを認可する権限

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>パブリック プレビュー - Azure AD アプリケーション プロキシでは、認証のためにヘッダーを使用するアプリケーションへのシングル サインオン アクセスがネイティブにサポートされる

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御
 
Azure Active Directory (Azure AD) アプリケーション プロキシ は、認証のためにヘッダーを使用するアプリケーションに対するシングル サインオン アクセスをサポートしています。 Azure AD で、お使いのアプリケーションに必要なヘッダー値を構成できます。 ヘッダー値は、アプリケーション プロキシを介してアプリケーションに送信されます。 詳細については、「[Azure AD アプリ プロキシを使用したオンプレミスのアプリに対するヘッダーベースのシングル サインオン](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)」を参照してください
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>一般提供 - Azure AD B2C Phone のカスタム ポリシーを使用したサインアップとサインイン

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

開発者や企業は、電話番号を使用したサインアップとサインインを使用して、SMS 経由でユーザーの電話番号に送信されるワンタイム パスワードを使用したユーザーのサインアップとサインインを許可できます。 この機能を使用すると、ユーザーが電話にアクセスできなくなった場合に自分の電話番号を変更することもできます。 カスタム ポリシー機能によって、開発者や企業は、ページのカスタマイズを通して自社のブランドを伝達できます。 「[Azure AD B2C でカスタム ポリシーを使用した電話のサインアップとサインインを設定する](../../active-directory-b2c/phone-authentication.md)」でその実行方法を確認してください。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 11 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic - Tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

詳細については、[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングの自動化](../app-provisioning/user-provisioning.md)に関する記事を参照してください。
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>パブリック プレビュー - 段階的なロールアウトを通じてデプロイできるようになった ProxyAddresses を使用した電子メール サインイン

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
テナント管理者は、段階的なロールアウトを使用して、ProxyAddresses を使用した電子メール サインインを特定の Azure AD グループにデプロイできるようになりました。 これは、テナント全体にデプロイする前に、ホーム領域検出ポリシーを使用して機能を試す際に役立ちます。 段階的なロールアウトを通じて ProxyAddresses を使用した電子メール サインインをデプロイする手順については、[ドキュメント](../authentication/howto-authentication-use-email-signin.md)を参照してください。
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>制限付きプレビュー - サインイン診断

**種類:** 新機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
サインイン診断の初期プレビュー リリースでは、管理者はユーザーのサインインを確認できるようになりました。管理者は、サインイン中に発生したことと問題の修正方法に関する、状況に応じた具体的で関連性のある詳細とガイダンスを受け取ることができます。 診断は Azure AD レベルと条件付きアクセスの診断と解決ブレードの両方で使用できます。 このリリースが対応する診断シナリオは、条件付きアクセス、Multi-Factor Authentication、成功したサインインです。

詳細については、「[Azure AD におけるサインイン診断とは](../reports-monitoring/overview-sign-in-diagnostics.md)」を参照してください。
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>通常とは異なるサインイン プロパティの機能強化

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

  通常とは異なるサインイン プロパティの検出が更新されました。 お客様は、より高いリスクの通常とは異なるサインイン プロパティが検出されることに気付くことがあります。 詳細については、「[リスクとは](../identity-protection/concept-identity-protection-risks.md)」を参照してください。
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>クラウド プロビジョニング エージェントのパブリック プレビューの更新が利用可能 (バージョン:1.1.281.0)

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD のクラウド プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
クラウド プロビジョニング エージェントがパブリック プレビューでリリースされ、ポータルから利用できるようになりました。 このリリースには、ドメインでの GMSA のサポートなど、いくつかの機能強化が含まれています。これにより、セキュリティが向上し、初期同期サイクルが改善され、大きいグループがサポートされます。 詳細については、リリース バージョンの[履歴](../app-provisioning/provisioning-agent-release-version-history.md)を参照してください。 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>BitLocker 回復キー API のエンドポイントが現在は /informationProtection の下に配置される

**種類:** 変更された機能  
**サービス カテゴリ:** デバイス アクセス管理  
**製品の機能:** デバイスのライフサイクル管理
 
以前は、/bitlocker のエンドポイントを使用して BitLocker キーを回復することができました。 このエンドポイントはいずれ非推奨になるため、お客様は /informationProtection の下に配置されるようになった API の使用を開始するようにしてください。 

これらの変更を反映するためのドキュメントの更新については、[BitLocker 回復 API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) に関する記事を参照してください。

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>リモート デスクトップ サービス HTML5 Web クライアントに対するアプリケーション プロキシ サポートの一般提供

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御
 
Azure AD リモート デスクトップ サービス (RDS) Web クライアントに対する Azure AD アプリケーション プロキシのサポートが、一般提供されるようになりました。 RDS Web クライアントを使用すると、ユーザーは、Microsoft Edge、Internet Explorer 11、Google Chrome などの任意の HTML5 対応ブラウザーを使用して、リモート デスクトップ インフラストラクチャにアクセスできます。 ユーザーは、どこからでもローカル デバイスと同様にリモート アプリやデスクトップと対話できます。 

Azure AD アプリケーション プロキシを使用すると、すべての種類のリッチ クライアント アプリに対して事前認証と条件付きアクセス ポリシーを適用することにより、RDS デプロイのセキュリティを強化できます。 詳細については、「[Azure AD アプリケーション プロキシを使用したリモート デスクトップの発行](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)」を参照してください
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>新しい機能強化された動的グループ サービス (パブリック プレビュー)

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション
 
機能強化された動的グループ サービスがパブリック プレビューになりました。 テナントに動的グループを作成する新しいお客様は、この新しいサービスを使用することになります。 動的グループの作成に必要な時間は、テナントのサイズではなく、作成されるグループのサイズに比例します。 この更新により、お客様がより小さいグループを作成した場合に、大きいテナントのパフォーマンスが大幅に向上します。 

また、この新しいサービスでは、属性が変更されたことによるメンバーの追加と削除を数分以内に完了することを目標としています。 1 つの処理エラーによってテナントの処理がブロックされることもありません。 動的グループの作成の詳細については、[ドキュメント](../enterprise-users/groups-create-rule.md)を参照してください。
 
---
## <a name="october-2020"></a>2020 年 10 月

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD オンプレミス ハイブリッド エージェントに影響する Azure TLS 証明書の変更

**種類:** 変更の計画  
**サービス カテゴリ:** 該当なし  
**製品の機能:** プラットフォーム

Microsoft では、異なるルート証明機関 (CA) のセットからの TLS 証明書を使用するように、Azure サービスが更新されています。 この更新は、現在の CA 証明書が CA/ブラウザー フォーラムのベースライン要件の 1 つに準拠していないことによるものです。 この変更は、オンプレミスにインストールされていて、ルート証明書の固定リストを使用して環境を強化している Azure AD ハイブリッド エージェントに影響します。それらは新しい証明書発行者を信頼するように更新する必要があります。

すぐに対処しないと、この変更によってサービスが中断されます。 これらのエージェントには、オンプレミスへのリモート アクセス用の[アプリケーション プロキシ コネクタ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy)、ユーザーが同じパスワードを使用してアプリケーションにサインインできるようにする[パススルー認証](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)エージェント、AD から Azure AD への同期を実行する[クラウド プロビジョニング プレビュー](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) エージェントが含まれます。 

ファイアウォール規則を使用している環境で、特定の証明書失効リスト (CRL) のダウンロードに限定して送信呼び出しを許可するように設定している場合は、次の CRL と OCSP の URL を許可する必要があります。 この変更およびアクセスを有効にする CRL と OCSP の URL の詳細については、「[Azure TLS 証明書の変更](../../security/fundamentals/tls-certificate-changes.md)」を参照してください。

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>プロビジョニング イベントが監査ログから削除されてプロビジョニング ログのみに公開されます

**種類:** 変更の計画  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
SCIM [プロビジョニング サービス](../app-provisioning/user-provisioning.md)によるアクティビティは、監査ログとプロビジョニング ログの両方に記録されています。 これには、ServiceNow でのユーザーの作成、G Suite でのグループ化、AWS からのロールのインポートなどのアクティビティが含まれます。 今後、これらのイベントはプロビジョニング ログでのみ公開されます。 この変更は、ログ間の重複イベントと、ログ分析でログを使用しているお客様に発生する追加のコストを防ぐために実装されます。 

日付が完了したときに更新プログラムが提供されます。 この非推奨化は、2020 カレンダー年には計画されていません。 

> [!NOTE]
> これは、プロビジョニング サービスによって出力された監査ログ内の同期イベント以外のイベントには影響しません。 アプリケーション、条件付きアクセス ポリシー、ディレクトリ内のユーザーなどの作成といったイベントは、引き続き監査ログに出力されます。 [詳細については、こちらを参照してください](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context)。
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD オンプレミス ハイブリッド エージェントに影響する Azure トランスポート層セキュリティ (TLS) 証明書の変更

**種類:** 変更の計画  
**サービス カテゴリ:** 該当なし  
**製品の機能:** プラットフォーム
 
Microsoft では、異なるルート証明機関 (CA) のセットからの TLS 証明書を使用するように、Azure サービスが更新されています。 現在の CA 証明書が CA/ブラウザー フォーラムのベースライン要件の 1 つに準拠していないため、更新が行われます。 この変更は、オンプレミスにインストールされていて、ルート証明書の固定リストを使用して環境を強化している Azure AD ハイブリッド エージェントに影響します。 これらのエージェントは、新しい証明書発行者を信頼するように更新する必要があります。

すぐに対処しないと、この変更によってサービスが中断されます。 次のエージェントが含まれます。 
- オンプレミスへのリモート アクセス用の[アプリケーション プロキシ コネクタ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- ユーザーが同じパスワードを使用してアプリケーションにサインインできるようにする[パススルー認証](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect)エージェント
- AD から Azure AD への同期を行う[クラウド プロビジョニング プレビュー](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) エージェント 

ファイアウォール規則を使用している環境で、特定の証明書失効リスト (CRL) のダウンロードに限定して送信呼び出しを許可するように設定している場合は、CRL と OCSP の URL を許可する必要があります。 この変更およびアクセスを有効にする CRL と OCSP の URL の詳細については、「[Azure TLS 証明書の変更](../../security/fundamentals/tls-certificate-changes.md)」を参照してください。
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>US Gov クラウドでの Azure Active Directory TLS 1.0、TLS 1.1、3DES の非推奨化

**種類:** 変更の計画  
**サービス カテゴリ:** すべての Azure AD アプリケーション  
**製品の機能:** 標準
 
2021 年 3 月 31 日までに Azure Active Directory で次のプロトコルが非推奨になります。
- TLS 1.0
- TLS 1.1
- 3DES 暗号スイート (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Azure、Office 365、Microsoft 365 サービスの Azure Active Directory へのセキュリティで保護された接続を維持するために、クライアントとサーバーおよびブラウザーとサーバーのすべての組み合わせで、TLS 1.2 と最新の暗号スイートを使用する必要があります。

影響を受ける環境は次のとおりです。
- Azure US Gov
- [Office 365 GCC High および DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>AU およびオブジェクト スコープのロールへのアプリケーションの割り当て

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
この機能を使用すると、管理単位のスコープで管理者ロールにアプリケーション (SPN) を割り当てることができます。 詳細については、「[スコープ付きロールを管理単位に割り当てる](../roles/admin-units-assign-roles.md)」を参照してください。

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>リソースへのアクセスが拒否された場合にゲスト ユーザーを無効化して削除できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
無効化して削除は、組織がグループとアプリで外部のゲストをより適切に管理するのに役立つ、Azure AD アクセス レビューの高度なコントロールです。 ゲストがアクセス レビューで拒否された場合、**無効化して削除** により、30 日間サインインをブロックできます。 30 日が経過すると、テナントから完全に削除されます。

この機能の詳細については、「[Azure AD アクセス レビューを使用して外部 ID の無効化および削除を行う](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews)」を参照してください。
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>アクセス レビューの作成者がレビュー担当者への電子メールにカスタム メッセージを追加できる

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
Azure AD アクセス レビューで管理者がレビューを作成す場合に、レビュー担当者宛てにカスタム メッセージを書くことができるようになりました。 レビュー担当者は、受信した電子メールで、レビューを完了するように求めるメッセージを確認できます。 この機能の使用方法の詳細については、「[1 つ以上のアクセス レビューを作成する](../governance/create-access-review.md#create-one-or-more-access-reviews)」セクションの手順 14 を参照してください。

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 10 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C の統合アシスタント

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
Azure AD B2C アプリの登録で統合アシスタント (プレビュー) エクスペリエンスを使用できるようになりました。 このエクスペリエンスは、一般的なシナリオ用にアプリケーションを構成するときに役立ちます。 「[Microsoft ID プラットフォームのベスト プラクティスと推奨事項](../develop/identity-platform-integration-checklist.md)」の詳細を参照してください。
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Azure portal UI でロール テンプレート ID を表示する

**種類:** 新機能  
**サービス カテゴリ:** Azure ロール  
**製品の機能:** アクセス制御
 

Azure portal 内で各 Azure AD ロールのテンプレート ID を表示できるようになりました。 Azure AD で、選択したロールの **[説明]** を選択します。 

お客様には、PowerShell スクリプトとコードで表示名の代わりにロール テンプレート ID を使用することをお勧めします。 ロール テンプレート ID は、[directoryRoles](/graph/api/resources/directoryrole) および [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) オブジェクトに使用できるようにサポートされています。 ロール テンプレート ID の詳細については、「[Azure AD の組み込みロール](../roles/permissions-reference.md)」を参照してください。

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Azure AD B2C サインアップ ユーザー フロー用 API コネクタがパブリック プレビューになりました

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 

API コネクタを Azure Active Directory B2C で使用できるようになりました。 API コネクタを使用すると、Web API を使用してサインアップ ユーザー フローをカスタマイズし、外部クラウド システムと統合することができます。 API コネクタを使用して次のことができます。

- カスタム承認ワークフローと統合する
- ユーザー入力データを検証する
- ユーザー属性を上書する 
- カスタム ビジネス ロジックを実行する 

 詳細については、[API コネクタを使用したサインアップのカスタマイズおよび拡張](../../active-directory-b2c/api-connectors-overview.md)に関するドキュメントを参照してください。

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>エンタイトルメント管理での接続されている組織の状態プロパティ

**種類:** 新機能  
**サービス カテゴリ:** ディレクトリ管理 **製品の機能:** エンタイトルメント管理
 

 接続されているすべての組織に、"状態" というプロパティが追加されています。 この状態を使用すると、"構成済みの接続されたすべての組織" を参照するポリシーで、接続されている組織がどのように使用されるかを制御できます。 値は "構成済み" (組織が "all" 句を使用するポリシーのスコープ内にあることを意味します) または "提案済み" (組織がスコープ外であることを意味します) のいずれかになります。  

手動で作成した接続されている組織の既定の設定は "構成済み" になります。 また、自動作成された (インターネットからあらゆるユーザーがアクセスを要求できるポリシーを使用して作成された) ものは、既定で "提案済み" になります。  2020 年 9 月より前に作成されたすべての接続されている組織は、"構成済み" に設定されます。 管理者は、必要に応じてこのプロパティを更新できます。 [詳細については、こちらを参照してください](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically)。
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory の外部 ID に B2C 用の Premium セキュリティ詳細設定が含まれるようになりました

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
ID 保護のリスクベースの条件付きアクセスとリスク検出機能が、[Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md) で利用できるようになりました。 これらの高度なセキュリティ機能により、お客様は次のことが可能になります。
- インテリジェントな分析情報を活用して、B2C アプリとエンド ユーザー アカウントにかかわるリスクを評価します。 検出には、特殊な移動、匿名 IP アドレス、マルウェアにリンクされた IP アドレス、Azure AD 脅威インテリジェンスが含まれます。 ポータルおよび API ベースのレポートも利用できます。
- B2C ユーザーに対するアダプティブ認証を構成することによって、リスクに自動的に対応します。 アプリの開発者と管理者は、検出されたユーザー リスク レベルに応じて多要素認証 (MFA) を要求したり、アクセスをブロックしたりすることによって、リアルタイムのリスクを軽減できます。また、場所、グループ、アプリに基づいて追加の制御を利用できます。
- Azure AD B2C のユーザー フローおよびカスタム ポリシーとの連携。 条件は、Azure AD B2C の組み込みユーザー フローからトリガーすることも、B2C カスタム ポリシーに組み込むこともできます。 B2C ユーザー フローの他の側面と同様に、エンド ユーザー エクスペリエンスのメッセージングもカスタマイズすることができます。 カスタマイズは、組織の考え方やブランド、対応策の選択肢によって異なります。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 10 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 10 月には、フェデレーションをサポートする次の 27 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Sentry](../saas-apps/sentry-tutorial.md)、[Bumblebee - Productivity Superapp](https://app.yellowmessenger.com/user/login)、[ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md)、[EAComposer](../saas-apps/eacomposer-tutorial.md)、[Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/)、[Zone Technologies Portal](https://portail.zonetechnologie.com/signin)、[Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md)、[Datawiza Access Broker](https://console.datawiza.com/)、[ZOKRI](https://app.zokri.com/)、[CheckProof](../saas-apps/checkproof-tutorial.md)、[Ecochallenge.org](https://events.ecochallenge.org/users/login)、[atSpoke](http://atspoke.com/login)、[Appointment Reminder](https://app.appointmentreminder.co.nz/account/login)、[Cloud.Market](https://cloud.market/)、[TravelPerk](../saas-apps/travelperk-tutorial.md)、[Greetly](https://app.greetly.com/)、[OrgVitality SSO](../saas-apps/orgvitality-sso-tutorial.md)、[Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md)、[Loop Flow CRM](../saas-apps/loop-flow-crm-tutorial.md)、[Starmind](../saas-apps/starmind-tutorial.md)、[Workstem](https://hrm.workstem.com/login)、[Retail Zipline](../saas-apps/retail-zipline-tutorial.md)、[Hoxhunt](../saas-apps/hoxhunt-tutorial.md)、[MEVISIO](../saas-apps/mevisio-tutorial.md)、[Samsara](../saas-apps/samsara-tutorial.md)、[Nimbus](../saas-apps/nimbus-tutorial.md)、[Pulse Secure virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>プロビジョニング ログをログ分析にストリーミングできるようになりました

**種類:** 新機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 

次のことを行うためにプロビジョニング ログをログ分析に公開します。
- プロビジョニング ログを 30 日以上保存する
- カスタムのアラートと通知を定義する
- ダッシュボードを構築してログを視覚化する
- 複雑なクエリを実行してログを分析する 

この機能の使用方法については、「[プロビジョニングを Azure Monitor ログと統合する方法の概要](../app-provisioning/application-provisioning-log-analytics.md)」を参照してください。
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>アプリケーション所有者がプロビジョニング ログを表示できるようになりました

**種類:** 変更された機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
プロビジョニング サービスによるアクティビティの監視と問題のトラブルシューティングをアプリケーション所有者が行うことを許可できるようになりました。特権ロールを付与する必要はなく、IT がボトルネックになることはありません。 [詳細については、こちらを参照してください](../reports-monitoring/concept-provisioning-logs.md)。
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>10 個の Azure Active Directory ロールの名前変更

**種類:** 変更された機能  
**サービス カテゴリ:** Azure ロール  
**製品の機能:** アクセス制御
 
一部の Azure Active Directory (AD) の組み込みロールには、Microsoft 365 管理センター、Azure AD ポータル、Microsoft Graph に表示される名前とは異なる名前が付いています。 この不整合によって、自動化されたプロセスで問題が発生する可能性があります。 この更新によって 10 個のロールの名前を変更し、一貫性を確保します。 次の表に新しいロール名を示します。

![MS Graph API と Azure portal のロール名、および M365 管理センター、Azure portal、API で提案された新しいロール名を示す表。](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>MSAL JS 2.x を使用した SPA の認証コード フローに対する Azure AD B2C サポート

**種類:** 変更された機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
MSAL.js バージョン 2.x でシングル ページ Web アプリ (SPA) の承認コード フローがサポートされるようになりました。 Azure AD B2C において Azure portal で SPA アプリの種類を使用できるようになりました。また、シングル ページ アプリを対象として、PKCE での MSAL.js 承認コード フローの使用がサポートされるようになりました。 これにより、Azure AD B2C を使用している SPA において、新しいブラウザーでの SSO を維持し、新しい認証プロトコルの推奨事項を遵守することができます。 「[Azure Active Directory B2C にシングルページ アプリケーション (SPA) を登録する](../../active-directory-b2c/tutorial-register-spa.md)」のチュートリアルから開始してください。

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>信頼されたデバイス設定での Multi-Factor Authentication (MFA) の記憶に対する更新

**種類:** 変更された機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 

信頼済みデバイス上での [Multi-Factor Authentication (MFA) の記憶](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication)機能に対する最近の更新によって、認証が最大 365 日間に延長されました。 Azure Active Directory (Azure AD) Premium ライセンスの場合は、[条件付きアクセス – サインイン頻度ポリシー](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)も使用できます。これにより、再認証の設定がより柔軟になります。

最適なユーザー エクスペリエンスのためには、信頼済みデバイスで MFA を記憶する設定の代わりに、条件付きアクセスのサインイン頻度を使用して、信頼済みのデバイスや場所、リスクの低いセッションでのセッションの有効期間を延長することをお勧めします。 最初に、[再認証エクスペリエンスの最適化に関する最新のガイダンス](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)を確認してください。

---

## <a name="september-2020"></a>2020 年 9 月

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot Security Awareness](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関するページをご覧ください。
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>クラウド プロビジョニングのパブリック プレビューの更新

**種類:** 新機能  
**サービス カテゴリ:** Azure AD クラウド プロビジョニング **製品の機能:** ID ライフサイクル管理
 
Azure AD Connect クラウド プロビジョニングのパブリック プレビューの更新には、お客様からのフィードバックによって開発された 2 つの主な機能強化が取り入れられています。 

- Azure Portal を使用した属性マッピングのエクスペリエンス

    この機能を使用すると、IT 管理者は、現在提供されているさまざまなマッピングの種類を使用して、AD から Azure AD にユーザー、グループ、または連絡先の属性をマップできます。 属性マッピングは、Active Directory から Azure Active Directory にフローする属性の値を標準化するために使用される機能です。 属性値を AD から Azure AD に直接マップするか、ユーザーのプロビジョニング時に式を使用して属性値を変換するかを決定できます。 [詳細情報](../cloud-sync/how-to-attribute-mapping.md)

- オンデマンド プロビジョニングまたはユーザー エクスペリエンスのテスト

    構成を設定したら、スコープ内のすべてのユーザーにそれを適用する前に、ユーザー変換が想定どおりに動作するかどうかをテストできます。 オンデマンド プロビジョニングを使用すると、IT 管理者は、AD ユーザーの識別名 (DN) を入力し、それらが想定どおりに同期されるかどうかを確認できます。 オンデマンド プロビジョニングを使用すると、以前に行っていた属性マッピングを想定どおりに動作させるための優れた方法が提供されます。 [詳細情報](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Azure AD での監査された BitLocker の回復 - パブリック プレビュー

**種類:** 新機能  
**サービス カテゴリ:** デバイス アクセス管理  
**製品の機能:** デバイスのライフサイクル管理
 
IT 管理者またはエンド ユーザーがアクセスできる BitLocker 回復キーを読み取ると、Azure Active Directory によって、回復キーにアクセスしたユーザーをキャプチャする監査ログが生成されるようになりました。 同じ監査で、BitLocker キーが関連付けられていたデバイスの詳細が提供されます。

エンド ユーザーは、[マイ アカウントを使用して回復キーにアクセスする](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key)ことができます。 IT 管理者は、[BitLocker 回復キー API (ベータ)](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) または Azure AD ポータルを使用して、回復キーにアクセスできます。 詳細については、[Azure AD ポータルでの BitLocker キーの表示またはコピー](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)に関する記事を参照してください。

---

### <a name="teams-devices-administrator-built-in-role"></a>Teams デバイス管理者の組み込みロール

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
[Teams デバイス管理者](../roles/permissions-reference.md#teams-devices-administrator)ロールを持つユーザーは、Teams 管理センターから [Teams 認定デバイス](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices)を管理できます。 

このロールを持つユーザーは、すべてのデバイスを一目で見ることができ、デバイスの検索とフィルター処理を行えます。 また、ユーザーは、ログイン アカウント、デバイスの製造元やモデルなど、各デバイスの詳細を確認できます。 ユーザーは、デバイスの設定を変更したり、ソフトウェアのバージョンを更新したりできます。 このロールにより、Teams アクティビティとデバイスの通話品質を調べるアクセス許可は付与されません。
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>ディレクトリ オブジェクトの高度なクエリ機能

**種類:** 新機能  
**サービス カテゴリ:** MS Graph  
**製品の機能:** 開発者エクスペリエンス
 
Azure AD API でディレクトリ オブジェクトに対して導入された新しいクエリ機能はすべて、v1.0 エンドポイントと運用対応で使用できるようになりました。 開発者は、標準の OData 演算子を使用して、ディレクトリ オブジェクトと関連リンクのカウント、検索、フィルター処理、および並べ替えを行うことができます。

詳細については、[こちら](https://aka.ms/BlogPostMezzoGA)のドキュメントを参照してください。また、この[簡単なアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)でフィードバックを送ることもできます。
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>パブリック プレビュー: 条件付きアクセス ポリシーを構成したテナントの継続的アクセス評価

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ID のセキュリティ & 保護
 
条件付きアクセス ポリシーを使用する Azure AD テナントに対し、継続的アクセス評価 (CAE) をパブリック プレビューで使用できるようになりました。 CAE を使用すると、重要なセキュリティ イベントとポリシーがリアルタイムで評価されます。 これには、アカウントの無効化、パスワードのリセット、場所の変更などが含まれます。 詳細については、「[継続的アクセス評価](../conditional-access/concept-continuous-access-evaluation.md)」を参照してください。

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>パブリック プレビュー: アクセス パッケージを要求しているユーザーに対し、承認の決定を向上させるための追加の質問をする

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
管理者は、Azure AD エンタイトルメント管理のマイ アクセス ポータルで、アクセス パッケージを要求しているユーザーに、業務上の理由だけでなく、追加の質問に回答するよう要求できるようになりました。 これにより、ユーザーの回答が承認者に対して表示され、より正確なアクセス承認を決定できるようになります。 詳細については、「[承認のための追加の要求元情報を収集する (プレビュー)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)」を参照してください。
 
---

### <a name="public-preview-enhanced-user-management"></a>パブリック プレビュー: 強化されたユーザー管理

**種類:** 新機能  
**サービス カテゴリ:** [ユーザー管理]  
**製品の機能:** [ユーザー管理]
 

Azure AD ポータルが更新され、[すべてのユーザー] ページと [削除済みのユーザー] ページで、ユーザーを簡単に見つけられるようになりました。 このプレビューの変更点は次のとおりです。 
- オブジェクト ID、ディレクトリ同期の状態、作成タイプ、および ID 発行者を含むより多くのユーザー プロパティが表示されます。
- 検索で、名前、電子メール、およびオブジェクト ID を組み合わせて検索できるようになりました。
- ユーザーの種類 (メンバー、ゲスト、なし)、ディレクトリ同期の状態、作成タイプ、会社名、およびドメイン名によるフィルター処理が機能強化されました。
- 名前、ユーザー プリンシパル名、削除日などのプロパティに基づく新しい並べ替え機能。
- 検索またはフィルターによって更新される新しい合計ユーザー数。

詳細については、「[Azure Active Directory でのユーザー管理の機能強化 (プレビュー)](../enterprise-users/users-search-enhanced.md)」を参照してください。

---

### <a name="new-notes-field-for-enterprise-applications"></a>エンタープライズ アプリケーション用の新しいメモ フィールド

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション **製品の機能:** SSO

エンタープライズ アプリケーションに、フリー テキストのメモを追加できます。 エンタープライズ アプリケーションでのアプリケーションの管理に役立つ関連情報を追加できます。 詳細については、「[クイック スタート: Azure Active Directory (Azure AD) テナントのアプリケーションのプロパティを構成する](../manage-apps/add-application-portal-configure.md)」を参照してください。 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2020 年 9 月には、フェデレーションをサポートする次の 34 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[VMware Horizon - Unified Access Gateway]()、[Pulse Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md)、[Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md)、[Frontitude](https://services.enteksystems.de/sso/microsoft/signup)、[BookWidgets](https://www.bookwidgets.com/sso/office365)、[ZVD_Server](https://zaas.zenmutech.com/user/signin)、[HashData for Business](https://hashdata.app/login.xhtml)、[SecureLogin](https://securelogin.securelogin.nu/sso/azure/login)、[CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md)、[CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md)、[LimbleCMMS](https://auth.limblecmms.com/)、[Glint Inc](../saas-apps/glint-inc-tutorial.md)、[zeroheight](../saas-apps/zeroheight-tutorial.md)、[Gender Fitness](https://app.genderfitness.com/)、[Coeo Portal](https://my.coeo.com/)、[Grammarly](../saas-apps/grammarly-tutorial.md)、[Fivetran](../saas-apps/fivetran-tutorial.md)、[Kumolus](../saas-apps/kumolus-tutorial.md)、[RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md)、[TeamzSkill](../saas-apps/teamzskill-tutorial.md)、[raumfürraum](../saas-apps/raumfurraum-tutorial.md)、[Saviynt](../saas-apps/saviynt-tutorial.md)、[BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup)、[Mobile Locker](../saas-apps/mobile-locker-tutorial.md)、[Zengine](../saas-apps/zengine-tutorial.md)、[CloudCADI](https://app.cloudcadi.com/login)、[Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/)、[Priva Identity & Access Management](https://my.priva.com/)、[Nitro Pro](https://www.gonitro.com/nps/product-details/downloads)、[Eventfinity](../saas-apps/eventfinity-tutorial.md)、[Fexa](../saas-apps/fexa-tutorial.md)、[Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)、[Secured Signing Enterprise Portal AAD Setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)、[Wistec Online](https://wisteconline.com/auth/oidc)、[Oracle PeopleSoft - Protected by F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください。

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Azure AD エンタイトルメント管理での新しい委任ロール: アクセス パッケージ割り当てマネージャー

**種類:** 新機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 
Azure AD エンタイトルメント管理に、割り当てを管理するための細かなアクセス許可が提供される、新しいアクセス パッケージ割り当てマネージャー ロールが追加されました。 このロールでタスクを委任されたユーザーは、アクセス パッケージの割り当て管理をビジネス所有者に委任できます。 ただし、アクセス パッケージ割り当てマネージャーは、アクセス パッケージのポリシーや、管理者によって設定されたその他のプロパティを、変更することはできません。 

この新しいロールを使用すると、割り当ての管理を委任し、他のすべてのアクセス パッケージ構成の管理制御を維持するために必要な、最小限の特権を実現できます。 詳細については、「[エンタイトルメント管理のロール](../governance/entitlement-management-delegate.md#entitlement-management-roles)」を参照してください。
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Privileged Identity Management のオンボード フローに対する変更

**種類:** 変更された機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
以前は、Privileged Identity Management (PIM) にオンボードするには、ユーザーの同意と、Azure AD MFA への登録が含まれる PIM のブレードでのオンボード フローが必要でした。 Azure AD の [ロールと管理者] ブレードへの PIM エクスペリエンスの最近の統合により、このエクスペリエンスは削除されています。 有効な P2 ライセンスを持つテナントは、PIM に自動的にオンボードされます。

PIM へのオンボードにより、テナントが直接悪影響を受けることはありません。 次の変更が予想されます。
- PIM または Azure AD の [ロールと管理者] ブレードで割り当てを行うときに、開始時刻と終了時刻でアクティブか有資格かなどの、追加の割り当てオプション。 
- 割り当てエクスペリエンスに直接導入された、管理単位やカスタム役割などの、追加のスコープ メカニズム。 
- 全体管理者または特権ロール管理者の場合は、PIM の週刊ダイジェストなど、いくつかの追加メールの受け取りを始めることができます。 
- さらに、ロールの割り当てに関連する監査ログに MS-PIM サービス プリンシパルが表示される場合もあります。 この予想される変更は、通常のワークフローには影響しません。

 詳細については、「[Privileged Identity Management の使用開始](../privileged-identity-management/pim-getting-started.md)」を参照してください。

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD エンタイトルメント管理: 選択されたカタログに現在含まれるリソースが、アクセス パッケージ リソースの [選択] ペインに既定で表示されるようになる

**種類:** 変更された機能  
**サービス カテゴリ:** ユーザー アクセス管理  
**製品の機能:** エンタイトルメント管理
 

アクセス パッケージ作成フローの [リソース ロール] タブで、[選択] ペインの動作が変更されています。 現在の既定の動作の場合、ユーザーによって所有されているすべてのリソースと、選択したカタログに追加されたリソースが表示されます。 

このエクスペリエンスは、カタログに現在追加されているリソースのみが既定で表示されるように変更されます。これにより、ユーザーはカタログからリソースを簡単に選択できるようになります。 この更新により、アクセス パッケージに追加するリソースを見つけやすくなり、カタログに含まれていないユーザーが所有するリソースを誤って追加するリスクが減ります。 詳細については、「[Azure AD エンタイトルメント管理で新しいアクセス パッケージを作成する](../governance/entitlement-management-access-package-create.md#resource-roles)」を参照してください。
 
---