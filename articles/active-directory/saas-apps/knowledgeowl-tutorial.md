---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と KnowledgeOwl の統合 | Microsoft Docs
description: Azure Active Directory と KnowledgeOwl の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: a35fb30ca19e08c68c99c9c9524231706b066e11
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549893"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と KnowledgeOwl の統合

このチュートリアルでは、KnowledgeOwl を Azure Active Directory (Azure AD) と統合する方法について説明します。 Azure AD と KnowledgeOwl を統合すると、次のことができます。

* KnowledgeOwl にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して KnowledgeOwl に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* KnowledgeOwl でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* KnowledgeOwl では、**SP と IDP** によって開始される SSO がサポートされます
* KnowledgeOwl では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-knowledgeowl-from-the-gallery"></a>ギャラリーからの KnowledgeOwl の追加

Azure AD への KnowledgeOwl の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に KnowledgeOwl を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**KnowledgeOwl**」と入力します。
1. 結果のパネルから **[KnowledgeOwl]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>KnowledgeOwl の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、KnowledgeOwl に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと KnowledgeOwl の関連ユーザーとの間にリンク関係を確立する必要があります。

KnowledgeOwl で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[KnowledgeOwl の SSO の構成](#configure-knowledgeowl-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[KnowledgeOwl のテスト ユーザーの作成](#create-knowledgeowl-test-user)** - KnowledgeOwl で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **KnowledgeOwl** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、次の形式で URL を入力します。
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際の識別子、応答 URL、サインオン URL に更新する必要があります。

1. KnowledgeOwl アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、KnowledgeOwl アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性 | 名前空間 |
    | ------------ | -------------------- | -----|
    | ssoid | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

1. **[KnowledgeOwl のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に KnowledgeOwl へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[KnowledgeOwl]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-knowledgeowl-sso"></a>KnowledgeOwl の SSO の構成

1. 別の Web ブラウザー ウィンドウで、KnowledgeOwl 企業サイトに管理者としてサインインします。

1. **[Settings]\(設定\)** をクリックし、 **[Security]\(セキュリティ\)** を選択します。

    ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/configure1.png)

1. **[SAML SSO Integration]\(SAML SSO 統合\)** までスクロールして、次の手順に従います。

    ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/configure2.png)

    a. **[Enable SAML SSO]\(SAML SSO を有効にする\)** をオンにします。

    b. **[SP Entity ID]\(SP エンティティ ID\)** 値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** に貼り付けます。

    c. **[SP Login URL]\(SP ログイン URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL] および [応答 URL]** テキスト ボックスに貼り付けます。

    d. **[IdP entityID]** テキストボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    e. **[IdP Login URL]\(IdP ログイン URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    f. **[IdP Logout URL]\(IdP ログアウト URL\)** テキスト ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    g. **[Upload IdP Certificate]\(IdP 証明書のアップロード\)** をクリックして、Azure portal からダウンロードした証明書をアップロードします。

    h. **[Map SAML Attributes]\(SAML 属性のマッピング\)** をクリックして属性をマップし、次の手順に従います。

    ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/configure3.png)

    * **[SSO ID]** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`」と入力します。
    * **[Username/Email]\(ユーザー名/電子メール\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。
    * **[First Name]\(名\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。
    * **[Last Name]\(姓\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。
    * **[保存]**

    i. ページの下部にある **[保存]** をクリックします。

    ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>KnowledgeOwl のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを KnowledgeOwl に作成します。 KnowledgeOwl では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 KnowledgeOwl にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[KnowledgeOwl サポート チーム](mailto:support@knowledgeowl.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [KnowledgeOwl] タイルをクリックすると、SSO を設定した KnowledgeOwl に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で KnowledgeOwl を試す](https://aad.portal.azure.com/)