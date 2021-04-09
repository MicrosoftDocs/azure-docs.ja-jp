---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と GitHub Enterprise Cloud - Enterprise Account の統合 | Microsoft Docs
description: Azure Active Directory とGitHub Enterprise Cloud - Enterprise Account の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と GitHub Enterprise Cloud - Enterprise Account の統合

このチュートリアルでは、GitHub Enterprise Cloud - Enterprise Account と Azure Active Directory (Azure AD) を統合する方法について説明します。 GitHub Enterprise Cloud - Enterprise Account と Azure AD を統合すると、次のことができます。

* GitHub Enterprise Account および Enterprise Account 内のすべての組織にアクセスできるユーザーを Azure AD で制御する。


## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* [GitHub Enterprise Account](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts)。
* Enterprise Account オーナーである GitHub ユーザー アカウント。 

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* GitHub Enterprise Cloud - Enterprise Account では、**SP** Initiated SSO および **IDP** Initiated SSO がサポートされます。
* GitHub Enterprise Cloud - Enterprise Account では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>ギャラリーからの GitHub Enterprise Cloud - Enterprise Account の追加

Azure AD への GitHub Enterprise Cloud - Enterprise Account の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に GitHub Enterprise Cloud - Enterprise Account を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**GitHub Enterprise Cloud - Enterprise Account**」と入力します。
1. 結果のパネルから **[GitHub Enterprise Cloud - Enterprise Account]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>GitHub Enterprise Cloud - Enterprise Account の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、GitHub Enterprise Cloud - Enterprise Account に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと GitHub Enterprise Cloud - Enterprise Account の関連ユーザーとの間にリンク関係を確立する必要があります。

GitHub Enterprise Cloud - Enterprise Account に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[ GitHub アプリへの Azure AD ユーザーとテスト ユーザー アカウントの割り当て](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** - 自分のユーザー アカウントとテスト ユーザー `B.Simon` が Azure AD シングル サインオンを使用できるようにします。
1. **[Enterprise Account とその組織の SAML の有効化とテスト](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[別の Enterprise Account オーナーまたは組織メンバー アカウントを使用した SSO のテスト](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **GitHub Enterprise Cloud - Enterprise Account** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. **[応答 URL]** ボックスに、`https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

     **[サインオン URL]** ボックスに、`https://github.com/enterprises/<ENTERPRISE-SLUG>/sso` の形式で URL を入力します。

    > [!NOTE]
    > `<ENTERPRISE-SLUG>` は、GitHub Enterprise Account の実際の名前に置き換えます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateBase64.png)

1. **[GitHub Enterprise Cloud - Enterprise Account のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で `B.Simon` というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>GitHub アプリへの Azure AD ユーザーとテスト ユーザー アカウントの割り当て

このセクションでは、`B.Simon` と自分のユーザー アカウントに GitHub Enterprise Cloud - Enterprise Account へのアクセスを許可して、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[GitHub Enterprise Cloud - Enterprise Account]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** と自分のユーザー アカウントを選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Enterprise Account とその組織の SAML の有効化とテスト

**GitHub Enterprise Cloud - Enterprise Account** 側でシングル サインオンを構成するには、[この GitHub ドキュメント](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account)に記載されている手順に従います。 
1. [Enterprise Account オーナー](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner)であるユーザー アカウントを使用して GitHub.com にサインインします。 
1. アプリの `Login URL` フィールドの値を Azure portal からコピーし、GitHub Enterprise Account の SAML 設定の `Sign on URL` フィールドに貼り付けます。 
1. アプリの `Azure AD Identifier` フィールドの値を Azure portal からコピーし、GitHub Enterprise Account の SAML 設定の `Issuer` フィールドに貼り付けます。 
1. 上記の手順でダウンロードした **証明書 (Base64)** ファイルの内容を Azure portal からコピーし、GitHub Enterprise Account の SAML 設定の適切なフィールドに貼り付けます。 
1. `Test SAML configuration` をクリックし、GitHub Enterprise Account から Azure AD に正常に認証できることを確認します。
1. テストが成功したら、設定を保存します。 
1. 初めて GitHub Enterprise Account から SAML 経由で認証を行うと、サインインした GitHub ユーザー アカウントを Azure AD ユーザー アカウントに関連付ける "_リンクされた外部 ID_" が GitHub Enterprise Account に作成されます。  
 
GitHub Enterprise Account に対して SAML SSO を有効にすると、その Enterprise Account によって所有されているすべての組織に対して SAML SSO が既定で有効になります。 すべてのメンバーは、自分がメンバーである組織にアクセスするには、SAML SSO を使用して認証するよう求められます。また、エンタープライズ所有者は、Enterprise Account にアクセスするときに SAML SSO を使用して認証するよう求められます。

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>別の Enterprise Account オーナーまたは組織メンバー アカウントを使用した SSO のテスト

GitHub Enterprise Account に対して SAML 統合を設定すると (Enterprise Account 内の GitHub 組織にも適用され)、Azure AD 内でアプリに割り当てられている他の Enterprise Account オーナーは、GitHub Enterprise Account の URL (`https://github.com/enterprises/<enterprise account>`) に移動し、SAML を介して認証し、GitHub Enterprise Account のポリシーと設定にアクセスできます。 

Enterprise Account 内の組織の組織所有者は、[GitHub 組織に参加するようユーザーを招待する](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization)ことができます。 組織所有者アカウントを使用して GitHub.com にサインインし、記事内の手順に従って `B.Simon` を組織に招待します。 `B.Simon` の GitHub ユーザー アカウントがまだ存在しない場合は作成する必要があります。 

`B.Simon` テスト ユーザー アカウントを使用して、Enterprise Account で GitHub 組織のアクセスをテストするには:
1. Enterprise Account 内の組織に `B.Simon` を組織所有者として招待します。 
1. `B.Simon` の Azure AD ユーザー アカウントにリンクするユーザー アカウントを使用して、GitHub.com にサインインします。
1. `B.Simon` のユーザー アカウントを使用して Azure AD にサインインします。
1. GitHub 組織に移動します。 SAML を介して認証するよう求めるメッセージがユーザーに表示されます。 SAML 認証が成功すると、`B.Simon` は組織のリソースにアクセスできるようになります。 

## <a name="next-steps"></a>次のステップ

GitHub Enterprise Cloud - Enterprise Account を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
