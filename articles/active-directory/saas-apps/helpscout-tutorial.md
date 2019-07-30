---
title: チュートリアル:Azure Active Directory と Help Scout の統合 | Microsoft Docs
description: Azure Active Directory と Help Scout の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3625a6bbf22a2d51546a91e8a5f7c58b7fbb264f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101185"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>チュートリアル:Azure Active Directory と Help Scout の統合

このチュートリアルでは、Help Scout と Azure Active Directory (Azure AD) を統合する方法について説明します。
Help Scout と Azure AD の統合には、次の利点があります。

* Help Scout にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Help Scout に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Help Scout と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Help Scout でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Help Scout では、**SP と IDP** によって開始される SSO がサポートされます
* Help Scout では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-help-scout-from-the-gallery"></a>ギャラリーからの Help Scout の追加

Azure AD への Help Scout の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Help Scout を追加する必要があります。

**ギャラリーから Help Scout を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Help Scout**」と入力し、結果パネルで **Help Scout** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Help Scout](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Help Scout で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Help Scout 内の関連ユーザー間にリンク関係が確立されている必要があります。

Help Scout で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Help Scout シングル サインオンの構成](#configure-help-scout-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Help Scout テスト ユーザーの作成](#create-help-scout-test-user)** - Help Scout で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Help Scout で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Help Scout** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Help Scout のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **識別子**は、Help Scout の**対象ユーザー URI (サービス プロバイダーのエンティティ ID)** で、先頭は `urn:` です

    b. **応答 URL** は、Help Scout の**ポスト バック URL (Assertion Consumer Service URL)** で、先頭は `https://` です 

    > [!NOTE]
    > これらの URL の値は、単なる例です。 これらの値は、実際の応答 URL と識別子で更新する必要があります。 この値は、[認証] セクションの **[シングル サインオン]** タブから取得します。これについては後で説明します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Help Scout のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、「`https://secure.helpscout.net/members/login/`」と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Help Scout のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-help-scout-single-sign-on"></a>Help Scout のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Help Scout 企業サイトに管理者としてログインします。

2. 上部のメニューで **[Manage]\(管理\)** をクリックし、ドロップダウン メニューの **[Company]\(会社\)** を選択します。

    ![Configure single sign-on](./media/helpscout-tutorial/settings1.png)

3. 左側のナビゲーション ウィンドウで **[Authentication]\(認証\)** を選択します。

    ![Configure single sign-on](./media/helpscout-tutorial/settings2.png)

4. [SAML 設定] セクションが表示されます。ここで次の手順に従います。

    ![Configure single sign-on](./media/helpscout-tutorial/settings3.png)

    a. **[Post-back URL (Assertion Consumer Service URL)]\(ポスト バック URL (Assertion Consumer Service URL)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    b. **[Audience URI (Service Provider Entity ID)]\(対象ユーザー URI (サービス プロバイダー エンティティ ID)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

5. **[SAML を有効にする]** をオンにして、次の手順を実行します。

    ![Configure single sign-on](./media/helpscout-tutorial/settings4.png)

    a. **[Single Sign-On URL]\(シングル サインオン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    b. **[証明書のアップロード]** をクリックして、Azure Portal からダウンロードした**証明書 (Base64)** をアップロードします。

    c. **[メール ドメイン]** ボックスに、組織のメール ドメイン (例: `contoso.com`) を入力します。 複数のドメインを指定する場合は、コンマで区切ります。 Help Scout ユーザーまたは管理者が [Help Scout ログイン ページ](https://secure.helpscout.net/members/login/)で特定のドメインを入力すると必ず、その資格情報で認証するために ID プロバイダーにルーティングされます。

    d. 最後に、ユーザーがこの方法以外で Help Scout にログオンできないようにする場合は、 **[Force SAML Sign-on]\(強制 SAML サインオン\)** の設定を切り替えてオンにします。 Help Scout 資格情報でも引き続きサインインできるようにする場合は、この設定をオフのままにします。 これを有効にしても、アカウント所有者は、いつでも自身のアカウント パスワードで Help Scout にログインにします。

    e. **[Save]** をクリックします。

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

このセクションでは、Britta Simon に Help Scout へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Help Scout]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Help Scout]** を選択します。

    ![アプリケーションの一覧の Help Scout のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-help-scout-test-user"></a>Help Scout テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Help Scout に作成します。 Help Scout では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Help Scout にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Help Scout] タイルをクリックすると、SSO を設定した Help Scout に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)