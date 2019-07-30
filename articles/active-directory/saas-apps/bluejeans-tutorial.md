---
title: チュートリアル:Azure Active Directory と BlueJeans の統合 | Microsoft Docs
description: Azure Active Directory と BlueJeans の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0308fbb103fb06bd2dffe0a442346a3fc4f7db62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106166"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>チュートリアル:Azure Active Directory と BlueJeans の統合

このチュートリアルでは、BlueJeans と Azure Active Directory (Azure AD) を統合する方法について説明します。
BlueJeans と Azure AD の統合には、次の利点があります。

* BlueJeans にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して BlueJeans に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

BlueJeans と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* BlueJeans でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* BlueJeans では、**SP** によって開始される SSO がサポートされます

* BlueJeans では、[**自動化された**ユーザー プロビジョニング](bluejeans-provisioning-tutorial.md)がサポートされます

## <a name="adding-bluejeans-from-the-gallery"></a>ギャラリーからの BlueJeans の追加

Azure AD への BlueJeans の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BlueJeans を追加する必要があります。

**ギャラリーから BlueJeans を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**BlueJeans**」と入力し、結果パネルで **[BlueJeans]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の BlueJeans](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、BlueJeans で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと BlueJeans 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

BlueJeans で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[BlueJeans シングル サインオンの構成](#configure-bluejeans-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[BlueJeans のテスト ユーザーの作成](#create-bluejeans-test-user)** - BlueJeans で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

BlueJeans で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **BlueJeans** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](media/bluejeans-tutorial/edit-urls-bluejeans.png)

4. **[基本的な SAML 構成]** ダイアログで、次の値を入力します。

    ![[BlueJeans のドメインと URL] のシングル サインオン情報](media/bluejeans-tutorial/tutorial_bluejeans-basic-configuration.png)

   - **[識別子]** テキスト ボックスに「`http://samlsp.bluejeans.com`」と入力します。
    
   - **[サインオン URL]** テキスト ボックスに、BlueJeans から提供されるランディング ページ URL 「`https://<companyname>.bluejeans.com`」を入力します (この値を取得するには、[BlueJeans クライアント サポート チーム](https://support.bluejeans.com/contact)にお問い合わせください)。
    
   - **[Save]** をクリックします。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[BlueJeans のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-bluejeans-single-sign-on"></a>BlueJeans シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、**BlueJeans** 企業サイトに管理者としてサインインします。

2. **[管理] \> [グループ設定] \> [セキュリティ]** の順にクリックします。

    ![管理](./media/bluejeans-tutorial/ic785868.png "Admin")

3. **[セキュリティ]** セクションで、次の手順を実行します。

    ![SAML シングル サインオン](./media/bluejeans-tutorial/ic785869.png "SAML Single Sign On")

    a. **[SAML シングル サインオン]** を選択します。

    b. **[自動プロビジョニングの有効化]** を選択します。

4. 次の手順を実行します。

    ![証明書パス](./media/bluejeans-tutorial/ic785870.png "Certificate Path")

    a. **[ファイルの選択]** を選択して、Azure portal からダウンロードした base 64 でエンコードされた証明書をアップロードします。

    b. **[ログイン URL]** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. **[Password Change URL]\(パスワード変更 URL\)** テキスト ボックスに、Azure portal からコピーした**パスワードの変更 URL** の値を貼り付けます。

    d. **[ログアウト URL]** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

5. 次の手順を実行します。

    ![変更を保存](./media/bluejeans-tutorial/ic785874.png "Save Changes")

    a. **[User id]\(ユーザー ID\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    b. **[Email]** (電子メール) ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    c. **[SAVE CHANGES]\(変更の保存\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon\@yourcompanydomain.extension`」と入力します。 たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、BlueJeans へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[BlueJeans]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[BlueJeans]** を選択します。

    ![アプリケーション一覧の [BlueJeans] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-bluejeans-test-user"></a>BlueJeans のテスト ユーザーの作成

このセクションの目的は、BlueJeans で Britta Simon というユーザーを作成することです。 BlueJeans では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](bluejeans-provisioning-tutorial.md)で確認できます。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. **BlueJeans** 企業サイトに管理者としてサインインします。

2. **[管理] \> [ユーザーの管理] \> [ユーザーの追加]** の順にクリックします。

    ![管理](./media/bluejeans-tutorial/ic785877.png "Admin")

    > [!IMPORTANT]
    > **[ユーザーの追加]** タブは、 **[セキュリティ]** タブの **[自動プロビジョニングの有効化]** がオフになっている場合にのみ使用できます。

3. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/bluejeans-tutorial/ic785886.png "Add User")

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    c. **[Pick a BlueJeans Username]\(BlueJeans ユーザー名の選択\)** テキスト ボックスに、ユーザーのユーザー名 (**Brittasimon** など) を入力します

    d. **[パスワードの作成]** テキスト ボックスにパスワードを入力します。

    e. **[会社]** テキスト ボックスに会社名を入力します。

    f. **[Email Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレスを入力します (例: `brittasimon\@contoso.com`)。

    g. **[Create a BlueJeans Meeting I.D]\(BlueJeans 会議 I.D の作成\)** テキスト ボックスに、会議の ID を入力します。

    h. **[Pick a Moderator Passcode]\(モデレーター パスコードの選択\)** テキスト ボックスに、パスコードを入力します。

    i. **[続行]** をクリックします。

    ![ユーザーの追加](./media/bluejeans-tutorial/ic785887.png "Add User")

    J. **[ユーザーの追加]** をクリックします。

> [!NOTE]
> BlueJeans から提供されている他の BlueJeans ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [BlueJeans] タイルをクリックすると、SSO を設定した BlueJeans に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
