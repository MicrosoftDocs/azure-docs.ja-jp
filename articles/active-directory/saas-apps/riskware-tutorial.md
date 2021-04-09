---
title: 'チュートリアル: Azure Active Directory と Riskware の統合 | Microsoft Docs'
description: Azure Active Directory と Riskware の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e33664501483280978c67b100cd56a714ef63a30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520763"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>チュートリアル: Azure Active Directory と Riskware の統合

このチュートリアルでは、Riskware と Azure Active Directory (Azure AD) を統合する方法について説明します。
Riskware と Azure AD の統合には、次の利点があります。

* Riskware にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Riskware にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Riskware と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Riskware でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Riskware では、**SP** によって開始される SSO がサポートされます

## <a name="adding-riskware-from-the-gallery"></a>ギャラリーからの Riskware の追加

Azure AD への Riskware の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Riskware を追加する必要があります。

**ギャラリーから Riskware を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Riskware**」と入力し、結果パネルで **[Riskware]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Riskware](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Riskware で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Riskware 内の関連ユーザー間にリンク関係が確立されている必要があります。

Riskware で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Riskware シングル サインオンの構成](#configure-riskware-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Riskware テスト ユーザーの作成](#create-riskware-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Riskware で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Riskware で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Riskware** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Riskware ドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    
    | 環境| URL パターン|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. **[識別子 (エンティティ ID)]** ボックスに  という URL を入力します。
    
    | 環境| URL パターン|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Riskware クライアント サポート チーム](mailto:support@pansoftware.com.au)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Riskware のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-riskware-single-sign-on"></a>Riskware シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Riskware 企業サイトに管理者としてサインインします。

1. 右上の **[Maintenance]\(メンテナンス\)** をクリックしてメンテナンス ページを開きます。

    ![Riskware 構成、メンテナンス](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. メンテナンス ページで、**[Authentication]\(認証\)** をクリックします。

    ![Riskware 構成、認証](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. **[Authentication Configuration]\(認証の構成\)** ページで、次の手順に従います。

    ![Riskware 構成、authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. 認証の **[Type]\(種類\)** として **[SAML]** を選択します。

    b. **[Code]\(コード\)** ボックスにコードを入力します (例: AZURE_UAT)。

    c. **[Description]\(説明\)** ボックスに説明を入力します (例:SSO 用 AZURE 構成)。

    d. **[Single Sign On Page]\(シングル サインオン ページ\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. **[Sign out Page]\(サインアウト ページ\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    f. **[Post Form Field]\(Post フォーム フィールド\)** ボックスに、SAML を含む Post 応答にあるフィールド名を入力します (例: SAMLResponse)

    g. **[XML Identity Tag Name]\(XML ID タグ名\)** ボックスに、SAML 応答内の一意の識別子を含む属性を入力します (例: NameID)。

    h. Azure Portal からダウンロードした **メタデータ Xml** をメモ帳で開き、メタデータ ファイルから証明書をコピーして **[証明書]\(Certificate\)** ボックスに貼り付けます

    i. **[Consumer URL]\(コンシューマー URL\)** ボックスに、サポート チームから入手した **応答 URL** の値を貼り付けます。

    j. **[Issuer]\(発行者\)** ボックスに、サポート チームから入手した **識別子** の値を貼り付けます。

    > [!Note]
    > これらの値を取得するには、[Riskware クライアント サポート チーム](mailto:support@pansoftware.com.au)にお問い合わせください

    k. **[Use POST]\(POST を使用する\)** チェックボックスをオンにします。

    l. **[Use SAML Request]\(SAML 要求を使用する\)** チェックボックスをオンにします。

    m. **[保存]** をクリックします。

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

このセクションでは、Britta Simon に Riskware へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Riskware]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Riskware]** を選択します。

    ![アプリケーションの一覧の Riskware のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-riskware-test-user"></a>Riskware テスト ユーザーの作成

Azure AD ユーザーが Riskware にサインインできるようにするには、ユーザーを Riskware にプロビジョニングする必要があります。 Riskware では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Riskware にサインインします。

1. 右上の **[Maintenance]\(メンテナンス\)** をクリックしてメンテナンス ページを開きます。 

    ![Riskware 構成、メンテナンス](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. メンテナンス ページで、**[People]\(ユーザー\)** をクリックします。

    ![Riskware 構成、ユーザー](./media/riskware-tutorial/tutorial_riskware_people.png)

1. **[Details]\(詳細\)** タブを選択し、次の手順を実行します。

    ![Riskware 構成、詳細](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. **[Person Type]\(ユーザーの種類\)** で、ユーザーの種類を選択します (例: [Employee]\(従業員\))。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名前を入力します (例: **Britta**)。

    c. **[Surname]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

1. **[セキュリティ]** タブで、次の手順に従います。

    ![Riskware 構成、セキュリティ](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. **[Authentication]\(認証\)** セクションで、既に設定済みの **認証** モードを選択します (例:SSO 用 AZURE 構成)。

    b. **[Logon Details]\(ログオンの詳細\)** セクションの **[User ID]\(ユーザー ID\)** ボックスに、ユーザーのメール アドレスを入力します (例: `brittasimon@contoso.com`)。

    c. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

1. **[Organization]\(組織\)** タブで、次の手順に従います。

    ![Riskware 構成、組織](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. **Level1** 組織としてオプションを選択します。

    b. **[Person's Primary Workplace]\(ユーザーのプライマリ ワークプレース\)** セクションで、**[Location]\(場所\)** ボックスに場所を入力します。

    c. **[Employee]\(従業員\)** セクションで、**[Employee Status]\(従業員のステータス\)** を選択します (例: [Casual]\(カジュアル\))。

    d. **[保存]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Riskware] タイルをクリックすると、SSO を設定した Riskware に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)