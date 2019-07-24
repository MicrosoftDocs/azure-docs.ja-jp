---
title: チュートリアル:Azure Active Directory と SAML SSO for Jira by resolution GmbH の統合 | Microsoft Docs
description: Azure Active Directory と SAML SSO for Jira by resolution GmbH の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0875e271a34577ed694caae4281df99b6b63411
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901867"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>チュートリアル:Azure Active Directory と SAML SSO for Jira by resolution GmbH の統合

このチュートリアルでは、SAML SSO for Jira by resolution GmbH と Azure Active Directory (Azure AD) を統合する方法について説明します。
SAML SSO for Jira by resolution GmbH と Azure AD の統合には、次の利点があります。

* SAML SSO for Jira by resolution GmbH にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで SAML SSO for Jira by resolution GmbH に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAML SSO for Jira by resolution GmbH の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAML SSO for Jira by resolution GmbH でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAML SSO for Jira by resolution GmbH では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>ギャラリーからの SAML SSO for Jira by resolution GmbH の追加

Azure AD への SAML SSO for Jira by resolution GmbH の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAML SSO for Jira by resolution GmbH を追加する必要があります。

**ギャラリーから SAML SSO for Jira by resolution GmbH を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. [検索] ボックスに「**SAML SSO for Jira by resolution GmbH**」と入力し、結果パネルで **[SAML SSO for Jira by resolution GmbH]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の SAML SSO for Jira by resolution GmbH](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SAML SSO for Jira by resolution GmbH で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SAML SSO for Jira by resolution GmbH 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

SAML SSO for Jira by resolution GmbH で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SAML SSO for Jira by resolution GmbH のシングル サインオンの構成](#configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SAML SSO for Jira by resolution GmbH テスト ユーザーの作成](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** - SAML SSO for Jira by resolution GmbH で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SAML SSO for Jira by resolution GmbH で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SAML SSO for Jira by resolution GmbH** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[SAML SSO for Jira by resolution GmbH のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します

    c. アプリケーションを **SP 開始**モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[SAML SSO for Jira by resolution GmbH のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SAML SSO for Jira by resolution GmbH クライアント サポート チーム](https://www.resolution.de/go/support)に問い合わせます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[SAML SSO for Jira by resolution GmbH のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on"></a>SAML SSO for Jira by resolution GmbH のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、**SAML SSO for Jira by resolution GmbH 管理者ポータル**に管理者としてログインします。

2. 歯車をポイントし、**[Add-ons]\(アドオン\)** をクリックします。
    
    ![Configure single sign-on](./media/samlssojira-tutorial/addon1.png)

3. [Administrator Access]\(管理者アクセス\) のページにリダイレクトされます。 **パスワード**を入力し、**[Confirm]\(確認\)** ボタンをクリックします。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon2.png)

4. [Add-ons]\(アドオン\) タブ セクションで、**[Find new add-ons]\(新しいアドオンの検索\)** をクリックします。 **[SAML Single Sign On (SSO) for JIRA]\(JIRA の SAML シングル サインオン \(SSO\)\)** を検索し、**[Install]\(インストール\)** ボタンをクリックして、新しい SAML プラグインをインストールします。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon7.png)

5. プラグインのインストールが開始されます。 **[閉じる]** をクリックします。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon8.png)

    ![Configure single sign-on](./media/samlssojira-tutorial/addon9.png)

6.  **Manage**をクリックします。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon10.png)
    
8. **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon11.png)

9. **[SAML SingleSignOn Plugin Configuration]\(SAML SingleSignOn プラグインの構成\)** ページで **[Add new IdP]\(新しい IDP の追加\)** ボタンをクリックし、ID プロバイダーの設定を構成します。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon4.png)

10. **[Choose your SAML Identity Provider]\(SAML ID プロバイダーの選択\)** ページで、次の手順を実行します。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon5a.png)
 
    a. IDP の種類として **[Azure AD]** を設定します。
    
    b. ID プロバイダーの**名前** (たとえば Azure AD) を追加します。
    
    c. ID プロバイダーの**説明** (たとえば Azure AD) を追加します。
    
    d. **[次へ]** をクリックします。
    
11. **[Identity provider configuration]\(ID プロバイダーの構成\)** ページで、**[次へ]** ボタンをクリックします。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon5b.png)

12. **[Import SAML IdP Metadata]\(SAML IDP メタデータのインポート\)** ページで、次の手順を実行します。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon5c.png)

    a. **[Load File]\(ファイルの読み込み\)** ボタンをクリックし、手順 5 でダウンロードしたメタデータ XML ファイルを選びます。

    b. **[インポート]** ボタンをクリックします。
    
    c. インポートが成功するまでしばらく待ちます。
    
    d. **[次へ]** ボタンをクリックします。
    
13. **[User ID attribute and transformation]\(ユーザーの ID 属性と変換\)** ページで、**[次へ]** ボタンをクリックします。

    ![Configure single sign-on](./media/samlssojira-tutorial/addon5d.png)
    
14. **[User creation and update]\(ユーザーの作成と更新\)** ページで、**[Save & Next]\(保存して次へ\)** をクリックして設定を保存します。   
    
    ![Configure single sign-on](./media/samlssojira-tutorial/addon6a.png)
    
15. **[Test your settings]\(設定のテスト\)** ページで、**[Skip test & configure manually]\(テストをスキップして手動で構成\)** をクリックしてここではユーザー テストをスキップします。 テストは次のセクションで実行し、Azure Portal でいくつか設定する必要があります。 
    
    ![Configure single sign-on](./media/samlssojira-tutorial/addon6b.png)
    
16. 表示される **[Skipping the test means...]\(テストをスキップすると...\)** ダイアログ ボックスで、**[OK]** をクリックします。
    
    ![Configure single sign-on](./media/samlssojira-tutorial/addon6c.png)

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

このセクションでは、Britta Simon に SAML SSO for Jira by resolution GmbH へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[SAML SSO for Jira by resolution GmbH]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**SAML SSO for Jira by resolution GmbH**」と入力して選択します。

    ![アプリケーションの一覧の SAML SSO for Jira by resolution GmbH のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>SAML SSO for Jira by resolution GmbH のテスト ユーザーの作成

Azure AD ユーザーが SAML SSO for Jira by resolution GmbH にログインできるようにするには、ユーザーを SAML SSO for Jira by resolution GmbH にプロビジョニングする必要があります。  
SAML SSO for Jira by resolution GmbH の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. SAML SSO for Jira by resolution GmbH 企業サイトに管理者としてログインします。

2. 歯車をポイントし、**[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](./media/samlssojira-tutorial/user1.png) 

3. [Administrator Access]\(管理者アクセス\) のページにリダイレクトされるので、**パスワード**を入力し、**[Confirm]\(確認\)** ボタンをクリックします。

    ![従業員の追加](./media/samlssojira-tutorial/user2.png) 

4. **[User management]\(ユーザー管理\)** タブ セクションで、**[create user]\(ユーザーの作成\)** をクリックします。

    ![従業員の追加](./media/samlssojira-tutorial/user3.png) 

5. **[Create new user]\(新しいユーザーの作成\)** ダイアログ ページで、以下の手順を実行します。

    ![従業員の追加](./media/samlssojira-tutorial/user4.png) 

    a. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    e. **[Create user]\(ユーザーの作成\)** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAML SSO for Jira by resolution GmbH] タイルをクリックすると、SSO を設定した SAML SSO for Jira by resolution GmbH に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

