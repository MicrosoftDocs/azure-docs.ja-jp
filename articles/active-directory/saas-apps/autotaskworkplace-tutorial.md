---
title: チュートリアル:Azure Active Directory と Autotask Workplace の統合 | Microsoft Docs
description: Azure Active Directory と Autotask Workplace の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7be7d8b9fc9aa8b6c9c1d888744c25d710ca79a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106496"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>チュートリアル:Azure Active Directory と Autotask Workplace の統合

このチュートリアルでは、Autotask Workplace と Azure Active Directory (Azure AD) を統合する方法について説明します。
Autotask Workplace と Azure AD の統合には、次の利点があります。

* Autotask Workplace にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで自動的に Autotask Workplace にサインオン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Autotask Workplace の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Autotask Workplace でのシングル サインオンが有効なサブスクリプション
* Autotask Workplace でのシングル サインオンが有効なサブスクリプション
* Workplace の管理者またはスーパー管理者である必要があります。
* Azure AD に Administrator アカウントを持っている必要があります。
* この機能を利用するユーザーは、Workplace と Azure AD にアカウントを持ち、それぞれのメール アドレスが一致している必要があります。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Autotask Workplace では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-autotask-workplace-from-the-gallery"></a>ギャラリーからの Autotask Workplace の追加

Azure AD への Autotask Workplace の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Autotask Workplace を追加する必要があります。

**ギャラリーから Autotask Workplace を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Autotask Workplace**」と入力し、結果ウィンドウで **[Autotask Workplace]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果一覧の Autotask Workplace](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Autotask Workplace で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Autotask Workplace 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Autotask Workplace で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Autotask Workplace シングル サインオンの構成](#configure-autotask-workplace-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Autotask Workplace のテスト ユーザーの作成](#create-autotask-workplace-test-user)** - Autotask Workplace で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Autotask Workplace で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Autotask Workplace** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Autotask Workplace のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Autotask Workplace のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.awp.autotask.net/loginsso` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Autotask Workplace クライアント サポート チーム](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Autotask Workplace のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-autotask-workplace-single-sign-on"></a>Autotask Workplace のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、管理者の資格情報を使って Workplace Online にログインします。

    > [!Note]
    > IdP を構成するときは、サブドメインを指定する必要があります。 正しいサブドメインを確認するには、Workplace Online にログインします。 ログインした後、URL のサブドメインを書き留めます。 サブドメインは "https://" と ".awp.autotask.net/" の間の部分であり、us、eu、ca、au のいずれかでなければなりません。

2. **[Configuration]\(構成\)**  >  **[Single Sign-On]\(シングル サインオン\)** に移動し、以下の手順を実行します。

    ![Autotask のシングル サインオンの構成](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. **[XML Metadata File]\(XML メタデータ ファイル\)** オプションを選び、Azure portal からダウンロードした**フェデレーション メタデータ XML** をアップロードします。

    b. **[ENABLE SSO]\(SSO を有効にする\)** をクリックします。

    ![Autotask シングル サインオン承認の構成](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. **[I confirm this information is correct and I trust this IdP]\(この情報が正しいことを確認し、この IdP を信頼します\)** チェック ボックスをオンにします。

    d. **[APPROVE]\(承認\)** をクリックします。

> [!Note]
> Autotask Workplace の構成でサポートを必要とする場合は、[こちらのページ](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)を参照して、Workplace アカウントでサポートを受けてください。

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

このセクションでは、Autotask Workplace へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Autotask Workplace]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Autotask Workplace]** を選択します。

    ![アプリケーション一覧での Autotask Workplace のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-autotask-workplace-test-user"></a>Autotask Workplace のテスト ユーザーの作成

このセクションでは、Autotask Workplace に Britta Simon というユーザーを作成します。 [Autotask Workplace サポート チーム](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)と連携し、Autotask Workplace プラットフォームにユーザーを追加してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Autotask Workplace] タイルをクリックすると、SSO を設定した Autotask Workplace に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
