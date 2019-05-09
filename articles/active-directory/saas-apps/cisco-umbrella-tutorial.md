---
title: チュートリアル:Azure Active Directory と Cisco Umbrella の統合 | Microsoft Docs
description: Azure Active Directory と Cisco Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b0763e33607367939476ca155040295de864c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837985"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>チュートリアル:Azure Active Directory と Cisco Umbrella の統合

このチュートリアルでは、Cisco Umbrella と Azure Active Directory (Azure AD) を統合する方法について説明します。
Cisco Umbrella と Azure AD の統合には、次の利点があります。

* Cisco Umbrella にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Cisco Umbrella にサインイン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Cisco Umbrella と Azure AD の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Cisco Umbrella シングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Cisco Umbrella では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-cisco-umbrella-from-the-gallery"></a>ギャラリーからの Cisco Umbrella の追加

Azure AD への Cisco Umbrella の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Cisco Umbrella を追加する必要があります。

**ギャラリーから Cisco Umbrella を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Cisco Umbrella**」と入力して、結果パネルから **[Cisco Umbrella]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果リスト内の Cisco Umbrella](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと [アプリケーション名] 内の関連ユーザー間にリンク関係が確立されている必要があります。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Cisco Umbrella シングル サインオンの構成](#configure-cisco-umbrella-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Cisco Umbrella のテスト ユーザーの作成](#create-cisco-umbrella-test-user)** - Cisco Umbrella で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Cisco Umbrella** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、**[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[Cisco Umbrella のドメインと URL] のシングル サインオン情報](common/both-preintegrated-signon.png)

    a. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    b. **[追加の URL を設定します]** をクリックします。

    c. **[サインオン URL]** ボックスに、「`https://login.umbrella.com/sso`」と入力します。

5. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)**  ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションから**メタデータ XML**をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Cisco Umbrella のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-cisco-umbrella-single-sign-on"></a>Cisco Umbrella シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Cisco Umbrella 企業サイトにサインオンします。

2. メニューの左側で **[Admin]\(管理者\)** をクリックして、**[Authentication]\(認証\)** に移動し、**[SAML]** をクリックします。

    ![管理](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. **[Other]\(その他\)** を選択して、**[NEXT]\(次\)** をクリックします。

    ![その他](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. **[Cisco Umbrella Metadata]\(Cisco Umbrella メタデータ\)** ページで、**[NEXT]\(次\)** をクリックします。

    ![メタデータ](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. **[Upload Metadata]\(メタデータのアップロード\)** タブで、SAML を事前構成した場合には、**[Click here to change them]\(ここをクリックしてそれらを変更する\)** オプションを選択し、次の手順に従います。

    ![次](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. **[Option A:Upload XML file]\(オプション A: XML ファイルのアップロード\)** で、Azure portal からダウンロードした**フェデレーション メタデータ XML** ファイルをアップロードします。アップロードすると、次の値が自動的に入力されます。**[NEXT]\(次\)** をクリックします。

    ![Choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. **[Validate SAML Configuration]\(SAML 構成の検証\)** セクションで、**[TEST YOUR SAML CONFIGURATION]\(SAML 構成のテスト\)** をクリックします。

    ![テスト](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Cisco Umbrella へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Cisco Umbrella]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Cisco Umbrella**」と入力して選択します。

    ![アプリケーションの一覧の [Cisco Umbrella] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-cisco-umbrella-test-user"></a>Cisco Umbrella テスト ユーザーの作成

Azure AD ユーザーが Cisco Umbrella にログインできるようにするには、ユーザーを Cisco Umbrella にプロビジョニングする必要があります。  
Cisco Umbrella の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 別の Web ブラウザーのウィンドウで、管理者として Cisco Umbrella 企業サイトにサインオンします。

2. メニューの左側で **[Admin]\(管理者\)** をクリックして、**[Accounts]\(アカウント\)** をクリックします。

    ![アカウント](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. **[Accounts]\(アカウント\)** ページの右側の一番上にある **[Add]\(追加\)** をクリックして、次の手順を実行します。

    ![ユーザー](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. **[First Name]\(名\)** フィールドに、名前 (**Britta** など) を入力します。

    b. **[Last Name]\(姓\)** フィールドに、姓 (**Simon** など) を入力します。

    c. **[Choose Delegated Admin Role]\(代理管理者ロールの選択\)** から自分のロールを選択します。
  
    d. **[Email address]\(メール アドレス\)** フィールドに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    e. **[Password]\(パスワード\)** フィールドにパスワードを入力します。

    f. **[Confirm Password]\(パスワードの確認\)** フィールドにパスワードをもう一度入力します。

    g. **[作成]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cisco Umbrella] タイルをクリックすると、SSO を設定した Cisco Umbrella に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
