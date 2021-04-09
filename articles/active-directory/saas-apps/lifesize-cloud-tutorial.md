---
title: 'チュートリアル: Azure Active Directory と Lifesize Cloud の統合 | Microsoft Docs'
description: Azure Active Directory と Lifesize Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 483377d0376a5a98d822392af1fa2d84ad8498e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458593"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>チュートリアル: Azure Active Directory と Lifesize Cloud の統合

このチュートリアルでは、Lifesize Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。
Lifesize Cloud と Azure AD の統合には、次の利点があります。

* Lifesize Cloud にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Lifesize Cloud に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Lifesize Cloud と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Lifesize Cloud でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Lifesize Cloud では、**SP** によって開始される SSO がサポートされます

* Lifesize Cloud では、**自動化された** ユーザー プロビジョニングがサポートされます

## <a name="adding-lifesize-cloud-from-the-gallery"></a>ギャラリーからの Lifesize Cloud の追加

Azure AD への Lifesize Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lifesize Cloud を追加する必要があります。

**ギャラリーから Lifesize Cloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Lifesize Cloud**」と入力し、結果パネルで **[Lifesize Cloud]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リスト内の Lifesize Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Lifesize Cloud で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Lifesize Cloud の関連ユーザー間にリンク関係が確立されている必要があります。

Lifesize Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Lifesize Cloud のシングル サインオンの構成](#configure-lifesize-cloud-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Lifesize Cloud のテスト ユーザーの作成](#create-lifesize-cloud-test-user)** - Lifesize Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Lifesize Cloud との Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Lifesize Cloud** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Lifesize Cloud のドメインと URL] のシングル サインオン情報](common/sp-identifier-relay.png)

    a. **[サインオン URL]** ボックスに、`https://login.lifesizecloud.com/ls/?acs` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://login.lifesizecloud.com/<companyname>` の形式で URL を入力します。

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** ボックスに、`https://webapp.lifesizecloud.com/?ent=<identifier>` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、リレー状態でこれらの値を更新します。 サインオン URL と識別子の値は、[Lifesize Cloud クライアント サポート チーム](https://www.lifesize.com/en/support)に問い合わせます。リレー状態の値は、このチュートリアルで後述する SSO 構成から取得できます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Lifesize Cloud のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-lifesize-cloud-single-sign-on"></a>Lifesize Cloud のシングル サインオンを構成する

1. アプリケーション用に構成された SSO を取得するには、管理者権限で Lifesize Cloud アプリケーションにログインします。

2. ページの右上にある自分の名前をクリックし、 **[詳細設定]** をクリックします。

    ![[Advanced Settings]\(詳細設定\) メニュー項目を示すスクリーンショット。](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. [詳細設定] で **[SSO 構成]** のリンクをクリックします。 インスタンスの [SSO 構成] ページが開きます。

    ![[S S O Configuration]\(S S O 構成\) を選択できる [Advanced Settings]\(詳細設定\) を示すスクリーンショット。](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. SSO 構成 UI で、次の値を構成します。

    ![説明されている値を入力できる [S S O Configuration]\(S S O 構成\) ページを示すスクリーンショット。](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. **[Identity Provider Issuer]\(ID プロバイダー発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    b.  **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X.509 証明書]** ボックスに貼り付けます。
  
    d. [名] ボックスの SAML 属性マッピングに、値を「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    e. **[姓]** ボックスの SAML 属性マッピングに、値を「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。

    f. **[電子メール]** ボックスの SAML 属性マッピングに、値を「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

5. 構成を確認するには、 **[テスト]** ボタンをクリックします。

    >[!NOTE]
    >テストを成功させるには、Azure AD の構成ウィザードを完了し、テストを実行するユーザーやグループにもアクセスを提供する必要があります。

6. **[SSO を有効にする]** ボタンをクリックして、SSO を有効にします。

7. **[更新]** ボタンをクリックして、すべての設定を保存します。 これにより、RelayState 値が生成されます。 テキスト ボックスに生成された RelayState の値をコピーし、 **[Lifesize Cloud のドメインと URL]** セクションの **[リレー状態]** ボックスに貼り付けます。

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

このセクションでは、Britta Simon に Lifesize Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Lifesize Cloud]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Lifesize Cloud]** を選択します。

    ![アプリケーションの一覧の Lifesize Cloud リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-lifesize-cloud-test-user"></a>Lifesize Cloud のテスト ユーザーの作成

このセクションでは、Lifesize Cloud で Britta Simon というユーザーを作成します。 Lifesize Cloud では、ユーザーの自動プロビジョニングがサポートされています。 Azure AD での認証に成功すると、ユーザーはアプリケーションで自動的にプロビジョニングされます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Lifesize Cloud のタイルをクリックすると、Lifesize Cloud アプリケーションのログイン ページが表示されます。 そこでユーザー名を入力する必要があります。その後、アプリケーションのホーム ページにリダイレクトされます。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)