---
title: チュートリアル:Azure Active Directory と ThousandEyes の統合 | Microsoft Docs
description: Azure Active Directory と ThousandEyes の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: bb78b014ffe2d40b9a61da8e47893056e435ddc6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088651"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>チュートリアル:Azure Active Directory と ThousandEyes の統合

このチュートリアルでは、ThousandEyes と Azure Active Directory (Azure AD) を統合する方法について説明します。
ThousandEyes と Azure AD の統合には、次の利点があります。

* ThousandEyes にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して ThousandEyes に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ThousandEyes と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* ThousandEyes でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ThousandEyes では、**SP** によって開始される SSO がサポートされます

* ThousandEyes では、[**自動化された**ユーザー プロビジョニング](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)がサポートされます

## <a name="adding-thousandeyes-from-the-gallery"></a>ギャラリーからの ThousandEyes の追加

Azure AD への ThousandEyes の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ThousandEyes を追加する必要があります。

**ギャラリーから ThousandEyes を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**ThousandEyes**」と入力して、結果パネルで **[ThousandEyes]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの ThousandEyes](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、ThousandEyes で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと ThousandEyes 内の関連ユーザー間にリンク関係が確立されている必要があります。

ThousandEyes で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[ThousandEyes シングル サインオンの構成](#configure-thousandeyes-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[ThousandEyes のテスト ユーザーの作成](#create-thousandeyes-test-user)** - ThousandEyes で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

ThousandEyes で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **ThousandEyes** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[ThousandEyes のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.thousandeyes.com/login/sso`」と入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[ThousandEyes のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-thousandeyes-single-sign-on"></a>ThousandEyes のシングル サインオンを構成する

1. 別の Web ブラウザーのウィンドウで、管理者として **ThousandEyes** 企業サイトにサインオンします。

2. 上部のメニューで **[Settings]** をクリックします。

    ![設定](./media/thousandeyes-tutorial/ic790066.png "Settings")

3. ページの下部にある **[Account]**

    ![Account](./media/thousandeyes-tutorial/ic790067.png "Account")

4. **[Security & Authentication]** タブをクリックします。

    ![セキュリティと認証](./media/thousandeyes-tutorial/ic790068.png "Security & Authentication")

5. **[Setup Single Sign-On]** セクションで、次の手順を実行します。

    ![シングル サインオンの設定](./media/thousandeyes-tutorial/ic790069.png "Setup Single Sign-On")

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[Login Page URL]\(ログイン ページの URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。

    c. **[Logout Page URL]\(ログアウト ページの URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** を貼り付けます。

    d. **[Identity Provider Issuer]\(ID プロバイダー発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**を貼り付けます。

    e. **[Verification Certificate]\(検証証明書\)** で **[Choose file]\(ファイルの選択\)** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。

    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ThousandEyes へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[ThousandEyes]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[ThousandEyes]** を選択します。

    ![アプリケーションの一覧の ThousandEyes のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-thousandeyes-test-user"></a>ThousandEyes テスト ユーザーの作成

このセクションの目的は、ThousandEyesで Britta Simon というユーザーを作成することです。 ThousandEyes では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](thousandeyes-provisioning-tutorial.md)を参照してください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. ThousandEyes 企業サイトに管理者としてサインインします。

2. **[設定]** をクリックします。

    ![設定](./media/thousandeyes-tutorial/IC790066.png "Settings")

3. **[アカウント]** クリックします。

    ![Account](./media/thousandeyes-tutorial/IC790067.png "Account")

4. **[Accounts & Users]** タブをクリックします。

    ![アカウントとユーザー](./media/thousandeyes-tutorial/IC790073.png "Accounts & Users")

5. **[Add Users & Accounts]** セクションで、次の手順を実行します。

    ![ユーザー アカウントの追加](./media/thousandeyes-tutorial/IC790074.png "Add User Accounts")

    a. **[Name]\(名前\)** ボックスに、ユーザーの氏名 (**Britta Simon** など) を入力します。

    b. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレス (brittasimon@contoso.com など) を入力します。

    b. **[Add New User to Account]** をクリックします。

    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、アカウントを確認してアクティブにするためのリンクを含むメールが送られます。

> [!NOTE]
> ThousandEyes から提供されている他の ThousandEyes ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [ThousandEyes] タイルをクリックすると、SSO を設定した ThousandEyes に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [[ユーザー プロビジョニングの構成]](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)
