---
title: チュートリアル:Azure Active Directory と PureCloud by Genesys の統合 | Microsoft Docs
description: Azure Active Directory と PureCloud by Genesys の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: ac476e2a66f7fd1d315adb37258917b3ff47373f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093463"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>チュートリアル:Azure Active Directory と PureCloud by Genesys の統合

このチュートリアルでは、PureCloud by Genesys と Azure Active Directory (Azure AD) を統合する方法について説明します。
PureCloud by Genesys と Azure AD の統合には、次の利点があります。

* PureCloud by Genesys にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して PureCloud by Genesys に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

PureCloud by Genesys と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* PureCloud by Genesys でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* PureCloud by Genesys では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>ギャラリーからの PureCloud by Genesys の追加

Azure AD への PureCloud by Genesys の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に PureCloud by Genesys を追加する必要があります。

**ギャラリーから PureCloud by Genesys を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**PureCloud by Genesys**」と入力し、結果パネルで **[PureCloud by Genesys]** を選び、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果リストの PureCloud by Genesys](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、PureCloud by Genesys で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと PureCloud by Genesys の関連ユーザーの間にリンク関係が確立されている必要があります。

PureCloud by Genesys で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[PureCloud by Genesys シングル サインオンの構成](#configure-purecloud-by-genesys-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[PureCloud by Genesys のテスト ユーザーの作成](#create-purecloud-by-genesys-test-user)** - PureCloud by Genesys で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

PureCloud by Genesys で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **PureCloud by Genesys** アプリケーション統合ページで、 **[シングル サインオン]** を選びます。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[PureCloud by Genesys のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、自分のリージョンに応じて URL を入力します。

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. **[応答 URL]** ボックスに、自分のリージョンに応じて URL を入力します。

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[PureCloud by Genesys のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、自分のリージョンに応じて URL を入力します。
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. PureCloud by Genesys アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]**  アイコンをクリックして、 **[ユーザー属性]**  ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. その他に、PureCloud by Genesys アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name | ソース属性|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

9. **[PureCloud by Genesys のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>PureCloud by Genesys シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、PureCloud by Genesys に管理者としてログインします。

2. 上部の **[Admin]\(管理\)** をクリックし、 **[Integrations]\(統合\)** の **[Single Sign-on]\(シングル サインオン\)** に移動します。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure01.png)

3. **[ADFS/Azure AD (Premium)]** タブに切り替えて、次の手順に従います。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. **[Browse]\(参照\)** をクリックして、Azure portal からダウンロードした base 64 でエンコードされた証明書を **[ADFS Certificate]\(ADFS 証明書\)** にアップロードします。

    b. **[ADFS Issuer URI]\(ADFS 発行者の URI\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[Target URI]\(ターゲット URI\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[Relying Party Identifier]\(証明書利用者識別子\)** の値については、Azure portal に移動し、**PureCloud by Genesys** アプリケーション統合ページで **[プロパティ]** タブをクリックして、 **[アプリケーション ID]** の値をコピーします。 それを **[Relying Party Identifier]\(証明書利用者識別子\)** ボックスに貼り付けます。 

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. **[保存]**   

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

このセクションでは、PureCloud by Genesys へのアクセスを許可して、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[PureCloud by Genesys]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[PureCloud by Genesys]** を選択します。

    ![アプリケーションの一覧の PureCloud by Genesys リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud by Genesys のテスト ユーザーの作成

Azure AD ユーザーが PureCloud by Genesys にログインできるようにするには、それらを PureCloud by Genesys にプロビジョニングする必要があります。 PureCloud by Genesys では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. PureCloud by Genesys に管理者としてログインします。

2. 上部の **[Admin]\(管理\)** をクリックし、 **[People & Permissions]\(ユーザーとアクセス許可\)** の **[People]\(ユーザー\)** に移動します。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure03.png)

3. [People]\(ユーザー\) ページで、 **[Add Person]\(ユーザーの追加\)** をクリックします。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure04.png)

4. **[Add People to the Organization]\(組織へのユーザーの追加\)** ポップアップで、次の手順に従います。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの名前を入力します (例: **Brittasimon**)。

    b. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。
    
    c. **Create** をクリックしてください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [PureCloud by Genesys] タイルをクリックすると、SSO を設定した PureCloud by Genesys に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

