---
title: 'チュートリアル: Azure Active Directory と Mozy Enterprise の統合 | Microsoft Docs'
description: Azure Active Directory と Mozy Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 97294bdd088f50a46786c3568f4230706f750c4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520950"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>チュートリアル: Azure Active Directory と Mozy Enterprise の統合

このチュートリアルでは、Mozy Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。
Mozy Enterprise と Azure AD の統合には、次の利点があります。

* Azure AD で誰が Mozy Enterprise にアクセスできるかを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Mozy Enterprise に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Mozy Enterprise と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Mozy Enterprise でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Mozy Enterprise では、**SP** によって開始される SSO がサポートされます

## <a name="adding-mozy-enterprise-from-the-gallery"></a>ギャラリーからの Mozy Enterprise の追加

Azure AD への Mozy Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Mozy Enterprise を追加する必要があります。

**ギャラリーから Mozy Enterprise を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Mozy Enterprise**」と入力し、結果パネルで **[Mozy Enterprise]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果一覧の Mozy Enterprise](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Mozy Enterprise で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Mozy Enterprise 内の関連ユーザー間にリンク関係が確立されている必要があります。

Mozy Enterprise で Azure AD のシングル サインオンを構成し、テストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Mozy Enterprise シングル サインオンの構成](#configure-mozy-enterprise-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Mozy Enterprise のテスト ユーザーの作成](#create-mozy-enterprise-test-user)** - Mozy Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Mozy Enterprise で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Mozy Enterprise** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Mozy Enterprise Domain and URLs]\(Mozy Enterprise のドメインと URL\) のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://<tenantname>.Mozyenterprise.com` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Mozy Enterprise クライアント サポート チーム](https://support.mozy.com/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Mozy Enterprise の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Mozy Enterprise でシングル サインオンを構成する

1. 別の Web ブラウザーのウィンドウで、Mozy Enterprise の企業サイトに管理者としてログインします。

2. **[構成]** セクションで、 **[認証ポリシー]** をクリックします。
   
    ![[構成] で [認証ポリシー] が選択されていることを示すスクリーンショット。](./media/mozy-enterprise-tutorial/ic777314.png "[認証ポリシー]")

3. **[認証ポリシー]** セクションで、次の手順に従います。
   
    ![[認証ポリシー] セクションを示すスクリーンショット。ここでは、説明されている値を入力することができます。](./media/mozy-enterprise-tutorial/ic777315.png "[認証ポリシー]")
   
    a. **[プロバイダー]** に **[ディレクトリ サービス]** を選択します。
   
    b. **[Use LDAP Push (LDAP プッシュを使用)]** を選択します。
   
    c. **[SAML 認証]** タブをクリックします。
   
    d. **[認証 URL]** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。
   
    e. **[SAML Endpoint]\(SAML エンドポイント\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** を貼り付けます。
   
    f. ダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[SAML 証明書]** ボックスに貼り付けます。
   
    g. **[Enable SSO for Admins to log in with their network credentials 管理者のネットワーク資格情報を使用した SSO を許可する]** を選択します。
   
    h. **[変更を保存]** をクリックします。

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

このセクションでは、Britta Simon に Mozy Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Mozy Enterprise]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Mozy Enterprise]\(Mozy Enterprise\)** を選択します。

    ![アプリケーションの一覧の Mozy Enterprise のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-mozy-enterprise-test-user"></a>Mozy Enterprise テスト ユーザーの作成

Azure AD ユーザーが Mozy Enterprise にログインできるようにするには、ユーザーを Mozy Enterprise にプロビジョニングする必要があります。 Mozy Enterprise の場合、プロビジョニングは手動で行います。

>[!NOTE]
>他の Mozy Enterprise ユーザー アカウント作成ツールや、Mozy Enterprise から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. お使いの **Mozy Enterprise** テナントにログインします。

2. **[ユーザー]** をクリックして、 **[新規ユーザーの追加]** をクリックします。
   
    ![ユーザー](./media/mozy-enterprise-tutorial/ic777317.png "ユーザー")
   
    >[!NOTE]
    >**[新規ユーザーの追加]** オプションは、 **[認証ポリシー]** で **[Mozy]** が選択されている場合にのみ表示されます。 SAML 認証が構成されている場合、ユーザーはシングル サインオンでの初回ログイン時に自動的に追加されます。
    
3. 新しいユーザーのダイアログで、次の手順に従います。
   
    ![ユーザーの追加](./media/mozy-enterprise-tutorial/ic777318.png "ユーザーの追加")
   
    a. **[Choose a Group (グループを選択)]** の一覧で、グループを選択します。
   
    b. **[What type of user (ユーザーのタイプ)]** の一覧で、タイプを選択します。
   
    c. **[User Name (ユーザー名)]** ボックスに、Azure AD ユーザーの名前を入力します。
   
    d. **[Email (メール)]** ボックスに、Azure AD ユーザーのメール アドレスを入力します。
   
    e. **[Send user instruction email (ユーザーに指示メールを送信する)]** を選択します。
   
    f. **[Add User(s) (ユーザーの追加)]** をクリックします。

     >[!NOTE]
     > ユーザーの作成後、Azure AD ユーザーに、アカウントがアクティブになる前にアカウントを確認するためのリンクが記載されたメールが送信されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Mozy Enterprise] タイルをクリックすると、SSO を設定した Mozy Enterprise に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)