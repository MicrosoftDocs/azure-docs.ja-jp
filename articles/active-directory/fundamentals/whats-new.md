---
title: 新機能 リリース ノート - Azure Active Directory | Microsoft Docs
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Active Directory の新着情報について説明します。
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea6e654037e545a1b969aa2a9b41245fb2892bff
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273822"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報

>URL `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` をコピーして、お使いの ![RSS フィード リーダー アイコン](./media/whats-new/feed-icon-16x16.png) フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

Azure AD は随時改善されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探す場合は、「[新着情報のアーカイブ - Azure Active Directory](whats-new-archive.md)」をご覧ください。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>変更の計画: Power BI コンテンツ パックの廃止

**種類:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

2019 年 10 月 1 日より、Power BI では、Azure AD Power BI コンテンツ パックを含む、すべてのコンテンツ パックの廃止が開始します。 このコンテンツ パックに代わり、Azure AD ブックを使用して、Azure AD 関連のサービスに関する分析情報を取得できます。 レポート専用モードの条件付きアクセス ポリシーに関するブック、アプリ同意に基づく分析情報など、その他のブックも表示されます。

ブックに関する詳細については、[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)に関する記事を参照してください。 コンテンツ パックの廃止に関する詳細は、「[Announcing Power BI template apps general availability](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)」 (Power BI テンプレート アプリの一般提供に関する告知) というブログ投稿をご覧ください。

---

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>グループの検索、フィルター処理、および並べ替えの機能強化は、Azure AD ポータル (パブリック プレビュー) で利用できます。

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

グループに関連する強化されたエクスペリエンスのパブリック プレビューが、Azure AD ポータルでご利用いただけるようになったことを発表いたします。 次を提供することで、これらの拡張機能がグループとメンバー リストをより適切に管理するのに役立ちます。

- グループ リストでの部分文字列検索などの高度な検索機能。
- メンバーと所有者のリストに対する高度なフィルター処理と並べ替えのオプション。
- メンバーと所有者のリストの新しい検索機能。
- 大規模なグループのより正確なグループ数。

詳細については、[Azure portal でのグループの管理](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)に関する記事をご覧ください。

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>アプリ登録管理に新しいカスタム ロールが使用できる (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** RBAC  
**製品の機能:** Access Control

(Azure AD P1 または P2 サブスクリプションで使用できる) カスタム ロールで、特定のアクセス許可を持つロール定義を作成し、そのロールを特定のリソースに割り当てて、詳細なアクセスを提供できるようになりました。 現在は、アプリの登録を管理するためのアクセス許可を使用し、ロールを特定のアプリに割り当てて、カスタム ロールを作成します。 カスタム ロールの詳細については、[Azure Active Directory でのカスタム管理者ロール](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)に関する記事を参照してください。

追加のアクセス許可やリソースのサポートが必要だが、現在表示されていない場合は、[Azure フィードバック サイト](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)にフィードバックをお送りください。ご要望は Microsoft の更新予定に追加されます。

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>新しいプロビジョニング ログを利用して、アプリのプロビジョニング デプロイの監視とトラブルシューティングができる (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

新しいプロビジョニング ログは、ユーザーとグループのプロビジョニング デプロイの監視とトラブルシューティングに役立ちます。 これらの新しいログ ファイルには、次の情報が含まれます。

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial) で正常に作成されたグループ
- [アマゾン ウェブ サービス (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws) からインポートされたロール
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) からインポートされなかった従業員

詳細については、[Azure Active Directory ポータルのプロビジョニング レポート (プレビュー)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) に関する記事を参照してください。

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>すべての Azure AD 管理者のための新しいセキュリティ レポート (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

すべての Azure AD 管理者は間もなく、既定で Azure AD 内の最新のセキュリティ レポートにアクセスできるようになります。 9 月末までは、最新のセキュリティ レポートの上部にあるバナーを使用し、前のレポートに戻ることができます。

最新のセキュリティ レポートでは、前のバージョンから次の追加機能が提供されます。

- 高度なフィルター処理と並べ替え
- ユーザー リスクの無視などの一括操作
- 侵害されたエンティティまたは安全なエンティティの確認
- 次のようなリスクの状態: 危険、無視、修復済み、セキュリティ侵害の確認済み
- 新しいリスク関連の検出 (Azure AD Premium のサブスクライバーが利用可能)

詳細については、[危険なユーザー](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report)、[リスクの高いサインイン](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)、[リスク検出](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report)に関する記事をご覧ください。

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>ユーザー割り当てマネージド ID が Virtual Machines と Virtual Machine Scale Sets で利用できる (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** Azure リソースのマネージド ID  
**製品の機能:** 開発者エクスペリエンス

ユーザー割り当てマネージド ID が Virtual Machines と Virtual Machine Scale Sets で一般提供されました。 この一環として、Azure では、使用中のサブスクリプションで信頼され、1 つまたは複数の Azure サービス インスタンスに割り当てることができる ID を Azure AD テナントで作成できます。 ユーザー割り当てマネージド ID の詳細については、[Azure リソースのマネージド ID とは](https://aka.ms/azuremanagedidentity)に関する記事を参照してください。

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>ユーザーはモバイル アプリまたはハードウェア トークンを利用してパスワードをリセットできる (一般提供)

**種類:** 変更された機能  
**サービス カテゴリ:** セルフサービスによるパスワードのリセット  
**製品の機能:** ユーザー認証

モバイル アプリを組織に登録しているユーザーは、Microsoft Authenticator アプリからの通知に同意するか、モバイル アプリまたはハードウェア トークンからのコードを入力すると、自分のパスワードをリセットできるようになりました。

詳細については、[動作のしくみ:Azure AD のセルフサービスによるパスワードのリセットのクイック スタート](https://aka.ms/authappsspr)に関する記事をご覧ください。 ユーザー エクスペリエンスの詳細については、[職場または学校のパスワードのリセットの概要](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)に関する記事をご覧ください。

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET が on-behalf-of シナリオで MSAL.NET 共有キャッシュを無視する

**種類:** 固定  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Azure AD Authentication ライブラリ (ADAL.NET) バージョン 5.0.0-preview 以降では、アプリの開発者が、[Web アプリおよび Web API のアカウントごとに 1 つのキャッシュをシリアル化](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)する必要があります。 そうしないと、`UserAssertion` の特定のユース ケースと共に [on-behalf-of フロー](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)を使用する一部のシナリオで、特権の昇格が生じることがあります。 この脆弱性を回避するために、on-behalf-of のシナリオでは、.NET 用 Microsoft 認証ライブラリ (MSAL.NET) の共有キャッシュが ADAL.NET で無視されるようになりました。

この問題の詳細については、[Azure Active Directory 認証ライブラリの特権の昇格の脆弱性](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)に関するページを参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 8 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 8 月に、フェデレーションを使用した以下の 26 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial)、[Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial)、[ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)、[Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial)、[Viareport's Inativ Portal (Europe)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial)、[Azure Databricks](https://azure.microsoft.com/services/databricks)、[Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)、[Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial)、[Priority Matrix](https://sync.appfluence.com/pmwebng/)、[Cousto MySpace](https://cousto.platformers.be/account/login)、[Uploadcare](https://uploadcare.com/accounts/signup/)、[Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial)、[CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial)、[Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial)、[deliver.media™ Portal](https://portal.deliver.media)、[Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial)、[F5](https://www.f5.com/products/security/access-policy-manager)、[stashcat AD connect](https://www.stashcat.com)、[Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)、[Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial)、[ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial)、[Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial)、[Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial)、[Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial)、[Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)、[EAB Navigate Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>AzureAD PowerShell モジュールと AzureAD Preview PowerShell モジュールの新しいバージョンを利用できる

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** Directory

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

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

Azure portal で利用できる動的グループ ルール ビルダーの UI をいくつか機能強化し、新しいルールの設定や既存のルールの変更が簡単になりました。 設計のこの機能強化により、1 つだけではなく、最大 5 つの式を使用してルールを作成できます。 また、デバイス プロパティ リストを更新し、非推奨のデバイス プロパティを削除しました。

詳細については、[動的メンバーシップ ルールの管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule)に関する記事を参照してください。

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>アクセス レビューで新しい Microsoft Graph アプリのアクセス許可が利用できるようになった

**種類:** 変更された機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance

Microsoft Graph アプリのアクセス許可に新しい `AccessReview.ReadWrite.Membership` を導入しました。これにより、アプリで、アクセス レビューを自動的に作成および取得して、グループ メンバーシップとアプリの割り当てができるようになりました。 このアクセス許可は、スケジュールを設定したジョブで使用したり、自動化の一部として使用したりでき、ログイン ユーザー コンテキストは必要ありません。

詳細については、「[Example how to create Azure AD access reviews using Microsoft Graph app permissions with PowerShell](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)」 (PowerShell で Microsoft Graph アプリのアクセス許可を使用して Azure AD アクセス レビューを作成する方法の例) というブログをご覧ください。

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD アクティビティ ログが Azure Monitor の政府クラウド インスタンスで利用できるようになった

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD アクティビティ ログが Azure Monitor の政府クラウド インスタンスで利用できるようになったことをお知らせします。 [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)、[Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)、[ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight) などの SIEM ツールと統合する目的で、ストレージ アカウントやイベント ハブに Azure AD ログを送信できるようになりました。 

Azure Monitor を設定する方法については、[Azure Monitor の Azure AD アクティビティ ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)に関する記事を参照してください。

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>セキュリティ情報の機能強化された新しいエクスペリエンスをユーザーに提供する

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)   
**製品の機能:** ユーザー認証

2019 年 9 月 25 日に、ユーザー セキュリティ情報の登録と管理のためのセキュリティ情報エクスペリエンスのうち、機能強化されていない前のバージョンを無効にし、新しい[機能強化されたバージョン](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)のみを有効にします。 つまり、ユーザーは以前のエクスペリエンスを使用できなくなります。

機能強化されたセキュリティ情報エクスペリエンスについては、[管理者ドキュメント](https://aka.ms/securityinfodocs) と[ユーザー ドキュメント](https://aka.ms/securityinfoguide)を参照してください。

#### <a name="to-turn-on-this-new-experience-you-must"></a>この新しいエクスペリエンスを有効にするには、次の操作を行う必要があります。

1. Azure portal にグローバル管理者またはユーザー管理者としてサインインします。

2.  **Azure Active Directory、[ユーザー設定]、[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。

3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます - 拡張]** 領域で **[選択済み]** を選択し、ユーザーのグループを選択するか、 **[すべて]** を選択してテナントのすべてのユーザーに対してこの機能をオンにします。

4. [ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます] 領域で **[なし]** を選択します。

5. 設定を保存します。

    設定を保存すると、以前のセキュリティ情報エクスペリエンスにアクセスできなくなります。

>[!Important]
>2019 年 9 月 25 日より前にこれらの手順を完了しないと、機能強化されたエクスペリエンスについて、Azure Active Directory テナントが自動的に有効になります。 ご質問がございましたら、registrationpreview@microsoft.com にお問い合わせください。

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>POST ログインを使用した認証要求の検証がさらに厳密になる

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 標準

2019 年 9 月 2 日より、POST メソッドを使用した認証要求は、HTTP 標準に対してさらに厳密に検証されます。 具体的には、スペースと二重引用符 (") が要求フォームの値から削除されなくなります。 これらの変更によって、既存のクライアントが中断されることがなく、Azure AD に送信された要求は毎回確実に処理されます。

詳細については、[Azure AD の破壊的変更の告知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)に関するページを参照してください。

---

## <a name="july-2019"></a>2019 年 7 月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>変更の計画: アプリケーション プロキシ サービスが、TLS 1.2 のみをサポートするように更新される

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

最強の暗号化を提供できるように、アプリケーション プロキシ サービスのアクセスを TLS 1.2 プロトコルのみに制限する作業が開始される予定です。 この制限は、最初のうちは TLS 1.2 プロトコルを既に利用されているお客様にロールアウトされるため、影響はありません。 TLS 1.0 と TLS 1.1 のプロトコルは、2019 年 8 月 31 日に完全に非推奨になります。 まだ TLS 1.0 および TLS 1.1 を利用されているお客様には、この変更に備えていただけるよう、事前に通知されます。

この変更期間中もアプリケーション プロキシ サービスへの接続を維持するには、クライアントとサーバーおよびブラウザーとサーバーの組み合わせを TLS 1.2 を使用するように更新することをお勧めします。 また、アプリケーション プロキシ サービスから公開されるアプリに従業員がアクセスする際に使用するすべてのクライアント システムを含めることもお勧めします。

詳しくは、「[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)」を参照してください。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>変更の計画: アプリケーション ギャラリーのデザインの更新が予定されている

**種類:** 変更の計画  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

**[アプリケーションの追加]** ブレードの **[ギャラリーから追加する]** 領域のデザインが、新しいユーザー インターフェイスに変更されます。 この変更により、自動プロビジョニング、OpenID Connect、Security Assertion Markup Language (SAML)、パスワード シングル サインオン (SSO) がサポートされているアプリをより簡単に見つけることができるようになります。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>変更の計画: Office 365 IP アドレスから MFA サーバーの IP アドレスを削除する

**種類:** 変更の計画  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

[Office 365 の IP アドレスと URL Web サービス](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)から、MFA サーバーの IP アドレスを削除する予定です。 現在これらのページに依存してファイアウォールの設定を更新している場合は、「[Azure Multi-Factor Authentication Server の概要](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)」の「**Azure Multi-Factor Authentication Server ファイアウォールの要件**」セクションに記載されている IP アドレスの一覧も含まれていることを確認する必要があります。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>アプリ専用トークンで、クライアント アプリがリソース テナントに存在することが必要になった

**種類:** 固定  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

2019 年 7 月 26 日に、[クライアント資格情報付与](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)によるアプリ専用トークンの提供方法を変更しました。 以前は、クライアント アプリがテナント内に存在するかどうかに関係なく、アプリでは他のアプリを呼び出すためのトークンを取得できました。 この動作が更新され、シングル テナント リソース (Web API とも呼ばれます) は、リソース テナントに存在するクライアント アプリによってのみ呼び出すことができるようになりました。

ご利用のアプリがリソース テナント内に存在しない場合、`The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` のようなエラー メッセージが表示されます。この問題を解決するには、[管理者の同意エンドポイント](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint)または [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell) を使用して、テナント内にクライアント アプリ サービス プリンシパルを作成する必要があります。これにより、テナントではテナント内で動作するアクセス許可がアプリに付与されます。

詳細については、[認証の新機能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)に関する記事をご覧ください。

> [!NOTE]
> クライアントと API の間の既存の同意は引き続き必要ありません。 アプリでは、引き続き独自の承認チェックを実行する必要があります。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>FIDO2 セキュリティ キーを使用した Azure AD への新しいパスワードなしのサインイン

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Azure AD のお客様は、組織のユーザーとグループに対する FIDO2 セキュリティ キーを管理するためのポリシーを設定できるようになりました。 また、エンド ユーザーは、セキュリティ キーを自分で登録したり、FIDO 対応デバイスを使っているときにキーを使って Web サイト上の Microsoft アカウントにサインインしたり、Azure AD 参加済みの Windows 10 デバイスにサインインしたりできます。

管理者関連の情報の詳細については、[Azure AD へのパスワードなしのサインインを有効にする (プレビュー)](/azure/active-directory/authentication/concept-authentication-passwordless)、エンド ユーザー関連の情報については、[セキュリティ キーを使用するようにセキュリティ情報を設定する (プレビュー)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) に関する記事をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 7 月に、フェデレーションを使用した以下の 18 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial)、[Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial)、[Clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial)、[AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial)、[Looop](https://www.looop.co/schedule-a-demo/)、[productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial)、[MS Azure SSO Access for Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso)、[Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial)、[Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial)、[Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial)、[Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)、[Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial)、[TwineSocial](https://twinesocial.com/)、[Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial)、[HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial)、[PharmID WasteWitness](https://www.pharmid.com/)、[i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)、[JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングの自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページを参照してください

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>ネットワーク セキュリティ グループ用の新しい Azure AD Domain Services サービス タグ

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

IP アドレスと範囲の長いリストの管理に苦労している場合は、Azure ネットワークのセキュリティ グループで新しい **AzureActiveDirectoryDomainServices** ネットワーク サービス タグを使うと、Azure AD Domain Services 仮想ネットワーク サブネットへの受信トラフィックをセキュリティで保護するのに役立ちます。

この新しいサービス タグの詳細については、[Azure AD Domain Services 用のネットワーク セキュリティ グループ](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)に関する記事をご覧ください。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 用の新しいセキュリティ監査 (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

Azure AD Domain Services のセキュリティ監査がパブリック プレビューにリリースされたことをお知らせします。 セキュリティ監査では、Azure AD Domain Services ポータルを使用して、Azure Storage、Azure Log Analytics ワークスペース、Azure Event Hub などのターゲット リソースにセキュリティ監査イベントをストリーミングして、認証サービスに関する重要な分析情報が提供されます。

詳細については、[Azure AD ドメイン サービスでセキュリティ監査を有効にする (プレビュー)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events) に関する記事をご覧ください。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新しい認証方法の使用状況と分析情報 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** セルフサービスによるパスワードのリセット  
**製品の機能:** 監視とレポート

新しい認証方法の使用状況と分析情報のレポートは、Azure Multi-Factor Authentication やパスワード リセットのセルフサービスなどの機能が組織内でどのように登録されて使用されているかを理解するのに役立ちます (各機能の登録済みユーザー数、パスワードのリセットでパスワード リセットのセルフサービスが使用される頻度、リセットが行われた方法など)。

詳細については、[認証方法の使用状況と分析情報 (プレビュー)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) に関する記事をご覧ください。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>新しいセキュリティ レポートは、すべての Azure AD 管理者が利用できます (パブリックプレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

すべての Azure AD 管理者が、**リスクのフラグ付きユーザー** レポートなど、既存のセキュリティ レポートの上部にあるバナーを選択して、**危険なユーザー** レポートや**危険なサインイン** レポートに表示される新しいセキュリティ エクスペリエンスを使い始めることができるようになりました。 時間をかけて、すべてのセキュリティ レポートが以前のバージョンから新しいバージョンに移行され、新しいレポートでは次の追加機能が提供されます。

- 高度なフィルター処理と並べ替え

- ユーザー リスクの無視などの一括操作

- 侵害されたエンティティまたは安全なエンティティの確認

- 次のようなリスクの状態: 危険、無視、修復済み、セキュリティ侵害の確認済み

詳細については、[危険なユーザー レポート](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report)と[リスクの高いサインイン レポート](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)に関する記事をご覧ください。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services 用の新しいセキュリティ監査 (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

Azure AD Domain Services のセキュリティ監査がパブリック プレビューにリリースされたことをお知らせします。 セキュリティ監査では、Azure AD Domain Services ポータルを使用して、Azure Storage、Azure Log Analytics ワークスペース、Azure Event Hub などのターゲット リソースにセキュリティ監査イベントをストリーミングして、認証サービスに関する重要な分析情報が提供されます。

詳細については、[Azure AD ドメイン サービスでセキュリティ監査を有効にする (プレビュー)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events) に関する記事をご覧ください。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>SAML/WS フィードを使用した新しい B2B 直接フェデレーション (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C

直接フェデレーションは、SAML または WS フィード標準をサポートする ID システムを使用すると、IT 管理対象の ID ソリューションが Azure AD ではないパートナーとの連携を容易にするのに役立ちます。 パートナーとの直接フェデレーション関係を設定した後、そのドメインから招待するすべての新しいゲスト ユーザーと、既存の組織アカウントを使って共同作業することができ、ゲストに対するユーザー エクスペリエンスがよりシームレスになります。

詳細については、[ゲスト ユーザーのための AD FS およびサード パーティ プロバイダーとの直接フェデレーション (プレビュー)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation) に関する記事をご覧ください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページをご覧ください。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Azure AD ポータルでの重複するグループ名の新しい確認

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

Azure AD ポータルからグループ名を作成または更新するときは、リソース内の既存のグループ名を複製しているどうかの確認が行われます。 名前が既に別のグループによって使用されていると判断された場合は、名前を変更するように求められます。

詳細については、[Azure AD ポータルでのグループの管](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)に関する記事をご覧ください。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>応答 (リダイレクト) URI での静的クエリ パラメーターの Azure AD による新しいサポート

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Azure AD アプリでは、OAuth 2.0 要求に対する静的クエリ パラメーター (`https://contoso.com/oauth2?idp=microsoft` など) で応答 (リダイレクト) URI を登録して使用できるようになりました。 静的クエリ パラメーターは、応答 URI の他の部分と同様に、応答 URI に対する文字列照合の対象になります。 URL でデコードされたリダイレクト URI に一致する登録文字列がない場合、要求は拒否されます。 応答 URI が見つかった場合は、静的なクエリ パラメーターを含む文字列全体が、ユーザーをリダイレクトするために使われます。

動的応答 URI はセキュリティ上のリスクがあり、認証要求全体で状態情報を保持するために使用できないため、引き続き許可されません。 この目的には、`state` パラメーターを使います。

現在、Azure portal のアプリ登録画面では、クエリ パラメーターは引き続きブロックされます。 ただし、アプリ マニフェストを手動で編集して、アプリにクエリ パラメーターを追加し、テストすることはできます。 詳細については、[認証の新機能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)に関する記事をご覧ください。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>PowerShell コマンドレットで使用できるようになった Azure AD のアクティビティログ (MS Graph API)

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD PowerShell モジュールを通じて Azure AD のアクティビティ ログ (監査レポートとサインイン レポート) を利用できるようになったことをお知らせします。 以前は、MS Graph API エンドポイントを使って独自のスクリプトを作成できましたが、その機能が PowerShell コマンドレットに拡張されました。

これらのコマンドレットの使い方の詳細については、[レポート用の Azure AD PowerShell コマンドレット](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)に関する記事をご覧ください。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Azure AD での監査ログとサインイン ログに関するフィルター コントロールを更新

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

監査ログ レポートとサインイン ログ レポートが更新され、レポート画面で列として追加しなくても、さまざまなフィルターを適用できるようになりました。 さらに、画面に表示するフィルターの数を決定できるようになりました。 これらの更新すべてが連携して、レポートがいっそう読みやすく、ニーズに合うようになります。

これらの更新の詳細については、[監査ログのフィルター処理](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs)および[サインイン アクティビティのフィルター処理](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)に関する記事をご覧ください。

---

## <a name="june-2019"></a>2019 年 6 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Microsoft Graph 用の新しい riskDetections API (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

Microsoft Graph 用の新しい riskDetections API のパブリック プレビューが開始されました。 この新しい API を使用すると、組織の Identity Protection に関連したユーザーおよびサインインのリスク検出の一覧を表示できます。 また、この API を使用して、検出の種類、状態、レベルなどの詳細を含むリスク検出のクエリをさらに効率良く行うことができます。

詳細については、[リスク検出 API のリファレンス ドキュメント](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019年 6 月に、フェデレーションを使用した以下の 22 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial)、[Otsuka Shokai](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial)、[ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial)、[Azure VPN Client](https://portal.azure.com/)、[ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial)、[Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)、[Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial)、[GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial)、[Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/)、[Skore](https://app.justskore.it/)、[Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial)、[CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial)、[Scrible Edu](https://www.scrible.com/sign-in/#/create-account)、[PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial)、[Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial)、[Proptimise OS](https://proptimise.co.uk/software/)、[Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial)、Oracle Access Manager for Oracle Retail Merchandising、Oracle Access Manager for Oracle E-Business Suite、Oracle IDCS for E-Business Suite、Oracle IDCS for PeopleSoft、Oracle IDCS for JD Edwards

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>新しくサポートされた SaaS アプリに対するユーザー アカウントのプロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

新しく統合された次のアプリでのユーザー アカウントの作成、更新、および削除を自動化できるようになりました。

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD による SaaS アプリケーションへのユーザー プロビジョニングの自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページを参照してください

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Azure AD プロビジョニング サービスのリアルタイムの進行状況を表示する

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

Azure AD プロビジョニング エクスペリエンスが更新されて、ユーザーのプロビジョニング プロセスがどのくらい進んでいるかを示す新しい進行状況バーが組み込まれました。 この更新されたエクスペリエンスでは、現在までにプロビジョニングされたユーザー数だけでなく、現在のサイクル中にプロビジョニングされたユーザー数に関する情報も提供されます。

詳細については、「[ユーザー プロビジョニングの状態を確認する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)」を参照してください。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>会社のブランドがサインアウト画面とエラー画面に表示される

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

Azure AD が更新されて、会社のブランドがサインイン ページだけでなくサインアウト画面やエラー画面にも表示されるようになりました。 この機能を有効にするための操作は一切不要で、Azure portal の **[会社のブランド]** 領域で既に設定した資産が Azure AD でそのまま使用されます。

会社のブランドの設定について詳しくは、[組織の Azure Active Directory ページへのブランドの追加](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)に関する記事を参照してください。

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>新しいデプロイでの Azure Multi-Factor Authentication (MFA) Server の廃止

**種類:** 非推奨  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティ & 保護

2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなります。 組織内で多要素認証が必要な新しいお客様は、今後はクラウドベースの Azure Multi-Factor Authentication を使用する必要があります。 7 月 1 日より前に MFA Server をアクティブ化したお客様には、変更が表示されません。 引き続き、最新バージョンをダウンロードしたり、今後の更新プログラムを取得したり、アクティブ化資格情報を生成したりすることができます。

詳細については、「[Azure Multi-Factor Authentication Server の概要](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)」を参照してください。 クラウドベースの Azure Multi-factor Authentication の詳細については、「[クラウド ベースの Azure Multi-Factor Authentication のデプロイの計画](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)」を参照してください。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>サービスの変更:アプリケーション プロキシ サービスでの TLS 1.2 プロトコルのみの今後のサポート

**種類:** 変更の計画  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

お客様にクラス最高の暗号化を提供するために、Application Proxy サービスでは TLS 1.2 プロトコルのみにアクセスを制限しています。 この変更は、TLS 1.2 プロトコルのみを既に使用しているお客様に徐々に適用されているため、変更を意識することはありません。

2019 年 8 月 31日で TLS 1.0 と TLS 1.1 が廃止されますが、改めて事前通知を行いますので、この変更に備える時間が与えられます。 この変更に備えるために、Application Proxy を通じて発行されたアプリへのアクセスにユーザーが使用するすべてのクライアントを含む、クライアントとサーバーおよびブラウザーとサーバーの組み合わせが、TLS 1.2 プロトコルを使用して Application Proxy サービスへの接続を維持するように更新されていることを確認してください。 詳しくは、「[Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)」を参照してください。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用状況および分析情報レポートを使用して、アプリに関連するサインイン データを表示する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

これで、Azure portal の **[エンタープライズ アプリケーション]** 領域にある使用状況および分析情報レポートを使用して、以下に関する情報を含んだサインイン データをアプリケーションを中心にして表示できます。

- 組織内で最もよく使われるアプリ

- サインインの失敗が最も多いアプリ

- アプリごとの上位のサインイン エラー

この機能の詳細については、「[Azure Active Directory ポータルの使用状況と分析情報のレポート](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)」を参照してください

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Azure AD を使用してクラウド アプリへのユーザー プロビジョニングを自動化する

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** 監視とレポート

これらの新しいチュートリアルに従って、Azure AD プロビジョニング サービスを使用して、次のクラウド ベース アプリのユーザー アカウントの作成、削除、および更新を自動化します。

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

グループ オブジェクトのプロビジョニング方法に関する情報が記載されている、この新しい [Dropbox チュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)に従うこともできます。

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD での SaaS アプリケーションへのユーザー プロビジョニングの自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)に関するページを参照してください。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Azure AD で ID セキュリティ スコアが利用可能 (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** 該当なし  
**製品の機能:** ID のセキュリティ & 保護

これで、Azure AD で ID セキュリティ スコア機能を使用することにより、ID セキュリティ体制を監視し改善できます。 ID セキュリティ スコア機能では、次の操作に役立つ単一のダッシュボードを使用します。

- 1 から 223 のスコアに基づいて ID セキュリティ体制を客観的に測定する。

- ID セキュリティの強化を計画する

- セキュリティの強化の成功を確認する

ID セキュリティ スコア機能の詳細については、「[Azure Active Directory の ID セキュリティ スコアとは](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)」を参照してください。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>新しいアプリの登録エクスペリエンスが利用可能 (一般提供)

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** 開発者エクスペリエンス

新しい[アプリの登録](https://aka.ms/appregistrations)エクスペリエンスが一般提供され利用可能になりました。 この新しいエクスペリエンスには、Azure portal とアプリケーション登録ポータルの使い慣れているすべての主要機能が含まれ、これらの機能は以下を通じて向上しています。

- **アプリの管理の向上。** さまざまなポータルにわたってアプリを表示する代わりに、1 か所ですべてのアプリを表示できるようになりました。

- **アプリの登録の簡略化。** 改善されたナビゲーション エクスペリエンスから、刷新されたアクセス許可選択エクスペリエンスまで、アプリの登録および管理が簡単になりました。

- **詳細情報。** クイック スタート ガイドなど、アプリに関する詳細が表示されます。

詳細については、[Microsoft ID プラットフォーム](https://docs.microsoft.com/azure/active-directory/develop/)と[新しいアプリの登録エクスペリエンスが一般に利用可能になった](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)ことに関するページを参照してください。 ブログでのお知らせ。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Identity Protection の Risky Users API で使用できる新機能

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

Risky Users API を使用して、ユーザーのリスク履歴を取得し、危険なユーザーを無視し、ユーザーを侵害対象として確認できるようになったことをお知らせします。 この変更により、より効率的にユーザーのリスク状態を更新し、リスク履歴を理解できるようになりました。

詳細については、[Risky Users API のリファレンス ドキュメント](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 5 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019年 5 月に、フェデレーションを使用した以下の 21 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial)、[Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial)、[Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)、[Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial)、[Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial)、[Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)、[Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial)、[Marketo Sales Engage](https://toutapp.com/login)、[ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial)、[OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial)、[Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial)、[Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)、[Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial)、[webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)、[RedFlag](https://pocketstop.com/redflag/)、[Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial)、[Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial)、[JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial)、[NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial)、[Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial)、[MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Azure AD ポータルの強化されたグループ作成および管理エクスペリエンス

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

Azure AD ポータルでグループに関連するエクスペリエンスの機能強化を行いました。 これらの機能強化により、管理者は、グループの一覧やメンバーの一覧をより適切に管理し、追加の作成オプションを提供できます。

機能強化は次のとおりです。

- メンバーシップの種類およびグループの種類による基本フィルター処理。

- ソースやメール アドレスなどの新しい列の追加。

- 簡単に選択解除するためのグループ、メンバー、および所有者の一覧を複数選択する機能。

- グループの作成中にメール アドレスを選択し、所有者を追加する機能。

詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)」を参照してください。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Azure AD ポータルで Office 365 グループの名前付けポリシーを構成する (一般提供)

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

管理者は、Azure AD ポータルを使用して、Office 365 グループの名前付けポリシーを構成できるようになりました。 この変更は、組織内のユーザーによって作成または編集される Office 365 グループに対して一貫性のある名前付け規則を適用するのに役立ちます。

Office 365 グループの名前付けポリシーは、次の 2 通りの方法で構成できます。

- グループ名に自動的に追加されるプレフィックスまたはサフィックスを定義します。

- グループ名で許可されない、組織の禁止されている単語のカスタマイズ セットをアップロードします (たとえば、“CEO、Payroll、HR”)。

詳細情報については、「[Azure Active Directory での Office 365 グループの名前付けポリシーの強制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)」をご覧ください。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API エンドポイントが Azure AD アクティビティ ログで利用可能(一般提供)

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD アクティビティ ログで Microsoft Graph API エンドポイントのサポートが一般的に利用できるようになったことをお知らせします。 このリリースでは、バージョン 1.0 の Azure AD 監査ログとサインイン ログ API を使用できるようになりました。

詳細については、[Azure AD 監査ログ API の概要](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)に関するページを参照してください。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>管理者が統合登録プロセスに条件付きアクセスを使用可能 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護  

管理者は、統合登録ページで使用する条件付きアクセス ポリシーを作成できるようになりました。 これには、次の場合に登録を許可するポリシーの適用が含まれます。

- ユーザーが信頼できるネットワーク上にいる場合。

- ユーザーのサインイン リスクが低い場合。

- ユーザーがマネージド デバイス上にある場合。

- ユーザーが組織の利用規約 (TOU) に同意した場合。

条件付きアクセスとパスワードのリセットの詳細については、[「Azure AD の MFA とパスワードのリセットの統合登録エクスペリエンスの条件付きアクセス」のブログ記事](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)をご覧ください。 統合登録プロセスの条件付きアクセス ポリシーの詳細については、「[統合登録の条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)」を参照してください。 Azure AD Terms of Use 機能の詳細については、「[Azure Active Directory Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)」を参照してください。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>新しい Azure AD 脅威インテリジェンス検出が、Azure AD Identity Protection で利用可能

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

Azure AD 脅威インテリジェンス検出が、Azure AD Identity Protection 機能の一部として利用できるようになりました。 この新しい機能は、Microsoft の内部および外部の脅威インテリジェンス ソースに基づいて、特定のユーザーにとって異常なユーザー アクティビティや、既知の攻撃パターンに一致するアクティビティを示す場合に役立ちます。

更新されたバージョンの Azure AD Identity Protection の詳細については、[Azure AD Identity Protection の 4 つの主要な機能強化がパブリック ビューに示されるようになった](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935)ことに関するブログと「[Azure Active Directory Identity Protection (更新版) とは](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2)」 の記事を参照してください。 Azure AD 脅威インテリジェンス検出の詳細については、[Azure Active Directory Identity Protection のリスク検出](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence)に関する記事を参照してください。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD エンタイトルメント管理が利用可能 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** Identity Governance  
**製品の機能:** Identity Governance

Azure AD エンタイトルメント管理 (現在パブリック プレビュー段階) は、従業員とビジネス パートナーがアクセスを要求する方法、承認を行うユーザー、アクセスできる時間を定義するアクセス パッケージの管理をお客様が委任できるようにします。 アクセス パッケージは、Azure AD と Office 365 の各グループでのメンバーシップ、エンタープライズ アプリケーションでのロールの割り当て、および SharePoint Online サイトのロールの割り当てを管理できます。 エンタイトルメント管理の詳細情報については、[Azure AD エンタイトルメント管理の概要](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)に関する記事をご覧ください。 Privileged Identity Management、アクセス レビュー、および利用規約などの Azure AD Identity Governance 機能の広がりについて詳しくは、「[Azure AD Identity Governance とは](../governance/identity-governance-overview.md)」をご覧ください。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Azure AD ポータルで Office 365 グループの名前付けポリシーを構成する (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

管理者は、Azure AD ポータルを使用して、Office 365 グループの名前付けポリシーを構成できるようになりました。 この変更は、組織内のユーザーによって作成または編集される Office 365 グループに対して一貫性のある名前付け規則を適用するのに役立ちます。

Office 365 グループの名前付けポリシーは、次の 2 通りの方法で構成できます。

- グループ名に自動的に追加されるプレフィックスまたはサフィックスを定義します。

- グループ名で許可されない、組織の禁止されている単語のカスタマイズ セットをアップロードします (たとえば、“CEO、Payroll、HR”)。

詳細情報については、「[Azure Active Directory での Office 365 グループの名前付けポリシーの強制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)」をご覧ください。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD アクティビティ ログを Azure Monitor で使用できるようになりました (一般提供)。

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD アクティビティ ログの視覚化に関するフィードバックに対応するために、Log Analytics に新しい分析情報機能を導入します。 この機能により、Workbooks という対話型テンプレートを使用して Azure AD リソースに関する分析情報を得ることができます。 これらの既製の Workbooks は、アプリまたはユーザーの詳細情報を提供することができます。これには以下が含まれます。

- **サインイン。** サインインの場所、使用中のオペレーティング システムまたはブラウザーのクライアントとバージョン、成功または失敗したサインインの回数などのアプリおよびユーザーの詳細情報を提供します。

- **レガシ認証と条件付きアクセス。** 条件付きアクセス ポリシーによってトリガーされる Multi-Factor Authentication の使用、条件付きアクセス ポリシーを使用するアプリなど、レガシ認証を使用するアプリおよびユーザーの詳細情報を提供します。

- **サインイン エラー分析。** サインイン エラーの発生原因がユーザー アクションか、ポリシーの問題か、またはインフラストラクチャかを判別するのを支援します。

- **カスタム レポート。** 新規の Workbooks を作成したり、既存の Workbooks を編集したりして、組織の Insights 機能のカスタマイズに役立てることができます。

詳細情報については、「[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 4 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 4 月に、フェデレーションを使用した以下の 21 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial)、[HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)、[Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial)、[MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial)、[Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)、[Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial)、[Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial)、[MileIQ](https://mileiq.onelink.me/991934284/7e980085)、[PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)、[EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)、[RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial)、[AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)、[Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)、[Alibaba Cloud (ロールベースの SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial)、[Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial)、[Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial)、[GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial)、[Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial)、[monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial)、[SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)、[Indiggo](https://indiggolead.com/)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新規アクセス レビューの頻度オプションと複数のロールの選択

**種類:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** Identity Governance

Azure AD アクセス レビューの新しい更新プログラムにより、次のことが可能になります。

- アクセス レビューの頻度を、以前からある、週に 1 度、月に 1 度、4 半期に 1 度、および 1 年に 1 度のオプションに加え、「**半年に 1 度**」に変更します。

- 1 つのアクセス レビューを作成するときに、複数の Azure AD と Azure リソースのロールを選択します。 この場合は、すべてのロールが同じ設定値で設定され、すべてのレビュー担当者に同時に通知が送られます。

アクセス レビューを作成する方法の詳細については、[Azure AD アクセス レビューでのグループまたはアプリケーションのアクセス レビューの作成](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)に関するページを参照してください

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect の電子メール アラート システムの切り替えが行われており、一部のお客様に対して新規電子メール送信者情報が送信される

**種類:** 変更された機能  
**サービス カテゴリ:** AD 同期  
**製品の機能:** プラットフォーム

Azure AD Connect で電子メール アラート システムの切り替えが行われており、一部のお客様に新規電子メール送信者が表示される可能性があります。 これに対処するには、`azure-noreply@microsoft.com` を組織の許可リストに追加する必要があります。さもないと、Office 365、Azure、またはお使いの同期サービスから重要なアラートを引き続き受信できなくなります。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Azure AD Connect でフェデレーション ドメイン間の UPN サフィックスの変更が正常に行われるようになりました

**種類:** 固定  
**サービス カテゴリ:** AD 同期  
**製品の機能:** プラットフォーム

Azure AD Connect で、あるフェデレーション ドメインから別のフェデレーション ドメインに、ユーザーの UPN サフィックスを正しく変更できるようになりました。 この修正により、同期サイクル中に FederatedDomainChangeError エラー メッセージを受け取ったり、"Azure Active Directory でこのオブジェクトを更新できません。属性 [FederatedUser.UserPrincipalName] が無効です。 ローカルのディレクトリ サービスでこの値を更新してください" という通知メールを受け取ったりすることがなくなります。

詳しくは、[同期中のエラーのトラブルシューティング](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)に関するページをご覧ください。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Azure AD でアプリ保護ベースの条件付きアクセス ポリシーを使用してセキュリティを強化 (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

**[アプリの保護ポリシーが必要]** を使用することで、アプリ保護ベースの条件付きアクセスを使用できるようになりました。 この新しいポリシーは、次のことを防止することで組織のセキュリティ向上を支援します。

- ユーザーが Microsoft Intune ライセンスなしでアプリにアクセスできるようにすること。

- ユーザーが Microsoft Intune アプリ保護ポリシーを取得できないようにすること。

- ユーザーが Microsoft Intune アプリ保護ポリシーなしでアプリにアクセスできるようにすること。

詳細については、[条件付きアクセスを使用してクラウド アプリへのアクセスにアプリ保護ポリシーを要求する方法](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)に関するページを参照してください。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge での Azure AD シングル サインオンと条件付きアクセスの新しいサポート (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

Microsoft Edge に対する Azure AD のサポートが強化されました。これには、Azure AD シングル サインオンと条件付きアクセスの新しいサポートの提供が含まれます。 以前に Microsoft Intune Managed Browser を使用していた場合は、代わりに Microsoft Edge を使用できるようになりました。

条件付きアクセスを使用したデバイスとアプリの設定と管理の詳細については、「[条件付きアクセスを使用してクラウド アプリへのアクセスにマネージド デバイスを要求する](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)」および「[条件付きアクセスを使用してクラウド アプリへのアクセスに承認されたクライアント アプリを要求する](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)」を参照してください。 Microsoft Intune ポリシーと Microsoft Edge を使用してアクセスを管理する方法の詳細については、「[Microsoft Intune のポリシーで保護されたブラウザーを使用してインターネット アクセスを管理する](https://docs.microsoft.com/intune/app-configuration-managed-browser)」を参照してください。

---

## <a name="march-2019"></a>2019 年 3 月

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C での Identity Experience Framework とカスタム ポリシーのサポートが使用可能になりました (GA)

**種類:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C

Azure AD B2C でカスタム ポリシーを作成できるようになりました。これには、Azure SLA のもとで大規模にサポートされる次のタスクが含まれます。

- カスタム ポリシーを使用して、カスタム認証のユーザー体験を作成およびアップロードする。

- 要求プロバイダー間のやり取りとしてユーザー体験を順を追って記述する。

- ユーザー体験の条件分岐を定義する。

- リアルタイムの意思決定および通信で使用するために、要求を変換およびマップする。

- カスタム認証のユーザー体験で REST API 対応サービスを使用する。 たとえば、電子メール プロバイダー、CRM、および独自の承認システムで。

- OpenIDConnect プロトコルに準拠している ID プロバイダーとフェデレーションする。 たとえば、マルチテナント Azure AD、ソーシャル アカウント プロバイダー、または 2 要素検証プロバイダーと。

カスタム ポリシーの作成の詳細については、[Azure Active Directory B2C でのカスタム ポリシーの開発者向けメモ](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom)に関するページ、および [Alex Simon のブログ記事 (ケース スタディを含む)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791) を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2019 年 3 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2019 年 3 月に、フェデレーションがサポートされる次の 14 の新しいアプリがアプリ ギャラリーに追加されました。

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/)、[MediusFlow](https://office365.cloudapp.mediusflow.com/)、[ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial)、[Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial)、[ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial)、[Explanation-Based Auditing System](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial)、[Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial)、[Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial)、[Cinode](https://cinode.com/)、[Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial)、[Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial)、[SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial)、[Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial)、[TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD ギャラリー内の新しい Zscaler および Atlassian プロビジョニング コネクタ - 2019 年 3 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合

次のアプリでのユーザー アカウントの作成、更新、および削除を自動化します。

[Zscaler](https://aka.ms/ZscalerProvisioning)、[Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning)、[Zscaler One](https://aka.ms/ZscalerOneProvisioning)、[Zscaler 2](https://aka.ms/ZscalerTwoProvisioning)、[Zscaler 3](https://aka.ms/ZscalerThreeProvisioning)、[Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning)、[Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

自動化されたユーザー アカウント プロビジョニングを使用して組織をより適切にセキュリティ保護する方法の詳細については、[Azure AD での SaaS アプリケーションへのユーザー プロビジョニングの自動化](https://aka.ms/ProvisioningDocumentation)に関するページを参照してください。

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Azure AD ポータルで削除された Office 365 グループの復元および管理

**種類:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

Azure AD ポータルから削除された Office 365 グループを表示および管理できるようになりました。 この変更により、どのグループが復元できるかを確認したり、組織で必要がなくなったすべてのグループを完全に削除したりできるようになります。

詳細については、[有効期限が切れたグループや削除されたグループの復元](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)に関するページを参照してください。

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Azure AD SAML で保護されたオンプレミス アプリで、アプリケーション プロキシ経由でシングル サインオンを使用できるようになりました (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

SAML で認証されたオンプレミス アプリで、これらのアプリへのリモート アクセスと共に、アプリケーション プロキシ経由でシングル サインオン (SSO) エクスペリエンスを使用できるようになりました。 オンプレミス アプリで SAML SSO を設定する方法の詳細については、「[アプリケーション プロキシ (プレビュー) を使用したオンプレミスのアプリケーションに対する SAML シングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)」を参照してください。

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>信頼性とユーザー エクスペリエンスを向上させるために要求ループ内のクライアント アプリが中断されます

**種類:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

クライアント アプリが、短期間に数百の同じログイン要求を誤って発行する場合があります。 これらの要求は、成功するかどうかにかかわらず、そのすべてがユーザー エクスペリエンスの低下や IDP のワークロードの増加につながるため、すべてのユーザーの待ち時間が長くなり、IDP の可用性が低下します。

この更新プログラムは、通常の動作の範囲を超えて、重複した要求を短期間に複数回発行するクライアント アプリに `invalid_grant` エラー: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` を送信します。 この問題が発生したクライアント アプリは対話型プロンプトを表示し、ユーザーに再度サインインすることを要求する必要があります。 この変更や、このエラーが発生した場合にアプリを修正する方法の詳細については、「[認証の新機能](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)」を参照してください。

---

### <a name="new-audit-logs-user-experience-now-available"></a>[監査ログ] の新しいユーザー エクスペリエンスが使用可能になりました

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

読みやすさと情報の検索方法の両方を向上させるのに役立つ新しい Azure AD **[監査ログ]** ページが作成されました。 新しい **[監査ログ]** ページを表示するには、Azure AD の **[アクティビティ]** セクションで **[監査ログ]** を選択します。

![サンプル情報を含む新しい [監査ログ] ページ](media/whats-new/audit-logs-page.png)

新しい **[監査ログ]** ページの詳細については、「[Azure Active Directory ポータルの監査アクティビティ レポート](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)」を参照してください。

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>正しく構成されていない条件付きアクセス ポリシーからの誤った管理者ロックアウトの防止に役立つ新しい警告およびガイダンス

**種類:** 変更された機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

管理者が、正しく構成されていない条件付きアクセス ポリシーにより自分のテナントから誤って自身をロックすることを防止するのに役立つように、Azure Portal に新しい警告および更新されたガイダンスが作成されました。 新しいガイダンスの詳細については、「[Azure Active Directory 条件付きアクセスのサービス依存関係の概要](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)」を参照してください。

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>モバイル デバイスでのエンドユーザーの利用規約エクスペリエンスの改善

**種類:** 変更された機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

モバイル デバイスで利用規約を確認して同意する方法の改善に役立つように、既存の利用規約エクスペリエンスが更新されました。 情報のズームイン/ズームアウト、前の画面への戻り、ダウンロード、およびハイパーリンクの選択が可能になりました。 更新された利用規約の詳細については、[Azure Active Directory Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)に関するページを参照してください。

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>新しい Azure AD アクティビティ ログのダウンロード エクスペリエンスが使用可能になりました

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

大量のアクティビティ ログを Azure Portal から直接ダウンロードできるようになりました。 この更新プログラムでは、次のことが可能になります。

- 最大 250,000 行をダウンロードする。

- ダウンロードが完了したら通知を受け取る。

- ファイル名をカスタマイズする。

- 出力形式 (JSON または CSV のどちらか) を決定する。

この機能の詳細については、「[クイック スタート: Azure portal を使用して監査レポートをダウンロードする](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)」を参照してください。

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>重大な変更:Exchange ActiveSync (EAS) による条件評価の更新

**種類:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** Access Control

現在、Exchange ActiveSync (EAS) が次の条件を評価する方法を更新しているところです。

- 国、地域、または IP アドレスに基づいたユーザーの場所

- サインイン リスク

- デバイスのプラットフォーム

以前に条件付きアクセス ポリシーでこれらの条件を使用している場合は、条件動作が変更される可能性があることに注意してください。 たとえば、以前にあるポリシーでユーザーの場所の条件を使用している場合は、そのポリシーが、ユーザーの場所に基づいて省略されるようになったことに気付く可能性があります。

---
