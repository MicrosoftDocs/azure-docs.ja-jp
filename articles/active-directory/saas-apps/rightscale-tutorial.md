---
title: 'チュートリアル: Azure Active Directory と Rightscale の統合 | Microsoft Docs'
description: Azure Active Directory と Rightscale の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 013eadedc00dee23a09eff89147406cc14f017ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516585"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>チュートリアル: Azure Active Directory と Rightscale の統合

このチュートリアルでは、Rightscale と Azure Active Directory (Azure AD) を統合する方法について説明します。
Rightscale と Azure AD の統合には、次の利点があります。

* Rightscale にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Rightscale に自動的にサインイン (シングル サインオン) するようにできます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Rightscale の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Rightscale でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Rightscale では、**SP と IDP** によって開始される SSO がサポートされます。

## <a name="adding-rightscale-from-the-gallery"></a>ギャラリーからの Rightscale の追加

Azure AD への Rightscale の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Rightscale を追加する必要があります。

**ギャラリーから Rightscale を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Rightscale**」と入力し、結果パネルから **[Rightscale]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の Rightscale](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Rightscale で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Rightscale の関連ユーザーの間にリンク関係が確立されている必要があります。

Rightscale で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Rightscale シングル サインオンの構成](#configure-rightscale-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Rightscale のテスト ユーザーの作成](#create-rightscale-test-user)** - Rightscale で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Rightscale で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) の **Rightscale** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[基本的な SAML 構成] ページを示すスクリーンショット。](common/preintegrated.png)

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに、URL として「`https://login.rightscale.com/`」と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Set up Rightscale] (Rightscale のセットアップ)** セクションで、要件に従った適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-rightscale-single-sign-on"></a>Rightscale シングル サインオンを構成する

1. アプリケーションに合わせて SSO を構成するには、管理者として RightScale テナントにサインオンする必要があります。

2. 上部にあるメニューの **[Settings]** タブをクリックし、 **[Single Sign-On]** を選択します。

    ![[Settings]\(設定\) の [Single Sign-On]\(シングル サインオン\) が選択されている画面のスクリーンショット。](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. **[new] (新規)** ボタンをクリックして、**SAML ID プロバイダー** を追加します。

    ![SAML ID プロバイダーを追加するための新しいボタンが選択されている画面のスクリーンショット。](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. **[Display Name]** テキスト ボックスに会社名を入力します。

    ![表示名の入力画面のスクリーンショット。](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. **[Allow RightScale-initiated SSO using a discovery hint]** を選択して、下のテキストボックスに **ドメイン名** を入力します。

    ![ログイン方法を指定する画面のスクリーンショット。](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Azure Portal からコピーした **[ログイン URL]** の値を RightScale の **[SAML SSO Endpoint] (SAML SSO エンドポイント)** に貼り付けます。

    ![SAML S S O エンドポイントの入力画面のスクリーンショット。](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Azure Portal からコピーした **[Azure AD 識別子]** の値を RightScale の **[SAML EntityID]** に貼り付けます。

    ![SAML エンティティ I D の入力画面のスクリーンショット。](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. **[Browser]\(ブラウザー\)** ボタンをクリックして、Azure Portal からダウンロードした証明書をアップロードします。

    ![SAML の署名証明書を指定する画面のスクリーンショット。](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Rightscale へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure Portal で、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Rightscale]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Rightscale]** を選択します。

    ![アプリケーションの一覧の [Rightscale] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-rightscale-test-user"></a>Rightscale のテスト ユーザーを作成する

このセクションでは、Rightscale で Britta Simon というユーザーを作成します。 [Rightscale クライアント サポート チーム](mailto:support@rightscale.com)と連携して、Rightscale プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Rightscale] タイルをクリックすると、SSO を設定した Rightscale に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)