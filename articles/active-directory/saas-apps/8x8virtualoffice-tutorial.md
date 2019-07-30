---
title: チュートリアル:Azure Active Directory と 8x8 Virtual Office の統合 | Microsoft Docs
description: Azure Active Directory と 8x8 Virtual Office の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fb9ee12c0373f2d7038417935709caae4a6eb43
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107482"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>チュートリアル:Azure Active Directory と 8x8 Virtual Office の統合

このチュートリアルでは、8x8 Virtual Office と Azure Active Directory (Azure AD) を統合する方法について説明します。
8x8 Virtual Office と Azure AD の統合には、次の利点があります。

* 8x8 Virtual Office にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで 8x8 Virtual Office に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と 8x8 Virtual Office の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* 8x8 Virtual Office でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。


* 8x8 Virtual Office では、**IDP** によって開始される SSO がサポートされます

* 8x8 Virtual Office では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>ギャラリーからの 8x8 Virtual Office の追加

Azure AD への 8x8 Virtual Office の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に 8x8 Virtual Office を追加する必要があります。

**ギャラリーから 8x8 Virtual Office を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**8x8 Virtual Office**」と入力し、結果ウィンドウで **[8x8 Virtual Office]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの 8x8 Virtual Office](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** という名前のテスト ユーザーに基づいて、8x8 Virtual Office で Azure AD のシングル サインオンを構成およびテストします。
シングル サインオンを機能させるには、Azure AD ユーザーと 8x8 Virtual Office 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

8x8 Virtual Office で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[8x8 Virtual Office のシングル サインオンの構成](#configure-8x8-virtual-office-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[8x8 Virtual Office のテスト ユーザーの作成](#create-8x8-virtual-office-test-user)** - 8x8 Virtual Office で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

8x8 Virtual Office で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **[8x8 Virtual Office]** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を実行します。

    ![[8x8 Virtual Office のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://sso.8x8.com/saml2` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://sso.8x8.com/saml2` のパターンを使用して URL を入力します

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (未加工)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[8x8 Virtual Office の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-8x8-virtual-office-single-sign-on"></a>8x8 Virtual Office のシングル サインオンの構成

1. 8x8 Virtual Office テナントに管理者としてサインオンします。

1. アプリケーション パネルで **[Virtual Office Account Mgr (Virtual Office アカウント マネージャー)]** を選択します。

    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. 管理する **[Business (ビジネス)]** アカウントを選択し、 **[Sign In (サインイン)]** をクリックします。

    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. メニュー リストの **[ACCOUNTS]\(アカウント\)** タブをクリックします。

   ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. アカウントの一覧で **[Single Sign On (シングル サインオン)]** をクリックします。
  
   ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. [Authentication methods]\(認証方法\) の **[Single Sign On]\(シングル サインオン\)** を選択し、 **[SAML]** をクリックします。

   ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. **[SAML Single Sign-On]\(SAML シングル サインオン\)** セクションで、次の手順に従います。

   ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

   a. **[Sign In URL]\(サインイン URL\)** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

   b. **[Sign Out URL]\(サインアウト URL\)** テキストボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

   c. **[Issuer URL]\(発行者 URL\)** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

   d. **[Browse]\(参照\)** ボタンをクリックして、Azure portal からダウンロードした証明書をアップロードします。

   e. **[保存]** ボタンをクリックします。

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

このセクションでは、8x8 Virtual Office へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[8x8 Virtual Office]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**8x8 Virtual Office**」と入力して選択します。

    ![アプリケーションの一覧の 8x8 Virtual Office リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-8x8-virtual-office-test-user"></a>8x8 Virtual Office のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを 8x8 Virtual Office に作成します。 8x8 Virtual Office では、**Just-In-Time ユーザー プロビジョニング**がサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 8x8 Virtual Office にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[8x8 Virtual Office サポート チーム](https://www.8x8.com/about-us/contact-us)に問い合わせる必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [8x8 Virtual Office] タイルをクリックすると、SSO を設定した 8x8 Virtual Office に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

