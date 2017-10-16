---
title: "アプリケーションを追加するときに使用するアプリケーションの種類の選択方法 | Microsoft Docs"
description: "Azure AD と統合可能なサポートされている種類のアプリケーション、およびアプリケーションに関連した構成オプションを理解する"
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
ms.openlocfilehash: e0d41d1933531c2c633613bcbc1bbcbf075d6a69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-choose-which-application-type-to-use-when-adding-an-application"></a>アプリケーションを追加するときに使用するアプリケーションの種類の選択方法

この記事は、Azure AD と統合できる 4 つの主要なアプリケーションの種類について、次の項目を理解するのに役立ちます。

* それぞれのアプリケーションのサポート内容
* 特定のアプリケーションを選択する理由
* アプリケーションの核となるプロパティを構成する方法 (ユーザーを**プロビジョニング**する方法、どの**シングル サインオン** テクノロジを使用するかなど)。

## <a name="supported-application-types-in-azure-ad"></a>Azure AD でサポートされているアプリケーションの種類

Azure AD は、4 つの主要なアプリケーションの種類をサポートしています。これらのアプリケーションは、**[エンタープライズ アプリケーション]** の **[追加]** 機能を使用して追加できます。 チェックの内容は次のとおりです

-   **Azure AD ギャラリー アプリケーション**– Azure AD でのシングル サインオンのために事前に統合されているアプリケーション。

-   **アプリケーション プロキシ アプリケーション** – 外部へのセキュリティで保護されたシングル サインオンを提供するオンプレミスの環境で実行されるアプリケーション。

-   **カスタム開発アプリケーション** – 組織が Azure AD アプリケーション開発プラットフォームでの開発を望んでいるがまだ存在しない可能性のあるアプリケーション。

-   **非ギャラリー アプリケーション** – ユーザー独自のアプリケーションを持ち込みます。 必要な Web リンク、またはユーザー名とパスワードのフィールドを表示するアプリケーションは、SAML または OpenID Connect プロトコルをサポートするか、シングル サインオンのために Azure AD と統合する SCIM をサポートしています。

## <a name="features-and-capabilities-supported-by-all-the-above-application-types"></a>上記のすべてのアプリケーションの種類でサポートされている機能

Azure AD では、上記 4 つのすべてのアプリケーションの種類で、次の機能がサポートされます。

-   **クイック スタート** – [簡単なデプロイ手順](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)に従って、アプリケーションのデプロイを速やかに開始します。

-   **全般プロパティの管理** – アプリケーションへの[直接のディープリンク](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users)を取得し、アプリケーションの[ブランドをカスタマイズ](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal)し、すべてのユーザーに対して[アプリケーションを無効](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)にします。

-   **ユーザーとグループの管理** – アプリケーションに対するユーザーとグループの[割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)や[削除](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)を行い、必要に応じてアプリケーションへのアクセスがあるユーザーとグループに特定のアプリケーション ロールを割り当てます。

-   **セルフ サービスアプリケーションのアクセス** – アプリケーションを直接追加するか、[セルフ サービスが有効なグループに参加させる](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)ことで、ユーザーがアプリケーション アクセス パネルからアプリケーションに対する[セルフ サービスアプリケーションのアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)を要求できるようにします。この際、必要に応じてビジネス承認が必要になります。

-   **サインイン ログ** – [1 つのアプリケーションへのすべてのサインイン](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)、またはすべてのアプリケーションへのすべてのサインインを確認します。

-   **監査ログ** – [1 つのアプリケーションの変更に関する詳細な監査ログ](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)、またはすべてのアプリケーションの変更に関する詳細な監査ログを確認します。

-   **リスクに基づく条件付きアクセス** – ユーザーが特定のアプリケーションにサインインしようとした場合に適用される、強力な[条件ベースのアクセス規則](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)を設定します。

-   **アクセス許可の表示** – 1 つの場所のディレクトリにおいてアプリケーションがアクセスを持つ、すべての [OAuth2 アクセス許可](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)を表示します。

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>特定のアプリケーションの種類でサポートされるシングル サインオンとプロビジョニング モード

次の表では、上記のアプリケーションの種類それぞれでサポートされるさまざまなシングル サインオンとプロビジョニングのモードについて説明します。 この表を使用すると、特定の目標をサポートするために追加する必要があるアプリケーションを理解するのに役立ちます。

  ![アプリケーションの種類の表](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>シングル サインオン モードを選ぶ方法

Azure AD アプリケーションに対してサポートされている**シングル サインオン** モードの一覧を以下に示します。

-   **Azure AD シングル サインオンが無効** – このアプリケーションを Azure AD とシングル サインオンで統合する準備ができていないか、単純にテストする場合は、[Azure AD シングル サインオンが無効] の**シングル サインオン モード**を選びます

-   **リンクされたサインオン** – 既存のシングル サインオン ソリューションに既に接続されているアプリケーションがある場合、または[アプリケーション アクセス パネル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)や [Office 365 アプリケーション起動プログラム](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)でユーザーの単純なリンクを公開するだけの場合は、[[リンクされたサインオン]](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **シングル サインオン モード**を選びます

-   **パスワード ベースのサインオン** – アプリケーションで HTML のユーザー名とパスワードのフィールドを表示し、後でアプリケーションを再生するためそのユーザー名とパスワードを安全に格納する必要がある場合は、[[パスワード ベースのサインオン]](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **シングル サインオン モード**を選びます

-   **SAML ベースのサインオン** – アプリケーションで SAML または OpenID Connect プロトコルをサポートするか、SAML 要求で定義するルールに基づいて特定のアプリケーション ロールにユーザーをマップできるようにする場合は、[[SAML ベースのサインオン]](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) シングル サインオン モードを選びます。*

   >[!NOTE]
   >このオプションは、アプリケーションに対してアプリケーション プロキシが構成されている場合は使用できません。
   >
   >

-   **ヘッダーベースのサインオン** – シングル サインオンを実行する、HTTP ヘッダー ベースの認証をサポートする PingAccess を使用しているアプリケーションがある場合は、[[ヘッダーベースのサインオン]](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) シングル サインオン モードを選びます。 

   >[!NOTE]
   >このオプションは、アプリケーションに対してアプリケーション プロキシと PingAccess が構成されている場合にのみ使用可能です。
   >
   >

-   **統合 Windows 認証** – シングル サインオンを実行するオンプレミスの WIA アプリケーションを公開する場合は、[[統合 Windows 認証]](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) シングル サインオン モードを選びます。 

   >[!NOTE]
   >このオプションは、アプリケーションに対してアプリケーション プロキシが構成されている場合にのみ使用可能です。
   >
   >

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

2.  左側のメイン ナビゲーション メニューの下部にある **[その他のサービス]** をクリックして、**Azure Active Directory 拡張機能**を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

  * ここに表示したいアプリケーションが表示されない場合は、**[すべてのアプリケーション リスト]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6.  シングル サインオンを構成するアプリケーションを選択します。

7.  アプリケーションを読み込んだら、アプリケーションの左側にあるナビゲーション メニューで **[シングル サインオン]** をクリックします。

## <a name="how-to-choose-a-provisioning-mode"></a>プロビジョニング モードを選択する方法

-   **手動プロビジョニング** – 既存のアカウントがある場合、または Azure AD 以外でこのアプリケーションのアカウントを管理する場合は、[手動](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes)プロビジョニング モードを選択します。

-   **自動プロビジョニング** – API ベースの自動プロビジョニングおよび/またはこのアプリケーションのユーザー アカウントのプロビジョニング解除を有効にする場合は、[自動](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning)**プロビジョニング モード**を選択します。 

   >[!NOTE]
   >このオプションは、[Azure AD アプリケーション ギャラリー](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery)の**おすすめ**カテゴリ内のアプリケーションにのみ使用できます。
   >
   >

-   **SCIM ベースの自動プロビジョニング** – Azure AD と統合したアプリケーションの変更に対して自動的に生成される、ユーザーとグループの変更を検出するための SCIM プロトコルがアプリケーションでサポートされている場合は、[SCIM ベースの自動プロビジョニング](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) を使用します。 

   >[!NOTE]
   >このオプションは特定のプロビジョニング モードとして表示されませんが、既定では Azure AD と統合しているすべてのアプリケーションで有効になっています。
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>アプリケーションのプロビジョニング モードを設定する方法

アプリケーションの**プロビジョニング** モードを設定するには、以下の手順に従います。

アプリケーションの**シングル サインオン** モードを設定するには、以下の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[その他のサービス]** をクリックして、**Azure Active Directory 拡張機能**を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

  * ここに表示したいアプリケーションが表示されない場合は、**[すべてのアプリケーション リスト]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6.  プロビジョニングを構成するアプリケーションを選択します。

7.  アプリケーションを読み込んだら、アプリケーションの左側にあるナビゲーション メニューで **[プロビジョニング]** をクリックします。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory でのアプリケーションの管理](active-directory-enable-sso-scenario.md)
