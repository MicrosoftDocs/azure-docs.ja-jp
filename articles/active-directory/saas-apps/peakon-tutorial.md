---
title: 'チュートリアル: Azure Active Directory と Peakon の統合 | Microsoft Docs'
description: Azure Active Directory と Peakon の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: a84d4d71e5190c455441d1e627381be86ef5e129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608529"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>チュートリアル: Azure Active Directory と Peakon の統合

このチュートリアルでは、Peakon と Azure Active Directory (Azure AD) を統合する方法について説明します。
Peakon と Azure AD の統合には、次の利点があります。

* Peakon にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Peakon に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Peakon と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Peakon でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Peakon では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-peakon-from-the-gallery"></a>ギャラリーからの Peakon の追加

Azure AD への Peakon の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Peakon を追加する必要があります。

**ギャラリーから Peakon を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Peakon**」と入力し、結果パネルで **[Peakon]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Peakon](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Peakon で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Peakon 内の関連ユーザー間にリンク関係が確立されている必要があります。

Peakon で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Peakon シングル サインオンの構成](#configure-peakon-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Peakon テスト ユーザーの作成](#create-peakon-test-user)** - Peakon で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Peakon で Azure AD シングル サインオンを構成するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) の **Peakon** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Peakon のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://app.peakon.com/saml/<companyid>/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.peakon.com/saml/<companyid>/assert` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[追加の URL を設定します] のスクリーンショット。[サインオン URL] というテキスト ボックスが強調表示されています。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.peakon.com/login`」と入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL に値を置き換えます。実際の値については後で説明します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (未加工)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

7. **[Peakon の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-peakon-single-sign-on"></a>Peakon のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、管理者として Peakon にサインインします。

2. ページの左側にあるメニュー バーの **[Configuration]\(構成\)** をクリックした後、 **[Integrations]\(統合\)** に移動します。

    ![構成](./media/peakon-tutorial/tutorial_peakon_config.png)

3. **[Integrations]\(統合\)** ページで、 **[Single Sign-On]\(シングル サインオン\)** をクリックします。

    ![シングル](./media/peakon-tutorial/tutorial_peakon_single.png)

4. **[Single Sign-On]\(シングル サインオン\)** セクションで、 **[Enable]\(有効化\)** をクリックします。

    ![有効化](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. **[Single sign-on for employees using SAML]\(シングル サインオンの設定\)** セクションで、次の手順に従います。

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. **[SSO Login URL]\(ログイン URL\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[SSO Logout URL]\(SSO ログアウト URL\)** テキスト ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    c. **[Choose a File]\(ファイルの選択\)** を選択して、Azure portal からダウンロードした証明書を [Certificate]\(証明書\) ボックスにアップロードします。

    d. **アイコン** をクリックして **[Entity ID]\(エンティティ ID\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

    e. **アイコン** をクリックして **[Reply URL (ACS)]\(応答 URL (ACS)\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    f. **[保存]**

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Peakon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Peakon]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Peakon]** を選択します。

    ![アプリケーションの一覧の [Peakon] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-peakon-test-user"></a>Peakon テスト ユーザーの作成

Azure AD ユーザーが Peakon にサインインできるようにするには、ユーザーを Peakon にプロビジョニングする必要があります。  
Peakon の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Peakon 企業サイトに管理者としてサインインします。

2. ページの左側にあるメニュー バーの **[Configuration]\(構成\)** をクリックした後、 **[Employees]\(従業員\)** に移動します。

    ![従業員](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. ページの右上の **[Add employee]\(従業員の追加\)** をクリックします。

    ![従業員の追加](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. **[New employee]\(新しい従業員\)** ダイアログ ページで、次の手順に従います。

    ![新しい従業員](./media/peakon-tutorial/tutorial_peakon_create.png)

    1. **[Name]\(名前\)** ボックスに、姓として「**Britta**」を、名として「**simon**」を入力します。

    1. **[Email]\(メール\)** ボックスに、メール アドレス (例: **Brittasimon\@contoso.com**) を入力します。

    1. **[Create employee]\(従業員の作成\)** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Peakon] タイルをクリックすると、SSO を設定した Peakon に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)