---
title: チュートリアル:Azure Active Directory と Moxtra の統合 | Microsoft Docs
description: Azure Active Directory と Moxtra の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: f22f70c587556793c2b02fc79fa6175584b0c5cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096636"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>チュートリアル:Azure Active Directory と Moxtra の統合

このチュートリアルでは、Moxtra と Azure Active Directory (Azure AD) を統合する方法について説明します。
Moxtra と Azure AD の統合には、次の利点があります。

* Moxtra にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Moxtra に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Moxtra と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Moxtra でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Moxtra では、**SP** によって開始される SSO がサポートされます

## <a name="adding-moxtra-from-the-gallery"></a>ギャラリーから Moxtra を追加する

Azure AD への Moxtra の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Moxtra を追加する必要があります。

**ギャラリーから Moxtra を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Moxtra**」と入力し、結果ウィンドウで **[Moxtra]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Moxtra](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Moxtra で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Moxtra 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Moxtra で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Moxtra シングル サインオンの構成](#configure-moxtra-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Moxtra テスト ユーザーの作成](#create-moxtra-test-user)** - Moxtra で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Moxtra で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Moxtra** アプリケーション統合ページで、 **[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Moxtra のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://www.moxtra.com/service/#login` という形式で URL を入力します。

5. Moxtra アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]**  アイコンをクリックして、 **[ユーザー属性]**  ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. その他に、Moxtra アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。 

    | Name | ソース属性|
    | ------------------- | -------------------- |    
    | firstname | User.givenname |
    | lastname | User.surname |
    | idpid    | < Azure AD 識別子 >

    > [!Note]
    > 属性 **idpid** の値は、実際のものではありません。 手順 8 の **[Set up Moxtra]\(Moxtra の設定\)** セクションから実際の値を取得できます。 

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Moxtra の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-moxtra-single-sign-on"></a>Moxtra シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、管理者として Moxtra 企業サイトにサインオンします。

2. 左のツール バーで、 **[管理コンソール] > [SAML Single Sign-on]\(SAML シングル サインオン\)** の順にクリックし、 **[新規]** をクリックします。
   
    ![Configure single sign-on](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. **[SAML]** ページで、次の手順を実行します。
   
    ![Configure single sign-on](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. **[名前]** テキスト ボックスに、構成の名前を入力します (例:*SAML*)。 
  
    b. **[IdP Entity ID]\(IdP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。 
 
    c. **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。 
 
    d. **[AuthnContextClassRef]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:ac:classes:Password**」と入力します。 
 
    e. **[NameID 形式]** ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**」と入力します。 
 
    f. Azure Portal からダウンロードした証明書をメモ帳で開き、その内容をコピーして、 **[証明書]** ボックスに貼り付けます。    
 
    g. SAML 電子メール ドメイン テキストボックスに、SAML 電子メール ドメインを入力します。    
  
    >[!NOTE]
    >ドメインを検証するための手順を確認するには、下の**i**をクリックします。

    h. **[Update]** をクリックします。

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

このセクションでは、Britta Simon に Moxtra へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Moxtra]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Moxtra]** を選択します。

    ![アプリケーションの一覧の Moxtra のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-moxtra-test-user"></a>Moxtra テスト ユーザーの作成

このセクションの目的は、Moxtra で Britta Simon というユーザーを作成することです。

**Moxtra で Britta Simon というユーザーを作成するには、次の手順を実行します。**

1. Moxtra 企業サイトに管理者としてサインオンします。

1. 左のツール バーで、 **[Admin Console (管理コンソール)]、[User Management (ユーザー管理)]** の順にクリックし、 **[Add User (ユーザーの追加)]** をクリックします。
   
    ![Configure single sign-on](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. **[Add User]** ダイアログで、次の手順を実行します。
  
    a. **[名]** ボックスに「**Britta**」と入力します。
  
    b. **[姓]** ボックスに「**Simon**」と入力します。
  
    c. **[電子メール]** ボックスに、Azure Portal と同じ Britta の電子メール アドレスを入力します。
  
    d. **[Division (事業部)]** ボックスに「**Dev**」と入力します。
  
    e. **[Department (部門)]** ボックスに「**IT**」と入力します。
  
    f. **[管理者]** を選択します。
  
    g. **[追加]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Moxtra] タイルをクリックすると、SSO を設定した Moxtra に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

