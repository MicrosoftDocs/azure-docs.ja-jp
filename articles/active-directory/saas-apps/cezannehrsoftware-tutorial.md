---
title: 'チュートリアル: Azure Active Directory と Cezanne HR Software の統合 | Microsoft Docs'
description: Azure Active Directory と Cezanne HR Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 0aa0dab7b512c85fbbdf374c962e6ee8e1c7d616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456286"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>チュートリアル: Azure Active Directory と Cezanne HR Software の統合

このチュートリアルでは、Cezanne HR Software と Azure Active Directory (Azure AD) を統合する方法について説明します。
Cezanne HR Software と Azure AD の統合には、次の利点があります。

* Cezanne HR Software にアクセスするユーザーを Azure AD 上でコントロールできます。
* ユーザーが自分の Azure AD アカウントで Cezanne HR Software に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Cezanne HR Software と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Cezanne HR Software でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Cezanne HR Software では、**SP** Initiated SSO がサポートされます

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>ギャラリーからの Cezanne HR Software の追加

Azure AD への Cezanne HR Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cezanne HR Software を追加する必要があります。

**ギャラリーから Cezanne HR Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Cezanne HR Software**」と入力し、結果ウィンドウで **Cezanne HR Software** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Cezanne HR Software](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Cezanne HR Software で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Cezanne HR Software 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Cezanne HR Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Cezanne HR Software のシングル サインオンの構成](#configure-cezanne-hr-software-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Cezanne HR Software のテスト ユーザーの作成](#create-cezanne-hr-software-test-user)** - Cezanne HR Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Cezanne HR Software で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Cezanne HR Software** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Cezanne HR Software のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. **[識別子 (エンティティ ID)]** ボックスに `https://w3.cezanneondemand.com/CezanneOnDemand/` という URL を入力します。

    c. **[応答 URL]** ボックスに、`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp` のパターンを使用して URL を入力します。
    
    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 これらの値を入手するには、[Cezanne HR Software クライアント サポート チーム](https://cezannehr.com/services/support/)にお問い合わせください。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Cezanne HR Software のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Cezanne HR Software のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Cezanne HR Software テナントにサインオンします。

2. 左側のナビゲーション ウィンドウで、 **[System Setup (システム設定)]** をクリックします。 **[Security Settings (セキュリティの設定)]** に移動します。 次に、 **[Single Sign-On Configuration (シングル サインオンの構成)]** に移動します。

    ![スクリーンショットは、[Security Settings]\(セキュリティの設定\) と [Single Sign-On Configuration]\(シングル サインオンの構成\) が選択されている Cezanne H R Software テナントを示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. **[Allow users to log in using the following Single Sign-On (SSO) Service (ユーザーに次のシングル サインオン (SSO) サービスを使用したログインを許可する)]** パネルで **[SAML 2.0]** チェック ボックスをオンにして、 **[Advanced Configuration (詳細設定)]** オプションを選択します。

    ![スクリーンショットは、[SAML 2.0] と [Advanced Configuration]\(詳細設定\) が選択されている [Allow users]\(ユーザーに許可する\) ペインを示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. **[Add New (新規追加)]** ボタンをクリックします。

    ![スクリーンショットは、[Add New]\(新規追加\) ボタンを示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. **[SAML 2.0 IDENTITY PROVIDERS (SAML 2.0 ID プロバイダー)]** セクションで、次の手順に従います。

    ![スクリーンショットは、この手順で説明した値を入力できるペインを示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. **[Display Name (表示名)]** に ID プロバイダー名を入力します。

    b. **[Entity Identifier]\(エンティティ識別子\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[SAML Binding (SAML バインディング)]** を "POST" に変更します。

    d. **[セキュリティ トークン サービス エンドポイント]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. [ユーザー ID の属性名] ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    f. **アップロード** アイコンをクリックして、Azure Portal からダウンロードした証明書をアップロードします。

    g. **[OK]** ボタンをクリックします。

6. **[保存]** ボタンをクリックします。

    ![スクリーンショットは、[Single Sign-on Configuration]\(シングル サインオン構成\) の [Save]\(保存\) ボタンを示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

このセクションでは、Britta Simon に Cezanne HR Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Cezanne HR Software]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Cezanne HR Software]** を選択します。

    ![アプリケーションの一覧の Cezanne HR Software のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-cezanne-hr-software-test-user"></a>Cezanne HR Software のテスト ユーザーの作成

Azure AD ユーザーが Cezanne HR Software にログインできるようにするには、ユーザーを Cezanne HR Software にプロビジョニングする必要があります。 Cezanne HR Software の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Cezanne HR Software 企業サイトに管理者としてログインします。

2. 左側のナビゲーション ウィンドウで、 **[System Setup (システム設定)]** をクリックします。 **[ユーザーの管理]** に移動します。 **[Add New User (新しいユーザーの追加)]** に移動します。

    ![[Manage Users]\(ユーザーの管理\) と [Add New User]\(新しいユーザーの追加\) が選択されている Cezanne H R Software テナントを示すスクリーンショット。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "[新しいユーザー]")

3. **[PERSON DETAILS]\(個人の詳細\)** セクションで、次の手順を実行します。

    ![スクリーンショットは、この手順で説明した値を入力できる [PERSON DETAILS]\(個人の詳細\) セクションを示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "[新しいユーザー]")

    a. **[Internal User (内部ユーザー)]** をオフに設定します。

    b. **[名]** ボックスに、ユーザーの名を入力します (この例では **Britta**)。  

    c. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    d. **[電子メール]** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

4. **[Account Information (アカウント情報)]** セクションで、次の手順を実行します。

    ![スクリーンショットは、この手順で説明した値を入力できる [Account Information]\(アカウント情報\) を示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "[新しいユーザー]")

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    b. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    c. **[Security Role (セキュリティ ロール)]** で **[HR Professional (人事担当者)]** を選択します。

    d. **[OK]** をクリックします。

5. **[Single Sign-On (シングル サインオン)]** タブに移動し、 **[SAML 2.0 Identifiers (SAML 2.0 識別子)]** 領域で **[Add New (新規追加)]** を選択します。

    ![スクリーンショットは、[Add New]\(新規追加\) を選択できる [Single Sign-On]\(シングル サインオン\) タブを示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "User")

6. **[Identity Provider (ID プロバイダー)]** でお使いの ID プロバイダーを選択し、 **[User Identifier (ユーザー識別子)]** ボックスに Britta Simon アカウントの電子メール アドレスを入力します。

    ![スクリーンショットは、ご使用の ID プロバイダーと [User Identifier]\(ユーザー識別子\) を選択できる [SAML 2.0 Identifiers]\(SAML 2.0 識別子\) を示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "User")

7. **[保存]** ボタンをクリックします。

    ![スクリーンショットは、[User Settings]\(ユーザー設定\) の[Save]\(保存\) ボタンを示します。](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "User")

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cezanne HR Software] タイルをクリックすると、SSO を設定した Cezanne HR Software に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)