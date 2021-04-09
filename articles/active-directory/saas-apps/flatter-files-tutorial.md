---
title: 'チュートリアル: Azure Active Directory と Flatter Files の統合 | Microsoft Docs'
description: Azure Active Directory と Flatter Files の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: fae21c98c7340614a96b6e85aa79bdea7889a807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453226"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>チュートリアル: Azure Active Directory と Flatter Files の統合

このチュートリアルでは、Flatter Files と Azure Active Directory (Azure AD) を統合する方法について説明します。
Flatter Files と Azure AD の統合には、次の利点があります。

* Flatter Files にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Flatter Files に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Flatter Files と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Flatter Files でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Flatter Files では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-flatter-files-from-the-gallery"></a>ギャラリーからの Flatter Files の追加

Azure AD への Flatter Files の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Flatter Files を追加する必要があります。

**ギャラリーから Flatter Files を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Flatter Files**」と入力し、結果ウィンドウで **[Flatter Files]** を選択してから、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Flatter Files](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Flatter Files で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Flatter Files 内の関連ユーザー間にリンク関係が確立されている必要があります。

Flatter Files で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Flatter Files シングル サインオンの構成](#configure-flatter-files-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Flatter Files テスト ユーザーの作成](#create-flatter-files-test-user)** - Flatter Files で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Flatter Files で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Flatter Files** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[Flatter Files のドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Flatter Files のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-flatter-files-single-sign-on"></a>Flatter Files のシングル サインオンの構成

1. 管理者として Flatter Files アプリケーションにサインオンします。

2. **[DASHBOARD]\(ダッシュボード\)** をクリックします。 
   
    ![Flatter Files アプリで選択されている [DASHBOARD]\(ダッシュボード\) を示すスクリーンショット。](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. **[設定]** をクリックし、 **[会社]** タブで次の手順に従います。 
   
    ![[Use S A M L 2.0 for Authentication]\(認証に S A M L 2.0 を使用する\) がオンになっていて、[Configure S A M L]\(S A M L の構成\) ボタンが選択されている [Company]\(会社\) タブを示すスクリーンショット。](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. **[Use SAML 2.0 for Authentication]** を選択します。
    
    b. **[Configure SAML]** をクリックします。

4. **[SAML Configuration]** ダイアログ ボックスで、次の手順を実行します。 
   
    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. **[ドメイン]** ボックスに、登録済みのドメインを入力します。
   
   > [!NOTE]
   > 登録済みのドメインがない場合は、Flatter Files のサポート チーム ( [support@flatterfiles.com](mailto:support@flatterfiles.com)」を参照してください。 
    
    b. **[Identity Provider URL]\(ID プロバイダー URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。
   
    c.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    d. **[Update]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Flatter Files へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Flatter Files]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Flatter Files]** を選択します。

    ![アプリケーションの一覧の Flatter Files のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-flatter-files-test-user"></a>Flatter Files テスト ユーザーの作成

このセクションの目的は、Flatter Files で Britta Simon というユーザーを作成することです。

**Flatter Files で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. **Flatter Files** 企業サイトに管理者としてサインオンします。

2. 左側のナビゲーション ウィンドウで、 **[Settings]\(設定\)** 、 **[Users]\(ユーザー\)** タブの順にクリックします。
   
    ![[Users]\(ユーザー\) タブが選択されている [Settings]\(設定\) ページを示すスクリーンショット。](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. **[ユーザーの追加]** をクリックします。 

4. **[Add User]** ダイアログで、次の手順を実行します。
   
    ![Flatter Files ユーザーの作成](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. **[名]** ボックスに「**Britta**」と入力します。
   
    b. **[姓]** ボックスに「**Simon**」と入力します。 
   
    c. **[Email Address]\(電子メール アドレス\)** ボックスに、Britta の Azure Portal の電子メール アドレスを入力します。
   
    d. **[送信]** をクリックします。   


### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Flatter Files] タイルをクリックすると、SSO を設定した Flatter Files に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)