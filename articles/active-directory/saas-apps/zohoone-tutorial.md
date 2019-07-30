---
title: チュートリアル:Azure Active Directory と Zoho One の統合 | Microsoft Docs
description: Azure Active Directory と Zoho One の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086241"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>チュートリアル:Azure Active Directory と Zoho One の統合

このチュートリアルでは、Zoho One と Azure Active Directory (Azure AD) を統合する方法について説明します。
Zoho One と Azure AD の統合には、次の利点があります。

* Zoho One にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Zoho One に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Zoho One と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Zoho One でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zoho One では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます

## <a name="adding-zoho-one-from-the-gallery"></a>ギャラリーからの Zoho One の追加

Azure AD への Zoho One の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Zoho One を追加する必要があります。

**ギャラリーから Zoho One を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zoho One**」と入力し、結果パネルで **[Zoho One]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Zoho One](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Zoho One で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Zoho One 内の関連ユーザー間にリンク関係が確立されている必要があります。

Zoho One で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Zoho One シングル サインオンの構成](#configure-zoho-one-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Zoho One のテスト ユーザーの作成](#create-zoho-one-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Zoho One で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Zoho One で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Zoho One** アプリケーション統合ページで、 **[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Zoho One ドメインと URL] のシングル サインオン情報](common/idp-relay.png)

    a. **[識別子]** テキスト ボックスに、`one.zoho.com` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://accounts.zoho.com/samlresponse/<saml-identifier>` のパターンを使用して URL を入力します

    > [!NOTE]
    > 上記の **[応答 URL]** の値は、実際の値ではありません。 `<saml-identifier>` の値は、このチュートリアルで後述する「**Zoho One のシングル サインオンの構成**」セクションの手順 4. で得られます。

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** テキスト ボックスに、URL `https://one.zoho.com` を入力します

5. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。


    ![[Zoho One ドメインと URL] のシングル サインオン情報](common/both-signonurl.png)

    **[サインオン URL]** ボックスに、`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` という形式で URL を入力します。 

    > [!NOTE] 
    > 上記の**サインオン URL** の値は、実際の値ではありません。 この値は、このチュートリアルで後述する「**Zoho One のシングル サインオンの構成**」セクションで得られる実際のサインオン URL に置き換えてください。 

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Set up Zoho One]\(Zoho One のセットアップ\)** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-zoho-one-single-sign-on"></a>Zoho One のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Zoho One 企業サイトに管理者としてサインインします。

2. **[Organization]\(組織\)** タブで、 **[SAML Authentication]\(SAML 認証\)** の **[Setup]\(設定\)** をクリックします。

    ![Zoho One、組織](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. ポップアップ ページで、次の手順に従います。

    ![Zoho One、sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. **[Sign-in URL]\(サインイン URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[Sign-out URL]\(サインアウト URL\)** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    c. **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードした**証明書 (Base64)** をアップロードします。

    d. **[Save]** をクリックします。

4. SAML 認証設定を保存した後、 **[SAML-Identifier]\(SAML 識別子\)** の値をコピーし、`<saml-identifier>` を置き換える形で**応答 URL** に追加します (例: `https://accounts.zoho.com/samlresponse/one.zoho.com`)。こうして得られた値を **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けてください。

    ![Zoho One、SAML](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. **[Domains]\(ドメイン\)** タブに移動し、 **[Add Domain]\(ドメインの追加\)** をクリックします。

    ![Zoho One、ドメイン](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. **[Add Domain]\(ドメインの追加\)** ページで、次の手順に従います。

    ![Zoho One、ドメインの追加](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. **[Domain Name]\(ドメイン名\)** ボックスに、ドメインを入力します (例: contoso.com)。

    b. **[追加]** をクリックします。

    >[!Note]
    >ドメインに追加した後、[これら](https://www.zoho.com/one/help/admin-guide/domain-verification.html)の手順に従って、ドメインを確認します。 ドメインを確認したら、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** でこのドメイン名を使用します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zoho One へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Zoho One]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Zoho One]** を選択します。

    ![アプリケーションの一覧の Zoho One のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-zoho-one-test-user"></a>Zoho One テスト ユーザーの作成

Azure AD ユーザーが Zoho One にサインインできるようにするには、そのユーザーを Zoho One にプロビジョニングする必要があります。 Zoho One では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Zoho One にサインインします。

2. **[Users]\(ユーザー\)** タブで、**ユーザー ロゴ**をクリックします。

    ![Zoho One、ユーザー](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. **[Add User]** ページで、次の手順に従います。

    ![Zoho One、ユーザーの追加](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. **[Name]\(名前\)** ボックスに、ユーザーの氏名を入力します (例: **Britta Simon**)。
    
    b. **[Email Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレスを入力します (例: brittasimon@contoso.com)。

    >[!Note]
    >ドメインの一覧から、確認済みドメインを選択します。

    c. **[追加]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Zoho One] タイルをクリックすると、SSO を設定した Zoho One に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

