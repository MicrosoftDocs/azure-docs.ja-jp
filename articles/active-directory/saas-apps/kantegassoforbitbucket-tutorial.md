---
title: チュートリアル:Azure Active Directory と Kantega SSO for Bitbucket の統合 | Microsoft Docs
description: Azure Active Directory と Kantega SSO for Bitbucket の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b04b44c907e3210f3cc3975b36639f4fe275eef9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099209"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>チュートリアル:Azure Active Directory と Kantega SSO for Bitbucket の統合

このチュートリアルでは、Kantega SSO for Bitbucket と Azure Active Directory (Azure AD) を統合する方法について説明します。
Kantega SSO for Bitbucket と Azure AD を統合すると、次の利点が得られます。

* Kantega SSO for Bitbucket にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Kantega SSO for Bitbucket に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Kantega SSO for Bitbucket と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Kantega SSO for Bitbucket でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Kantega SSO for Bitbucket では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>ギャラリーからの Kantega SSO for Bitbucket の追加

Azure AD への Kantega SSO for Bitbucket の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kantega SSO for Bitbucket を追加する必要があります。

**ギャラリーから Kantega SSO for Bitbucket を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Kantega SSO for Bitbucket**」と入力し、結果パネルで **[Kantega SSO for Bitbucket]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Kantega SSO for Bitbucket](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーを基に、Kantega SSO for Bitbucket で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Kantega SSO for Bitbucket 内の関連ユーザー間にリンク関係が確立されている必要があります。

Kantega SSO for Bitbucket で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Kantega SSO for Bitbucket のシングル サインオンの構成](#configure-kantega-sso-for-bitbucket-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Kantega SSO for Bitbucket のテスト ユーザーの作成](#create-kantega-sso-for-bitbucket-test-user)** - Kantega SSO for Bitbucket で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Kantega SSO for Bitbucket で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Kantega SSO for Bitbucket** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Kantega SSO for Bitbucket のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Kantega SSO for Bitbucket のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は Bitbucket プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Kantega SSO for Bitbucket のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Kantega SSO for Bitbucket のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Bitbucket 管理者ポータルに管理者としてサインインします。

1. 歯車アイコンをクリックして、 **[Find new add-ons]\(新しいアドオンの検出\)** をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. **Kantega SSO for Bitbucket SAML & Kerberos** を検索し、 **[Install]\(インストール\)** ボタンをクリックして、新しい SAML プラグインをインストールします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. プラグインのインストールが開始されます。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. インストールが完了したら、 **[閉じる]** をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. **Manage**をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. **[SAML]** セクションに移動します。 **[Add identity provider]\(ID プロバイダーの追加\)** ボックスで **[Azure Active Directory (Azure AD)]** を選択します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. サブスクリプション レベルは **[Basic]** を選択します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. **[App properties]\(アプリのプロパティ\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. **[アプリケーション ID/URI]** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションで**識別子、応答 URL、サインオン URL** として使用します。

    b. **[次へ]** をクリックします。

1. **[Metadata import]\(メタデータのインポート\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. **[Metadata file on my computer]\(コンピューターにあるメタデータ ファイル\)** を選び、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    b. **[次へ]** をクリックします。

1. **[Name and SSO location]\(名前と SSO の場所\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. **[Identity provider name]\(ID プロバイダー名\)** ボックスに、ID プロバイダーの名前 (例: Azure AD) を追加します。

    b. **[次へ]** をクリックします。

1. 署名証明書を確認し、 **[Next]\(次へ\)** をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. **[Bitbucket user accounts]\(Bitbucket ユーザー アカウント\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. **[Create users in Bitbucket's internal Directory if needed]\(必要に応じて Bitbucket の内部ディレクトリにユーザーを作成する\)** を選択して、ユーザー グループの適切な名前を入力します (グループはコンマで区切られた複数の番号 になる場合があります)。

    b. **[次へ]** をクリックします。

1. **[完了]** をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. **[Known domains for Azure AD]\(既知の Azure AD ドメイン\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. ページの左側のパネルにある **[Known domains]\(既知のドメイン\)** を選択します。

    b. **[Known domains]\(既知のドメイン\)** ボックスにドメイン名を入力します。

    c. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Kantega SSO for Bitbucket へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Kantega SSO for Bitbucket]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Kantega SSO for Bitbucket]** を選択します。

    ![アプリケーションの一覧の Kantega SSO for Bitbucket のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Kantega SSO for Bitbucket のテスト ユーザーの作成

Azure AD ユーザーが Bitbucket にサインインできるようにするには、ユーザーを Bitbucket にプロビジョニングする必要があります。 Kantega SSO for Bitbucket の場合、プロビジョニング作業は手動で行うことになります。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Bitbucket 企業サイトに管理者としてサインインします。

1. 設定アイコンをクリックします。

    ![従業員の追加](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. **[Administration]\(管理\)** タブ セクションで、 **[Users]\(ユーザー\)** をクリックします。

    ![従業員の追加](./media/kantegassoforbitbucket-tutorial/user2.png)

1. **[Create user]\(ユーザーの作成\)** をクリックします。

    ![従業員の追加](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. **[Create User]\(ユーザーの作成\)** ダイアログ ページで、以下の手順を実行します。

    ![従業員の追加](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    e. **[Confirm Password]\(パスワードの確認\)** ボックスに、ユーザーのパスワードを再入力します。

    f. **[Create user]\(ユーザーの作成\)** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Kantega SSO for Bitbucket] タイルをクリックすると、SSO を設定した Kantega SSO for Bitbucket に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

