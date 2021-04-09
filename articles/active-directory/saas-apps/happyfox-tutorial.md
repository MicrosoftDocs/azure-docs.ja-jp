---
title: 'チュートリアル: Azure Active Directory と HappyFox の統合 | Microsoft Docs'
description: Azure Active Directory と HappyFox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 1c359f6eb61124f7b1c3d2b38c25fd50041c5710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92446081"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>チュートリアル: Azure Active Directory と HappyFox の統合

このチュートリアルでは、HappyFox と Azure Active Directory (Azure AD) を統合する方法について説明します。
HappyFox と Azure AD の統合には、次の利点があります。

* HappyFox にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで HappyFox に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

HappyFox と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* HappyFox でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* HappyFox では、**SP** Initiated SSO がサポートされます


* HappyFox では、**Just-In-Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-happyfox-from-the-gallery"></a>ギャラリーから HappyFox を追加する

Azure AD への HappyFox の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HappyFox を追加する必要があります。

**ギャラリーから HappyFox を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**HappyFox**」と入力し、結果パネルで **HappyFox** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の HappyFox](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、HappyFox で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと HappyFox 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

HappyFox で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[HappyFox のシングル サインオンの構成](#configure-happyfox-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[HappyFox のテスト ユーザーの作成](#create-happyfox-test-user)** - HappyFox で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

HappyFox で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **HappyFox** アプリケーションの統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[HappyFox のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.happyfox.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.happyfox.com/saml/metadata/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[HappyFox クライアント サポート チーム](https://support.happyfox.com/home)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[HappyFox のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-happyfox-single-sign-on"></a>HappyFox のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として HappyFox テナントにサインオンします。

2. **[管理]** に移動し、 **[統合]** タブをクリックします。

    ![[Integrations]\(統合\) タブが選択されている [Manage]\(管理\) ページを示すスクリーンショット。](./media/happyfox-tutorial/header.png) 

3. [統合] タブで、 **[SAML 統合]** にある **[構成]** をクリックし、[シングル サインオンの設定] を開きます。

    ![[configure]\(構成\) アクションが選択されている [SAML Integration]\(SAML 統合\) 設定を示すスクリーンショット。](./media/happyfox-tutorial/configure.png)

4. [SAML configuration]\(SAML 構成\) セクションで、 **[SSO Target URL]\(SSO ターゲット URL\)** テキストボックスに Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    ![[SSO Target URL]\(SSO ターゲット URL\) テキストボックスが強調表示されている [SAML Configuration]\(SAML 構成\) セクションを示すスクリーンショット。](./media/happyfox-tutorial/targeturl.png)

5. Azure Portal からダウンロードした証明書をメモ帳で開き、 **[IdP Signature]\(IdP 署名\)** セクションにコンテンツを貼り付けます。

    ![[IdP Signature]\(IdP 署名\) が強調表示されているスクリーンショット。](./media/happyfox-tutorial/cert.png)

6. **[設定の保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/happyfox-tutorial/savesettings.png)

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

このセクションでは、Britta Simon に HappyFox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[HappyFox]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[HappyFox]** を選択します。

    ![アプリケーションの一覧の HappyFox のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-happyfox-test-user"></a>HappyFox のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを HappyFox に作成します。 HappyFox では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 HappyFox にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

1. アクセス パネルで [HappyFox] タイルをクリックすると、HappyFox アプリケーションのログイン ページが表示されます。 サインイン ページに **‘SAML’** ボタンが表示されます。

    ![プラグイン](./media/happyfox-tutorial/saml.png)

2. **SAML** ボタンをクリックして、Azure AD アカウントを使って HappyFox にログインします

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)