---
title: 'チュートリアル: Azure Active Directory と HackerOne の統合 | Microsoft Docs'
description: Azure Active Directory と HackerOne の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: ac8bdc37bc2572b580deebc35931e324ed107d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92446555"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>チュートリアル: Azure Active Directory と HackerOne の統合

このチュートリアルでは、HackerOne と Azure Active Directory (Azure AD) を統合する方法について説明します。
HackerOne と Azure AD の統合には、次の利点があります。

* HackerOne にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して HackerOne に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と HackerOne の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* HackerOne でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* HackerOne では、**SP** によって開始される SSO がサポートされます
* HackerOne では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-hackerone-from-the-gallery"></a>ギャラリーからの HackerOne の追加

Azure AD への HackerOne の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HackerOne を追加する必要があります。

**ギャラリーから HackerOne を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**HackerOne**」と入力し、結果パネルで **HackerOne** を選択して、 **[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の HackerOne](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、HackerOne で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと HackerOne 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

HackerOne で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[HackerOne シングル サインオンの構成](#configure-hackerone-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[HackerOne のテスト ユーザーの作成](#create-hackerone-test-user)** - HackerOne で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

HackerOne で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **HackerOne** アプリケーション統合ページで、 **[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![HackerOne のドメインと URL のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに「`https://hackerone.com/users/saml/sign_in?email=<configured domain>`」と入力します。

    b. **[識別子 (エンティティ ID)]** テキスト ボックスに「`hackerone.com`」と入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[HackerOne のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-hackerone-single-sign-on"></a>HackerOne シングル サインオンの構成

1. HackerOne テナントに管理者としてサインオンします。

2. 上部のメニューで **[Settings]** をクリックします。

    ![メニューで [Settings]\(設定\) が選択されているスクリーンショット。](./media/hackerone-tutorial/tutorial_hackerone_001.png)

3. **[Authentication]\(認証\)** に移動し、 **[Add SAML settings]\(SAML 設定の追加\)** をクリックします。

    ![[Add SAML settings]\(SAML 設定の追加\) ボタンが選択されている [Authentication Settings]\(認証設定\) ページを示すスクリーンショット。](./media/hackerone-tutorial/tutorial_hackerone_003.png)

4. **[SAML Settings (SAML の設定)]** ダイアログで、次の手順を実行します。

    ![Configure single sign-on](./media/hackerone-tutorial/tutorial_hackerone_004.png)

    a. 登録されているドメインを **[電子メール ドメイン]** ボックスに入力します。

    b. **[Single Sign-On URL]\(シングル サインオン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. Azure portal からダウンロードした **証明書ファイル** をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X509 Certificate]\(X509 証明書\)** ボックスに貼り付けます。

    d. **[保存]** をクリックします。

5. [Authentication Settings] ダイアログで、次の手順を実行します。

    ![[Run test]\(テストの実行\) ボタンが選択されている [Authentication Settings]\(認証設定\) ダイアログを示すスクリーンショット。](./media/hackerone-tutorial/tutorial_hackerone_005.png)

    a. **[テストの実行]** をクリックします。

6. テストが正常に完了し、 **[Status]\(状態\)** フィールドに **[Last test status: success]\(最新のテスト状態: 成功\)** と表示されている場合は、 **[Request Verification]\(確認の要求\)** ボタンを選択して HackerOne に承認を求めます。

    ![HackerOne に承認を求める](./media/hackerone-tutorial/tutorial-hackerone-006.png)

7. HackerOne によって設定が承認されたら、 **[Migrate Users]\(ユーザーを移行する\)** ボタンを選択して、すべてのユーザーに SSO 認証を要求することができます。

    ![SAML を有効にする](./media/hackerone-tutorial/tutorial-hackerone-007.png)

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

このセクションでは、Britta Simon に HackerOne へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[HackerOne]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[HackerOne]** を選択します。

    ![アプリケーションの一覧の HackerOne のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-hackerone-test-user"></a>HackerOne のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを HackerOne に作成します。 HackerOne では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 HackerOne にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [HackerOne] タイルをクリックすると、SSO を設定した HackerOne に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)