---
title: アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する | Microsoft Docs
description: シングル サインオンをサポートするアプリケーションを Azure Active Directory アプリ ギャラリーで公開する方法を説明します
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41b407b4343db4f594049c4b1027fe4279dab840
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482945"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>方法:アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する

この記事では、Azure AD アプリケーション ギャラリーでアプリケーションを一覧表示し、シングル サインオン (SSO) を実装し、一覧を管理する方法を示します。

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーとは

- 顧客は考えられる最良のシングル サインオン エクスペリエンスを探しています。
- アプリケーションの構成は簡単で最小限です。
- クイック検索でギャラリー内のアプリケーションが検索されます。
- Free、Basic、Premium すべての Azure AD ユーザーがこの情報を使用できます。
- 共通の顧客向けの詳細な構成手順チュートリアルがあります。
- SCIM を使っているユーザーは、同じアプリのプロビジョニングを使うことができます。

## <a name="prerequisites"></a>前提条件

- フェデレーション アプリケーション (Open ID と SAML/WS-Fed) の場合、Azure AD ギャラリーに登録するにはアプリケーションで SaaS モデルをサポートする必要があります。 エンタープライズ ギャラリー アプリケーションは、特定の顧客ではなく複数の顧客構成をサポートする必要があります。

- Open ID Connect の場合、アプリケーションはマルチテナントである必要があり、[Azure AD 同意フレームワーク](consent-framework.md)がアプリケーションに適切に実装されている必要があります。 ユーザーは、すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにログイン要求を送信できます。 トークンで受け取ったテナント ID とユーザーの UPN に基づいてユーザー アクセスを制御できます。

- SAML 2.0/WS-Fed の場合、アプリケーションは、SP または IDP モードで SAML/WS-Fed SSO 統合を行う機能を備えている必要があります。 要求を送信する前に、これが正しく機能していることを確認してください。

- パスワード SSO の場合、シングル サインオンが期待どおりに動作するように、アプリケーションがフォーム認証をサポートしてパスワード保管を実行できることを確認してください。

- 自動ユーザー プロビジョニング要求の場合、SAML 2.0/WS-Fed を使用して、シングル サインオン機能を有効にして、アプリケーションをギャラリーに一覧表示する必要があります。 ポータルで SSO とユーザー プロビジョニングを一緒に要求することができます (まだ一覧表示されていない場合)。

>[!NOTE]
>SCIM コネクタに関する多数の要求に取り組んでいるため、ポータルでの新しい要求の受け取りを停止しました。 今後、通知があるまでは要求を控えてください。 この遅延により、ご不便をおかけして申し訳ありません。

## <a name="submit-the-request-in-the-portal"></a>ポータルで要求を送信する

アプリケーションの統合が Azure AD で動作することをテストした後は、[アプリケーション ネットワーク ポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)へのアクセスを求める要求を送信します。 Office 365 アカウントがある場合は、それを使ってこのポータルにサインインします。 ない場合は、Microsoft アカウント (Outlook、Hotmail など) を使ってサインインします。

サインイン後に次のページが表示されている場合、リクエストの送信に使用する電子メール アカウントを指定して、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合せください。 すると Azure AD チームにより、アカウントが Microsoft Application Network Portal に追加されます。

![SharePoint ポータルでのアクセス要求](./media/howto-app-gallery-listing/errorimage.png)

アカウントが追加されると、Microsoft Application Network Portal にサインインできるようになります。

そしてサインイン後に次のページが表示される場合、ビジネスでアクセスが必要な正当な理由をテキスト ボックスに入力し、 **[アクセスの要求]** を選びます。

  ![SharePoint ポータルでのアクセス要求](./media/howto-app-gallery-listing/accessrequest.png)

Microsoft のチームが詳細をレビューし、それに応じてアクセスを提供します。 要求が承認されると、ポータルにサインインし、ホーム ページの **[Submit Request (ISV)]** \(要求の送信 (ISV)\) タイルをクリックして、要求を送信できます。

![SharePoint ポータルのホーム ページ](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

## <a name="implementing-sso-using-federation-protocol"></a>フェデレーション プロトコルを使用した SSO の実装

Azure AD アプリケーション ギャラリーにアプリケーションを一覧表示するには、まず、AzureAD でサポートされている、次のフェデレーション プロトコルのいずれかを実装し、Azure AD アプリケーション　ギャラリーの条件に同意する必要があります。 [こちら](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)から、Azure AD アプリケーション ギャラリーの使用条件を読み取ります。

- **OpenID Connect**:Open ID Connect プロトコルを使用してアプリケーションを Azure AD と統合するには、[開発者向けの手順](authentication-scenarios.md)に従ってください。

    ![ギャラリーに OpenID Connect アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/openid.png)

    * OpenID Connect を使用してギャラリー内の一覧にアプリケーションを追加する場合は、上記の **[OpenID Connect & OAuth 2.0]** を選択します。
    * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。 

- **SAML 2.0** または **WS-Fed**:アプリが SAML 2.0 をサポートしている場合、[カスタム アプリケーションを追加する手順](../active-directory-saas-custom-apps.md)を使って、Azure AD テナントと直接統合できます。

  ![ギャラリーに SAML 2.0 または WS-Fed アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/saml.png)

  * **SAML 2.0** または **WS-Fed** を使用してギャラリー内の一覧にアプリケーションを追加する場合は、上記の **[SAMl 2.0/WS-Fed]** を選択します。
  * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

## <a name="implementing-sso-using-password-sso"></a>パスワード SSO を使用した SSO の実装

HTML サインイン ページがある Web アプリケーションを作成して、[パスワードベースのシングル サインオン](../manage-apps/what-is-single-sign-on.md)を構成します。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。

![ギャラリーに Password SSO アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/passwordsso.png)

* Password SSO を使用してギャラリー内の一覧にアプリケーションを追加する場合は、上記の **[Password SSO]** を選択します。
* アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。

## <a name="updateremove-existing-listing"></a>既存の公開を更新/削除する

Azure AD アプリ ギャラリーの既存のアプリケーションを更新または削除するには、最初に[アプリケーション ネットワーク ポータル](https://microsoft.sharepoint.com/teams/apponboarding/Apps)で要求を送信する必要があります。 Office 365 アカウントがある場合は、それを使ってこのポータルにサインインします。 ない場合は、Microsoft アカウント (Outlook、Hotmail など) を使ってサインインします。

- 次の画像に示すように適切なオプションを選択します。

    ![ギャラリーに SAML アプリケーションを公開するタイムライン](./media/howto-app-gallery-listing/updateorremove.png)

    * 既存のアプリケーションを更新する場合は、 **[Update existing application listing]\(既存のアプリケーション公開を更新する\)** を選びます。
    * Azure AD ギャラリーから既存のアプリケーションを削除する場合は、 **[Remove existing application listing]** \(既存のアプリケーション公開を削除する\) を選びます。
    * アクセスに関して問題が発生した場合は、[Azure AD の SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にお問い合わせください。 

## <a name="listing-requests-by-customers"></a>要求を顧客別に一覧表示する

顧客は、 **[App requests by Customers]\(顧客によるアプリ要求\)**  ->  **[Submit new request]\(新しい要求の送信\)** をクリックして、アプリケーションの一覧表示の要求を送信できます。

![顧客が要求したアプリ タイルを示します](./media/howto-app-gallery-listing/customer-submit-request.png)

以下は、顧客が要求したアプリケーションのフローです

![顧客が要求したアプリ フローを示します](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>タイムライン

SAML 2.0 または WS-Fed アプリケーションをギャラリーに公開するプロセスのタイムラインは、7 ～ 10 営業日です。

   ![ギャラリーに SAML アプリケーションを一覧するタイムライン](./media/howto-app-gallery-listing/timeline.png)

OpenID Connect アプリケーションをギャラリーに公開するプロセスのタイムラインは、2 ～ 5 営業日です。

   ![ギャラリーに SAML アプリケーションを一覧するタイムライン](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>エスカレーション

すべてのエスカレーションについては、[Azure AD SSO 統合チーム](mailto:SaaSApplicationIntegrations@service.microsoft.com) (SaaSApplicationIntegrations@service.microsoft.com) にメールでご連絡いただけば、可能な限り早く対応します。
