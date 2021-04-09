---
title: 'チュートリアル: Azure Active Directory と N2F - Expense reports の統合 | Microsoft Docs'
description: Azure Active Directory と N2F - Expense reports の間のシングル サインオンの設定方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: b260e51321e14a6ea1d1ee75f88ca7564b83d492
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516751"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>チュートリアル: Azure Active Directory と N2F - Expense reports の統合

このチュートリアルでは、N2F - Expense reports と Azure Active Directory (Azure AD) を統合する方法について説明します。
N2F - Expense reports と Azure AD の統合には、次の利点があります。

* N2F - Expense reports にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで N2F - Expense reports に自動的にサインイン (シングル サインオン) されるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

N2F - Expense reports と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* N2F - Expense reports でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* N2F - Expense reports では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>ギャラリーからの N2F - Expense reports の追加

Azure AD への N2F - Expense reports の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから N2F - Expense reports を追加する必要があります。

**ギャラリーから N2F - Expense reports を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**N2F - Expense reports**」と入力し、結果ウィンドウで **N2F - Expense reports** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の N2F - Expense reports](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、N2F - Expense reports で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと N2F - Expense reports 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

N2F - Expense reports で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[N2F - Expense reports のシングル サインオンの構成](#configure-n2f---expense-reports-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[N2F - Expense reports のテスト ユーザーの作成](#create-n2f---expense-reports-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを N2F - Expense reports で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

N2F - Expense reports で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **N2F - Expense reports** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **IDP** 開始モードでアプリケーションを構成する場合は、アプリケーションが Azure と既に統合されているため、 **[基本的な SAML 構成]** セクションで実行する必要がある手順はありません。

    ![[SAML ベースのサインオン] ページを示すスクリーンショット。[基本的な SAML 構成] が表示されています。](common/preintegrated.png)

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[統合] ページを示すスクリーンショット。ここでは、Azure AD シングル サインオンを追加できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://www.n2f.com/app/`」と入力します。

6. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

7. **[myPolicies のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>N2F - Expense reports のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、N2F - Expense reports 企業サイトに管理者としてサインインします。

2. **[Settings]\(設定\)** をクリックし、ドロップダウンから **[Advance Settings]\(詳細設定\)** を選択します。

    ![スクリーンショットでは、[Advance Settings]\(詳細設定\) が選択されています。](./media/n2f-expensereports-tutorial/configure1.png)

3. **[Account & Settings]\(アカウントと設定\)** を選択します。

    ![スクリーンショットでは、[Account settings]\(アカウント設定\) が選択されています。](./media/n2f-expensereports-tutorial/configure2.png)

4. **[Authentication]\(認証\)** を選択し、 **[+ Add an authentication method]\(認証方法の追加\)** タブを選択します。

    ![スクリーンショットは、認証方法を追加できる [Account settings]\(アカウント設定\) の [Authentication]\(認証\) を示しています。](./media/n2f-expensereports-tutorial/configure3.png)

5. 認証方法として **[SAML Microsoft Office 365]** を選択します。

    ![スクリーンショットは、[SAML Microsoft Office 365] が選択されている [Authentication method]\(認証方法\) を示しています。](./media/n2f-expensereports-tutorial/configure4.png)

6. **[Authentication method]\(認証方法\)** セクションで、次の手順に従います。

    ![スクリーンショットは、説明した値を入力できる [Authentication method]\(認証方法\) を示しています。](./media/n2f-expensereports-tutorial/configure5.png)

    a. **[エンティティ ID]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    b. **[Metadata URL]\(メタデータ URL\)** ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

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
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に N2F - Expense reports へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[N2F - Expense reports]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[N2F - Expense reports]** を選択します。

    ![アプリケーションの一覧の N2F - Expense reports リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-n2f---expense-reports-test-user"></a>N2F - Expense reports テストユーザーの作成

Azure AD ユーザーが N2F - Expense reports にログインできるようにするには、そのユーザーを N2F - Expense reports にプロビジョニングする必要があります。 N2F - Expense reports の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. N2F - Expense reports 企業サイトに管理者としてログインします。

2. **[Settings]\(設定\)** をクリックし、ドロップダウンから **[Advance Settings]\(詳細設定\)** を選択します。

    ![スクリーンショットでは、[Advance Settings]\(詳細設定\) が選択されています。](./media/n2f-expensereports-tutorial/configure1.png)

3. ナビゲーション パネルで **[Users]\(ユーザー\)** タブをクリックします。

    ![スクリーンショットは、[Users]\(ユーザー\) を示しています。](./media/n2f-expensereports-tutorial/user1.png)

4. **[+ New user]\(+ 新規ユーザー\)** タブを選択します。

    ![スクリーンショットは、[New user]\(新規ユーザー\) オプションを示しています。](./media/n2f-expensereports-tutorial/user2.png)

5. **[User]\(ユーザー\)** セクションで、次の手順を実行します。

    ![スクリーンショットは、説明した値を入力できるセクションを示しています。](./media/n2f-expensereports-tutorial/user3.png)

    a. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    b. **[First name]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    c. **[Name]\(名前\)** ボックスに、ユーザーの名前 (**BrittaSimon** など) を入力します。

    d. 組織の要件に応じて、 **[Role]\(ロール\)、[Direct manager (N+1)]\(直属の上司\)** および **[Division]\(部門\)** を選択します。

    e. **[Validate and send invitation]\(検証して招待を送信\)** をクリックします。

    > [!NOTE]
    > ユーザーを追加するときに問題が発生した場合は、[N2F - Expense reports のサポート チーム](mailto:support@n2f.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで N2F - Expense reports のタイルをクリックすると、SSO を設定した N2F - Expense reports に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)