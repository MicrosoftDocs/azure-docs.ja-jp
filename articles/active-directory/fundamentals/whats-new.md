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
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6694043f083de30f3bca10051fc7012ef1471c37
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798819"
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

## <a name="july-2020"></a>2020 年 7 月

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>IT 管理者は、条件付きアクセスを使用してクライアント アプリを対象にする必要があります

**種類:** 変更の計画   
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
条件付きアクセスでのクライアント アプリ条件の GA リリースにより、新しいポリシーが既定で、レガシ認証クライアントを含むすべてのクライアント アプリケーションに新しいポリシーが適用されるようになりました。 既存のポリシーは変更されませんが、ポリシーによって提供されるクライアント アプリを簡単に確認できるように、"*構成の "はい、いいえ"* " の切り替えは既存のポリシーから削除されます。 

新しいポリシーを作成するときは、従来の認証をまだ使用しているユーザーとサービス アカウントを除外してください。そうしないと、ブロックされます。 [詳細については、こちらを参照してください](https://aka.ms/caclientapps)。
 
---

### <a name="upcoming-scim-compliance-fixes"></a>今後の SCIM コンプライアンスの修正

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Azure AD プロビジョニング サービスでは、アプリケーションとの統合のために SCIM 標準が利用されています。 SCIM 標準の実装は進化しており、PATCH 操作の実行方法に関する変更や、リソースでのプロパティ "アクティブ" の設定が想定されています。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility)。
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure 管理者ポータルのグループ所有者の設定が変更される

**種類:** 変更の計画  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

グループの全般設定ページの所有者の設定は、Azure 管理者ポータルとアクセス パネルで制限されたユーザー グループに対して所有者割り当て特権を制限するように構成できます。 間もなく、この 2 つの UX ポータルでグループ所有者特権を割り当てることができるだけでなく、PowerShell や Microsoft Graph など、バックエンドでポリシーを適用してエンドポイント間で一貫性のある動作を実現することもできるようになります。 

現在の設定を使用していないお客様については現在の設定を無効にする作業が始められており、今後数か月の間に、グループ所有者特権に対してユーザーを対象にするオプションが提供されます。 グループ設定の更新に関するガイダンスについては、[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context) を使用したグループ情報の編集に関するページを参照してください。

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 登録サービスでの TLS 1.0 および 1.1 のサポートが終了します

**種類:** 変更の計画  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** プラットフォーム

トランスポート層セキュリティ (TLS) 1.2 およびサーバーとクライアントの更新は、間もなく Azure Active Directory Device Registration サービスと通信するようになります。 Azure AD Device Registration サービスとの通信用の TLS 1.0 および 1.1 のサポートは廃止されます。
- 2020 年 8 月 31 日、すべてのソブリン クラウド (GCC High、DoD など)
- 2020 年 10 月 30 日、すべての商用クラウド

Azure AD 登録サービスに対する TLS 1.2 について[詳細を確認してください](https://docs.microsoft.com/azure/active-directory/devices/reference-device-registration-tls-1-2)。

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD サインイン ログで確認できる Windows Hello for Business のサインイン

**種類:** 固定  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
Windows Hello for Business を使用すると、エンド ユーザーはジェスチャ (PIN や生体認証など) を使用して Windows コンピューターにサインインできます。 Azure AD 管理者は、組織のパスワードレス認証への移行の過程として、Windows Hello for Business のサインインを他の Windows サインインと区別することができます。 

管理者は、Azure portal の Azure AD サインイン ブレードで Windows サインイン イベントに対する [認証の詳細] タブを調べることにより、Windows 認証で Windows Hello for Business が使用されたかどうかを確認できます。 Windows Hello for Business 認証では、[認証方法] フィールドに "WindowsHelloForBusiness" が含まれます。 サインイン ログの解釈の詳細については、[サインイン ログのドキュメント](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)を参照してください。
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>グループ削除の動作の修正とパフォーマンスの向上

**種類:** 固定  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
以前は、グループを "スコープ内" から "スコープ外" に変更し、変更が完了する前に管理者が再起動をクリックすると、グループ オブジェクトは削除されませんでした。 現在は、グループ オブジェクトがスコープ外になった (無効、削除済み、未割り当て、またはスコープ フィルターを通過しなかった) 時点で、ターゲット アプリケーションから削除されます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles)。
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>パブリック プレビュー:管理者は、アクセス レビューを作成するときに、レビュー担当者宛のメールにカスタム コンテンツを追加できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance
 
新しいアクセス レビューが作成されると、レビュー担当者は、アクセス レビューの完了を求めるメールを受け取ります。 多くのお客様から、連絡先情報などのカスタム コンテンツ、またはレビュー担当者に指示するための他のサポート コンテンツをメールに追加する機能の要望がありました。 

パブリック プレビューになった機能を使用すると、管理者は、Azure AD アクセス レビューの [詳細設定] セクションでコンテンツを追加することにより、レビュー担当者に送信されるメールでカスタム コンテンツを指定できます。 アクセス レビューの作成に関するガイダンスについては、「[グループとアプリケーションのアクセス レビューを Azure AD アクセス レビューで作成する](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)」を参照してください。
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>シングルページ アプリの承認コード フローを使用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 開発者エクスペリエンス
 
最新のブラウザーのサード パーティ Cookie の制限 (Safari ITP など) により、SPA では、SSO を維持するために、暗黙的フローではなく、承認コード フローを使用する必要があります。MSAL.js v 2.x では、認証コード フローをサポートするようになりました。 

Azure portal の対応する更新により、ご自分の SPA の種類を "spa" に更新し、認証コード フローを使用することができます。 詳細については、「[承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code)」を参照してください。
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD アプリケーション プロキシでリモート デスクトップ サービス Web クライアントがサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** アクセス制御

Azure AD アプリケーション プロキシでリモート デスクトップ サービス (RDS) Web クライアントがサポートされるようになりました。 RDS Web クライアントを使用すると、ユーザーは、Microsoft Edge、Internet Explorer 11、Google Chrome などの任意の HTML5 対応ブラウザーを使用して、リモート デスクトップ インフラストラクチャにアクセスできます。ユーザーは、どこからでもローカル デバイスと同様にリモート アプリやデスクトップと対話できます。 Azure AD アプリケーション プロキシを使用すると、すべての種類のリッチ クライアント アプリに対して事前認証と条件付きアクセス ポリシーを適用することにより、RDS デプロイのセキュリティを強化できます。 詳細については、「[Azure AD アプリケーション プロキシを使用したリモート デスクトップの発行](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services)」を参照してください。
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>次世代の Azure AD B2C ユーザー フローのパブリック プレビュー

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
簡素化されたユーザー フロー エクスペリエンスは、プレビュー機能と同等の機能を備えており、すべての新機能のホームとなります。 ユーザーは、同じユーザー フロー内で新機能を有効にすることができるため、新しい機能をリリースするたびに複数のバージョンを作成する必要が少なくなります。 最後に、新しいユーザー フレンドリな UX により、ユーザー フローの選択と作成が簡単になります。 今すぐ[ユーザー フローを作成](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)して試してください。 

ユーザー フローの詳細については、「[Azure Active Directory B2C のユーザー フロー バージョン](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-versions#:~:text=%20%20%20%20User%20flow%20%20%2caccount.%20Usi%20...%20%201%20more%20rows%20)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 7 月には、フェデレーションをサポートする次の 55 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Clap Your Hands](http://www.rmit.com.ar/)、[Appreiz](https://microsoftteams.appreiz.com/)、[Inextor Vault](https://inexto.com/inexto-suite/inextor)、[Beekast](https://my.beekast.com/)、[Templafy OpenID Connect](https://app.templafy.com/)、[PeterConnects receptionist](https://msteams.peterconnects.com/)、[AlohaCloud](https://appfusions.alohacloud.com/auth)、[Control Tower](https://bpm.tnxcorp.com/sso/microsoft)、[Cocoom](https://start.cocoom.com/)、[COINS Construction Cloud](https://sso.coinsconstructioncloud.com/#login/)、[Medxnote MT](https://task.teamsmain.medx.im/authorization)、[Reflekt](https://reflekt.konsolute.com/login)、[Rever](https://app.reverscore.net/access)、[MyCompanyArchive](https://login.mycompanyarchive.com/)、[GReminders](https://app.greminders.com/o365-oauth)、[Titanfile](https://docs.microsoft.com/azure/active-directory/saas-apps/titanfile-tutorial)、[Wootric](https://docs.microsoft.com/azure/active-directory/saas-apps/wootric-tutorial)、[SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US)、[OpenText Directory Services](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial)、[Datasite](https://docs.microsoft.com/azure/active-directory/saas-apps/datasite-tutorial)、[BlogIn](https://docs.microsoft.com/azure/active-directory/saas-apps/blogin-tutorial)、[IntSights](https://docs.microsoft.com/azure/active-directory/saas-apps/intsights-tutorial)、[kpifire](https://docs.microsoft.com/azure/active-directory/saas-apps/kpifire-tutorial)、[Textline](https://docs.microsoft.com/azure/active-directory/saas-apps/textline-tutorial)、[Cloud Academy - SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-academy-sso-tutorial)、[Community Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/community-spark-tutorial)、[Chatwork](https://docs.microsoft.com/azure/active-directory/saas-apps/chatwork-tutorial)、[CloudSign](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudsign-tutorial)、[C3M Cloud Control](https://docs.microsoft.com/azure/active-directory/saas-apps/c3m-cloud-control-tutorial)、[SmartHR](https://smarthr.jp/)、[NumlyEngage™](https://docs.microsoft.com/azure/active-directory/saas-apps/numlyengage-tutorial)、[Michigan Data Hub Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/michigan-data-hub-single-sign-on-tutorial)、[Egress](https://docs.microsoft.com/azure/active-directory/saas-apps/egress-tutorial)、[SendSafely](https://docs.microsoft.com/azure/active-directory/saas-apps/sendsafely-tutorial)、[Eletive](https://app.eletive.com/)、[Right-Hand Cybersecurity ADI](https://right-hand.ai/)、[Fyde Enterprise Authentication](https://enterprise.fyde.com/)、[Verme](https://docs.microsoft.com/azure/active-directory/saas-apps/verme-tutorial)、[Lenses.io](https://docs.microsoft.com/azure/active-directory/saas-apps/lensesio-tutorial)、[Momenta](https://docs.microsoft.com/azure/active-directory/saas-apps/momenta-tutorial)、[Uprise](https://app.uprise.co/sign-in)、[Q](https://q.moduleq.com/login)、[CloudCords](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudcords-tutorial)、[TellMe Bot](https://tellme365liteweb.azurewebsites.net/)、[Inspire](https://app.inspiresoftware.com/)、[Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/)、[Smartschool (School Management System)](https://smart-schoolapp.com/frmLoginForm)、[Zepto - Intelligent timekeeping](https://user.zepto-ai.com/signin)、[Studi.ly](https://studi.ly/)、[Trackplan](http://www.trackplanfm.com/)、[Skedda](https://docs.microsoft.com/azure/active-directory/saas-apps/skedda-tutorial)、[WhosOnLocation](https://docs.microsoft.com/azure/active-directory/saas-apps/whos-on-location-tutorial)、[Coggle](https://docs.microsoft.com/azure/active-directory/saas-apps/coggle-tutorial)、[Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/)、[BrowserStack Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/browserstack-single-sign-on-tutorial)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください

Azure AD アプリ ギャラリーにアプリケーションを公開する場合は、こちらの詳細 (https://aka.ms/AzureADAppRequest ) をお読みください

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合

新しく統合されたアプリ [LinkedIn Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/linkedin-learning-provisioning-tutorial) でのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページをご覧ください。

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>すべてのスコープでロールの割り当てを表示し、それらを csv ファイルにダウンロードする機能

**種類:** 変更された機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** アクセス制御
 
Azure AD ポータルの [ロールと管理者] タブで、ロールのすべてのスコープについてロールの割り当てを表示できるようになりました。 また、ロールごとにそれらのロール割り当てを CSV ファイルにダウンロードすることもできます。 ロール割り当ての表示と追加に関するガイダンスについては、「[Azure Active Directory で管理者ロールを表示して割り当てる](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)」を参照してください。
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>非推奨になった Azure Multi-Factor Authentication ソフトウェア開発 (Azure MFA SDK)

**種類:** 非推奨  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護
 
2017 年 11 月に最初に発表されたように、2018 年 11 月 14 日をもって Azure Multi-Factor Authentication ソフトウェア開発 (Azure MFA SDK) の提供は終了しました。 Microsoft は、2020 年 9 月 30 日に有効な SDK サービスを終了します。 SDK に対するすべての呼び出しは失敗します。

組織で Azure MFA SDK を使用している場合は、2020 年 9 月 30 日までに移行する必要があります。
- MIM に対する Azure MFA SDK: MIM で SDK を使用している場合は、Azure MFA Server に移行し、次の[の手順](https://docs.microsoft.com/microsoft-identity-manager/working-with-mfaserver-for-mim)に従って Privileged Access Management (PAM) をアクティブ化する必要があります。   
- カスタマイズされたアプリに対する Azure MFA SDK: アプリを Azure AD に統合し、条件付きアクセスを使用して MFA を適用することを検討してください。 始めるには、こちらの[ページ](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)を確認してください。 

---

## <a name="june-2020"></a>2020 年 6 月 

### <a name="user-risk-condition-in-conditional-access-policy"></a>条件付きアクセス ポリシーのユーザー リスク条件

**種類:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 

Azure AD 条件付きアクセス ポリシーのユーザー リスク サポートを使用すると、複数のユーザー リスクベースのポリシーを作成できます。 ユーザーやアプリごとに、必要な最小ユーザー リスク レベルが異なる可能性があります。 ユーザー リスクに基づいて、アクセスをブロックしたり、多要素認証を要求したり、パスワードの変更をセキュリティで保護したり、Microsoft Cloud App Security にリダイレクトして追加監査などのセッション ポリシーを強制したりするポリシーを作成できます。

ユーザー リスク条件には、Azure AD Premium P2 が必要です。P2 オファリングである Azure Identity Protection が使用されるためです。 条件付きアクセスの詳細については、[Azure AD 条件付きアクセスのドキュメント](https://docs.microsoft.com/azure/active-directory/conditional-access/)を参照してください。

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>要求時に SPNameQualifier を設定する必要があるアプリが SAML SSOでサポートされるようになりました

**種類:** 固定  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
一部の SAML アプリケーションでは、要求時にアサーション サブジェクトで SPNameQualifier が返される必要があります。 要求 NameID ポリシーで SPNameQualifier が要求されたときに、Azure AD が正しく応答するようになりました。 これは、SP initiated サインインでも機能します。また、IdP initiated サインインでも機能するようになる予定です。  Azure Active Directory での SAML プロトコルの詳細については、「[シングル サインオンの SAML プロトコル](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)」を参照してください。

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B Collaboration により Azure Government テナントでの MSA および Google ユーザーの招待がサポートされます

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

B2B Collaboration 機能を使用する Azure Government テナントは、Microsoft または Google アカウントを持つユーザーを招待できるようになりました。 お使いのテナントでこれらの機能を使用できるかどうかを確認するには、「[B2B コラボレーションが Azure US Government テナントで利用可能かどうかを確認するにはどうすればよいですか？](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)」の手順に従ってください。

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 のユーザー オブジェクトに externalUserState と externalUserStateChangedDateTime プロパティが含まれるようになりました

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

externalUserState と externalUserStateChangedDateTime プロパティを使用すると、招待したがまだ招待を受諾していない B2B ゲストを検索できます。また、数日間経過した後にまだ招待を受諾していないユーザーを削除するなどのビルド自動化を行うことができます。 これらのプロパティが、MS Graph v1 で使用できるようになりました。 これらのプロパティの使用方法については、[ユーザー リソースの種類](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)に関するページを参照してください。
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD 条件付きアクセスでの認証セッションの管理が一般提供されるようになりました

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
認証セッションの管理機能を使用すると、ユーザーがサインイン資格情報を提供する必要がある頻度と、ブラウザーを閉じて再び開いた後に資格情報を提供する必要があるかどうかを構成することで、環境内でより高いセキュリティと柔軟性を提供できます。
 
また、認証セッション管理は以前、Azure AD 参加済み、Hybrid Azure AD 参加済み、および Azure AD 登録済みのデバイスでの第一要素認証のみに適用されていました。 現在、認証セッション管理は MFA にも適用されるようになりました。 詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD アプリケーション ギャラリーで新しいフェデレーション アプリが利用できるようになりました - 2020 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 6 月、フェデレーションをサポートする次の 29 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial)、[Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial)、[MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial)、[BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial)、[Raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial)、[Segment](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial)、[Ai Auditor](https://www.mindbridge.ai/products/ai-auditor/)、[Pobuca Connect](https://app.pobu.ca/)、[Proto.io](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial)、[Gatekeeper](https://www.gatekeeperhq.com/)、[Hub Planner](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial)、[Ansira-Partner Go-to-Market Toolbox](https://ansira.com/technology/channel-engagement)、[IBM Digital Business Automation on Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial)、[Kisi Physical Security](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial)、[ViewpointOne](https://team.viewpoint.com/)、[IntelligenceBank](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial)、[pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial)、[Zero](https://www.teamzero.com/)、[InStation](https://instation.invillia.com/)、[edX for Business SAML 2.0 Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial)、[MOOC Office 365](https://mooc.office365-training.com/en/)、[SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial)、[PKIsigning platform](https://platform.pkisigning.nl/)、[SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial)、[Field iD](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial)、[Curricula SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial)、[Perforce Helix Core - Helix Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial)、[MyCompliance Cloud](https://cloud.metacompliance.com/)、[Smallstep SSH](https://smallstep.com/sso-ssh/)  

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

API コネクタで可能なすべてのエクスペリエンスの詳細については、「[API コネクタを使用してセルフサービス サインアップをカスタマイズおよび拡張する](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)」、または[Web API 統合を使用した External Identities セルフサービス サインアップのカスタマイズ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)に関する記事を参照してください。
 
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
 
新しい委任されたアクセス許可 EntitlementManagement.Read.All を Microsoft Graph ベータ版のエンタイトルメント管理 API で使用できるようになりました。 使用可能な API の詳細については、[Azure AD エンタイトルメント管理 API の操作](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)に関する記事を参照してください。

---

### <a name="identity-protection-apis-available-in-v10"></a>Identity Protection API を v1.0 で利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護
 
riskyUsers および riskDetections Microsoft Graph API が一般提供されるようになりました。 v1.0 エンドポイントで利用できるようになったので、運用環境で使用することをお勧めします。 詳細については、[Microsoft Graph のドキュメント](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)を参照してください。
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Microsoft 365 グループにポリシーを適用するための秘密度ラベルが一般提供されるようになりました

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション
 

秘密度ラベルを作成し、そのラベルの設定を使用して、プライバシー (パブリックまたはプライベート) と外部ユーザー アクセス ポリシーを含むポリシーを Microsoft 365 グループに適用できるようになりました。 プライバシー ポリシーをプライベートに設定し、外部ユーザー アクセス ポリシーを、ゲスト ユーザーの追加を許可しないように設定したラベルを作成できます。 ユーザーがこのラベルをグループに適用すると、そのグループはプライベートになり、ゲスト ユーザーをグループに追加することは許可されません。 

秘密度ラベルは、ビジネスに不可欠なデータを保護するうえで重要であり、これを使用することにより、コンプライアンスに準拠しセキュリティで保護された方法で、大規模なグループを管理できます。 秘密度ラベルの使用のガイダンスについては、「[Azure Active Directory で Office 365 グループに秘密度ラベルを割り当てる (プレビュー)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels)」を参照してください。
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Azure AD Premium のお客様を対象とする Microsoft Identity Manager のサポートに対する更新

**種類:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理
 
Microsoft Identity Manager 2016 の延長サポートの終了により、Microsoft Identity Manager 2016 の Azure AD 統合コンポーネントに対して Azure サポートが利用できるようになりました。 詳細については、「[Microsoft Identity Manager を利用している Azure AD Premium のお客様向けの更新のサポート](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)」をお読みください。

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>SSO 要求構成でのグループ メンバーシップ条件の使用が増加しています

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
以前は、1 つのアプリケーション構成内で、グループ メンバーシップに基づいて条件付きで要求を変更するときに使用できるグループの数は、10 個に制限されていました。 SSO 要求構成でのグループ メンバーシップ条件の使用が、最大 50 グループに増加しました。 要求を構成する方法の詳細については、[エンタープライズ アプリケーションの SSO 要求の構成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions)に関する記事を参照してください。 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>会社のブランドのサインイン ページ テキスト コンポーネントにおける基本的な書式設定の有効化

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD/Microsoft 365 ログイン エクスペリエンスでの会社のブランド機能が更新され、ハイパーリンクや、太字、下線、斜体などの書式設定をお客様が追加できるようになりました。 この機能の使用方法については、「[組織の Azure Active Directory のサインイン ページにブランドを追加する](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)」を参照してください。

---

### <a name="provisioning-performance-improvements"></a>プロビジョニング パフォーマンスの向上

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
[増分サイクル](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles)が完了するまでの時間を短縮するようにプロビジョニング サービスが更新されました。 これは、ユーザーとグループが、以前よりも迅速にアプリケーションにプロビジョニングされることを意味します。 2020 年 6 月 10 日より後に作成されたすべての新しいプロビジョニング ジョブは、自動的にパフォーマンス向上の恩恵を受けます。 2020 年 6 月 10 日までにプロビジョニング用に構成されたアプリケーションでこのパフォーマンス向上を活用するためには、2020 年 6 月 10 日より後に 1 回再起動する必要があります。 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>ADAL と MS Graph パリティの非推奨の発表

**種類:** 非推奨  
**サービス カテゴリ:** 該当なし  
**製品の機能:** デバイスのライフサイクル管理

Microsoft 認証ライブラリ (MSAL) が利用できるようになったため、Azure Active Directory 認証ライブラリ (ADAL) への新機能の追加は行われなくなり、セキュリティ更新プログラムは 2022 年 6 月 30 日に終了となります。 MSAL への移行方法については、「[Microsoft Authentication Library (MSAL) へのアプリケーションの移行](https://docs.microsoft.com/azure/active-directory/develop/msal-migration)」を参照してください。

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
 

現時点では、Azure AD のフェデレーション ドメインに属していても、テナントに同期されていないユーザーは、Teams にアクセスできません。 6 月末から、この新機能により、既存のメール確認済みサインアップ機能を拡張することで、そうすることができるようになります。 これにより、フェデレーション IdP にサインインできても、まだ Azure ID 内にまだユーザー オブジェクトがないユーザーは、ユーザー オブジェクトが自動的に作成され、Teams に対して認証することができます。 ユーザー オブジェクトは、"セルフサービス サインアップ" としてマークされます。 これは、マネージド ドメイン内のユーザーが実行でき、同じフラグを使用して制御できる、メール確認済みセルフサインアップを実行するための、既存機能の拡張機能です。 この変更は、今後 2 か月の間にロールアウトが完了する予定です。 [こちら](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)のドキュメントの更新にご期待ください。
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>予定されている修正点: Azure Government クラウドの OIDC 検出ドキュメントが正しい Graph エンドポイントを参照するように更新されます。

**種類:** 変更の計画  
**サービス カテゴリ:** ソブリン クラウド  
**製品の機能:** ユーザー認証
 
6 月以降、[Azure Government クラウド](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) エンドポイント (login.microsoftonline.us) の OIDC 検出ドキュメント [Microsoft ID プラットフォームと OpenID Connect プロトコル](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)では、指定されたテナントに基づいて正しい[国内クラウド Graph](https://docs.microsoft.com/graph/deployments) エンドポイント (https://graph.microsoft.us または https://dod-graph.microsoft.us) ) が返されるようになります。  現在は、正しくない Graph エンドポイント (graph.microsoft.com) の "msgraph_host" フィールドが返されています。  

このバグ修正は約 2 か月の間に段階的にロールアウトされます。  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government ユーザーが login.microsoftonline.com にサインインできなくなります

**種類:** 変更の計画  
**サービス カテゴリ:** ソブリン クラウド  
**製品の機能:** ユーザー認証
 
2018 年 6 月 1 日、Azure Government に対する Azure Active Directory (AAD) の公式な機関が、 https://login-us.microsoftonline.com から https://login.microsoftonline.us に変更されました。 Azure Government テナント内でアプリケーションを所有している場合は、.us エンドポイント上でユーザーをサインインさせるようにご使用のアプリケーションを更新する必要があります。

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
 
IT 管理者は、Azure AD Connect クラウド プロビジョニングを設定するための最小特権ロールとして新しい "ハイブリッド管理" ロールの使用を開始できます。 この新しいロールにより、クラウド プロビジョニングの設定と構成に全体管理者ロールを使用する必要がなくなります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD アプリケーション ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 5 月、フェデレーションをサポートする次の 36 個の新しいアプリケーションがアプリ ギャラリーに追加されました。

[Moula](https://moula.com.au/pay/merchants)、[Surveypal](https://www.surveypal.com/app)、[Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/)、[TackleBox](http://www.tacklebox.app/)、[Powell Teams](https://powell-software.com/en/powell-teams-en/)、[Talentsoft Assistant](https://msteams.talent-soft.com/)、[ASC Recording Insights](https://teams.asc-recording.app/product)、[GO1](https://www.go1.com/)、[B-Engaged](https://b-engaged.se/)、[Competella Contact Center Workgroup](http://www.competella.com/)、[Asite](http://www.asite.com/)、[ImageSoft Identity](https://identity.imagesoftinc.com/)、[My IBISWorld](https://identity.imagesoftinc.com/)、[insuite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial)、[Change Process Management](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial)、[Cyara CX Assurance Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial)、[Smart Global Governance](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial)、[Prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial)、[Mapbox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial)、[Datava Enterprise Service Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial)、[Whimsical](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial)、[Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial)、[EasySSO for Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial)、[EasySSO for BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial)、[EasySSO for Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial)、[Torii](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial)、[Axiad Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial)、[Humanage](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial)、[ColorTokens ZTNA](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial)、[CCH Tagetik](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial)、[ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial)、[Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial)、[TextExpander](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial)、[Anyone Home CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial)、[askSpoke](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial)、[ice Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial)

すべてのアプリケーションのドキュメントについては、こちら (https://aka.ms/AppsTutorial ) をご覧ください。

Azure AD アプリ ギャラリー内でご自分のアプリケーションを公開する場合、詳細については、こちら (https://aka.ms/AzureADAppRequest ) をご覧ください。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件付きアクセスのレポート専用モードの一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

[Azure AD 条件付きアクセスのレポート専用モード](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only)を使用すると、アクセス制御を実施せずにポリシーの結果を評価できます。 組織全体でレポート専用ポリシーをテストし、その影響について理解してから有効にすることで、展開がより安全で簡単になります。 これまで数か月にわたって、私たちはレポート専用モードを広範に導入しており、既に 2,600 万を超えるユーザーがレポート専用ポリシーのスコープ内に存在しています。 本日のこの発表では、新しい Azure AD 条件付きアクセス ポリシーが、既定でレポート専用モードで作成されます。 これは、ポリシーが作成された時点からその影響を監視できることを意味します。 また、MS Graph API シリーズを使用しているユーザーは、[プログラムによってレポート専用ポリシーを管理する](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)こともできます。 

---

### <a name="self-service-sign-up-for-guest-users"></a>ゲスト ユーザーのセルフサービス サインアップ

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD 内で外部 ID を使用すると、ご自身の組織外のユーザーに組織のアプリとリソースへのアクセスを許可することができ、その際、外部ユーザーは希望する任意の ID を使用してサインインできます。 アプリケーションを外部ユーザーと共有する場合、アプリケーションにアクセスする必要があるユーザーが常に事前にわかっているとは限りません。 [セルフサービス サインアップ](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)を使用すると、ゲスト ユーザーがサインアップして、あなたの基幹業務 (LOB) アプリのゲスト アカウントを取得できるようになります。 サインアップ フローを作成して、Azure AD とソーシャル ID をサポートするようにカスタマイズできます。 サインアップ時にユーザーに関する追加情報を収集することもできます。

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件付きアクセスに関する分析情報とレポートのブックの一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

[分析情報とレポートのブック](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting)によって、管理者が各自のテナントの Azure AD 条件付きアクセスの概要ビューを得られます。 個々のポリシーを選択する機能によって、管理者が各ポリシーの動作をよりよく理解し、変更をリアルタイムで監視できます。 このブックにより、Azure Monitor に格納されているデータがストリームされます。これは、[こちらの手順に従って](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)数分で設定可能です。 ダッシュボードは、見つけやすくするために、Azure AD 条件付きアクセス メニュー内の新しい分析情報とレポートのタブに移動されました。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>条件付きアクセスのポリシー詳細ブレードのパブリック プレビュー開始

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

新しい[ポリシーの詳細ブレード](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access)には、条件付きアクセス ポリシーの評価中に満たされた割り当て、条件、および制御が表示されます。 このブレードにアクセスするには、サインインの詳細の [条件付きアクセス] タブまたは [レポート専用] タブにある行を選択します。

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
 
ベータ版の MS Graph API シリーズを使用した Azure AD ギャラリーからのアプリケーションの作成と構成がサポートされるようになりました。 アプリケーションの複数のインスタンスに対して SAML ベースのシングル サインオンを設定する必要がある場合に、Microsoft Graph API シリーズを使用して [SAML ベースのシングル サインオンの構成を自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api)することで、時間を節約できます。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクタ - 2020 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic by Organization](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページをご覧ください。

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML トークン暗号化の一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
[SAML トークン暗号化](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)を使用すると、暗号化された SAML アサーションを受信するようにアプリケーションを構成できます。 この機能は、すべてのクラウドで一般提供が開始されています。
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>アプリケーション トークン内のグループ名要求の一般提供開始

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
トークンで発行されたグループ要求が、アプリケーションに割り当てられたグループのみに制限されるようになりました。  これは、ユーザーが属しているグループのメンバーが多数いて、トークン サイズの制限を超えるリスクがある場合に特に重要です。 この新機能の導入により、[トークンにグループ名を追加する](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)機能の一般提供が開始されました。
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday Writeback で勤務先電話番号属性の設定がサポートされるようになりました

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Workday Writeback プロビジョニング アプリを強化し、勤務先電話番号と携帯電話番号の属性の書き戻しがサポートされるようになりました。 メールとユーザー名に加えて、電話番号の値が Azure AD から Workday にフローするように Workday Writeback プロビジョニング アプリを構成できるようになりました。 電話番号の書き戻しを構成する方法の詳細については、[Workday Writeback](https://aka.ms/WorkdayWriteback) アプリのチュートリアルを参照してください。 

---

### <a name="publisher-verification-preview"></a>発行者の確認 (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** 開発者エクスペリエンス
 
発行者の確認 (プレビュー) は、管理者とエンド ユーザーが、Microsoft ID プラットフォームと統合するアプリケーション開発者の信頼性を理解するのに役立ちます。 詳細については、「[発行者の確認 (プレビュー)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview)」を参照してください。
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>シングルページ アプリの承認コード フロー

**種類:** 変更された機能 **サービス カテゴリ:** 認証 **製品の機能:** 開発者エクスペリエンス

最新のブラウザーの[サード パーティ Cookie の制限 (Safari ITP など)](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas) により、SPA では、SSO を維持するために、暗黙的フローではなく、承認コード フローを使用する必要があります。MSAL.js v 2.x では、認証コード フローをサポートするようになりました。 Azure portal の対応する更新により、ご自分の SPA の種類を "spa" に更新し、認証コード フローを使用することができます。 ガイダンスについては、「[クイックスタート: 承認コード フローを使用して JavaScript SPA 内でユーザーをサインインさせ、アクセス トークンを取得する](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code)」を参照してください。

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>デバイスの強化されたフィルター処理のパブリック プレビュー開始

**種類:** 変更された機能   
**サービス カテゴリ:** デバイス管理 **製品の機能:** デバイスのライフサイクル管理
 
以前は、使用できるフィルターは "有効" と "アクティビティの日付" のみでした。 OS の種類、結合の種類、コンプライアンスなど、[より多くのプロパティでデバイスの一覧をフィルター処理](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview)できるようになりました。 これらの追加により、特定のデバイスを簡単に見つけることができます。

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

詳細については、[Azure AD B2C の新しいアプリの登録エクスペリエンス](https://aka.ms/b2cappregtraining)に関するページを参照してください。

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

継続的アクセス評価は新しいセキュリティ機能です。これを使うと、Azure AD でイベント (ユーザー アカウントの削除など) が発生した場合に、Azure AD アクセス トークンを利用する証明書利用者に対してほぼリアルタイムでポリシーを適用できるようになります。 この機能は、最初に Teams と Outlook のクライアント向けにロールアウトされます。 詳細については、Microsoft の[ブログ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)と[ドキュメント](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)をご覧ください。

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS サインイン:現場担当者による、電話番号を使用したパスワードなしの Azure AD 対応アプリケーションへのサインイン

**種類:** 新機能

**サービス カテゴリ:** 認証 (ログイン)

**製品の機能:** ユーザー認証

Office では、非従来型の組織と、主要なコミュニケーション方法として電子メールを使用しない大規模な組織の従業員に対応した、一連のモバイルファースト ビジネス アプリが発表されています。 これらのアプリは、雇用者からメール アドレスを提供されない場合がある現場の従業員、デスクレス ワーカー、フィールド エージェント、または小売従業員が、コンピューターまたは IT にアクセスできるようにすることを目的としています。 このプロジェクトにより、このような従業員が、電話番号を入力してコードを送り返すことによってビジネス アプリケーションにサインインできます。 詳細については、[管理者向けのドキュメント](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin)と[エンド ユーザー向けのドキュメント](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer)を参照してください。

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>内部ユーザーを B2B コラボレーションの使用に招待する

**種類:** 新機能

**サービス カテゴリ:** B2B

**製品の機能:**

B2B の招待機能は拡張中であり、今後、B2B コラボレーションの資格情報を使用するように既存の内部アカウントを招待できるようにする予定です。 これを実行するために、招待 API に対して、招待されたメール アドレスなどの通常のパラメーターに加えてユーザー オブジェクトが渡されます。 そのユーザーのオブジェクト ID、UPN、グループ メンバーシップ、アプリの割り当てなどは変更されませんが、今後は、招待前に使用していた内部資格情報ではなく、B2B を使用してホーム テナントの資格情報で認証を行うようになります。 詳細については、こちらの[ドキュメント](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)をご覧ください。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件付きアクセスのレポート専用モードの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

[Azure AD 条件付きアクセスのレポート専用モード](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only)を使用すると、アクセス制御を実施せずにポリシーの結果を評価できます。 組織全体でレポート専用ポリシーをテストし、その影響について理解してから有効にすることで、展開がより安全で簡単になります。 これまで数か月にわたって、私たちはレポート専用モードを広範に導入しており、既に 26M のユーザーがレポート専用ポリシーのスコープ内に存在しています。 この発表では、新しい Azure AD 条件付きアクセス ポリシーが、既定でレポート専用モードで作成されます。 これは、ポリシーが作成された時点からその影響を監視できることを意味します。 また、MS Graph API を使用しているユーザーは、[プログラムによってレポート専用ポリシーを管理する](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)こともできます。 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件付きアクセスに関する分析情報とレポートのブックの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

条件付きアクセスに関する[分析情報とレポートのブック](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting)によって、管理者が各自のテナントの Azure AD 条件付きアクセスの概要ビューを得られます。 個々のポリシーを選択する機能によって、管理者が各ポリシーの動作をよりよく理解し、変更をリアルタイムで監視できます。 このブックにより、Azure Monitor に格納されているデータがストリームされます。これは、[こちらの手順に従って](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)数分で設定可能です。 ダッシュボードは、見つけやすくするために、Azure AD 条件付きアクセス メニュー内の新しい分析情報とレポートのタブに移動されました。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>条件付きアクセスのポリシー詳細ブレードのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** 条件付きアクセス

**製品の機能:** ID のセキュリティ & 保護

新しい[ポリシーの詳細ブレード](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access)には、条件付きアクセス ポリシーの評価中に満たされた割り当て、条件、および制御が表示されます。 このブレードにアクセスするには、サインインの詳細の **[条件付きアクセス]** タブまたは **[レポート専用]** タブにある行を選択します。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 4 月

**種類:** 新機能

**サービス カテゴリ:** エンタープライズ アプリケーション

**製品の機能:** サード パーティ統合

2020 年 4 月に、フェデレーションを使用した以下の 31 の新規アプリのサポートが、アプリ ギャラリーに追加されました。 

[SincroPool Apps](https://www.sincropool.com/)、[SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)、[Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial)、[LMS365](https://lms.365.systems/)、[IWT Procurement Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial)、[Lunni](https://lunni.fi/)、[EasySSO for Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial)、[Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)、[Meraki Dashboard](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial)、[Office 365 Mover](https://app.mover.io/login)、[Speaker Engage](https://speakerengage.com/login.php)、[Honestly](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial)、[Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial)、[DutyFlow](https://app.dutyflow.nl/)、[AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial)、[gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial)、[Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial)、[HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial)、[Harmony](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial)、[Timetabling Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial)、[SynchroNet CLICK](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial)、[empower](https://www.made-in-office.com/en/)、[Fortes Change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial)、[Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial)、[GroupTalk](https://recorder.grouptalk.com/)、[Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial)、[MongoDB Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial)、[TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial)、[COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/)、[Nitro Productivity Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) 、[Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>oAuth2PermissionGrant に対する Microsoft Graph デルタ クエリのサポートのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

oAuth2PermissionGrant に対するデルタ クエリがパブリック プレビューで利用可能になりました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、変更を追跡できるようになりました。 [詳細情報。](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>組織の連絡先に対する Microsoft Graph デルタ クエリのサポートの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

組織の連絡先に対するデルタ クエリが一般提供されました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、実稼働アプリの変更を追跡できるようになりました。 パフォーマンスを大幅に改善するために、orgContact データに継続的にポーリングする既存のコードをデルタ クエリで置き換えてください。 [詳細情報。](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>アプリケーションに対する Microsoft Graph デルタ クエリのサポートの一般提供開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

アプリケーションに対するデルタ クエリが一般提供されました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、実稼働アプリの変更を追跡できるようになりました。 パフォーマンスを大幅に改善するために、アプリケーション データに継続的にポーリングする既存のコードをデルタ クエリで置き換えてください。 [詳細情報。](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>管理単位に対する Microsoft Graph デルタ クエリのサポートのパブリック プレビュー開始

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 管理単位に対する開発者エクスペリエンスのデルタ クエリがパブリック プレビューで利用可能になりました。 ユーザーは、Microsoft Graph を継続的にポーリングすることなく、変更を追跡できるようになりました。 [詳細情報。](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>新しい Microsoft Graph ベータ API で認証用電話番号などを管理する

**種類:** 新機能

**サービス カテゴリ:** MS Graph

**製品の機能:** 開発者エクスペリエンス

これらの API は、ユーザーの認証方法を管理するための主要なツールです。 MFA とセルフサービス パスワード リセット (SSPR) に使用される認証子を、プログラムで事前登録および管理できるようになりました。 これは、Azure MFA、SSPR、および Microsoft Graph の領域で最も要求の多い機能の 1 つでした。 このウェーブでリリースされた新しい API により、次のことが可能になります。

- ユーザーの認証用電話を読み取り、追加、更新、削除する
- ユーザーのパスワードをリセットする
- SMS サインインを有効または無効にする

詳細については、「[Azure AD 認証方法の API の概要](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)」をご覧ください。

---

### <a name="administrative-units-public-preview"></a>管理単位のパブリック プレビュー

**種類:** 新機能

**サービス カテゴリ:** RBAC

**製品の機能:** アクセス制御

管理単位を使用すると、定義した組織の部門、リージョン、またはその他のセグメントに制限されている管理者アクセス許可を付与できます。 管理単位を使用して、各地域の管理者に権限を委任したり、ポリシーを細かく設定したりできます。 たとえば、ユーザー アカウント管理者は、管理単位内のユーザーに対してのみ、プロファイル情報の更新、パスワードのリセット、およびライセンスの割り当てを実行できます。

全体管理者は、管理単位を使用して以下を行うことができます。

- リソースの分散管理のために管理単位を作成する
- 管理単位内の Azure AD ユーザーのみに対して管理アクセス許可を含むロールを割り当てる
- 必要に応じてユーザーとグループで管理単位を設定する

詳細については、「[Azure Active Directory での管理単位の管理 (プレビュー)](https://aka.ms/AdminUnitsDocs)」をご覧ください。

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>プリンター管理者とプリンター技術者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** RBAC

**製品の機能:** アクセス制御

**プリンター管理者**:このロールを持つユーザーは、プリンターを登録して、ユニバーサル印刷コネクタの設定など、Microsoft ユニバーサル印刷ソリューションのすべてのプリンター構成のすべての側面を管理できます。 すべての委任された印刷アクセス許可要求に同意することができます。 プリンター管理者は、印刷レポートにアクセスすることもできます。 

**プリンター技術者**:このロールを持つユーザーは、プリンターを登録して、Microsoft ユニバーサル印刷ソリューションでプリンターの状態を管理できます。 すべてのコネクタ情報を読み取ることもできます。 プリンター技術者が行うことができない主要なタスクは、プリンターに対するユーザー アクセス許可の設定と、プリンターの共有です。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>ハイブリッド ID 管理者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** RBAC

**製品の機能:** アクセス制御

このロールのユーザーは、Azure AD でのハイブリッド ID の有効化に関連するサービスと設定を有効化、構成、管理できます。 このロールでは、サポートされている 3 つの認証方法 &#8212; パスワード ハッシュ同期 (PHS)、パススルー認証 (PTA)、フェデレーション (AD FS またはサード パーティのフェデレーション プロバイダー) &#8212; のいずれかに Azure AD を構成し、関連するオンプレミス インフラストラクチャを展開してそれらを有効にすることが許可されます。 オンプレミス インフラストラクチャには、プロビジョニング エージェントと PTA エージェントが含まれます。 このロールでは、Windows 10 以外のデバイスまたは Windows Server 2016 以外のコンピューターでシームレス シングル サインオン (S-SSO) を有効にして、シームレスな認証を有効にすることが許可されます。 さらに、このロールでは、監視とトラブルシューティングのために、サインイン ログを表示し、正常性と分析にアクセスすることが許可されます。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>ネットワーク管理者の組み込みロール

**種類:** 新機能

**サービス カテゴリ:** RBAC

**製品の機能:** アクセス制御

このロールを持つユーザーは、ユーザーの場所からのネットワーク テレメトリに基づいて、Microsoft によるネットワーク境界アーキテクチャに関する推奨事項を確認できます。 Office 365 のネットワーク パフォーマンスは、通常はユーザーの場所に固有である、企業の顧客の慎重なネットワーク境界アーキテクチャに依存します。 このロールを使用では、検出されたユーザーの場所と、それらの場所に対するネットワーク パラメーターの構成を編集して、テレメトリの測定と設計に関する推奨事項の改善できます。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 管理ポータル エクスペリエンスでの一括アクティビティとダウンロード

**種類:** 新機能

**サービス カテゴリ:** [ユーザー管理]

**製品の機能:** ディレクトリ

Azure AD 管理ポータルのエクスペリエンスにおいて、CSV ファイルをアップロードすることで Azure AD のユーザーとグループに対して一括アクティビティを実行できるようになりました。 ユーザーの作成、ユーザーの削除、ゲスト ユーザーの招待を行うことができます。 また、グループのメンバーを追加および削除することもできます。

また、Azure AD 管理ポータルのエクスペリエンスから Azure AD リソースの一覧をダウンロードすることもできます。 ディレクトリ内のユーザーの一覧、ディレクトリ内のグループの一覧、および特定のグループのメンバーをダウンロードできます。

詳細については、以下を参照してください。

- [ユーザーの作成](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add)または[ゲスト ユーザーの招待](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)に関する記事
- [ユーザーの削除](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete)または[削除済みユーザーの復元](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)に関する記事
- [ユーザーの一覧のダウンロード](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download)または[グループの一覧のダウンロード](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)に関する記事
- グループに対する[メンバーの追加 (インポート)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)、[メンバーの削除](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)、または[メンバーの一覧のダウンロード](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)に関する記事

---

### <a name="my-staff-delegated-user-management"></a>マイ スタッフによって委任されたユーザー管理

**種類:** 新機能

**サービス カテゴリ:** [ユーザー管理]

**製品の機能:**

ストア マネージャーなどの現場のマネージャーが、マイ スタッフを使用して、自分のスタッフ メンバーが Azure AD アカウントにアクセスできるようにすることができます。 組織では、パスワードのリセットや電話番号の変更などの一般的なタスクを、中央のヘルプデスクに頼る代わりに現場のマネージャーに委任することができます。 マイ スタッフを使用すると、自分のアカウントにアクセスできないユーザーが数回のクリックでアクセスを回復することができ、ヘルプデスクや IT スタッフは必要ありません。 詳細については、「[マイ スタッフを使用してユーザーを管理する (プレビュー)](https://aka.ms/MyStaffAdminDocs)」および「[マイ スタッフでユーザーの管理を委任する (プレビュー)](https://aka.ms/MyStaffUserDocs)」をご覧ください。

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>アクセス レビューにおけるアップグレードされたエンド ユーザー エクスペリエンス

**種類:** 変更された機能

**サービス カテゴリ:** アクセス レビュー

**製品の機能:** Identity Governance

マイ アプリ ポータルでの Azure AD アクセス レビューにおけるレビュー担当者エクスペリエンスが更新されました。 4 月の終わりに、Azure AD アクセス レビューのレビュー担当者エクスペリエンスにログインしたレビュー担当者に、マイ アクセスでの更新されたエクスペリエンスを試すことができるバナーが表示されます。 更新されたアクセス レビュー エクスペリエンスでは、現在のエクスペリエンスと同じ機能が提供されますが、ユーザーの生産性を向上させる新機能に加えてユーザー インターフェイスが改善されることにご注意ください。 [更新されたエクスペリエンスの詳細については、こちらをご覧ください](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。 このパブリック プレビューは、2020 年 7 月の終わりまで続きます。 7 月の終わりに、このプレビュー エクスペリエンスをオプトインしていないレビュー担当者が、アクセス レビューを実行するためのマイ アクセスに自動的にリダイレクトされます。 レビュー担当者が今すぐマイ アクセスのプレビュー エクスペリエンスに完全に切り替えられるようにしたい場合は、[こちらからリクエストを作成してください](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 受信ユーザー プロビジョニングおよび書き戻しアプリでの最新バージョンの Workday Web Services API サポートの開始

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** 

お客様からのフィードバックに基づいて、エンタープライズ アプリ ギャラリーの Workday 受信ユーザー プロビジョニングおよび書き戻しアプリが更新され、最新バージョンの Workday Web Services (WWS) API がサポートされるようになりました。 この変更により、お客様は、接続文字列内で使用したい WWS API バージョンを指定できるようになります。 これにより、お客様は Workday のリリースで使用可能なさらなる HR 属性を取得できるようになります。 Workday Writeback アプリでは、Maintain_Contact_Info の制限を克服するために、推奨される Change_Work_Contact_Info Workday Web サービスが使用されるようになりました。

接続文字列内でバージョンが指定されなかった場合、既定では、Workday 受信プロビジョニング アプリでは引き続き WWS v21.1 が使用されます。受信ユーザー プロビジョニングを最新の Workday API に切り替えるには、[こちらのチュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)の記述に従って接続文字列を更新し、また、[Workday 属性リファレンス ガイド](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)の記述に従って Workday 属性で使用される XPATH を更新する必要があります。 

書き戻しに新しい API を使用する場合、Workday Writeback プロビジョニング アプリを変更する必要はありません。 Workday 側では、チュートリアルのセクション「[ビジネス プロセス セキュリティ ポリシーのアクセス許可の構成](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)」に記載されているように、Workday 統合システム ユーザー (ISU) アカウントに Change_Work_Contact ビジネス プロセスを呼び出すためのアクセス許可が付与されていることを確認してください。 

新しい API バージョンのサポートを反映するために、[チュートリアル ガイド](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)が更新されています。

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>既定のアクセス ロールを持つユーザーのプロビジョニング対象範囲への追加

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** ID ライフサイクル管理

従来、既定のアクセス ロールのユーザーはプロビジョニングの対象外でした。 このロールのユーザーはプロビジョニングの対象にする必要があるというフィードバックが寄せられています。 2020 年 4 月 16 日以降、すべての新しいプロビジョニング構成で既定のアクセス ロールを持つユーザーのプロビジョニングが許可されます。 既存のプロビジョニング構成の動作は、このロールを持つユーザーのプロビジョニングをサポートするために段階的に変更されます。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>プロビジョニング UI の更新

**種類:** 変更された機能

**サービス カテゴリ:** アプリ プロビジョニング

**製品の機能:** ID ライフサイクル管理

より焦点を絞った管理ビューを作成するために、プロビジョニング エクスペリエンスが更新されました。 既に構成済みのエンタープライズ アプリケーションのプロビジョニング ブレードに移動したときに、簡単にプロビジョニングの進行状況を監視し、プロビジョニングの開始、停止、再開などの操作を管理できるようになります。 [詳細情報。](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>動的グループ ルールの検証のパブリック プレビュー開始

**種類:** 変更された機能

**サービス カテゴリ:** グループ管理

**製品の機能:** コラボレーション

Azure Active Directory (Azure AD) で、動的グループ ルールを検証する手段が提供されるようになりました。 **[ルールの検証]** タブで、サンプル グループ メンバーに対して動的ルールを検証し、ルールが想定どおりに機能していることを確認できます。 動的グループ ルールの作成時または更新時に、管理者は、ユーザーまたはデバイスがグループのメンバーであるかどうかを確認できます。 これは、ユーザーまたはデバイスがルールの条件を満たしているかどうかを評価し、メンバーシップが想定されていない場合のトラブルシューティングを支援するのに役立ちます。

詳細については、[動的グループ メンバーシップ ルールの検証 (プレビュー)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation) に関する記事をご覧ください。

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>ID セキュリティ スコア - セキュリティの既定値群と MFA の改善アクションの更新

**種類:** 変更された機能

**サービス カテゴリ:** 該当なし

**製品の機能:** ID のセキュリティ & 保護

**Azure AD 改善アクションのセキュリティの既定値群のサポート:** Microsoft セキュリティ スコアでは、[Azure AD のセキュリティの既定値群](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)をサポートするように改善アクションが更新されます。これにより、一般的な攻撃に対する事前構成済みのセキュリティ設定を使用して組織を保護しやすくなります。 これは、次の改善アクションに影響します。

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

これらの新しい改善アクションでは、ディレクトリ全体で多要素認証 (MFA) のためにユーザーまたは管理者を登録し、組織のニーズに合った適切な一連のポリシーを確立する必要があります。 主な目的は、すべてのユーザーと管理者が多要素またはリスクベースの本人確認プロンプトで認証できるようにしながら、柔軟性を高めることです。 それには、スコープに基づく決定を適用する複数のポリシーの使用や、ユーザーに MFA を要求するタイミングが Microsoft によって決定されるようにするためのセキュリティの既定値群の設定 (3 月 16 日以降) などの形態があります。 [Microsoft セキュリティ スコアの新機能をご覧ください](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021 年 3 月の B2B 更新プログラムでのアンマネージド Azure Active Directory アカウント

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
**2021 年 3 月 31 日以降**、Microsoft では、B2B コラボレーション シナリオ用にアンマネージド Azure Active Directory (Azure AD) アカウントおよびテナントを作成することによって招待の引き換えをサポートしなくなります。 これに備えて、[電子メール ワンタイム パスコード認証](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)を選択することをお勧めします。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>既定のアクセス ロールのユーザーは、プロビジョニングの対象ユーザーとなります

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
従来、既定のアクセス ロールのユーザーはプロビジョニングの対象外でした。 このロールのユーザーはプロビジョニングの対象にする必要があるというフィードバックが寄せられています。 すべての新しいプロビジョニング構成で既定のアクセス ロールのユーザーがプロビジョニングできるように、変更の展開に取り組んでいます。 段階的に、このロールを使用したユーザーのプロビジョニングをサポートするために、既存のプロビジョニング構成の動作を変更していきます。 お客様による対応は必要ありません。 この変更の準備が整ったら、[ドキュメント](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)に更新プログラムを投稿します。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B コラボレーションは、21Vianet (Azure China 21Vianet) テナントで運用される Microsoft Azure で利用できるようになります

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD B2B コラボレーション機能は、21Vianet (Azure China 21Vianet) テナントが運用している Microsoft Azure で利用できるようになります。これにより、Azure China 21Vianet テナントのユーザーは、他の Azure China 21Vianet テナントのユーザーとシームレスに共同作業を行えるようになります。 [詳細については、Azure AD B2B コラボレーションを参照してください](https://docs.microsoft.com/azure/active-directory/b2b/)。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Collaboration の招待メールの再設計

**種類:** 変更の計画  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
ユーザーをディレクトリに招待するために Azure AD B2B コラボレーションの招待サービスによって送信される[メール](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)は、招待情報とユーザーの次の手順がわかりやすくなるよう再設計されます。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery ポリシーの変更は、監査ログに表示されるようになります

**種類:** 固定  
**サービス カテゴリ:** Audit  
**製品の機能:** 監視とレポート
 
[HomeRealmDiscovery ポリシー](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)の変更が監査ログに含まれていないというバグを修正しました。 これで、ポリシーがいつどのように、だれによって変更されたかを確認できるようになります。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 3 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 3 月に、フェデレーションがサポートされる次の 51 の新しいアプリがアプリ ギャラリーに追加されました。 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)、[Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial)、[PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/)、[Profit.co SAML App](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial)、[iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial)、[contexxt.ai SPHERE](https://contexxt-sphere.com/login)、[Wisdom By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial)、[Flare Digital Signage](https://spark-dev.pixelnebula.com/login)、[Logz.io - Cloud Observability for Engineers](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial)、[SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial)、[BizzContact](https://bizzcontact.app/)、[Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial)、[MarketSignShare](http://www.signshare.com/)、[CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial)、[Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial)、[FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial)、[RIB A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757)、[GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial)、[Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial)、[Zscaler B2B User Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial)、[LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial)、[Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial)、[WatchTeams](https://www.devfinition.com/)、[Aster](https://demo.asterapp.io/login)、[Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial)、[Node Insight](https://admin.nodeinsight.com/AADLogin.aspx)、[IP Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial)、[InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial)、[Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial)、[Showcase Workshop](https://app.showcaseworkshop.com/)、[Greenlight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial)、[Greenlight Compliant Access Management](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial)、[Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial)、[Miradore Online](https://login.online.miradore.com/)、[Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial)、[AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial)、[TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial)、[Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)、[Bizagi Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial)、[insuiteX](https://www.insuite.jp/)、[sybo](https://www.systexsoftware.com.tw/)、[Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial)、[WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial)、[E-days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial)、[Kollective SDN](https://portal.kollective.app/login)、[Witivio](https://app.witivio.com/)、[Playvox](https://my.playvox.com/login)、[Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial)、[Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial)、[Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial)、[Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B Collaboration が Azure Government テナントで利用可能になりました

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 
Azure AD B2B コラボレーション機能が Azure Government テナント間で利用可能になりました。  お使いのテナントでこれらの機能を使用できるかどうかを確認するには、「[B2B コラボレーションが Azure US Government テナントで利用可能かどうかを確認するにはどうすればよいですか？](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)」の手順に従ってください。

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure ログの Azure Monitor 統合が Azure Government で利用できるようになりました

**種類:** 新機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
Azure AD ログとの Azure Monitor 統合が Azure Government で利用できるようになりました。 Azure AD ログ (監査ログとサインイン ログ) をストレージ アカウント、イベント ハブ、Log Analytics にルーティングすることができます。 Azure AD のシナリオについては、[詳細なドキュメント](https://aka.ms/aadlogsinamd)と[レポートと監視のデプロイ計画](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)に関するページを確認してください。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Government での Identity Protection の更新

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

[Microsoft Azure Government ポータル](https://portal.azure.us/)で、更新された [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)  エクスペリエンスをロールアウトしました。 詳しくは、[お知らせブログの投稿](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)をご覧ください。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>ディザスター リカバリー:プロビジョニング構成のダウンロードと保存

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理
 
Azure AD プロビジョニング サービスには、豊富な構成機能が用意されています。 お客様は、後で参照したり、既知の適切なバージョンにロールバックしたりするために、構成を保存できる必要があります。 プロビジョニング構成を JSON ファイルとしてダウンロードし、必要に応じてアップロードする機能が追加されました。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (セルフサービス パスワード リセット) で、21Vianet (Azure China 21Vianet) によって運営される Microsoft Azure の管理者に 2 つのゲートが必要になりました 

**種類:** 変更された機能  
**サービス カテゴリ:** セルフサービス パスワード リセット  
**製品の機能:** ID のセキュリティ & 保護
 
以前は、21Vianet (Azure China 21Vianet) によって運営されている Microsoft Azure で、セルフサービス パスワード リセット (SSPR) を使用して自分のパスワードをリセットする管理者は、ID を証明するために必要な "ゲート" (チャレンジ) は 1 つだけでした。 パブリックおよびその他の国内のクラウドでは、通常、管理者は SSPR を使用するときに 2 つのゲートを使用して ID を証明する必要があります。 しかし、Azure China 21Vianet では SMS や電話をサポートしていなかったため、管理者による 1 ゲートのパスワード リセットを許可していました。

Azure China 21Vianet とパブリック クラウドの間に SSPR 機能パリティを作成中です。 今後、管理者は SSPR を使用するときに 2 つのゲートを使用する必要があります。 SMS、電話、認証アプリの通知とコードがサポートされるようになります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>パスワードの長さは 256 文字に制限されています

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD サービスの信頼性を確保するために、ユーザーのパスワードの長さが 256 文字までに制限されるようになりました。 これよりも長いパスワードを持つユーザーは、管理者に連絡するか、セルフサービス パスワード リセット機能を使用して、以降のログインでパスワードを変更するように求められます。

この変更は、2020 年 3 月 13 日、午前 10 時 PST (UTC - 18 時) に有効になりました。エラーは AADSTS 50052、InvalidPasswordExceedsMaxLength です。 詳細については、[重大な変更に関する通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)を参照してください。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD サインイン ログが Azure portal を通じてすべての無料テナントで利用できるようになりました

**種類:** 変更された機能  
**サービス カテゴリ:** レポーティング  
**製品の機能:** 監視とレポート
 
今後、無料のテナントを所有しているお客様は、最大 7 日間、[Azure portal から Azure AD サインイン ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)にアクセスできます。 以前は、サインイン ログを利用できるのは、Azure Active Directory Premium ライセンスをお持ちのお客様でした。 この変更により、すべてのテナントがポータル経由でこれらのログにアクセスできるようになります。

> [!NOTE]
> また、Microsoft Graph API と Azure Monitor を通じてサインイン ログにアクセスするには、Premium ライセンス (Azure Active Directory Premium P1 または P2) が必要です。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Azure portal のグループの全般設定からのディレクトリ全体のグループ オプションの廃止

**種類:** 非推奨  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

お客様がニーズに最も合ったディレクトリ全体のグループを作成するためのより柔軟な方法を提供するために、Azure portal の **[グループ]**  >  **[全般]** 設定の **[ディレクトリ全体のグループ]** オプションを[動的グループのドキュメント](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)へのリンクに置き換えました。 管理者がゲスト ユーザーを含めたり除外したりするすべてのユーザー グループを作成できるように、ドキュメントを改良しました。

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

これらの新しい改善アクションでは、ユーザーまたは管理者をディレクトリ全体での MFA のために登録し、組織のニーズに合った適切な一連のポリシーを確立する必要があります。 主な目的は、すべてのユーザーと管理者が多要素またはリスクベースの本人確認プロンプトで認証できるようにしながら、柔軟性を高めることです。 これには、Microsoft がユーザーに MFA を要求するタイミングを決定するセキュリティの既定値群の設定や、スコープに基づく決定を適用する複数のポリシーの使用などの形態があります。 これらの改善アクションの更新の一環として、ベースライン保護ポリシーはスコアの計算に含まれなくなります。 [Microsoft セキュリティ スコアに関する最新情報をご覧ください](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)。

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services の SKU の選択

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services
 
Microsoft では、Azure AD Domain Services をご利用のお客さまより、インスタンスのパフォーマンス レベルをより柔軟に選択したいというフィードバックをいただいておりました。 2020 年 2 月 1 日より、動的モデル (オブジェクト数に基づいて Azure AD のパフォーマンスと価格を決定するモデル) から自己選択モデルへの切り替えを行いました。 これにより、お客様は環境に合ったパフォーマンス レベルを選択できるようになりました。 この変更により、リソース フォレストなどの新しいシナリオや、毎日のバックアップなどの Premium 機能をも有効にすることができます。 すべての SKU でオブジェクト数は無制限になりましたが、各レベルでのオブジェクト数の提案は引き続き提供されます。

**お客様による早急な対応は必要はありません。** 既存のお客様については、2020 年 2 月 1 日時点で使用していた動的レベルによって、新しい既定のレベルが決まります。 この変更による、価格やパフォーマンスへの影響はありません。 今後、Azure AD DS をご利用のお客様は、ディレクトリのサイズとワークロードの特性の変化に応じて、パフォーマンス要件を評価する必要があります。 サービス レベル間の切り替え操作は引き続きダウンタイムなしで行われ、お客様がディレクトリの拡張に応じて新しいレベルに自動的に移動されることはなくなります。 さらに、価格の引き上げは行われず、新しい価格は現在の課金モデルに従います。 詳細については、[Azure AD DS SKU のドキュメント](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) および [Azure AD Domain Services の価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory-ds/)を参照してください。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2020 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2020 年 2 月に、フェデレーションがサポートされる次の 31 の新しいアプリがアプリ ギャラリーに追加されました。 

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)、[Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)、[NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)、[Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure)、[ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad)、[In Case of Crisis - Online Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial)、[BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial)、[Beekeeper Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial)、[Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess)、[Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial)、[Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial)、[Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial)、[EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial)、[New Relic (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial)、[Thulium](https://admin.thulium.com/login/instance)、[Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial)、[Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams)、[Beesy](https://www.beesy.me/index.php/site/login)、[Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial)、[MURAL](https://app.mural.co/signup)、[Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial)、[LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)、[Wakelet](https://wakelet.com/login)、[Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial)、[ThingLink for Teachers and Schools](https://www.thinglink.com/)、[Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial)、[NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product)、[WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial)、[InvitePeople](https://invitepeople.com/login)、[Reprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial)、[TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD アプリケーション ギャラリーの新しいプロビジョニング コネクター - 2020 年 2 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページをご覧ください。

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>ハイブリッド環境での FIDO2 セキュリティ キーの Azure AD サポート

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Microsoft は、ハイブリッド環境での FIDO2 セキュリティ キーの Azure AD サポートのパブリック プレビューを発表しました。 ユーザーは、FIDO2 セキュリティ キーを使用して Hybrid Azure AD 参加済み Windows 10 デバイスにサインインし、オンプレミスとクラウドのリソースにシームレスにサインオンできるようになりました。 Azure AD 参加済みデバイスの FIDO2 サポートのパブリック プレビューを最初にローンチして以来、ハイブリッド環境のサポートは、パスワードレス認証をご利用のお客様からのご要望に最も多い機能でした。 生体認証や公開キーおよび秘密キーの暗号化などの高度なテクノロジーを使用したパスワードレス認証は、セキュリティを担保しつつ、利便性と使いやすさを提供します。 このパブリック プレビューにより、FIDO2 セキュリティ キーのような最新の認証を使用して、従来の Active Directory リソースにアクセスできるようになりました。 詳細については、「[オンプレミスのリソースへの SSO](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)」を参照してください。 

使用を開始するには、「[テナントの FIDO2 セキュリティ キーを有効にする](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)」で詳しい手順を参照してください。 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>新しいマイ アカウントのエクスペリエンスの一般公開

**種類:** 変更された機能  
**サービス カテゴリ:** マイ プロファイル/アカウント  
**製品の機能:** エンド ユーザー エクスペリエンス
 
すべてのエンド ユーザー アカウント管理のニーズに対応する機能を 1 か所に統合するマイ アカウントが一般公開されました。 エンド ユーザーは、URL から、または新しいマイ アプリ エクスペリエンスのヘッダーから、この新しいサイトにアクセスできます。 新しいエクスペリエンスで提供されるすべてのセルフサービス機能の詳細については、「[マイ アカウント ポータルの概要](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)」を参照してください。

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>マイ アカウントのサイトの URL が myaccount.microsoft.com に更新

**種類:** 変更された機能  
**サービス カテゴリ:** マイ プロファイル/アカウント  
**製品の機能:** エンド ユーザー エクスペリエンス
 
新しいマイ アカウントのエンド ユーザーエクスペリエンスの URL は、来月中に `https://myaccount.microsoft.com` に更新されます。 エクスペリエンスと、エンド ユーザーに提供されるアカウントのすべてのセルフサービス機能の詳細については「[マイ アカウント ポータルのヘルプ](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)」を参照してください。

