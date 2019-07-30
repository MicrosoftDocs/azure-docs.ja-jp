---
title: チュートリアル:Azure Active Directory と GitHub の統合 | Microsoft Docs
description: Azure Active Directory と GitHub の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0730dd89a61b046f73e7e39c8226e66700b3494
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101646"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>チュートリアル:Azure Active Directory と GitHub の統合

このチュートリアルでは、GitHub と Azure Active Directory (Azure AD) を統合する方法について説明します。
GitHub と Azure AD の統合には、次の利点があります。

* GitHub にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して GitHub に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

GitHub と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) に作成された GitHub 組織 ([GitHub Enterprise 課金プラン](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)が必要)

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* GitHub では、**SP** によって開始される SSO がサポートされます

* GitHub では、[**自動化された**ユーザー プロビジョニング](github-provisioning-tutorial.md)がサポートされます

## <a name="adding-github-from-the-gallery"></a>ギャラリーからの GitHub の追加

Azure AD への GitHub の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に GitHub を追加する必要があります。

**ギャラリーから GitHub を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 結果ボックスに「**GitHub**」と入力し、結果パネルで **[GitHub]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の GitHub](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、GitHub で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと GitHub 内の関連ユーザー間にリンク関係が確立されている必要があります。

GitHub で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[GitHub シングル サインオンの構成](#configure-github-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[GitHub テスト ユーザーの作成](#create-github-test-user)** - GitHub で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

GitHub で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. **GitHub** アプリケーション統合ページ上の [Azure portal](https://portal.azure.com/) で、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![GitHubドメインと URL シングル サインオンの情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://github.com/orgs/<entity-id>/sso`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子で、これらの値を更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、GitHub 管理者セクションに移動します。

5. GitHub アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 GitHub アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[GitHub のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-github-single-sign-on"></a>GitHub シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、GitHub 組織サイトに管理者としてログインします。

2. **[設定]** に移動し、 **[セキュリティ]** をクリックします。

    ![設定](./media/github-tutorial/tutorial_github_config_github_03.png)

3. **[Enable SAML authentication (SAML 認証を有効にする)]** チェック ボックスをオンにして、シングル サインオンの構成フィールドを表示します。 次に、シングル サインオン URL 値を使用して、Azure AD 構成でシングル サインオン URL を更新します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_13.png)

4. 次のフィールドを構成します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. **[シングル サインオン URL]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[発行者]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書 をメモ帳で開き、その内容を **[Public Certificate]\(公開証明書\)** ボックスに貼り付けます。

    d. **[Edit]\(編集\)** アイコンをクリックし、 **[Signature Method]\(署名方法\)** と **[Digest Method]\(ダイジェスト方法\)** を編集して、**RSA-SHA1** および **SHA1** から **RSA-SHA256** および **SHA256** に変更します (下図参照)。

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. **[Test SAML configuration (SAML 構成のテスト)]** をクリックして、SSO の際に検証の失敗やエラーがないことを確認します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_06.png)

6. **[保存]**

> [!NOTE]
> GitHub でのシングル サインオンは GitHub で特定の組織を認証するものです。GitHub そのものの認証に取って代わることはできません。 つまり、ユーザーの github.com セッションの有効期限が切れた場合は、シングル サインオン プロセス中に GitHub の ID とパスワードで認証するように求められることがあります。

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

このセクションでは、Britta Simon に GitHub へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[GitHub]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **GitHub** を選択します。

    ![アプリケーションの一覧の GitHub のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-github-test-user"></a>GitHub テスト ユーザーの作成

このセクションの目的は、GitHub で Britta Simon というユーザーを作成することです。 GitHub では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](github-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. GitHub 企業サイトに管理者としてログインします。

2. **[ユーザー]** をクリックします。

    ![ユーザー](./media/github-tutorial/tutorial_github_config_github_08.png "People")

3. **[メンバーの招待]** をクリックします。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_09.png "ユーザーの招待")

4. **[メンバーの招待]** ダイアログ ページで、次の手順を実行します。

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_10.png "ユーザーの招待")

    b. **[Send Invitation (招待状の送信)]** をクリックします。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_11.png "ユーザーの招待")

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [GitHub] タイルをクリックすると、SSO を設定した GitHub に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
