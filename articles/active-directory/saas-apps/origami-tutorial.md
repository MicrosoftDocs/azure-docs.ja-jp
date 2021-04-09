---
title: 'チュートリアル: Azure Active Directory と Origami の統合 | Microsoft Docs'
description: Azure Active Directory と Origami の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 02c79e8385c7a7e9d60a3dcbed603ca94cb1dc43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522269"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>チュートリアル: Azure Active Directory と Origami の統合

このチュートリアルでは、Origami と Azure Active Directory (Azure AD) を統合する方法について説明します。
Origami と Azure AD の統合には、次の利点があります。

* Origami にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Origami にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Origami の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Origami でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Origami では、**SP** によって開始される SSO がサポートされます

## <a name="adding-origami-from-the-gallery"></a>ギャラリーからの Origami の追加

Azure AD への Origami の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Origami を追加する必要があります。

**ギャラリーから Origami を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Origami**」と入力し、結果ウィンドウで **[Origami]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Origami](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Origami で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Origami 内の関連ユーザー間にリンク関係が確立されている必要があります。

Origami で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Origami シングル サインオンの構成](#configure-origami-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Origami のテスト ユーザーの作成](#create-origami-test-user)** - Origami で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Origami で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Origami** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Origami のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://live.origamirisk.com/origami/account/login?account=<companyname>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Origami クライアント サポート チーム](https://wordpress.org/support/theme/origami)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Origami の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-origami-single-sign-on"></a>Origami シングル サインオンの構成

1. 管理者権限で Origami アカウントにログインします。

2. 上部のメニューで **[Admin]** をクリックします。
   
    ![Origami ホーム ページのスクリーンショット。[Admin]\(管理者\) が選択されています。](./media/origami-tutorial/tutorial_origami_51.png)

3. [Single Sign On Setup] ダイアログ ページで、次の手順に従います。
   
    ![[Single Sign On Setup]\(シングル サインオンの設定\) ページのスクリーンショット。[Enable Single Sign-on]\(シングル サインオンを有効にする\) が選択され、テキスト ボックスが強調表示されています。](./media/origami-tutorial/tutorial_origami_531.png)

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[Identity Provider's Sign-in Page URL]\(ID プロバイダー サインイン ページ URL\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[Identity Provider's Sign-out Page URL (ID プロバイダー シングル サインアウト ページ URL)]** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[Browse (参照する)]** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。

    e. **[変更を保存]** をクリックします。

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

このセクションでは、Britta Simon に Origami へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Origami]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Origami]** を選択します。

    ![アプリケーションの一覧の Origami のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-origami-test-user"></a>Origami テスト ユーザーの作成

このセクションでは、Origami で Britta Simon というユーザーを作成します。 

1. 管理者権限で Origami アカウントにログインします。

2. 上部のメニューで **[Admin]** をクリックします。
   
    ![Origami アカウント ホーム ページのスクリーンショット。[Admin]\(管理者\) が選択されています。](./media/origami-tutorial/tutorial_origami_51.png)

3. **[Users and Security]** ダイアログ ボックスで、 **[Users]** をクリックします。
   
    ![[Users and Security]\(ユーザーとセキュリティ\) ダイアログのスクリーンショット。[ユーザー] が選択されています。](./media/origami-tutorial/tutorial_origami_54.png)

4. **[新しいユーザーの追加]** をクリックします。
   
    ![[新規ユーザーの追加] ボタンが選択されている画面のスクリーンショット。](./media/origami-tutorial/tutorial_origami_55.png)

5. [新規ユーザーの追加] ダイアログで、次の手順を実行します。
   
    ![[新規ユーザーの追加] ダイアログのスクリーンショット。[ユーザー名]、[名]、[姓] テキスト ボックスが強調表示されています。](./media/origami-tutorial/tutorial_origami_56.png)

    a. **[ユーザー名]** テキスト ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    b. **[パスワード]** ボックスに、パスワードを入力します。

    c. **[パスワードの確認]** ボックスに、パスワードを再度入力します。

    d. **[名]** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    e. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    f. **[保存]** をクリックします。
   
    ![[保存] ボタンが選択されている画面のスクリーンショット。](./media/origami-tutorial/tutorial_origami_57.png)

6. **ユーザー ロール** と **クライアント アクセス** をユーザーに割り当てます。 
   
    ![Configure single sign-on](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Origami] タイルをクリックすると、SSO を設定した Origami に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)