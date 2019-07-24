---
title: チュートリアル:Azure Active Directory と Lucidchart の統合 | Microsoft Docs
description: Azure Active Directory と Lucidchart の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97bbb6b802f4a7a6378f283efd02cfb74873a903
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266819"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>チュートリアル:Azure Active Directory と Lucidchart の統合

このチュートリアルでは、Lucidchart と Azure Active Directory (Azure AD) を統合する方法について説明します。
Lucidchart と Azure AD の統合には、次の利点があります。

* Lucidchart にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Lucidchart にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Lucidchart と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Lucidchart でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Lucidchart では、**SP** によって開始される SSO がサポートされます
* Lucidchart では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-lucidchart-from-the-gallery"></a>ギャラリーからの Lucidchart の追加

Azure AD への Lucidchart の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lucidchart を追加する必要があります。

**ギャラリーから Lucidchart を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Lucidchart**」と入力し、結果パネルで **Lucidchart** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Lucidchart](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Lucidchart で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Lucidchart 内の関連ユーザー間にリンク関係が確立されている必要があります。

Lucidchart で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Lucidchart シングル サインオンの構成](#configure-lucidchart-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Lucidchart のテスト ユーザーの作成](#create-lucidchart-test-user)** - Lucidchart で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Lucidchart で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Lucidchart** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Lucidchart のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** テキスト ボックスに、「`https://chart2.office.lucidchart.com/saml/sso/azure`」と入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Lucidchart のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-lucidchart-single-sign-on"></a>Lucidchart のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Lucidchart の企業サイトに管理者としてログインします。

2. 上部のメニューで **[チーム]** をクリックします。

    ![Team](./media/lucidchart-tutorial/ic791190.png "Team")

3. **[アプリケーション] \> [SAML の管理]** をクリックします。

    ![Manage SAML](./media/lucidchart-tutorial/ic791191.png "Manage SAML")

4. **[SAML Authentication Settings]** ダイアログ ページで、次の手順に従います。

    a. **[SAML 認証を有効にする]** を選んで、**[オプション]** をクリックします。

    ![SAML Authentication Settings](./media/lucidchart-tutorial/ic791192.png "SAML Authentication Settings")

    b. **[ドメイン]** ボックスで、使用するドメインを入力して、**[証明書の変更]** をクリックします。

    ![Change Certificate](./media/lucidchart-tutorial/ic791193.png "Change Certificate")

    c. ダウンロードしたメタデータ ファイルを開いて内容をコピーし、 **[Upload Metadata (メタデータのアップロード)]** ボックスに貼り付けます。

    ![Upload Metadata](./media/lucidchart-tutorial/ic791194.png "Upload Metadata")

    d. **[Automatically Add new users to the team]\(新しいユーザーを自動的にチームに追加する\)** を選んで、**[Save changes]\(変更を保存\)** をクリックします。

    ![変更を保存](./media/lucidchart-tutorial/ic791195.png "Save Changes")

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Lucidchart へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Lucidchart]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Lucidchart]** を選択します。

    ![アプリケーションの一覧の [Lucidchart] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-lucidchart-test-user"></a>Lucidchart のテスト ユーザーの作成

Lucidchart へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  割り当て済みユーザーがアクセス パネルを使用して Lucidchart にログインしようとすると、そのユーザーが存在するかどうかが Lucidchart によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lucidchart により自動的に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Lucidchart] タイルをクリックすると、SSO を設定した Lucidchart に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
