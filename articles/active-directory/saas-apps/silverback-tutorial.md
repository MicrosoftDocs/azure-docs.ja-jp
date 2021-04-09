---
title: チュートリアル:Azure Active Directory と Silverback の統合 | Microsoft Docs
description: Azure Active Directory と Silverback の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: a70b6bb50b397429af1af41869bbe9ecf7e8bad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004179"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>チュートリアル:Azure Active Directory と Silverback の統合

このチュートリアルでは、Silverback と Azure Active Directory (Azure AD) を統合する方法について説明します。
Silverback と Azure AD の統合には、次の利点があります。

* Silverback にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Silverback に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Silverback と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Silverback でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Silverback では、**SP** によって開始される SSO がサポートされます

## <a name="adding-silverback-from-the-gallery"></a>ギャラリーから Silverback を追加する

Silverback の Azure AD への統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Silverback を追加する必要があります。

**ギャラリーから Silverback を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Silverback**」と入力し、結果パネルで **[Silverback]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Silverback](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Silverback で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Silverback 内の関連ユーザー間にリンク関係が確立されている必要があります。

Silverback で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Silverback シングル サインオンの構成](#configure-silverback-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Silverback のテスト ユーザーの作成](#create-silverback-test-user)** - Silverback で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Silverback で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Silverback** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Silverback のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<YOURSILVERBACKURL>.com/ssp` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`<YOURSILVERBACKURL>.com` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<YOURSILVERBACKURL>.com/sts/authorize/login` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Silverback クライアント サポート チーム](mailto:helpdesk@matrix42.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Silverback シングル サインオンを構成する

1. 別の Web ブラウザーで、Silverback Server に管理者としてログインします。

2. **[Admin]\(管理者\)**  >  **[Authentication Provider]\(認証プロバイダー\)** に移動します。

3. **[Authentication Provider Settings]\(認証プロバイダーの設定\)** ページで、次の手順を行います。

    ![管理者](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  **[Import from URL]\(URL からインポートする\)** をクリックします。

    b.  コピーしたメタデータ URL を貼り付け、 **[OK]** をクリックします。

    c.  **[OK]** をクリックして確定すると、値が自動的に入力されます。

    d.  **[Show on Login Page]\(ログイン ページに表示する\)** をオンにします。

    e.  (省略可能) Azure AD で承認されたユーザーを自動的に追加するには、 **[Dynamic User Creation]\(動的なユーザー作成\)** をオンにします。

    f.  [Self Service Portal]\(セルフサービス ポータル\) でボタンの **[Title]\(タイトル\)** を作成します。

    g.  **[Choose File]\(ファイルの選択\)** をクリックして **[Icon]\(アイコン\)** をアップロードします。

    h.  ボタンの背景 **色** を選択します。

    i.  **[保存]** をクリックします。

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

このセクションでは、Britta Simon に Silverback へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Silverback]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Silverback]** を選択します。

    ![アプリケーションの一覧の [Silverback] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-silverback-test-user"></a>Silverback テスト ユーザーを作成する

Azure AD ユーザーが Silverback にログインできるようにするには、そのユーザーを Silverback にプロビジョニングする必要があります。 Silverback では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Silverback Server に Administrator としてログインします。

2. **[Users]\(ユーザー\)** に移動し、**新しいデバイス ユーザーを追加** します。

3. **[Basic]\(基本\)** ページで、次の手順を行います。

    ![ユーザー](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーの名前を入力します (例: **Britta**)。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    d. **[E-mail Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (**Brittasimon\@contoso.com** など) を入力します。

    e. **[Password]\(パスワード\)** ボックスにパスワードを入力します。

    f. **[Confirm Password]\(パスワードの確認入力\)** ボックスにパスワードをもう一度入力して確認します。

    g. **[保存]** をクリックします。

> [!NOTE]
> 手動で各ユーザーを作成しない場合は、**[Admin]\(管理者\)** > **[Authentication Provider]\(認証プロバイダー\)** の **[Dynamic User Creation]\(動的ユーザーの作成\)** チェックボックスをオンにします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Silverback] タイルをクリックすると、SSO を設定した Silverback に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)