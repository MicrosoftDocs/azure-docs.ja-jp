---
title: 'チュートリアル: Azure Active Directory と Thoughtworks Mingle の統合 | Microsoft Docs'
description: Azure Active Directory と Thoughtworks Mingle の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c3eeb1577e628965e3e5a35fa20c072224383149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514624"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>チュートリアル: Azure Active Directory と Thoughtworks Mingle の統合

このチュートリアルでは、Thoughtworks Mingle と Azure Active Directory (Azure AD) を統合する方法について説明します。
Thoughtworks Mingle と Azure AD の統合には、次の利点があります。

* Thoughtworks Mingle にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Thoughtworks Mingle に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Thoughtworks Mingle の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Thoughtworks Mingle でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Thoughtworks Mingle では、**SP** Initiated SSO がサポートされます

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>ギャラリーからの Thoughtworks Mingle の追加

Azure AD への Thoughtworks Mingle の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Thoughtworks Mingle を追加する必要があります。

**ギャラリーから Thoughtworks Mingle を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Thoughtworks Mingle**」と入力し、結果ウィンドウで **[Thoughtworks Mingle]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Thoughtworks Mingle](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Thoughtworks Mingle で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Thoughtworks Mingle 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Thoughtworks Mingle で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Thoughtworks Mingle シングル サインオンの構成](#configure-thoughtworks-mingle-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Thoughtworks Mingle テスト ユーザーの作成](#create-thoughtworks-mingle-test-user)** - Thoughtworks Mingle で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Thoughtworks Mingle で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Thoughtworks Mingle** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Thoughtworks Mingle のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.mingle.thoughtworks.com` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Thoughtworks Mingle クライアント サポート チーム](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Thoughtworks Mingle のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Thoughtworks Mingle シングル サインオンの構成

1. **Thoughtworks Mingle** 企業サイトに管理者としてサインインします。

2. [**管理**] タブをクリックし、[**SSO 構成**] をクリックします。
   
    ![[Admin]\(管理\) タブ](./media/thoughtworks-mingle-tutorial/ic785157.png "[SSO Config]")

3. [**SSO 構成**] セクションで、以下の手順を実行します。
   
    ![[SSO Config]](./media/thoughtworks-mingle-tutorial/ic785158.png "[SSO Config]")
    
    a. メタデータ ファイルをアップロードするには、[**ファイルの選択**] をクリックします。 

    b. **[変更を保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Thoughtworks Mingle へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Thoughtworks Mingle]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、**[Thoughtworks Mingle]** を選択します。

    ![アプリケーションの一覧の Thoughtworks Mingle リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-thoughtworks-mingle-test-user"></a>Thoughtworks Mingle テスト ユーザーの作成

Azure AD ユーザーがサインインできるようにするには、Azure Active Directory ユーザー名を使用して、Azure AD ユーザーを Thoughtworks Mingle アプリケーションにプロビジョニングする必要があります。 Thoughtworks Mingle の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. Thoughtworks Mingle 企業サイトに管理者としてサインインします。

2. [**プロファイル**] をクリックします。
   
    ![最初のプロジェクト](./media/thoughtworks-mingle-tutorial/ic785160.png "最初のプロジェクト")

3. [**管理**] タブをクリックし、[**ユーザー**] をクリックします。
   
    ![ユーザー](./media/thoughtworks-mingle-tutorial/ic785161.png "ユーザー")

4. [**New User**] をクリックします。
   
    ![[New User]\(新しいユーザー\)](./media/thoughtworks-mingle-tutorial/ic785162.png "[新しいユーザー]")

5. **[New User]** ダイアログ ページで、次の手順に従います。
   
    ![[New User]\(新しいユーザー\) ダイアログ](./media/thoughtworks-mingle-tutorial/ic785163.png "[新しいユーザー]")  
 
    a. プロビジョニングする有効な Azure AD アカウントの **サインイン名**、**表示名**、**パスワードの選択**、**パスワードの確認** を該当するボックスに入力します。 

    b. [**ユーザーの種類**] として、[**完全なユーザー**] を選択します。

    c. [**このプロファイルを作成**] をクリックします。

>[!NOTE]
>他の Thoughtworks Mingle ユーザー アカウント作成ツールや、Thoughtworks Mingle から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Thoughtworks Mingle] タイルをクリックすると、SSO を設定した Thoughtworks Mingle に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)