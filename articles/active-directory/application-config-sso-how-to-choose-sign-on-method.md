---
title: "使用するシングル サインオン方法を確認する方法 | Microsoft Docs"
description: "Azure AD でサポートされているシングル サインオン モードと、対象となるアプリケーションに対してモードを選ぶ方法を理解します。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 6aba9206ab0634e9379d63520ad9e2d0256b7e94
ms.contentlocale: ja-jp
ms.lasthandoff: 04/11/2017

---

# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>使用するシングル サインオン方法を確認する方法

この記事は、Azure AD でサポートされているシングル サインオン モードと、対象となるアプリケーションに対してモードを選ぶ方法を理解するのに役立ちます。

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>特定のアプリケーションの種類でサポートされるシングル サインオンとプロビジョニングのモード

次の表では、上記のアプリケーションの種類それぞれでサポートされるさまざまなシングル サインオンとプロビジョニングのモードについて説明します。 この表を使用すると、特定の目標をサポートするために追加する必要があるアプリケーションを理解するのに役立ちます。

  ![アプリケーションの種類の表](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>シングル サインオン モードを選ぶ方法

Azure AD アプリケーションに対してサポートされている**シングル サインオン** モードの一覧を以下に示します。

-   **Azure AD シングル サインオンが無効** – このアプリケーションを Azure AD とシングル サインオンで統合する準備ができていないか、単純にテストする場合は、[Azure AD シングル サインオンが無効] の**シングル サインオン モード**を選びます

-   **リンクされたサインオン** – 既存のシングル サインオン ソリューションに既に接続されているアプリケーションがある場合、または[アプリケーション アクセス パネル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)や [Office 365 アプリケーション起動プログラム](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)でユーザーの単純なリンクを公開するだけの場合は、[[リンクされたサインオン]](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **シングル サインオン モード**を選びます

-   **パスワード ベースのサインオン** – アプリケーションで HTML のユーザー名とパスワードのフィールドを表示し、後でアプリケーションを再生するためそのユーザー名とパスワードを安全に格納する必要がある場合は、[[パスワード ベースのサインオン]](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **シングル サインオン モード**を選びます

-   **SAML ベースのサインオン** – アプリケーションで SAML または OpenID Connect プロトコルをサポートするか、SAML 要求で定義するルールに基づいて特定のアプリケーション ロールにユーザーをマップできるようにする場合は、[[SAML ベースのサインオン]](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) シングル サインオン モードを選びます。*(*"*注:*"* このオプションは、アプリケーションに対してアプリケーション プロキシが構成されている場合は使用できません)*

-   **ヘッダーベースのサインオン** – シングル サインオンを実行する、HTTP ヘッダー ベースの認証をサポートする PingAccess を使用しているアプリケーションがある場合は、[[ヘッダーベースのサインオン]](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) シングル サインオン モードを選びます。*(*"*注:*"* このオプションは、アプリケーションに対してアプリケーション プロキシと PingAccess が構成されている場合にのみ使用可能です) *

-   **統合 Windows 認証** – シングル サインオンを実行するオンプレミスの WIA アプリケーションを公開する場合は、[[統合 Windows 認証]](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) シングル サインオン モードを選びます *(*"*注:*"* このオプションは、アプリケーションに対してアプリケーション プロキシが構成されている場合にのみ使用可能です) *

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>カスタム開発アプリケーションのシングル サインオン モード

[カスタム開発アプリケーション](#_Custom-Developed_Applications) エクスペリエンスでカスタム開発したアプリケーションは、上記の一覧にない追加のシングル サインオンもサポートします。 チェックの内容は次のとおりです

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) ベースのサインオン

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) ベースのサインオン

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) ベースのサインオン

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) ベースのサインオン

これらのシングル サインオン モードをサポートするカスタム開発アプリケーションの作成方法について詳しくは、「[Azure Active Directory 開発者ガイド](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)」をご覧ください。

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>アプリケーションのシングル サインオン モードを設定する方法

アプリケーションの**シングル サインオン** モードを設定するには、以下の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[More services (その他のサービス)]** をクリックして **[Azure Active Directory Extension (Azure Active Directory 拡張機能)]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** アイテムを選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[All Applications (すべてのアプリケーション)]** をクリックしてすべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[All Applications (すべてのアプリケーション)]** に設定します。

6.  シングル サインオンを構成するアプリケーションを選択します

7.  アプリケーションを読み込んだら、アプリケーションの左側にあるナビゲーション メニューで **[シングル サインオン]** をクリックします。

## <a name="next-steps"></a>次のステップ
[アプリケーション プロキシを使用してアプリにシングル サインオンを提供](active-directory-application-proxy-sso-using-kcd.md)


