---
title: チュートリアル:Azure Active Directory と NetSuite の統合 | Microsoft Docs
description: Azure Active Directory と NetSuite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2600989273d6ebfe4319a048cc65c8c3ff9ecdbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096313"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>チュートリアル:Azure Active Directory と NetSuite の統合

このチュートリアルでは、NetSuite と Azure Active Directory (Azure AD) を統合する方法について説明します。
NetSuite と Azure AD の統合には、次の利点があります。

* NetSuite にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に NetSuite にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と NetSuite の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* NetSuite でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* NetSuite では、**IDP** によって開始される SSO がサポートされます
* NetSuite では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* NetSuite では、[自動化されたユーザー プロビジョニング](NetSuite-provisioning-tutorial.md)がサポートされます

## <a name="adding-netsuite-from-the-gallery"></a>ギャラリーからの NetSuite の追加

Azure AD への NetSuite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に NetSuite を追加する必要があります。

**ギャラリーから NetSuite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**NetSuite**」と入力し、結果ウィンドウで **[NetSuite]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の NetSuite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、NetSuite で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと NetSuite 内の関連ユーザー間にリンク関係が確立されている必要があります。

NetSuite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[NetSuite シングル サインオンの構成](#configure-netsuite-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[NetSuite のテスト ユーザーの作成](#create-netsuite-test-user)** - NetSuite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

NetSuite で Azure AD のシングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **NetSuite** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[NetSuite のドメインと URL] のシングル サインオン情報](common/idp-reply.png)

    **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[NetSuite クライアント サポート チーム](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. NetSuite アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。
    
    | Name | ソース属性 | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

    >[!NOTE]
    >account 属性の値は、実際のものではありません。 この値は後で置き換えます。これについては、このチュートリアルで後ほど説明します。

4. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)**  ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションから**メタデータ XML**をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[NetSuite のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-netsuite-single-sign-on"></a>NetSuite シングル サインオンの構成

1. ブラウザーで新しいタブを開き、NetSuite の会社のサイトに管理者としてサインインします。

2. ページの上部にあるツール バーで、 **[セットアップ]** をクリックして、 **[会社]** に移動し、 **[機能の有効化]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setupsaml.png)

3. ページの中央にあるツール バーで、 **[SuiteCloud]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-suitecloud.png)

4. **[Manage Authentication]\(認証の管理)** セクションで、 **[SAML シングル サインオン]** を選択して、NetSuite での SAML シングル サインオン オプションを有効にします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-ticksaml.png)

5. ページの上部にあるツール バーで、 **[セットアップ]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

6. **[セットアップ タスク]** 一覧で、 **[統合]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-integration.png)

7. **[Manage Authentication]\(認証の管理\)** セクションで、 **[SAML シングル サインオン]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-saml.png)

8. **[SAML Setup]\(SAML セットアップ\)** ページの **[NetSuite の構成]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **[PRIMARY AUTHENTICATION METHOD]\(プライマリ認証方法\)** を選択します。

    b. **[SAMLV2 IDENTITY PROVIDER METADATA]\(SAMLV2 ID プロバイダー メタデータ\)** というラベルが付いたフィールドで、 **[UPLOAD IDP METADATA FILE]\(IDP メタデータ ファイルのアップロード\)** を選択します。 **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    c. **[送信]** をクリックします。

9. NetSuite で **[セットアップ]** をクリックし、 **[会社]** に移動して、上部の ナビゲーション メニューから **[会社情報]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-com.png)

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-account-id.png)

    b. 右側の列の **[会社情報]** ページで、**アカウント ID** をコピーします。

    c. NetSuite アカウントからコピーした**アカウント ID** を Azure AD の**属性値**フィールドに貼り付けます。 

10. ユーザーは NetSuite にシングル サインオンする前に、まず、NetSuite で適切なアクセス許可が割り当てられている必要があります。 次の手順に従って、アクセス許可を割り当てます。

    a. ナビゲーション メニューで、 **[セットアップ]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

    b. 左側のナビゲーション メニューで、 **[Users/Roles]** 、 **[Manage Roles]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. **[New Role]** をクリックします。

    d. 新しいロールの**名前**を入力します:

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-new-role.png)

    e. **[Save]** をクリックします。

    f. 上部のメニューで、 **[Permissions]** をクリックします。 **[Setup]** をクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-sso.png)

    g. **[SAML シングル サインオン]** を選択して、 **[追加]** をクリックします。

    h. **[Save]** をクリックします。

    i. 上部のナビゲーション メニューで、 **[Setup]** 、 **[Setup Manager]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

    j. 左側のナビゲーション メニューで、 **[Users/Roles]** 、 **[Manage Users]** の順にクリックします。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-manage-users.png)

    k. テスト ユーザーを選択します。 次に、 **[編集]** をクリックし、 **[アクセス]** タブに移動します。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-edit-user.png)

    l. [ロール] ダイアログで、作成した適切なロールを割り当てます。

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-add-role.png)

    m. **[Save]** をクリックします。

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

このセクションでは、Britta Simon に NetSuite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[NetSuite]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**NetSuite**」と入力して選択します。

    ![アプリケーションの一覧の NetSuite のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-netsuite-test-user"></a>NetSuite のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを NetSuite に作成します。 NetSuite では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 NetSuite にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [NetSuite] タイルをクリックすると、SSO を設定した NetSuite に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [[ユーザー プロビジョニングの構成]](NetSuite-provisioning-tutorial.md)

