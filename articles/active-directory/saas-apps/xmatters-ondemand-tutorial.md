---
title: チュートリアル:Azure Active Directory と xMatters OnDemand の統合 | Microsoft Docs
description: Azure Active Directory と xMatters OnDemand の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086516"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>チュートリアル:Azure Active Directory と xMatters OnDemand の統合

このチュートリアルでは、xMatters OnDemand と Azure Active Directory (Azure AD) を統合する方法について説明します。
xMatters OnDemand と Azure AD の統合には、次の利点があります。

* xMatters OnDemand にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して xMattersxMatters OnDemand に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

xMatters OnDemand と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* xMatters OnDemand でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* xMatters OnDemand では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>ギャラリーからの xMatters OnDemand の追加

Azure AD への xMatters OnDemand の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に xMatters OnDemand を追加する必要があります。

**ギャラリーから xMatters OnDemand を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**xMatters OnDemand**」と入力し、結果パネルで **[xMatters OnDemand]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の xMatters OnDemand](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、xMatters OnDemand で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと xMatters OnDemand 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

xMatters OnDemand で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[xMatters OnDemand のシングル サインオンの構成](#configure-xmatters-ondemand-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[xMatters OnDemand のテスト ユーザーの作成](#create-xmatters-ondemand-test-user)** - xMatters OnDemand で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

xMatters OnDemand で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **xMatters OnDemand** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[xMatters OnDemand のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[xMatters OnDemand クライアント サポート チーム](https://www.xmatters.com/company/contact-us/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

    > [!IMPORTANT]
    > [xMatters OnDemand サポート チーム](https://www.xmatters.com/company/contact-us/)に証明書を転送する必要があります。 シングル サインオンの構成を確定するには、その前に、xMatters サポート チームによって証明書がアップロードされる必要があります。

6. **[Set up xMatters OnDemand]\(xMatters OnDemand の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>xMatters OnDemand のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、XMatters OnDemand の企業サイトに管理者としてサインインします。

2. 上部のツールバーで、 **[管理者]** をクリックし、左側にあるナビゲーション バーで **[会社の詳細]** をクリックします。

    ![管理](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

3. **[SAML 構成]** ページで、次の手順を実行します。

    ![SAML 構成](./media/xmatters-ondemand-tutorial/IC776796.png "SAML 構成")

    a. **[Enable SAML]** を選択します。

    b. **[Identity Provider ID]\(ID プロバイダーの ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[シングル サインオン URL]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[Single Logout URL]\(シングル ログアウト URL\)** テキストボックスに、Azure portal からコピーした**ログアウト URL** を貼り付けます。

    e. [会社の詳細] ページで、上部にある **[変更の保存]** をクリックします。

    ![会社の詳細](./media/xmatters-ondemand-tutorial/IC776797.png "Company details")

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に xMatters OnDemand へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[xMatters OnDemand]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーション一覧で **[xMatters OnDemand]** を選択します。

    ![アプリケーション一覧の xMatters OnDemand リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-xmatters-ondemand-test-user"></a>xMatters OnDemand のテスト ユーザーの作成

このセクションの目的は、xMatters OnDemand で Britta Simon というユーザーを作成することです。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. **XMatters OnDemand** テナントにサインインします。

2. **[Users]\(ユーザー\)** タブをクリックし、 **[Add User]\(ユーザーの追加\)** をクリックします。

    ![ユーザー](./media/xmatters-ondemand-tutorial/IC781048.png "Users")

3. **[ユーザーの追加]** セクションで、次の手順に従います。

    ![Add a User](./media/xmatters-ondemand-tutorial/IC781049.png "Add a User")

    a. **[アクティブ]** を選択します。

    b. **[User ID]\(ユーザー ID\)** ボックスに、ユーザーの ID を入力します (この例では Brittasimon@contoso.com)。

    c. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では Britta)。

    d. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    e. **[Site]\(サイト\)** ボックスに、プロビジョニングする有効な Azure AD アカウントの有効なサイトを入力します。

    f. **[Save]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [xMatters OnDemand] タイルをクリックすると、SSO を設定した xMatters OnDemand に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

