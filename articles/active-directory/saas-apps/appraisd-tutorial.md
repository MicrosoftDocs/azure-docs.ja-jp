---
title: チュートリアル:Azure Active Directory と Appraisd の統合 | Microsoft Docs
description: Azure Active Directory と Appraisd の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1beb825eda7e4d6a59810aada7063863b48d8ec
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59260903"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>チュートリアル:Azure Active Directory と Appraisd の統合

このチュートリアルでは、Appraisd と Azure Active Directory (Azure AD) を統合する方法について説明します。
Appraisd と Azure AD の統合には、次の利点があります。

* Appraisd にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Appraisd に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Appraisd と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Appraisd でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Appraisd では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-appraisd-from-the-gallery"></a>ギャラリーから Appraisd を追加する

Azure AD への Appraisd の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Appraisd を追加する必要があります。

**ギャラリーから Appraisd を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Appraisd**」と入力し、結果ウィンドウで **[Appraisd]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Appraisd](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Appraisd で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Appraisd 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Appraisd で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Appraisd のシングル サインオンの構成](#configure-appraisd-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Appraisd のテスト ユーザーの作成](#create-appraisd-test-user)** - Appraisd で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Appraisd で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **[Appraisd]** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Appraisd のドメインと URL] のシングル サインオン情報](common/both-preintegrated-advanced-urls.png)

    a. **[追加の URL を設定します]** をクリックします。

    b. **[リレー状態]** テキスト ボックスに、URL `<TENANTCODE>` を入力します

    c. **[SP]** 開始モードでアプリケーションを構成する場合は、**[サインオン URL]** テキストボックスに `https://app.appraisd.com/saml/<TENANTCODE>` のパターンで URL を入力します。

    > [!NOTE]
    > このチュートリアルで後述する Appraisd SSO の [Configuration]\(構成\) ページで、実際の [Sign-on URL]\(サインオン URL\) と [Relay State]\(リレー状態\) の値を取得します。

5. Appraisd アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、**[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name |  ソース属性|
    | ---------------| --------------- |
    | nameidentifier | User.mail |
    | | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Set up Appraisd]\(Appraisd の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-appraisd-single-sign-on"></a>Appraisd のシングル サインオンを構成する

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Appraisd にサインインします。

2. ページの右上にある **[Settings]\(設定\)** アイコンをクリックし、**[Configuration]\(構成\)** に移動します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

3. メニューの左側から **[SAML single sign-on]\(SAML シングル サインオン\)** をクリックします。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

4. **[SAML 2.0 Single Sign-On configuration]\(SAML 2.0 のシングル サインオンの構成\)** ページで、次の手順を実行します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. **[Default Relay State]\(既定のリレー状態\)** 値をコピーし、Azure portal の **[基本的な SAML 構成]** の **[リレー状態]** ボックスに貼り付けます。

    b. **[Service-initiated login URL]\(サービス開始ログイン URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** の **[サインオン URL]** ボックスに貼り付けます。

5. 同じページの **[Identifying users]\(ユーザーの識別\)** まで下にスクロールし、次の手順を実行します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. **[Identity Provider Single Sign-On URL]\(ID プロバイダー シングル サインオン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付け、**[Save]\(保存\)** をクリックします。

    b. **[Identity Provider Issuer URL]\(ID プロバイダーの発行者 URL\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付け、**[Save]\(保存\)** をクリックします。

    c. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付け、**[Save]\(保存\)** をクリックします。

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

このセクションでは、Britta Simon に Appraisd へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Appraisd]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Appraisd]** を選択します。

    ![アプリケーションの一覧の Appraisd のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** をクリックします。

### <a name="create-appraisd-test-user"></a>Appraisd テスト ユーザーを作成する

Azure AD ユーザーが Appraisd にサインインできるようにするには、ユーザーを Appraisd にプロビジョニングする必要があります。 Appraisd では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Appraisd にサインインします。

2. ページの右上にある **[Settings]\(設定\)** アイコンをクリックし、**[Administration centre]\(管理センター\)** に移動します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. ページの上部にあるツール バーで、**[People]\(人\)** をクリックし、**[Add a new user]\(新しいユーザーの追加\)** に移動します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. **[Add a new user]\(新しいユーザーの追加\)** ページで、次の手順を実行します。

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. **[First name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    b. **[Last name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (**Brittasimon\@contoso.com** など) を入力します。

    d. **[ユーザーの追加]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Appraisd] タイルをクリックすると、SSO を設定した Appraisd に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
