---
title: チュートリアル:Azure Active Directory と Adobe Creative Cloud の統合 | Microsoft Docs
description: Azure Active Directory と Adobe Creative Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35bd52904ab081e41cb43a346288234c18a7f43b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904050"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>チュートリアル:Azure Active Directory と Adobe Creative Cloud の統合

このチュートリアルでは、Adobe Creative Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。
Adobe Creative Cloud と Azure AD の統合には、次の利点があります。

* Adobe Creative Cloud にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Adobe Creative Cloud にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Adobe Creative Cloud と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Adobe Creative Cloud でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Adobe Creative Cloud では、**SP** Initiated SSO がサポートされます

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>ギャラリーから Adobe Creative Cloud を追加する

Azure AD への Adobe Creative Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Creative Cloud を追加する必要があります。

**ギャラリーから Adobe Creative Cloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Adobe Creative Cloud**」と入力し、結果パネルで **Adobe Creative Cloud** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Adobe Creative Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Adobe Creative Cloud で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Adobe Creative Cloud 内の関連ユーザー間にリンク関係が確立されている必要があります。

Adobe Creative Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Adobe Creative Cloud シングル サインオンの構成](#configure-adobe-creative-cloud-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Adobe Creative Cloud のテスト ユーザーの作成](#create-adobe-creative-cloud-test-user)** - Adobe Creative Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Adobe Creative Cloud で Azure AD のシングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Adobe Creative Cloud** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Adobe Creative Cloud のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、値として「`https://adobe.com`」を入力します。

    b. **[識別子]** ボックスに、`https://www.okta.com/saml2/service-provider/<token>` の形式で URL を入力します。

    > [!NOTE]
    > この識別子の値は実際のものではありません。 実際の識別子でこの値を更新します。 この値を取得するには、[Adobe Creative Cloud クライアント サポート チーム](https://www.adobe.com/au/creativecloud/business/teams/plans.html)に問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. Adobe Creative Cloud アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name | ソース属性|
    |----- | --------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | 電子メール | User.mail

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

    > [!NOTE]
    > ユーザーは、電子メール要求の値を、SAML 応答で設定するために、 有効な Office 365 ExO ライセンス　を持つ必要があります。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Adobe Creative Cloud のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Adobe Creative Cloud シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、[Adobe Admin Console](https://adminconsole.adobe.com) に管理者としてログインします。

2. 上部のナビゲーション バーの **[Settings]\(設定\)** に移動して、**[Identity]\(ID\)** を選択します。 ドメインの一覧が開きます。 自分のドメインの **[Configure]\(構成\)** リンクをクリックします。 その後、**[Single Sign On Configuration Required (シングル サインオンの構成が必要です)]** セクションで、次の手順を実行します。 詳細については、「[Setup a domain (ドメインの設定)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)」を参照してください。

    ![設定](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Settings")

    a. **[Browse (参照)]** をクリックして、Azure AD からダウンロードした証明書を **[IDP Certificate (IDP 証明書)]** にアップロードします。

    b. **[IDP issuer (IDP 発行者)]** ボックスで、Azure ポータルの **[サインオンの構成]** セクションからコピーした **SAML エンティティ ID** の値を入力します。

    c. **[IDP Login URL (IDP ログイン URL)]** ボックスで、Azure ポータルの **[サインオンの構成]** セクションからコピーした **SAML SSO サービスの URL** の値を入力します。

    d. **[IDP Binding (IDP バインディング)]** として **[HTTP - Redirect (HTTP - リダイレクト)]** を選択します。

    e. **[User Login Setting (ユーザー ログイン設定)]** として **[Email Address (電子メール アドレス])** を選択します。

    f. **[保存]** ボタンをクリックします。

3. ダッシュボードに、XML の **"メタデータのダウンロード"** ファイルが表示されます。 これには、アドビの EntityDescriptor URL と AssertionConsumerService URL が含まれています。 ファイルを開き、Azure AD アプリケーションでこれらを構成してください。

    ![アプリ側でのシングル サインオンの構成](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. **[アプリケーション設定の構成]** ダイアログの**識別子**として、アドビによって提供された EntityDescriptor 値を使用します。

    b. **[アプリケーション設定の構成]** ダイアログの **[応答 URL]** として、アドビによって提供された AssertionConsumerService 値を使用します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adobe Creative Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Adobe Creative Cloud]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Adobe Creative Cloud]** を選択します。

    ![アプリケーションの一覧の Adobe Creative Cloud リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud のテスト ユーザーの作成

Azure AD ユーザーが Adobe Creative Cloud にログインできるようにするには、そのユーザーを Adobe Creative Cloud にプロビジョニングする必要があります。 Adobe Creative Cloud の場合、プロビジョニングは手動で実行します。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1. [Adobe Admin Console](https://adminconsole.adobe.com) サイトに管理者としてサインインします。

2. Adobe のコンソール内でフェデレーション ID としてユーザーを追加し、製品のプロファイルに割り当てます。 ユーザーの追加の詳細については、「[Add users in Adobe Admin Console (Adobe Admin Console でのユーザーの追加)](https://helpx.adobe.com/enterprise/using/users.html#Addusers)」を参照してください。 

3. ここで、Adobe サインイン フォームにメール アドレス/UPN を入力し、Tab キーを押すと、Azure AD にフェデレーションされます。
   * Web アクセス: www\.adobe.com > サインイン
   * デスクトップ アプリ ユーティリティ内 > サインイン
   * アプリケーション内 > ヘルプ > サインイン

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adobe Creative Cloud] タイルをクリックすると、SSO を設定した Adobe Creative Cloud に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [ドメインをセットアップする (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Adobe SSO で使用するために Azure を構成する (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
