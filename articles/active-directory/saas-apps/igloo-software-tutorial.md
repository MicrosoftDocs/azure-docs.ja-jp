---
title: 'チュートリアル: Azure Active Directory と Igloo Software の統合 | Microsoft Docs'
description: Azure Active Directory と Igloo Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 033561ac3c4a510927691dc8db4f61196f54ec2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460395"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>チュートリアル: Azure Active Directory と Igloo Software の統合

このチュートリアルでは、Igloo Software と Azure Active Directory (Azure AD) を統合する方法について説明します。
Igloo Software と Azure AD の統合には、次の利点があります。

* Igloo Software にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Igloo Software に自動的にサインイン (シングル サインオン) されるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Igloo Software と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Igloo Software でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Igloo Software では、**SP** によって開始される SSO がサポートされます
* Igloo Software では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-igloo-software-from-the-gallery"></a>ギャラリーからの Igloo Software の追加

Azure AD への Igloo Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Igloo Software を追加する必要があります。

**ギャラリーから Igloo Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Igloo Software**」と入力し、結果パネルの **[Igloo Software]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の Igloo Software](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Igloo Software で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Igloo Software 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Igloo Software で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Igloo Software のシングル サインオンの構成](#configure-igloo-software-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Igloo Software のテスト ユーザーの作成](#create-igloo-software-test-user)** - Igloo Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Igloo Software で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Igloo Software** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Igloo Software のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<company name>.igloocommmunities.com` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<company name>.igloocommmunities.com/saml.digest` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<company name>.igloocommmunities.com/saml.digest` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Igloo Software クライアント サポート チーム](https://www.igloosoftware.com/services/support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Igloo Software のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-igloo-software-single-sign-on"></a>Igloo Software のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Igloo Software 企業サイトにログインします。

2. [**コントロール パネル**] に移動します。

     ![コントロール パネル](./media/igloo-software-tutorial/ic799949.png "コントロール パネル")

3. [**メンバーシップ**] タブで、[**サイン イン設定**] をクリックします。

    ![[サインインの設定]](./media/igloo-software-tutorial/ic783968.png "[サインインの設定]")

4. [SAML 構成] セクションで、[**SAML 認証を構成する**] をクリックします。

    ![SAML の構成](./media/igloo-software-tutorial/ic783969.png "SAML の構成")

5. [**一般構成**] セクションで、次の手順を実行します。

    ![[全般構成]](./media/igloo-software-tutorial/ic783970.png "[全般構成]")

    a. [**接続名**] テキスト ボックスに、構成の名前を入力します。

    b. **[IdP Login URL]\(IdP ログイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[IdP Logout URL]\(IdP ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[POST]\(POST\)** として、**[Logout Response and Request HTTP Type]\(ログアウト応答と要求 HTTP のタイプ\)** を選択します。

    e. Azure Portal からダウンロードした **base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[Public Certificate]\(パブリック証明書\)** ボックスに貼り付けます。

6. [**応答と認証の構成**] で、次の手順を実行します。

    ![[応答および認証の構成]](./media/igloo-software-tutorial/IC783971.png "[応答および認証の構成]")
  
    a. [**Id プロバイダー**] として [**Microsoft ADFS**] を選択します。

    b. [**識別子の型**] として [**電子メール アドレス**] を選択します。 

    c. [**電子メール属性**] テキスト ボックスに「**emailaddress**」と入力します。

    d. [**名属性**] テキスト ボックスに「**givenname**」を入力します。

    e. [**姓属性**] テキスト ボックスに「**surname**」を入力します。

7. 次の手順を実行して、構成を完成させます。

    ![サインインでユーザー作成](./media/igloo-software-tutorial/IC783972.png "サインインでユーザー作成") 

    a. [**サインインでのユーザー作成**] で [**サインイン時に新規ユーザーを作成**] を選択します。

    b. [**サインイン設定**] で [**サインイン画面で SAML ボタンを使用**] を選択します。

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
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Igloo Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Igloo Software]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Igloo Software]** を選択します。

    ![アプリケーションの一覧の Igloo Software リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-igloo-software-test-user"></a>Igloo Software のテスト ユーザーの作成

Igloo Software へのユーザー プロビジョニングを構成するときに必要な操作はありません。  

割り当てられたユーザーがアクセス パネルを使用して Igloo Software にログインしようとすると、そのユーザーが存在するかどうかが Igloo Software によって確認されます。  使用可能なユーザー アカウントがない場合、ユーザー アカウントは Igloo Software によって自動的に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Igloo Software] タイルをクリックすると、SSO を設定した Igloo Software に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)