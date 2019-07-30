---
title: チュートリアル:Azure Active Directory と Workteam の統合 | Microsoft Docs
description: Azure Active Directory と Workteam の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 697f5c06d2c1d6b669cfa244f0328f4fb86aeea2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086830"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>チュートリアル:Azure Active Directory と Workteam の統合

このチュートリアルでは、Workteam と Azure Active Directory (Azure AD) を統合する方法について説明します。
Workteam と Azure AD の統合には、次の利点があります。

* Workteam にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Workteam に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Workteam と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Workteam でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Workteam では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-workteam-from-the-gallery"></a>ギャラリーからの Workteam の追加

Azure AD への Workteam の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Workteam を追加する必要があります。

**ギャラリーから Workteam を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Workteam**」と入力し、結果パネルで **[Workteam]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Workteam](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Workteam で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Workteam 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Workteam で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Workteam シングル サインオンの構成](#configure-workteam-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Workteam のテスト ユーザーの作成](#create-workteam-test-user)** - Workteam で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Workteam で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Workteam** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4.  **IDP** 開始モードでアプリケーションを構成する場合は、アプリケーションが Azure と既に統合されているため、 **[基本的な SAML 構成]** セクションで実行する必要がある手順はありません。

    ![[Workteam のドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Workteam のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.workte.am`」と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Set up Workteam]\(Workteam の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-workteam-single-sign-on"></a>Workteam シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Workteam にサインインします。

2. 右上隅にある**プロファイル ロゴ**をクリックし、 **[Organization settings]\(組織の設定\)** をクリックします。 

    ![Workteam の設定](./media/workteam-tutorial/tutorial_workteam_settings.png)

3. **[AUTHENTICATION]\(認証\)** セクションで、**設定ロゴ**をクリックします。

     ![Workteam - Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

4. **[SAML Settings]** ページで、次の手順を実行します。

     ![Workteam - SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. **[SAML IdP]** で **[AD Azure]** を選択します。

    b. **[SAML Single Sign-On Service URL]\(SAML シングル サインオン サービス URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. **[SAML Entity ID]\(SAML エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    d. Azure portal からダウンロードした **Base-64 でエンコードされた証明書**をメモ帳で開き、その内容をコピーして **[SAML Signing Certificate (Base64)]\(SAML 署名証明書 (Base64)\)** ボックスに貼り付けます。

    e. Click **OK**.

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Workteam へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Workteam]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Workteam]** を選択します。

    ![アプリケーションの一覧の Workteam のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-workteam-test-user"></a>Workteam のテスト ユーザーの作成

Azure AD ユーザーが Workteam にサインインできるようにするには、そのユーザーを Workteam にプロビジョニングする必要があります。 Workteam では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Workteam にサインインします。

2. **[Organization settings]\(組織の設定\)** ページで、上部中央の **[USERS]\(ユーザー\)** をクリックし、 **[NEW USER]\(新しいユーザー\)** をクリックします。

    ![Workteam - ユーザー](./media/workteam-tutorial/tutorial_workteam_user.png)

3. **[New employee]\(新しい従業員\)** ページで、次の手順を実行します。

    ![Workteam - 新しい従業員](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. **[Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Brittasimon**)。

    b. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (例: **Brittasimon\@contoso.com**) を入力します。

    c. Click **OK**.

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Workteam] タイルをクリックすると、SSO を設定した Workteam に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

