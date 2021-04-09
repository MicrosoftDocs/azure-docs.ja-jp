---
title: 'チュートリアル: Azure Active Directory と Central Desktop の統合 | Microsoft Docs'
description: Azure Active Directory と Central Desktop の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 36ba61c86082e191831c2c890de4466181f1a4db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674208"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>チュートリアル: Azure Active Directory と Central Desktop の統合

このチュートリアルでは、Central Desktop と Azure Active Directory (Azure AD) を統合する方法について説明します。
Central Desktop と Azure AD の統合には、次の利点があります。

* Central Desktop にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで Central Desktop に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Central Desktop の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Central Desktop でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Central Desktop では、**SP** Initiated SSO がサポートされます

## <a name="adding-central-desktop-from-the-gallery"></a>ギャラリーからの Central Desktop の追加

Azure AD への Central Desktop の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Central Desktop を追加する必要があります。

**ギャラリーから Central Desktop を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Central Desktop**」と入力し、結果パネルで **Central Desktop** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧での Central Desktop](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Central Desktop で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Central Desktop 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Central Desktop で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Central Desktop のシングル サインオンの構成](#configure-central-desktop-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Central Desktop のテスト ユーザーの作成](#create-central-desktop-test-user)** - Central Desktop で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Central Desktop で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Central Desktop** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Central Desktop のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.centraldesktop.com` という形式で URL を入力します。

    b. **[識別子]** ボックスに、次のパターンを使用して URL を入力します。

    ```http
    https://<companyname>.centraldesktop.com/saml2-metadata.php
    https://<companyname>.imeetcentral.com/saml2-metadata.php
    ```

    c. **[応答 URL]** ボックスに、`https://<companyname>.centraldesktop.com/saml2-assertion.php` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Central Desktop クライアント サポート チーム](https://imeetcentral.com/contact-us)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (未加工)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[Central Desktop のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-central-desktop-single-sign-on"></a>Central Desktop のシングル サインオンの構成

1. **Central Desktop** テナントにサインインします。

2. **[設定]** に移動します。 **[Advanced]\(詳細\)** を選び、**[Single Sign On]\(シングル サインオン\)** を選びます。

    ![設定 - 詳細設定](./media/central-desktop-tutorial/ic769563.png "設定 - 詳細設定")

3. **[Single Sign On Settings]\(シングル サインオンの設定\)** ページで、次の手順を実行します。

    ![シングル サインオンの設定](./media/central-desktop-tutorial/ic769564.png "[Single Sign On Settings]")

    a. **[Enable SAML v2 Single Sign On]** を選択します。

    b. **[SSO URL]** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    c. **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    d. **[SSO Logout URL]\(SSO ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。

4. **[Message Signature Verification Method]\(メッセージ署名検証方法\)** セクションで、次の手順を実行します。

    ![[Message Signature Verification Method]\(メッセージ署名検証方法\)](./media/central-desktop-tutorial/ic769565.png "[Message Signature Verification Method]")

    a. **[Certificate]** を選択します。

    b. **[SSO Certificate]\(SSO 証明書\)** ボックスの一覧で、**[RSH SHA256]** を選びます。

    c. ダウンロードした証明書をメモ帳で開きます。 証明書の内容をコピーして、**[SSO Certificate]\(SSO 証明書\)** フィールドに貼り付けます。

    d. **[Display a link to your SAMLv2 login page]** を選択します。

    e. **[Update]\(更新\)** を選択します。

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

このセクションでは、Central Desktop へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Central Desktop]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Central Desktop]** を選択します。

    ![アプリケーションの一覧の Central Desktop のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-central-desktop-test-user"></a>Central Desktop のテスト ユーザーの作成

Azure AD ユーザーがサインインできるようにするには、ユーザーを Central Desktop アプリケーションにプロビジョニングする必要があります。 このセクションでは、Central Desktop で Azure AD ユーザー アカウントを作成する方法について説明します。

> [!NOTE]
> Azure AD ユーザー アカウントをプロビジョニングするには、Central Desktop から提供されている他の Central Desktop ユーザー アカウント作成ツールまたは API を使います。

**Central Desktop にユーザー アカウントをプロビジョニングするには:**

1. Central Desktop テナントにサインインします。

2. **[People]\(ユーザー\)** を選択し、**[Add Internal Members]\(内部メンバーの追加\)** を選択します。

    ![ユーザー](./media/central-desktop-tutorial/ic781051.png "ユーザー")

3. **[Email Address of New Members]\(新しいメンバーの電子メール アドレス\)** ボックスにプロビジョニングする Azure AD アカウントを入力し、**[Next]\(次へ\)** を選びます。

    ![新しいメンバーの電子メール アドレス](./media/central-desktop-tutorial/ic781052.png "新しいメンバーの電子メール アドレス")

4. **[Add Internal member(s)]\(内部メンバーの追加\)** を選びます。

    ![内部メンバーの追加](./media/central-desktop-tutorial/ic781053.png "内部メンバーの追加")

   > [!NOTE]
   > 追加したユーザーが、アカウント アクティブ化のための確認リンクを含むメールを受け取ります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Central Desktop] タイルをクリックすると、SSO を設定した Central Desktop に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)