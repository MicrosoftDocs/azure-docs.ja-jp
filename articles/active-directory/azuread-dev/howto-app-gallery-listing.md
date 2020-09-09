---
title: Azure AD アプリケーション ギャラリーにアプリを公開する | Microsoft Docs
description: シングル サインオンをサポートするアプリケーションを Azure Active Directory アプリ ギャラリーで公開する方法を説明します
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/20/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 662bda03fc2d389ed316bd2c7141d5e6a30cef1a
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814633"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

この記事では、Azure Active Directory (Azure AD) アプリケーション ギャラリーでアプリケーションを一覧表示し、シングル サインオン (SSO) を実装し、一覧を管理する方法を示します。

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーとは

- 顧客は考えられる最良のシングル サインオン エクスペリエンスを探しています。
- アプリケーションの構成は簡単で最小限です。
- クイック検索でギャラリー内のアプリケーションが検索されます。
- Free、Basic、Premium すべての Azure AD ユーザーがこの情報を使用できます。
- 共通の顧客向けの詳細な構成手順チュートリアルがあります。
- System for Cross-domain Identity Management ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) を使用している顧客は、同じアプリのプロビジョニングを使用できます。

## <a name="prerequisites"></a>前提条件

- フェデレーション アプリケーション (Open ID と SAML/WS-Fed) の場合、Azure AD アプリ ギャラリーに一覧表示するには、アプリケーションでサービスとしてのソフトウェア (SaaS) モデルをサポートする必要があります。 エンタープライズ ギャラリー アプリケーションでは、特定の顧客ではなく複数の顧客構成をサポートする必要があります。
- Open ID Connect の場合、アプリケーションはマルチテナントである必要があり、[Azure AD 同意フレームワーク](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)がアプリケーションに適切に実装されている必要があります。 ユーザーは、すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにサインイン要求を送信できます。 トークンで受け取ったテナント ID とユーザーの UPN に基づいてユーザー アクセスを制御できます。
- SAML 2.0/WS-Fed の場合、ご利用のアプリケーションには、SP または IDP モードで SAML/WS-Fed SSO 統合を行う機能を備えている必要があります。 要求を送信する前に、この機能が正しく動作していることを確認してください。
- パスワード SSO の場合、シングル サインオンが期待どおりに動作するように、アプリケーションでフォーム認証をサポートしてパスワード保管を行えることを確認します。
- テストには、2 人以上のユーザーが登録されている永続的なアカウントが必要です。

**開発者向け Azure AD の取得方法**

すべての Premium Azure AD 機能を持つ無料のテスト アカウントを入手できます。90 日間無料で、開発作業を行っている限り、延長することができます: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>ポータルで要求を送信する

Azure ADでアプリケーションの統合が作動するのをテストした後、[Microsoftアプリケーションネットワークポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)へのアクセス要求を送信してください。

サインイン後に次のページが表示された場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。 要求の送信に使用する電子メール アカウントを指定します。 [name@yourbusiness.com](mailto:name@yourbusiness.com)などの会社の電子メールアドレスをお勧めします。 Azure AD チームにより、アカウントが Microsoft アプリケーション ネットワーク ポータルに追加されます。

![SharePoint ポータルでのアクセス要求メッセージ](./media/howto-app-gallery-listing/errorimage.png)

アカウントが追加されると、Microsoft アプリケーション ネットワーク ポータルにサインインできるようになります。

サインイン後に次のページが表示される場合、アクセスが必要な業務上の正当な理由をテキスト ボックスに入力します。 **[アクセス権の要求]** を選択します。

  ![SharePoint ポータル上の業務上の正当な理由ボックス](./media/howto-app-gallery-listing/accessrequest.png)

Microsoft のチームが詳細をレビューし、それに応じてアクセスを提供します。 要求が承認されると、ポータルにサインインし、ホーム ページの **[要求の送信 (ISV)]** タイルを選択することで、要求を送信できます。

![ホーム ページの [要求の送信 (ISV)] タイル](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>ポータルへのログインに関する問題

ログイン中に次のエラーが表示される問題について、ここで詳細と解決方法を説明します。

* サインインが次に示すようにブロックされた場合。

  ![ギャラリーのアプリケーションの解決に関する問題](./media/howto-app-gallery-listing/blocked.png)

**起きていること:**

ゲスト ユーザーは、ホーム テナントにフェデレーションされます。このテナントも、Azure AD です。 ゲスト ユーザーは高リスクです。 Microsoft では、高リスクのユーザーにリソースへのアクセスを許可していません。 すべての高リスク ユーザー (従業員またはゲストおよびベンダー) が Microsoft リソースにアクセスするには、リスクを修復またはクローズする必要があります。 ゲスト ユーザーの場合、このユーザーのリスクはホーム テナントに由来しており、ポリシーはリソース テナント (この場合は Microsoft) から取得されます。
 
**安全なソリューション:**

* MFA に登録されたゲスト ユーザーは、自分のユーザー リスクを修復します。 そうするには、ゲスト ユーザーがホーム テナントで、セキュリティで保護されたパスワード変更またはリセット (https://aka.ms/sspr) を行います (これにはホーム テナントでの MFA および SSPR が必要です)。 セキュリティで保護されたパスワード変更またはリセットは、オンプレミスではなく Azure AD で開始する必要があります。

* ゲスト ユーザーは、管理者にリスクを修正してもらいます。 この場合、管理者はパスワードのリセット (一時的なパスワードの生成) を行います。 これには Identity Protection は必要ありません。 ゲスト ユーザーの管理者は、 https://aka.ms/RiskyUsers にアクセスして、[パスワードのリセット] をクリックできます。

* ゲスト ユーザーは、管理者にリスクをクローズまたは無視してもらいます。 この場合も、Identity Protection は必要ありません。 管理者は https://aka.ms/RiskyUsers にアクセスして、[ユーザー リスクを無視する] をクリックできます。 ただし、管理者はユーザー リスクをクローズする前にデュー デリジェンスを行い、これが偽陽性のリスク評価であったことを確認する必要があります。 そうしない場合、調査なしでリスク評価を無視することで、自分と Microsoft のリソースをリスクにさらすことになります。

> [!NOTE]
> アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

## <a name="implement-sso-by-using-the-federation-protocol"></a>フェデレーション プロトコルを使用して SSO を実装する

Azure AD アプリ ギャラリーにアプリケーションを公開するには、まず、Azure AD でサポートされている以下のフェデレーション プロトコルのいずれかを実装する必要があります。 また、Azure AD アプリケーション ギャラリーの使用条件に同意する必要もあります。 [この Web ページ](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)で Azure AD アプリケーション ギャラリーの使用条件を読み取ります。

- **OpenID Connect**:Open ID Connect プロトコルを使用してアプリケーションを Azure AD と統合するには、[開発者向けの手順](v1-authentication-scenarios.md)に従ってください。

    ![ギャラリーでの OpenID Connect アプリケーションの一覧表示](./media/howto-app-gallery-listing/openid.png)

    * OpenID Connect を使用してギャラリー内の一覧にご利用のアプリケーションを追加する場合は、上記のように **[OpenID Connect & OAuth 2.0]** を選択します。
    * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

- **SAML 2.0** または **WS-Fed**:ご利用のアプリで SAML 2.0 をサポートしている場合、[カスタム アプリケーションを追加する手順](../manage-apps/view-applications-portal.md)を使って、Azure AD テナントと直接統合できます。

  ![ギャラリーでの SAML 2.0 または WS-Fed アプリケーションの一覧表示](./media/howto-app-gallery-listing/saml.png)

  * **SAML 2.0** または **WS-Fed** を使用してギャラリー内の一覧にご利用のアプリケーションを追加する場合は、上記のように **[SAML 2.0/WS-Fed]** を選択します。

  * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

## <a name="implement-sso-by-using-the-password-sso"></a>パスワード SSO を使用して SSO を実装する

HTML サインイン ページがある Web アプリケーションを作成して、[パスワードベースのシングル サインオン](../manage-apps/what-is-single-sign-on.md)を構成します。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、組織のソーシャル メディア アプリ アカウントなど、1 つのアカウントを共有する必要があるシナリオにも便利です。

![ギャラリーでのパスワード SSO アプリケーションの一覧表示](./media/howto-app-gallery-listing/passwordsso.png)

* パスワード SSO を使用してギャラリー内の一覧にご利用のアプリケーションを追加する場合は、次に示すように **[Password SSO(UserName & Password)]** を選択します。
* アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

## <a name="request-for-user-provisioning"></a>ユーザー プロビジョニングの要求

次の画像に示されたプロセスに従って、ユーザー プロビジョニングを要求します。

   ![ユーザー プロビジョニングの要求](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>既存の一覧を更新または削除する

Azure AD アプリ ギャラリーの既存のアプリケーションを更新または削除するには、まず[アプリケーション ネットワーク ポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)で要求を送信する必要があります。 Office 365 アカウントがある場合は、それを使ってこのポータルにサインインします。 ない場合は、自分の Microsoft アカウント (Outlook、Hotmail など) を使ってサインインします。

- 次の画像に示すように適切なオプションを選択します。

    ![ギャラリーでの SAML アプリケーションの一覧表示](./media/howto-app-gallery-listing/updateorremove.png)

    * 既存のアプリケーションを更新するには、要件に従って適切なオプションを選択します。
    * Azure AD アプリ ギャラリーから既存のアプリケーションを削除する場合は、 **[Remove my application listing from the gallery]\(自分のアプリケーション一覧をギャラリーから削除する\)** を選択します。
    * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

## <a name="list-requests-by-customers"></a>顧客による一覧表示の要求

顧客は、 **[App requests by Customers]\(顧客によるアプリ要求\)**  >  **[新しい要求の送信]** を選択することで、アプリケーションの一覧表示の要求を送信できます。

![顧客が要求したアプリ タイルを示します](./media/howto-app-gallery-listing/customer-submit-request.png)

顧客が要求したアプリケーションのフローは。次のとおりです。

![顧客が要求したアプリ フローを示します](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>タイムライン

SAML 2.0 または WS-Fed アプリケーションをギャラリーに一覧表示するプロセスのタイムラインは、7 から 10 営業日です。

  ![ギャラリーに SAML アプリケーションを一覧するタイムライン](./media/howto-app-gallery-listing/timeline.png)

OpenID Connect アプリケーションをギャラリーに一覧表示するプロセスのタイムラインは、2 から 5 営業日です。

  ![ギャラリーに OpenID Connect アプリケーションを一覧表示するタイムライン](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>エスカレーション

エスカレーションについてはすべて、[Azure AD の SSO 統合チーム](mailto:SaaSApplicationIntegrations@service.microsoft.com)の SaaSApplicationIntegrations@service.microsoft.com にメールでご連絡いただけば、可能な限り早く対応します。