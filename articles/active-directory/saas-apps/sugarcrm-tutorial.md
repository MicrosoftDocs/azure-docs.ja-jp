---
title: チュートリアル:Azure Active Directory と Sugar CRM の統合 | Microsoft Docs
description: Azure Active Directory と Sugar CRM の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 2dea1dcd2f6ecef580d65a95d1227380901213eb
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565506"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>チュートリアル:Azure Active Directory と Sugar CRM の統合

このチュートリアルでは、Sugar CRM と Azure Active Directory (Azure AD) を統合する方法について説明します。
Sugar CRM と Azure AD の統合には、次の利点があります。

* Sugar CRM にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Sugar CRM に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Sugar CRM と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Sugar CRM でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Sugar CRM では、**SP** によって開始される SSO がサポートされます

## <a name="adding-sugar-crm-from-the-gallery"></a>ギャラリーからの Sugar CRM の追加

Azure AD への Sugar CRM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sugar CRM を追加する必要があります。

**ギャラリーから Sugar CRM を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Sugar CRM**」と入力し、結果パネルで **[Sugar CRM]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Sugar CRM](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Sugar CRM で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Sugar CRM 内の関連ユーザー間にリンク関係が確立されている必要があります。

Sugar CRM で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Sugar CRM シングル サインオンの構成](#configure-sugar-crm-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Sugar CRM テスト ユーザーの作成](#create-sugar-crm-test-user)** - Sugar CRM で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Sugar CRM で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/)の **Sugar CRM** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Sugar CRM のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Sugar CRM クライアント サポート チーム](https://support.sugarcrm.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Sugar CRM の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sugar-crm-single-sign-on"></a>Sugar CRM でのシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Sugar CRM 企業サイトに管理者としてサインインします。

1. **[Admin]** に移動します。

    ![管理](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. **[Administration]** セクションで、**[Password Management]** をクリックします。

    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

1. **[Enable SAML Authentication]** を選択します。

    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

1. **[SAML Authentication]** セクションで、次の手順に従います。

    ![SAML Authentication](./media/sugarcrm-tutorial/ic795891.png "SAML Authentication")  

    a. **[ログイン URL]** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。
  
    b. **[SLO URL]\(SLO の URL\)** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。
  
    c. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
  
    d. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Sugar CRM へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Sugar CRM]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Sugar CRM]** を選びます。

    ![アプリケーションの一覧で [Sugar CRM] を選びます。](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-sugar-crm-test-user"></a>Sugar CRM テスト ユーザーの作成

Azure AD ユーザーが Sugar CRM にサインインできるようにするには、そのユーザーを Sugar CRM にプロビジョニングする必要があります。 Sugar CRM の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Sugar CRM** 企業サイトに管理者としてサインインします。

1. **[Admin]** に移動します。

    ![管理](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. **[Administration]** セクションで、**[User Management]** をクリックします。

    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

1. **[Users]\>[Create New User]** の順に選択します。

    ![Create New User](./media/sugarcrm-tutorial/ic795894.png "Create New User")

1. **[User Profile]** タブで、次の手順に従います。

    ![New User](./media/sugarcrm-tutorial/ic795895.png "New User")

    * 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**ユーザー名**、**姓**、および**メール アドレス**を入力します。
  
1. **[Status]** として、**[Active]** を選択します。

1. [Password] タブで、次の手順に従います。

    ![New User](./media/sugarcrm-tutorial/ic795896.png "New User")

    a. 該当するテキスト ボックスにパスワードを入力します。

    b. **[Save]** をクリックします。

> [!NOTE]
> Sugar CRM から提供されている他の Sugar CRM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Sugar CRM] タイルをクリックすると、SSO を設定した Sugar CRM に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

