---
title: チュートリアル:Azure Active Directory と CloudPassage の統合 | Microsoft Docs
description: Azure Active Directory と CloudPassage の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b94d852ec5e0cba133fc1e141a5695e1c898f16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105341"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>チュートリアル:Azure Active Directory と CloudPassage の統合

このチュートリアルでは、CloudPassage と Azure Active Directory (Azure AD) を統合する方法について説明します。
CloudPassage と Azure AD の統合には、次の利点があります。

* CloudPassage にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して CloudPassage に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

CloudPassage と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* CloudPassage でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* CloudPassage では、**SP** によって開始される SSO がサポートされます

## <a name="adding-cloudpassage-from-the-gallery"></a>ギャラリーからの CloudPassage の追加

Azure AD への CloudPassage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に CloudPassage を追加する必要があります。

**ギャラリーから CloudPassage を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**CloudPassage**」と入力し、結果パネルで **[CloudPassage]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの CloudPassage](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、CloudPassage で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと CloudPassage 内の関連ユーザー間にリンク関係が確立されている必要があります。

CloudPassage での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[CloudPassage シングル サインオンの構成](#configure-cloudpassage-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[CloudPassage テスト ユーザーの作成](#create-cloudpassage-test-user)** - CloudPassage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

CloudPassage との Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **CloudPassage** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[CloudPassage のドメインと URL] のシングル サインオン情報](common/sp-reply.png)

    a. **[サインオン URL]** ボックスに、`https://portal.cloudpassage.com/saml/init/accountid` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://portal.cloudpassage.com/saml/consume/accountid` のパターンを使用して URL を入力します。 この属性の値は、CloudPassage ポータルの **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで **[SSO Setup documentation (SSO セットアップのドキュメント)]** をクリックすることで取得できます。

    ![Configure single sign-on](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 これらの値を取得するには、[CloudPassage クライアント サポート チーム](https://www.cloudpassage.com/company/contact/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. CloudPassage アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。 

    | Name | ソース属性|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |

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

8. **[CloudPassage のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-cloudpassage-single-sign-on"></a>CloudPassage シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として CloudPassage 企業サイトにサインオンします。

1. 上部のメニューの **[Settings (設定)]** をクリックし、 **[Site Administration (サイトの管理)]** をクリックします。 
   
    ![Configure single sign-on][12]

1. **[Authentication Settings (認証設定)]** タブをクリックします。 
   
    ![Configure single sign-on][13]

1. **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで、次の手順に従います。 
   
    ![Configure single sign-on][14]

    a. **[Enable Single sign-on(SSO)(SSO Setup Documentation)]\(シングル サインオン (SSO)(SSO セットアップ ドキュメント) を有効にする\)** チェックボックスをオンにします。
    
    b. **Azure AD 識別子**を **[SAML issuer URL]\(SAML 発行者 URL\)** ボックスに貼り付けます。
  
    c. **ログイン URL** を **[SAML endpoint URL]\(SAML エンドポイント URL\)** ボックスに貼り付けます。
  
    d. **ログアウト URL** を **[Logout landing page]\(ログアウト ランディング ページ\)** ボックスに貼り付けます。
  
    e. ダウンロードした証明書をメモ帳で開き、ダウンロードした証明書の内容をクリップボードにコピーして、 **[x 509 certificate]\(x 509 証明書\)** ボックスに貼り付けます。
  
    f. **[Save]** をクリックします。

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

このセクションでは、Britta Simon に CloudPassage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[CloudPassage]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[CloudPassage]** を選択します。

    ![アプリケーションの一覧の CloudPassage のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-cloudpassage-test-user"></a>CloudPassage テスト ユーザーの作成

このセクションの目的は、CloudPassage で Britta Simon というユーザーを作成することです。

**CloudPassage で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. **CloudPassage** 企業サイトに管理者としてサインオンします。 

1. 上部にあるツールバーの **[Settings (設定)]** をクリックし、 **[Site Administration (サイトの管理)]** をクリックします。 
   
    ![CloudPassage テスト ユーザーの作成][22] 

1. **[Users (ユーザー)]** タブをクリックし、 **[Add New User (新しいユーザーの追加)]** をクリックします。 
   
    ![CloudPassage テスト ユーザーの作成][23]

1. **[新しいユーザーの追加]** セクションで、次の手順を実行します。 
   
    ![CloudPassage テスト ユーザーの作成][24]
    
    a. **[名]** ボックスに「Britta」と入力します。 
  
    b. **[姓]** ボックスに「Simon」と入力します。
  
    c. **[Username]** 、 **[Email]** 、 **[Retype Email]** の各ボックスに、Britta の Azure AD でのユーザー名を入力します。
  
    d. **[Access Type]** で **[Enable Halo Portal Access]** を選択します。
  
    e. **[追加]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [CloudPassage] タイルをクリックすると、SSO を設定した CloudPassage に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

