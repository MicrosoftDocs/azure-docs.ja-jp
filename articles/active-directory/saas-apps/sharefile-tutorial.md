---
title: チュートリアル:Azure Active Directory と Citrix ShareFile の統合 | Microsoft Docs
description: Azure Active Directory と Citrix ShareFile の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9904ba14e8d5a518807eda371ba655963b33bd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091009"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>チュートリアル:Azure Active Directory と Citrix ShareFile の統合

このチュートリアルでは、Citrix ShareFile と Azure Active Directory (Azure AD) を統合する方法について説明します。
Citrix ShareFile と Azure AD の統合には、次の利点があります。

* Citrix ShareFile にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが Azure AD アカウントで自動的に Citrix ShareFile にサインインできるようにします (シングル サインオン)。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Citrix ShareFile の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Citrix ShareFile でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Citrix ShareFile では、**SP** によって開始される SSO がサポートされます

## <a name="adding-citrix-sharefile-from-the-gallery"></a>ギャラリーからの Citrix ShareFile の追加

Azure AD への Citrix ShareFile の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Citrix ShareFile を追加する必要があります。

**ギャラリーから Citrix ShareFile を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Citrix ShareFile**」と入力し、結果ウィンドウで **[Citrix ShareFile]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Citrix ShareFile](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Citrix ShareFile で Azure AD シングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Citrix ShareFile 内の関連ユーザー間にリンク関係が確立されている必要があります。

Citrix ShareFile で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Citrix ShareFile シングル サインオンの構成](#configure-citrix-sharefile-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Citrix ShareFile テスト ユーザーの作成](#create-citrix-sharefile-test-user)** - Citrix ShareFile で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Citrix ShareFile で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Citrix ShareFile** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Citrix ShareFile のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.sharefile.com/saml/login` という形式で URL を入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得する場合は、[Citrix ShareFile クライアント サポート チーム](https://www.citrix.co.in/products/citrix-content-collaboration/support.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Citrix ShareFile のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-citrix-sharefile-single-sign-on"></a>Citrix ShareFile のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、 **Citrix ShareFile** 企業サイトに管理者としてログインします。

2. 上部のツールバーの **[Admin]** をクリックします。

3. 左側のナビゲーション ウィンドウで、 **[Configure Single Sign-On]** を選択します。
   
    ![Account Administration](./media/sharefile-tutorial/ic773627.png "Account Administration")

4. **[Single Sign-On/ SAML 2.0 Configuration]** ダイアログ ページの **[基本設定]** で、次の手順を実行します。
   
    ![シングル サインオン](./media/sharefile-tutorial/ic773628.png "シングル サインオン")
   
    a. **[Enable SAML]** をクリックします。
    
    b. **[Your IDP Issuer/ Entity ID]\(IDP 発行者/エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[X.509 Certificate]\(X.509証明書\)** フィールドの横の **[変更]** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。
    
    d. **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。
    
    e. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。

5. Citrix ShareFile 管理ポータルで **[Save]** をクリックします。

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

このセクションでは、Britta Simon に Citrix ShareFile へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Citrix ShareFile]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Citrix ShareFile**」と入力して選択します。

    ![アプリケーション一覧の Citrix ShareFile リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-citrix-sharefile-test-user"></a>Citrix ShareFile テスト ユーザーの作成

Azure AD ユーザーが Citrix ShareFile にログインできるようにするには、ユーザーを Citrix ShareFile にプロビジョニングする必要があります。 Citrix ShareFile の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Citrix ShareFile** テナントにログインします。

2. **[ユーザーの管理]\>[Manage Users Home (ユーザーの管理: ホーム)]\>[+ Create Employee (従業員の作成)]** の順にクリックします。
   
    ![Create Employee](./media/sharefile-tutorial/IC781050.png "Create Employee")

3. **[Basic Information]** セクションで、次の手順を実行します。
   
    ![Basic Information](./media/sharefile-tutorial/IC799951.png "Basic Information")
   
    a. **[メール アドレス]** テキストボックスに、Britta Simon のメール アドレスとして「**brittasimon\@contoso.com**」と入力します。
   
    b. **[名]** ボックスに、ユーザーの**名**を、「**Britta**」と入力します。
   
    c. **[姓]** ボックスに、ユーザーの**姓**を、「**Simon**」と入力します。

4. **[ユーザーの追加]** をクリックします。
  
    >[!NOTE]
    >Azure AD のアカウント所有者が電子メールを受信し、リンクをたどって自分のアカウントを確認すると、アカウントがアクティブになります。Azure AD ユーザー アカウントのプロビジョニングには、他の Citrix ShareFile ユーザー アカウント作成ツールまたは Citrix ShareFile が提供する API を使用できます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Citrix ShareFile] タイルをクリックすると、SSO を設定した Citrix ShareFile に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

