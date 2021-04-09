---
title: チュートリアル:Azure Active Directory と InstaVR Viewer の統合 | Microsoft Docs
description: Azure Active Directory と InstaVR Viewer の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: e60e8c73c9f1da617851cc67fb2dbab7171f1cb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460007"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>チュートリアル:Azure Active Directory と InstaVR Viewer の統合

このチュートリアルでは、InstaVR Viewer と Azure Active Directory (Azure AD) を統合する方法について説明します。
InstaVR Viewer と Azure AD の統合には、次の利点があります。

* InstaVR Viewer にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して InstaVR Viewer に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

InstaVR Viewer と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* InstaVR Viewer でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* InstaVR Viewer では、**SP** によって開始される SSO がサポートされます
* InstaVR Viewer は、**Just-In-Time** ユーザー プロビジョニングをサポートしています

## <a name="adding-instavr-viewer-from-the-gallery"></a>ギャラリーから InstaVR Viewer を追加する

Azure AD への InstaVR Viewer の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に InstaVR Viewer を追加する必要があります。

**ギャラリーから InstaVR Viewer を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**InstaVR Viewer**」と入力し、結果パネルで **[InstaVR Viewer]** を選び、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果一覧の InstaVR Viewer](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、InstaVR Viewer で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと InstaVR Viewer 内の関連ユーザー間にリンク関係が確立されている必要があります。

InstaVR Viewer で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[InstaVR Viewer シングル サインオンの構成](#configure-instavr-viewer-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[InstaVR Viewer のテスト ユーザーの作成](#create-instavr-viewer-test-user)** - InstaVR Viewer で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

InstaVR Viewer で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **[InstaVR Viewer]** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[InstaVR Viewer のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > サインオン URL に固定のパターンはありません。 これは InstaVR Viewer の顧客が Web パッケージを行うときに生成されます。 これはすべての顧客とパッケージに対して一意になります。 正確なサインオン URL を取得するには、InstaVR Viewer インスタンスにログインして Web パッケージを実行する必要があります。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > この識別子の値は実際のものではありません。 この値は、実際の識別子の値に置き換えてください。これについては後で説明します。

5. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **証明書 (Base64)** と **フェデレーション メタデータ ファイル** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadata-certificatebase64.png)

6. **[InstaVR Viewer のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-instavr-viewer-single-sign-on"></a>InstaVR Viewer シングル サインオンの構成

1. 新しい Web ブラウザー ウィンドウを開き、InstaVR Viewer 企業サイトに管理者としてログインします。

2. **ユーザー アイコン** をクリックして、 **[Account]\(アカウント\)** を選択します。

    ![ユーザーが選択されている InstaVR Viewer サイトを示すスクリーンショット。](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. 下へスクロールして **[SAML Auth]\(SAML 認証\)** に移動し、次の手順に従います。

    ![この手順で説明されている値を入力できる [SAML Auth]\(SAML 認証\) ページを示すスクリーンショット。](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. **[SSO URL]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[Logout URL]\(ログアウト URL\)** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    c. **[Entity ID]\(エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    d. ダウンロードした証明書ファイルをアップロードするには、 **[Update]\(更新\)** をクリックします。

    e. ダウンロードしたフェデレーション メタデータ ファイルをアップロードするには、 **[Update]\(更新\)** をクリックします。

    f. **[Entity ID]\(エンティティ ID\)** 値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** テキスト ボックスに貼り付けます。

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

このセクションでは、Britta Simon に InstaVR Viewer へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[InstaVR Viewer]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**InstaVR Viewer**」と入力して選択します。

    ![アプリケーションの一覧の [InstaVR Viewer] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-instavr-viewer-test-user"></a>InstaVR Viewer テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを InstaVR Viewer に作成します。 InstaVR Viewer では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 InstaVR Viewer にユーザーがまだ存在していない場合は、認証後に新規に作成されます。 問題が発生した場合には、[InstaVR Viewer のサポート チーム](mailto:contact@instavr.co)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

1. 新しい Web ブラウザー ウィンドウを開き、InstaVR Viewer 企業サイトに管理者としてログインします。

2. 左側のナビゲーション パネルから **[Package]\(パッケージ\)** を選択し、 **[Make package for Web]\(Web 用にパッケージ化\)** を選択します。

    ![[Select Package]\(パッケージの選択\) と [Make package for Web]\(Web 用にパッケージ化\) が選択された InstaVR Viewer 会社サイトを示すスクリーンショット。](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. **[Download]** を選択します。

    ![[Download]\(ダウンロード\) アイコンが選択されているスクリーンショット。](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. **[Open Hosted Page]\(ホストされているページを開く\)** を選択すると、ログインのために Azure AD にリダイレクトされます。

    ![[Open Hosted Page]\(ホストされているページを開く\) が選択されているスクリーンショット。](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. SSO を介して Azure AD に正常にログインするには、Azure AD 資格情報を入力します。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)