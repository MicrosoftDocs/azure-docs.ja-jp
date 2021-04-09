---
title: チュートリアル:Azure Active Directory と ClickUp Productivity の統合 | Microsoft Docs
description: Azure Active Directory と ClickUp Productivity Platform の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 753256adddf8b28518bac45011d582bc7a079629
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97670404"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>チュートリアル:Azure Active Directory と ClickUp Productivity Platform の統合

このチュートリアルでは、ClickUp Productivity Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。
ClickUp Productivity Platform と Azure AD の統合には、次の利点があります。

* ClickUp Productivity Platform にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで ClickUp Productivity Platform に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ClickUp Productivity Platform と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* ClickUp Productivity Platform でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ClickUp Productivity Platform では、**SP** Initiated SSO がサポートされます

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>ギャラリーからの ClickUp Productivity Platform の追加

Azure AD への ClickUp Productivity Platform の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に ClickUp Productivity Platform を追加する必要があります。

**ギャラリーから ClickUp Productivity Platform を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**ClickUp Productivity Platform**」と入力し、結果パネルで **ClickUp Productivity Platform** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ClickUp Productivity Platform](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、ClickUp Productivity Platform で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと ClickUp Productivity Platform 内の関連ユーザーの間にリンク関係が確立されている必要があります。

ClickUp Productivity Platform で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[ClickUp Productivity Platform のシングル サインオンの構成](#configure-clickup-productivity-platform-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[ClickUp Productivity Platform のテスト ユーザーの作成](#create-clickup-productivity-platform-test-user)** - ClickUp Productivity Platform で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

ClickUp Productivity Platform で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **ClickUp Productivity Platform** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[ClickUp Productivity Platform ドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに URL として「`https://app.clickup.com/login/sso`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > この識別子の値は実際のものではありません。 この値は、実際の識別子に置き換えてください。これについては後で説明します。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>ClickUp Productivity Platform のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として ClickUp Productivity Platform テナントにサインオンします。

2. **[User profile]\(ユーザー プロファイル\)** をクリックし、 **[Settings]\(設定\)** を選択します。

    ![[スクリーンショットは、Settings]\(設定\) アイコンが選択された ClickUp Productivity テナントを示します。](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![スクリーンショットは、[Settings]\(設定\) を示します。](./media/clickup-productivity-platform-tutorial/configure1.png)

3. [Single Sign-On (SSO) Provider]\(シングル サインオン (SSO) プロバイダー\) で **[Microsoft]** を選択します。

    ![スクリーンショットは、[Microsoft] が選択されている [Authentication]\(認証\) ペインを示します。](./media/clickup-productivity-platform-tutorial/configure2.png)

4. **[Configure Microsoft Single Sign On]\(Microsoft のシングル サインオンの構成\)** ページで、次の手順を実行します。

    ![スクリーンショットは、[Configure Microsoft Single Sign On]\(Microsoft のシングル サインオンの構成\) ページを示します。ここで、[Entity I D]\(エンティティ I D\) をコピーし、[Azure Federation Metadata U R L]\(Azure フェデレーション メタデータ U R L\) を保存できます。](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. **[Copy]\(コピー\)** をクリックして [Entity ID]\(エンティティ ID\) の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** ボックスに貼り付けます。

    b. **[Azure Federation Metadata Url]\(Azure のフェデレーション メタデータ URL\)** ボックスに、Azure portal からコピーした [アプリのフェデレーション メタデータ URL] の値を貼り付けて、 **[Save]\(保存\)** をクリックします。

5. この設定を完了するには、 **[Authenticate With Microsoft to complete setup]\(Microsoft で認証してセットアップを完了する\)** をクリックして、Microsoft アカウントで認証します。

    ![スクリーンショットは、[Authenticate With Microsoft to complete setup]\(Microsoft で認証してセットアップを完了する\) ボタンを示します。](./media/clickup-productivity-platform-tutorial/configure4.png)

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

このセクションでは、Britta Simon に ClickUp Productivity Platform へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[ClickUp Productivity Platform]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[ClickUp Productivity Platform]** を選択します。

    ![アプリケーションの一覧の ClickUp Productivity Platform のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-clickup-productivity-platform-test-user"></a>ClickUp Productivity Platform のテスト ユーザーの作成

1. 別の Web ブラウザーのウィンドウで、管理者として ClickUp Productivity Platform テナントにサインオンします。

2. **[User profile]\(ユーザー プロファイル\)** をクリックし、 **[People]\(ユーザー\)** を選択します。

    ![スクリーンショットは、ClickUp Productivity テナントを示します。](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![スクリーンショットは、[People]\(ユーザー\) リンクが選択されていることを示します。](./media/clickup-productivity-platform-tutorial/user1.png)

3. テキスト ボックスにユーザーのメール アドレスを入力して、 **[Invite]\(招待\)** をクリックします。

    ![スクリーンショットは、[Team Users Settings]\(チーム ユーザー設定\) を示します。これを設定するとメールでユーザーを招待できます。](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > ユーザーは通知を受け取ったら、招待を承諾してアカウントをアクティブにする必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [ClickUp Productivity Platform] タイルをクリックすると、SSO を設定した ClickUp Productivity Platform に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)