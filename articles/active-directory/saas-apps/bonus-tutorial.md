---
title: 'チュートリアル: Azure Active Directory と Bonusly の統合 | Microsoft Docs'
description: Azure Active Directory と Bonusly の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: e93d03ac9ec404a18b480a0cd1bfe289944b60d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689115"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>チュートリアル: Azure Active Directory と Bonusly の統合

このチュートリアルでは、Bonusly と Azure Active Directory (Azure AD) を統合する方法について説明します。
Bonusly と Azure AD の統合には、次の利点があります。

* Bonusly にアクセスできるユーザーを Azure AD 上で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Bonusly に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Bonusly と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Bonusly でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Bonusly では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-bonusly-from-the-gallery"></a>ギャラリーからの Bonusly の追加

Azure AD への Bonusly の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Bonusly を追加する必要があります。

**ギャラリーから Bonusly を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Bonusly**」と入力し、結果ウィンドウで **Bonusly** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Bonusly](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Bonusly で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Bonusly 内の関連ユーザー間にリンク関係が確立されている必要があります。

Bonusly で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Bonusly シングル サインオンの構成](#configure-bonusly-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Bonusly のテスト ユーザーの作成](#create-bonusly-test-user)** - Bonusly で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Bonusly で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Bonusly** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Bonusly のドメインと URL] のシングル サインオン情報](common/idp-reply.png)

    **[応答 URL]** ボックスに、`https://Bonus.ly/saml/<tenant-name>` のパターンを使用して URL を入力します

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Bonusly クライアント サポート チーム](https://bonus.ly/contact)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

6. **[SAML 署名証明書]** セクションで **[THUMBPRINT]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

7. **[Bonusly のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-bonusly-single-sign-on"></a>Bonusly シングル サインオンの構成

1. 別のブラウザー ウィンドウで、**Bonusly** テナントにサインインします。

1. 上部のツール バーの **[Settings]\(設定\)** をクリックし、 **[Integrations and apps]\(統合とアプリ\)** を選択します。

    ![Bonusly ソーシャル セクション](./media/bonus-tutorial/ic773686.png "Bonusly")
1. **[Single Sign-On]** の **[SAML]** を選択します。

1. **[SAML]** ダイアログ ページで、次の手順を実行します。

    ![Bonusly SAML ダイアログ ページ](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. **[IdP SSO target URL]\(IdP SSO ターゲット URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    b. **[IdP Login URL]\(IdP ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. **[IdP Issuer]\(IdP 発行者\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。
    
    d. Azure Portal からコピーした **拇印** の値を、 **[Cert Fingerprint]\(証明書の指紋\)** ボックスに貼り付けます。
    
    e. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Bonusly へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Bonusly]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Bonusly]** を選択します。

    ![アプリケーションの一覧の Bonusly のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-bonusly-test-user"></a>Bonusly のテスト ユーザーの作成

Azure AD ユーザーが Bonusly にサインインできるようにするには、そのユーザーを Bonusly にプロビジョニングする必要があります。 Bonusly の場合、プロビジョニングは手動で行います。

> [!NOTE]
> 他の Bonusly ユーザー アカウント作成ツールや、Bonusly から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. Web ブラウザー ウィンドウで、Bonusly テナントにサインインします。

1. **[設定]** をクリックします。

    ![[設定]](./media/bonus-tutorial/ic781041.png "設定")

1. **[Users and bonuses]** タブをクリックします。

    ![[Users and bonuses]\(ユーザーとボーナス\)](./media/bonus-tutorial/ic781042.png "[Users and bonuses]")

1. **[Manage Users]** をクリックします。

    ![ユーザーの管理](./media/bonus-tutorial/ic781043.png "[Manage Users]")

1. **[ユーザーの追加]** をクリックします。

    ![スクリーンショットは、[Add User]\(ユーザーの追加\) を選択できる [Manage Users]\(ユーザーの管理\) を示しています。](./media/bonus-tutorial/ic781044.png "ユーザーの追加")

1. **[Add User]** ダイアログで、次の手順を実行します。

    ![スクリーンショットは、この情報を入力できる [Add User]\(ユーザーの追加\) ダイアログ ボックスを示しています。](./media/bonus-tutorial/ic781045.png "ユーザーの追加")  

    a. **[First name]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    b. **[Last name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[メール]** ボックスに、ユーザーのメール (`brittasimon@contoso.com` など) を入力します。

    d. **[保存]** をクリックします。

    > [!NOTE]
    > アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。  

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Bonusly] タイルをクリックすると、SSO を設定した Bonusly に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)