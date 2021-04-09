---
title: 'チュートリアル: Azure Active Directory と LockPath Keylight の統合 | Microsoft Docs'
description: Azure Active Directory と LockPath Keylight の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 81949736603d22cac779d08d14bd6db65065d730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459086"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>チュートリアル: Azure Active Directory と LockPath Keylight の統合

このチュートリアルでは、LockPath Keylight と Azure Active Directory (Azure AD) を統合する方法について説明します。
LockPath Keylight と Azure AD の統合には、次の利点があります。

* LockPath Keylight にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に LockPath Keylight にサインイン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

LockPath Keylight と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* LockPath Keylight でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LockPath Keylight では、**SP** によって開始される SSO がサポートされます
* LockPath Keylight では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-lockpath-keylight-from-the-gallery"></a>ギャラリーからの LockPath Keylight の追加

Azure AD への LockPath Keylight の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LockPath Keylight を追加する必要があります。

**ギャラリーから LockPath Keylight を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**LockPath Keylight**」と入力し、結果ウィンドウで **LockPath Keylight** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の LockPath Keylight](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、LockPath Keylight で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと LockPath Keylight 内の関連ユーザー間にリンク関係が確立されている必要があります。

LockPath Keylight で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[LockPath Keylight シングル サインオンの構成](#configure-lockpath-keylight-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[LockPath Keylight テスト ユーザーの作成](#create-lockpath-keylight-test-user)** - LockPath Keylight で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

LockPath Keylight で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **LockPath Keylight** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[LockPath Keylight のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<company name>.keylightgrc.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<company name>.keylightgrc.com`

    c. **[応答 URL]** ボックスに、`https://<company name>.keylightgrc.com/Login.aspx` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、識別子、および応答 URL で更新してください。 これらの値を取得するには、[LockPath Keylight クライアント サポート チーム](https://www.lockpath.com/contact/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (未加工)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[LockPath Keylight のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>LockPath Keylight のシングル サインオンを構成する

1. LockPath Keylight で SSO を有効にするには、次の手順に従います。

    a. 管理者として LockPath Keylight アカウントにサインオンします。

    b. 上部のメニューで **[人]** をクリックして **[Keylight セットアップ]** を選択します。

    !["歯車" アイコンが選択され、ドロップダウンの [Keylight Setup]\(Keylight セットアップ\) が選択されているスクリーンショット。](./media/keylight-tutorial/401.png)

    c. 左側のツリー ビューで **[SAML]** をクリックします。

    ![ツリー ビューで [SAML] が選択されているスクリーンショット。](./media/keylight-tutorial/402.png)

    d. **[SAML 設定]** ダイアログで **[編集]** をクリックします。

    ![[Edit]\(編集\) ボタンが選択されている [SAML Settings]\(SAML の設定\) ウィンドウを示すスクリーンショット。](./media/keylight-tutorial/404.png)

1. **[SAML 設定の編集]** ダイアログ ページで、次の手順を実行します。

    ![Configure single sign-on](./media/keylight-tutorial/405.png)

    a. **[SAML 認証]** を **[アクティブ]** に設定します。

    b. **[ID プロバイダーのログイン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[ID プロバイダーのログアウト URL]** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[Choose File]\(ファイルの選択\)** をクリックし、ダウンロードした LockPath Keylight 証明書を選択して、 **[Open]\(開く\)** をクリックして証明書をアップロードします。

    e. **[SAML ユーザー ID の場所]** を **[Subject ステートメントの NameIdentifier 要素]** に設定します。

    f. **Keylight サービス プロバイダー** を `https://<CompanyName>.keylightgrc.com` の形式で指定します。

    g. **[ユーザーの自動プロビジョニング]** を **[アクティブ]** に設定します。

    h. **[アカウント タイプの自動プロビジョニング]** を **[すべてのユーザー]** に設定します。

    i. **[Auto-provision security role]\(セキュリティ ロールの自動プロビジョニング\)** を設定し、 **[Standard User with SAML]\(SAML を使用する標準ユーザー\)** を選択します。

    j. **[Auto-provision security config]\(セキュリティ構成の自動プロビジョニング\)** を設定し、 **[Standard User Configuration]\(標準ユーザー構成\)** を選択します。

    k. **[Email Attribute]\(電子メール属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    l. **[First name attribute]\(名属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    m. **[Last name attribute]\(姓属性\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。

    n. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LockPath Keylight へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[LockPath Keylight]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[LockPath Keylight]** を選択します。

    ![アプリケーションの一覧の LockPath Keylight のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-lockpath-keylight-test-user"></a>LockPath Keylight テスト ユーザーを作成する

このセクションでは、LockPath Keylight で Britta Simon というユーザーを作成します。 LockPath Keylight では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 LockPath Keylight にユーザーがまだ存在していない場合は、認証後に新規に作成されます。 ユーザーを手動で作成する必要がある場合は、[LockPath Keylight クライアント サポート チーム](https://www.lockpath.com/contact/)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [LockPath Keylight] タイルをクリックすると、SSO を設定した LockPath Keylight に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)