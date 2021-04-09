---
title: 'チュートリアル: Azure Active Directory と Sauce Labs - Mobile and Web Testing の統合 | Microsoft Docs'
description: Azure Active Directory と Sauce Labs - Mobile and Web Testing の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 087e2d7f2db8f27378b54675095e97256d6aae9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895112"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>チュートリアル: Azure Active Directory と Sauce Labs - Mobile and Web Testing の統合

このチュートリアルでは、Sauce Labs - Mobile and Web Testing と Azure Active Directory (Azure AD) を統合する方法について説明します。
Sauce Labs - Mobile and Web Testing と Azure AD の統合には、次の利点があります。

* Sauce Labs - Mobile and Web Testing にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが各自の Azure AD アカウントで Sauce Labs - Mobile and Web Testing に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Sauce Labs - Mobile and Web Testing の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Sauce Labs - Mobile and Web Testing でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Sauce Labs - Mobile and Web Testing では、**IDP** によって開始される SSO がサポートされます
* Sauce Labs - Mobile and Web Testing では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>ギャラリーから Sauce Labs - Mobile and Web Testing を追加する

Azure AD への Sauce Labs - Mobile and Web Testing の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Sauce Labs - Mobile and Web Testing を追加する必要があります。

**ギャラリーから Sauce Labs - Mobile and Web Testing を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Sauce Labs - Mobile and Web Testing**」と入力し、結果パネルで **[Sauce Labs - Mobile and Web Testing]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Sauce Labs - Mobile and Web Testing](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Sauce Labs - Mobile and Web Testing で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Sauce Labs - Mobile and Web Testing 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Sauce Labs - Mobile and Web Testing で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Sauce Labs - Mobile and Web Testing のシングル サインオンの構成](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Sauce Labs - Mobile and Web Testing テスト ユーザーの作成](#create-sauce-labs---mobile-and-web-testing-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Sauce Labs - Mobile and Web Testing で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Sauce Labs - Mobile and Web Testing で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Sauce Labs - Mobile and Web Testing** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[Sauce Labs - Mobile and Web Testing のドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Sauce Labs - Mobile and Web Testing のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Sauce Labs - Mobile and Web Testing のシングル サインオンの構成

1. Web ブラウザーの別のウィンドウで、Sauce Labs - Mobile and Web Testing 企業サイトに管理者としてサインインします。

2. **[User]\(ユーザー\) アイコン** をクリックし、 **[Team Management]\(チーム管理\)** タブを選択します。

    ![[User]\(ユーザー\) アイコンと、選択された [Team Management]\(チーム管理\) ドロップダウンを示すスクリーンショット。](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. テキストボックスに **ドメイン名** を入力します。

    ![テキスト ボックス内のドメイン名の例を示すスクリーンショット。](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. **[Configure]\(構成\)** タブをクリックします。

    ![[Single Sign On is Enabled]\(シングル サインオンが有効\) で選択されている [Configure]\(構成\) タブを示すスクリーンショット。](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. **[Configure Single Sign On]\(シングル サインオンの構成\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. **[Browse]\(参照\)** をクリックし、Azure AD からダウンロードしたメタデータ ファイルをアップロードします。

    b. **[ALLOW JUST-IN-TIME PROVISIONING]\(Just-In-Time プロビジョニングを許可する\)** チェックボックスをオンにします。

    c. **[保存]** をクリックします。

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

このセクションでは、Britta Simon に Sauce Labs - Mobile and Web Testing へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Sauce Labs - Mobile and Web Testing]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Sauce Labs - Mobile and Web Testing]** を選択します。

    ![アプリケーションの一覧の [Sauce Labs - Mobile and Web Testing] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Sauce Labs - Mobile and Web Testing テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Sauce Labs - Mobile and Web Testing に作成します。 Sauce Labs - Mobile and Web Testing では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Sauce Labs - Mobile and Web Testing にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Sauce Labs - Mobile and Web Testing サポート チーム](mailto:support@saucelabs.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Sauce Labs - Mobile and Web Testing] タイルをクリックすると、SSO を設定した Sauce Labs - Mobile and Web Testing に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)