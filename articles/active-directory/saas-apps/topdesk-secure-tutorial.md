---
title: チュートリアル:Azure Active Directory と TOPdesk - Secure の統合 | Microsoft Docs
description: Azure Active Directory と TOPdesk - Secure の間のシングル サインオンの設定方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eded8eb446d36a321acf46231eee3e764ba41504
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088442"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>チュートリアル:Azure Active Directory と TOPdesk - Secure の統合

このチュートリアルでは、TOPdesk - Secure と Azure Active Directory (Azure AD) を統合する方法について説明します。
TOPdesk - Secure と Azure AD を統合すると、次の利点があります。

* TOPdesk - Secure にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで TOPdesk - Secure に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

TOPdesk- Secure と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* TOPdesk - Secure でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TOPdesk - Secure では、**SP** によって開始される SSO がサポートされます

## <a name="adding-topdesk---secure-from-the-gallery"></a>ギャラリーから TOPdesk - Secure を追加する

Azure AD への TOPdesk - Secure の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから TOPdesk - Secure を追加する必要があります。

**ギャラリーから TOPdesk - Secure を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**TOPdesk - Secure**」と入力して、結果パネルで **[TOPdesk - Secure]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の TOPdesk - Secure](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、TOPdesk - Secure で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと TOPdesk - Secure 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

TOPdesk - Secure で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[TOPdesk - Secure のシングル サインオンの構成](#configure-topdesk---secure-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[TOPdesk - Secure のテスト ユーザーの作成](#create-topdesk---secure-test-user)** - TOPdesk - Secure で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

TOPdesk - Secure で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **TOPdesk - Secure** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[TOPdesk - Secure のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.topdesk.net` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.topdesk.net/tas/secure/login/verify` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<companyname>.topdesk.net/tas/public/login/saml` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[TOPdesk - Secure クライアント サポート チーム](https://www.topdesk.com/us/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[TOPdesk - Secure のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-topdesk---secure-single-sign-on"></a>TOPdesk - Secure のシングル サインオンの構成

1. **TOPdesk - Secure** 企業サイトに管理者としてサインオンします。

2. **[TOPdesk]** メニューで **[Settings]** をクリックします。

    ![設定](./media/topdesk-secure-tutorial/ic790598.png "Settings")

3. **[Login Settings]** をクリックします。

    ![Login Settings](./media/topdesk-secure-tutorial/ic790599.png "Login Settings")

4. **[Login Settings]** メニューを展開し、 **[General]** をクリックします。

    ![General](./media/topdesk-secure-tutorial/ic790600.png "General")

5. **[SAML login]** 構成セクションの **[Secure]** セクションで、次の手順に従います。

    ![Technical Settings](./media/topdesk-secure-tutorial/ic790855.png "Technical Settings")

    a. **[Download]** をクリックしてパブリック メタデータ ファイルをダウンロードし、コンピューターにローカルに保存します。

    b. メタデータ ファイルを開いて、 **AssertionConsumerService** ノードを探します。

    ![Assertion Consumer Service](./media/topdesk-secure-tutorial/ic790856.png "Assertion Consumer Service")

    c. **[AssertionConsumerService]** の値をコピーし、 **[TOPdesk - Secure のドメインと URL]** セクションの [応答 URL] ボックスにこの値を貼り付けます。

6. 証明書ファイルを作成するには、次の手順を実行します。

    ![Certificate](./media/topdesk-secure-tutorial/ic790606.png "Certificate")

    a. Azure Portal からダウンロードしたメタデータ ファイルを開きます。

    b. **fed:ApplicationServiceType** の **xsi:type** を持つ **RoleDescriptor** ノードを展開します。

    c. **X509Certificate** ノードの値をコピーします。

    d. コピーした **X509Certificate** の値をコンピューター上のファイル内にローカルに保存します。

7. **[Public]** セクションで、 **[Add]** をクリックします。

    ![Add](./media/topdesk-secure-tutorial/ic790607.png "Add")

8. **[SAML configuration assistant]** ダイアログ ページで、次の手順を実行します。

    ![SAML Configuration Assistant](./media/topdesk-secure-tutorial/ic790608.png "SAML Configuration Assistant")

    a. Azure Portal からダウンロードしたメタデータ ファイルをアップロードするには、 **[Federation Metadata]** で **[Browse]** をクリックします。

    b. 証明書ファイルをアップロードするには、 **[Certificate (RSA)]** で **[Browse]** をクリックします。

    c. **[Private key(RSA, PKCS8, DER)]** では、独自の秘密キーをアップロードするか、[TOPdesk - Secure Client サポート チーム](https://www.topdesk.com/us/support)に問い合わせて秘密キーを取得できます。

    d. TOPdesk サポート チームから入手したロゴのファイルをアップロードするには、 **[Logo icon]** の下の **[Browse]** をクリックします。

    e. **[User name attribute]** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    f. **[Display name]** テキスト ボックスに、構成の名前を入力します。

    g. **[Save]** をクリックします。

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

このセクションでは、Britta Simon に TOPdesk - Public へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[TOPdesk - Secure]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**TOPdesk - Secure**」と入力して選択します。

    ![アプリケーションの一覧の TOPdesk - Public のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-topdesk---secure-test-user"></a>TOPdesk - Secure のテスト ユーザーの作成

Azure AD ユーザーが TOPdesk - Secure にログインできるようにするには、そのユーザーを TOPdesk - Secure にプロビジョニングする必要があります。  
TOPdesk - Secure の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **TOPdesk - Secure** 企業サイトに管理者としてサインオンします。

2. 上部のメニューで、 **[TOPdesk]\>[New]\>[Support Files]\>[Operator]** の順にクリックします。

    ![Operator](./media/topdesk-secure-tutorial/ic790610.png "Operator")

3. **[New Operator]** ダイアログで、次の手順を実行します。

    ![New Operator](./media/topdesk-secure-tutorial/ic790611.png "New Operator")

    a. **[General]** タブをクリックします。

    b. **[Surname]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Location]** セクションでアカウントの **[Site]** を選択します。

    d. **[TOPdesk Login]** セクションの **[Login Name]** テキストボックスに、ユーザーのログイン名を入力します。

    e. **[Save]** をクリックします。

> [!NOTE]
> 他の TOPdesk - Secure ユーザー アカウントの作成ツールまたは TOPdesk - Secure から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [TOPdesk - Secure] タイルをクリックすると、SSO を設定した TOPdesk - Secure に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

