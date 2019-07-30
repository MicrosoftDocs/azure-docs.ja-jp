---
title: チュートリアル:Azure Active Directory と Marketo の統合 | Microsoft Docs
description: Azure Active Directory と Marketo の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 28dd21bac2c1fb1602fd66ca237b230cdb12bdac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097651"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>チュートリアル:Marketo と Azure Active Directory の統合

このチュートリアルでは、Marketo と Azure Active Directory (Azure AD) を統合する方法について説明します。
Marketo と Azure AD の統合には、次の利点があります。

* Marketo にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Marketo に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Marketo と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Marketo でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Marketo では、**IDP** Initiated SSO がサポートされます

## <a name="adding-marketo-from-the-gallery"></a>ギャラリーからの Marketo の追加

Azure AD への Marketo の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Marketo を追加する必要があります。

**ギャラリーから Marketo を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Marketo**」と入力し、結果パネルで **[Marketo]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Marketo](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Marketo で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Marketo 内の関連ユーザー間にリンク関係が確立されている必要があります。

Marketo で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Marketo シングル サインオンの構成](#configure-marketo-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Marketo のテスト ユーザーの作成](#create-marketo-test-user)** - Marketo で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Marketo で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Marketo** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[Marketo のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://saml.marketo.com/sp` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://login.marketo.com/saml/assertion/\<munchkinid\>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Marketo クライアント サポート チーム](https://investors.marketo.com/contactus.cfm)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Marketo のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-marketo-single-sign-on"></a>Marketo のシングル サインオンの構成

1. アプリケーションの Munchkin ID を取得するには、管理者の資格情報を使用して Marketo にログインし、次の操作を実行します。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 統合メニューに移動し、**Munchkin リンク**をクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. 画面に表示される Munchkin ID をコピーし、Azure AD の構成ウィザードで、応答 URL を完了します。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. アプリケーションで SSO を構成するには、以下の手順に従ってください。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 統合メニューに移動し、 **[シングルサインオン]** をクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. SAML 設定を有効にするには、 **[編集]** ボタンをクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. シングル サインオン設定を**有効**にします。
   
    f. **Azure AD 識別子**を **[発行者 ID]** ボックスに貼り付けます。
   
    g. **[エンティティ ID]** ボックスに、URL「`http://saml.marketo.com/sp`」を入力します。
   
    h. **[Name Identifier element]\(名前識別子要素\)** としてユーザー ID の場所を選択します。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > ユーザー識別子が UPN 値ではない場合、[属性] タブで値を変更します。
   
    i. Azure AD の構成ウィザードからダウンロードした証明書をアップロードします。 設定を**保存**します。
   
    j. ページのリダイレクト設定を編集します。
   
    k. **[ログイン URL]** ボックスに**ログイン URL** を貼り付けます。
   
    l. **[ログアウト URL]** ボックスに**ログアウト URL** を貼り付けます。
   
    m. **[エラー URL]** に **Marketo インスタンス URL** をコピーし、 **[保存]** ボタンをクリックして設定を保存します。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_10.png)

3. ユーザーの SSO を有効にするには、次の操作を行います。
   
    a. 管理者の資格情報を使用して Marketo アプリにログインします。
   
    b. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. **[セキュリティ]** メニューに移動して、 **[ログイン設定]** をクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. **[SSO 必須]** オプションをオンにして、設定を**保存**します。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_14.png)

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

このセクションでは、Britta Simon に Marketo へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Marketo]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Marketo]** を選択します。

    ![アプリケーションの一覧の [Marketo] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-marketo-test-user"></a>Marketo のテスト ユーザーの作成

このセクションでは、Marketo で Britta Simon というユーザーを作成します。 Marketo プラットフォームでユーザーを作成するには、次の手順に従ってください。

1. 管理者の資格情報を使用して Marketo アプリにログインします。

2. 上部のナビゲーション ウィンドウで **[管理者]** ボタンをクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. **[セキュリティ]** メニューに移動して、 **[ユーザーと役割]** をクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. [ユーザー] タブで **[新しいユーザーの追加]** をクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. [新しいユーザーの追加] ウィザードで、次の情報を入力します。
   
    a. テキスト ボックスにユーザーの **[メール]** アドレスを入力します。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. テキスト ボックスに **[名]** を入力します。
   
    c. テキスト ボックスに **[姓]** を入力します。
   
    d. **[次へ]** をクリックします

6. **[アクセス許可]** タブで **[userRoles]** を選択して **[次へ]** をクリックします。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_17.png)
7. **[送信]** ボタンをクリックしてユーザーの招待を送信します。
   
    ![Configure single sign-on](./media/marketo-tutorial/tutorial_marketo_18.png)

8. ユーザーは、電子メール通知を受け取った後、リンクをクリックしてパスワードを変更し、アカウントをアクティブ化する必要があります。 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Marketo] タイルをクリックすると、SSO を設定した Marketo に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

