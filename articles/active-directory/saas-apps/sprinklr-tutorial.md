---
title: チュートリアル:Azure Active Directory と Sprinklr の統合 | Microsoft Docs
description: Azure Active Directory と Sprinklr の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: e76386c8c276c6c8c07751474b3718f98af18f4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519454"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>チュートリアル:Azure Active Directory と Sprinklr の統合

このチュートリアルでは、Sprinklr と Azure Active Directory (Azure AD) を統合する方法について説明します。
Sprinklr と Azure AD の統合には、次の利点があります。

* Sprinklr にアクセスできるユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで Sprinklr に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Sprinklr の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Sprinklr でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Sprinklr では、**SP** Initiated SSO がサポートされます

## <a name="adding-sprinklr-from-the-gallery"></a>ギャラリーからの Sprinklr の追加

Azure AD への Sprinklr の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sprinklr を追加する必要があります。

**ギャラリーから Sprinklr を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Sprinklr**」と入力して、結果パネルで **[Sprinklr]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Sprinklr](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Sprinklr で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Sprinklr 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Sprinklr で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Sprinklr のシングル サインオンの構成](#configure-sprinklr-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Sprinklr のテスト ユーザーの作成](#create-sprinklr-test-user)** - Sprinklr で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Sprinklr で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Sprinklr** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Sprinklr のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.sprinklr.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Sprinklr クライアント サポート チーム](https://www.sprinklr.com/contact-us/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Sprinklr のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sprinklr-single-sign-on"></a>Sprinklr のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Sprinklr 企業サイトに管理者としてログインします。

1. **[管理] \> [設定]** の順に選択します。

    ![管理](./media/sprinklr-tutorial/ic782907.png "管理")

1. 左側のパネルで、 **[Manage Partner] \> [Single Sign on]** の順に選択します。

    ![[Manage Partner]\(パートナーの管理\)](./media/sprinklr-tutorial/ic782908.png "パートナーの管理")

1. **[+Add Single Sign Ons]** をクリックします。

    ![[Add Single Sign Ons]\(シングル サインオンの追加\) ボタンのスクリーンショット。](./media/sprinklr-tutorial/ic782909.png "シングル サインオン")

1. **[Single Sign on]** ページで、次の手順に従います。

    ![[Single Sign on]\(シングル サインオン\) ページのスクリーンショット。ここで、説明されている値を入力できます。](./media/sprinklr-tutorial/ic782910.png "シングル サインオン")

    a. **[Name]\(名前\)** ボックスに、構成の名前を入力します (例:*WAADSSOTest*)。

    b. **[Enabled]** を選択します。

    c. **[Use new SSO Certificate]** を選択します。

    d. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    e. **[Entity Id]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    f. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    g. **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    h. **[SAML User ID Type]\(SAML ユーザー ID の種類\)** として、 **[Assertion contains User’s sprinklr.com username]\(アサーションにユーザーの sprinklr.com ユーザー名を含む\)** を選択します。

    i. **[SAML User ID Location]** として **[User ID is in the Name Identifier element of the Subject statement]** を選択します。

    j. **[保存]** をクリックします。

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

このセクションでは、Britta Simon に Sprinklr へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Sprinklr]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Sprinklr]** を選択します。

    ![アプリケーションの一覧の Sprinklr のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-sprinklr-test-user"></a>Sprinklr テスト ユーザーの作成

1. Sprinklr 企業サイトに管理者としてログインします。

1. **[管理] \> [設定]** の順に選択します。

    ![管理](./media/sprinklr-tutorial/ic782907.png "管理")

1. 左側のパネルで、 **[クライアントの管理] \> [ユーザー]** の順に選択します。

    ![[設定] の [ユーザー] にある [ユーザーの追加] ボタンのスクリーンショット。](./media/sprinklr-tutorial/ic782914.png "設定")

1. **[ユーザーの追加]** をクリックします。

    ![[Edit user]\(ユーザーの編集\) ダイアログ ボックスを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/sprinklr-tutorial/ic782915.png "設定")

1. **[Edit user]** ダイアログで、次の手順に従います。

    ![[Edit user]\(ユーザーの編集\)](./media/sprinklr-tutorial/ic782916.png "[Edit user]")

    a. **[Email]** 、 **[First Name]** 、および **[Last Name]** テキスト ボックスに、プロビジョニングする Azure AD のユーザー アカウントの情報を入力します。

    b. **[Password Disabled]** を選択します。

    c. **[Language]\(言語\)** を選択します。

    d. **[User Type]\(ユーザー タイプ\)** を選択します。

    e. **[Update]** をクリックします。

    > [!IMPORTANT]
    > **[[Password Disabled]]** を選択する必要があります。 

1. **[Role]** に移動して、次の手順に従います。

    ![パートナーのロール](./media/sprinklr-tutorial/ic782917.png "パートナーのロール")

    a. **[Global]** ボックスの一覧から、 **[ALL_Permissions]** を選択します。  

    b. **[Update]** をクリックします。

> [!NOTE]
> Sprinklr から提供されている他の Sprinklr ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Sprinklr] タイルをクリックすると、SSO を設定した Sprinklr に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)