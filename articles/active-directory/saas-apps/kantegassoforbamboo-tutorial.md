---
title: 'チュートリアル: Azure Active Directory と Kantega SSO for Bamboo の統合 | Microsoft Docs'
description: Azure Active Directory と Kantega SSO for Bamboo の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: aa5f908cdf25925db63054adaf1e6dab15f5260b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459307"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>チュートリアル: Azure Active Directory と Kantega SSO for Bamboo の統合

このチュートリアルでは、Kantega SSO for Bamboo と Azure Active Directory (Azure AD) を統合する方法について説明します。
Kantega SSO for Bamboo と Azure AD を統合すると、次の利点が得られます。

* Kantega SSO for Bamboo にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Kantega SSO for Bamboo に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Kantega SSO for Bamboo と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Kantega SSO for Bamboo でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Kantega SSO for Bamboo では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>ギャラリーからの Kantega SSO for Bamboo の追加

Azure AD への Kantega SSO for Bamboo の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kantega SSO for Bamboo を追加する必要があります。

**ギャラリーから Kantega SSO for Bamboo を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Kantega SSO for Bamboo**」と入力し、結果パネルで **[Kantega SSO for Bamboo]** を選び、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の Kantega SSO for Bamboo](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーを基に、Kantega SSO for Bamboo で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Kantega SSO for Bamboo 内の関連ユーザー間にリンク関係が確立されている必要があります。

Kantega SSO for Bamboo で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Kantega SSO for Bamboo シングル サインオンの構成](#configure-kantega-sso-for-bamboo-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Kantega SSO for Bamboo のテスト ユーザーの作成](#create-kantega-sso-for-bamboo-test-user)** - Kantega SSO for Bamboo で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Kantega SSO for Bamboo で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Kantega SSO for Bamboo** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は Bamboo プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Set up Kantega SSO for Bamboo]\(Kantega SSO for Bamboo の設定\)** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>Kantega SSO for Bamboo シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、オンプレミス サーバーの Bamboo に管理者としてサインインします。

1. 歯車をポイントし、 **[Add-ons]\(アドオン\)** をクリックします。

    ![[Settings]\(設定\) メニューの [Add-ons]\(アドオン\) が選択されているスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon1.png)

1. [Add-ons]\(アドオン\) タブ セクションで、 **[Find new add-ons]\(新しいアドオンの検索\)** をクリックします。 **[Kantega SSO for Bamboo (SAML & Kerberos)]** を検索し、 **[Install]\(インストール\)** をクリックして、新しい SAML プラグインをインストールします。

    ![[Kantega S S O for Bamboo] が選択されている [Bamboo Administration]\(Bamboo の管理\) が示されているスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon2.png)

1. プラグインのインストールが開始されます。

    ![Kantega S S O for Bamboo のインストールの進行状況が示されているスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon21.png)

1. インストールが完了したら、 **[閉じる]** をクリックします。

    ![[Close]\(閉じる\) ボタンを示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon33.png)

1. **Manage** をクリックします。

    ![[Manage]\(管理\) ボタンを示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon34.png)

1. **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![[Configure]\(構成\) が選択された [User-installed add-ons]\(ユーザーがインストールしたアドオン\) を示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon3.png)

1. **[SAML]** セクションに移動します。 **[Add identity provider]\(ID プロバイダーの追加\)** ボックスで **[Azure Active Directory (Azure AD)]** を選択します。

    ![ID プロバイダーとして Azure A D が選択された [Kantega Single Sign-On]\(Kantega シングル サインオン\) を示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon4.png)

1. サブスクリプション レベルは **[Basic]** を選択します。

    ![[Basic] が選択されている [Prepare Azure A D]\(Azure A D の準備\) を示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon5.png)

1. **[App properties]\(アプリのプロパティ\)** セクションで、次の手順を実行します。

    ![この手順の情報を指定できる [App properties]\(アプリのプロパティ\) セクションを示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. **[アプリケーション ID/URI]** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションで **識別子、応答 URL、サインオン URL** として使用します。

    b. **[次へ]** をクリックします。

1. **[Metadata import]\(メタデータのインポート\)** セクションで、次の手順を実行します。

    ![メタデータ ファイルに移動できる [Metadata import]\(メタデータのインポート\) セクションを示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. **[Metadata file on my computer]\(コンピューターにあるメタデータ ファイル\)** を選び、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    b. **[次へ]** をクリックします。

1. **[Name and SSO location]\(名前と SSO の場所\)** セクションで、次の手順を実行します。

    ![Azure A D が ID プロバイダー名である [Name and S S O location]\(名前と S S O の場所\) を示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. **[Identity provider name]\(ID プロバイダー名\)** ボックスに、ID プロバイダーの名前 (例: Azure AD) を追加します。

    b. **[次へ]** をクリックします。

1. 署名証明書を確認し、 **[Next]\(次へ\)** をクリックします。

    ![署名の確認を示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon9.png)

1. **[Bamboo user accounts]\(Bamboo ユーザー アカウント\)** セクションで、次の手順を実行します。

    ![ユーザーを作成するためのオプションがある [Bamboo user accounts]\(Bamboo ユーザー アカウント\) を示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. **[Create users in Bamboo's internal Directory if needed]\(必要に応じて Bamboo の内部ディレクトリにユーザーを作成する\)** を選択して、ユーザー グループの適切な名前を入力します (グループはコンマで区切られた複数の番号 になる場合があります)。

    b. **[次へ]** をクリックします。

1. **[完了]** をクリックします。

    ![[Summary]\(概要\) ページを示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon11.png)

1. **[Known domains for Azure AD]\(既知の Azure AD ドメイン\)** セクションで、次の手順を実行します。

    ![以下の手順を実行できる [Known domains for Azure A D]\(既知の Azure A D ドメイン\) を示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. ページの左側のパネルにある **[Known domains]\(既知のドメイン\)** を選択します。

    b. **[Known domains]\(既知のドメイン\)** ボックスにドメイン名を入力します。

    c. **[保存]** をクリックします。

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

このセクションでは、Britta Simon に Kantega SSO for Bamboo へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Kantega SSO for Bamboo]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Kantega SSO for Bamboo]** を選択します。

    ![アプリケーションの一覧の Kantega SSO for Bamboo のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-kantega-sso-for-bamboo-test-user"></a>Kantega SSO for Bamboo のテスト ユーザーの作成

Azure AD ユーザーが Bamboo にサインインできるようにするには、そのユーザーを Bamboo にプロビジョニングする必要があります。 Kantega SSO for Bamboo の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの Bamboo にサインインします。

1. 歯車をポイントし、 **[User management]\(ユーザー管理\)** をクリックします。

    ![[Settings]\(設定\) メニューの [User Management]\(ユーザー管理\) が選択されているスクリーンショット。](./media/kantegassoforbamboo-tutorial/user1.png)

1. **[ユーザー]** をクリックします。 **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![以下の手順を実行できる [Add user]\(ユーザーの追加\) ペインを示すスクリーンショット。](./media/kantegassoforbamboo-tutorial/user2.png)

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    b. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    c. **[Confirm Password]\(パスワードの確認\)** ボックスに、ユーザーのパスワードを再入力します。

    d. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    e. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    f. **[保存]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Kantega SSO for Bamboo] タイルをクリックすると、SSO を設定した Kantega SSO for Bamboo に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)