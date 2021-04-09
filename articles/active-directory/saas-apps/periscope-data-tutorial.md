---
title: チュートリアル:Azure Active Directory と Periscope Data の統合 | Microsoft Docs
description: Azure Active Directory と Periscope Data の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: deac4dcde51049d744706b7479f5b5f3bbb4d9c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520860"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>チュートリアル:Azure Active Directory と Periscope Data の統合

このチュートリアルでは、Periscope Data と Azure Active Directory (Azure AD) を統合する方法について説明します。
Periscope Data と Azure AD の統合には、次の利点があります。

* Periscope Data にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Periscope Data に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Periscope Data と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Periscope Data でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Periscope Data では、**SP** Initiated SSO がサポートされます

## <a name="adding-periscope-data-from-the-gallery"></a>ギャラリーからの Periscope Data の追加

Azure AD への Periscope Data の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Periscope Data を追加する必要があります。

**ギャラリーから Periscope Data を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Periscope Data**」と入力し、結果ウィンドウで **Periscope Data** を選択して、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Periscope Data](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Periscope Data で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Periscope Data 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Periscope Data で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Periscope Data のシングル サインオンの構成](#configure-periscope-data-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Periscope Data のテスト ユーザーの作成](#create-periscope-data-test-user)** - Periscope Data で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Periscope Data で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Periscope Data** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Periscope Data ドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のいずれかの URL を入力します。
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL で値を更新してください。 この値を取得するには、[Periscope Data クライアント サポート チーム](mailto:support@periscopedata.com)にお問い合わせください。識別子の値は、このチュートリアルで後ほど説明する「**Periscope Data のシングル サインオンの構成**」セクションで取得します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Periscope Data のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、管理者として Periscope Data にサインインします。

2. 左下隅にあるギア メニューを開き、 **[Billing]\(課金\)**  >  **[Security]\(セキュリティ\)** メニューの順に開いてから、次の手順を実行します。 これらの設定にアクセスできるのは管理者のみです。

    ![[セキュリティ] ダイアログのスクリーンショット。設定が選択されています。](./media/periscope-data-tutorial/configure01.png)

    a. 手順 5 の **[SAML 署名証明書]** の **[アプリのフェデレーション メタデータ URL]** をコピーし、ブラウザーで開きます。 XML ドキュメントが開きます。

    b. **[Single Sign-On]\(シングル サインオン\)** ボックスで、 **[Azure Active Directory]** を選択します。

    c. **SingleSignOnService** タグを見つけて、**Location** 値を **[SSO URL]\(SSO の URL\)** ボックスに貼り付けます。

    d. **SingleLogoutService** タグを見つけて、**Location** 値を **[SLO URL]\(SLO の URL\)** ボックスに貼り付けます。

    e. インスタンスの **[Identifier]\(識別子\)** をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** ボックスに貼り付けます。

    f. XML ファイルの最初のタグを見つけて、**entityID** の値をコピーし、 **[Issuer]\(発行者\)** ボックスに貼り付けます。

    g. SAML プロトコルで **IDPSSODescriptor** タグを見つけます。 そのセクション内で、**use=signing** を含む **KeyDescriptor** タグを見つけます。 **X509Certificate** の値をコピーして、 **[Certificate]\(証明書\)** ボックスに貼り付けます。

    h. 複数の領域を含むサイトでは、既定の領域を **[Default Space]\(既定の領域\)** ドロップダウンから選択できます。 これは、新しいユーザーが初めて Periscope Data にログインしたときに追加され、Active Directory シングル サインオンを介してプロビジョニングされる領域です。

    i. 最後に、 **[Save]\(保存\)** をクリックし、「**Logout**」と入力して SSO 設定を **確定** します。

    ![SSO 構成更新ダイアログのスクリーンショット。テキストボックスに「logout」と入力され、[確認] ボタンが選択されています。](./media/periscope-data-tutorial/configure02.png)

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

このセクションでは、Britta Simon に Periscope Data へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Periscope Data]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Periscope Data]** を選択します。

    ![アプリケーションの一覧の Periscope Data のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-periscope-data-test-user"></a>Periscope Data のテスト ユーザーの作成

Azure AD ユーザーが Periscope Data にログインできるようにするには、ユーザーを Periscope Data にプロビジョニングする必要があります。 Periscope Data では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Periscope Data に管理者としてログインします。

2. メニューの左下にある **[Settings]\(設定\)** アイコンをクリックして、 **[Permissions]\(権限\)** に移動します。

    ![[Settings]\(設定\) メニューのスクリーンショット。[Permissions]\(権限\) が選択されています。](./media/periscope-data-tutorial/configure03.png)

3. **[Add User]\(ユーザーの追加\)** をクリックして、次の手順を実行します。

      ![Periscope Data の構成情報](./media/periscope-data-tutorial/configure04.png)

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    c. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    d. **[ADD]\(追加\)** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Periscope Data] タイルをクリックすると、SSO を設定した Periscope Data に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)