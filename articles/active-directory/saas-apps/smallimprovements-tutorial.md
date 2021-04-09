---
title: チュートリアル:Azure Active Directory と Small Improvements の統合 | Microsoft Docs
description: Azure Active Directory と Small Improvements の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 6eced120a05ddaca8d8cf426fd2a977891b3e36b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997139"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>チュートリアル:Azure Active Directory と Small Improvements の統合

このチュートリアルでは、Small Improvements と Azure Active Directory (Azure AD) を統合する方法について説明します。
Small Improvements と Azure AD の統合には、次の利点があります。

* Small Improvements にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Small Improvements に自動的にサインイン (シングル サインオン) されるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Small Improvements の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Small Improvements でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Small Improvements では、**SP** によって開始される SSO がサポートされます

## <a name="adding-small-improvements-from-the-gallery"></a>ギャラリーから Small Improvements を追加する

Azure AD への Small Improvements の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Small Improvements を追加する必要があります。

**ギャラリーから Small Improvements を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Small Improvements**」と入力し、結果パネルの **[Small Improvements]** を選択し、 **[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の Small Improvements](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Small Improvements で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Small Improvements 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Small Improvements で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Small Improvements のシングル サインオンの構成](#configure-small-improvements-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Small Improvements のテスト ユーザーの作成](#create-small-improvements-test-user)** - Small Improvements で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Small Improvements で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Small Improvements** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Small Improvements のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.small-improvements.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Small Improvements クライアント サポート チーム](mailto:support@small-improvements.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Small Improvements のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-small-improvements-single-sign-on"></a>Small Improvements のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、管理者として Small Improvements 企業サイトにサインオンします。

1. メイン ダッシュボード ページで、左側の **[管理]** ボタンをクリックします。

    ![[管理] ボタンが選択された画面のスクリーンショット。](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. **[統合]** セクションで、**[SAML SSO]** ボタンをクリックします。

    ![[統合] で SAML S S O アイコンが選択された画面のスクリーンショット。](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. [SSO Setup] ページで、次の手順に従います。

    ![[S S O Setup ]\(S S O の設定\) ページを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. **[HTTP Endpoint]\(HTTP エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、 **[x509 証明書]** ボックスに貼り付けます。 

    c. ユーザーが SSO およびログイン フォーム認証オプションを使用できるようにするには、 **[Enable access via login/password too (ログイン/パスワードによるアクセスも有効にする)]** オプションをオンにします。  

    d. **[SAML プロンプト]** ボックスに、SSO ログイン ボタンの名前として適切な値を入力します。  

    e. **[保存]** をクリックします。

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

このセクションでは、Britta Simon に Small Improvements へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Small Improvements]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Small Improvements]** を選択します。

    ![アプリケーションの一覧の Small Improvements リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-small-improvements-test-user"></a>Small Improvements のテスト ユーザーの作成

Azure AD ユーザーが Small Improvements にログインできるようにするには、そのユーザーを Small Improvements にプロビジョニングする必要があります。 Small Improvements の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Small Improvements 企業サイトにサインオンします。

1. ホーム ページの左側のメニューに移動し、 **[管理]** をクリックします。

1. [ユーザー管理] セクションで、 **[ユーザー ディレクトリ]** ボタンをクリックします。

    ![[管理の概要] の [ユーザー ディレクトリ] が選択された画面のスクリーンショット。](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. **[ユーザーの追加]** をクリックします。

    ![[ユーザーの追加] ボタンのスクリーンショット。](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. **[ユーザーの追加]** ダイアログで、次の手順を実行します。 

    ![[ユーザーの追加] ダイアログ ボックスを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. 「**Britta**」のように、ユーザーの **[名]** を入力します。

    b. 「**Simon**」のように、ユーザーの **[姓]** を入力します。

    c. **brittasimon@contoso.com** のように、ユーザーの **[メール]** を入力します。

    d. **[通知電子メールを送信する]** ボックスに、個人のメッセージを入力することもできます。 通知を送信しない場合は、このチェックボックスをオフにします。

    e. **[ユーザーの作成]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Small Improvements] タイルをクリックすると、SSO を設定した Small Improvements に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)