---
title: 'チュートリアル: Azure Active Directory と ClickTime の統合 | Microsoft Docs'
description: Azure Active Directory と ClickTime の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: 3337d66934c1fe317296cffaa9a663e212cce12d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673175"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>チュートリアル: Azure Active Directory と ClickTime の統合

このチュートリアルでは、ClickTime と Azure Active Directory (Azure AD) を統合する方法について説明します。
ClickTime と Azure AD の統合には、次の利点があります。

* ClickTime にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して ClickTime に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ClickTime と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* ClickTime でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ClickTime では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-clicktime-from-the-gallery"></a>ギャラリーからの ClickTime の追加

Azure AD への ClickTime の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ClickTime を追加する必要があります。

**ギャラリーから ClickTime を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**ClickTime**」と入力し、結果パネルで **ClickTime** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの ClickTime](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、ClickTime で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと ClickTime 内の関連ユーザー間にリンク関係が確立されている必要があります。

ClickTime で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[ClickTime シングル サインオンの構成](#configure-clicktime-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[ClickTime のテスト ユーザーの作成](#create-clicktime-test-user)** - ClickTime で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

ClickTime で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **ClickTime** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[ClickTime のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** テキスト ボックスに、`https://app.clicktime.com/sp/` という URL を入力します。

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    ```http
    https://app.clicktime.com/Login/
    https://app.clicktime.com/App/Login/Consume.aspx
    ```

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[ClickTime のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-clicktime-single-sign-on"></a>ClickTime シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、ClickTime 企業サイトに管理者としてログインします。

1. 上部にあるツールバーで **[Preferences]** 、 **[Security Settings]** の順にクリックします。

1. **[Single Sign-On Preferences]** 構成セクションで、次の手順を実行します。

    ![Security Settings](./media/clicktime-tutorial/tic777280.png "セキュリティ設定")

    a.  **[Azure AD]** を指定し、シングル サインオン (SSO) を使用したサインインの **[Allow (許可)]** を選択します。

    b. **[Identity Provider Endpoint]\(ID プロバイダー エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。

    c.  Azure Portal からダウンロードされた **Base-64 でエンコードされた証明書** を **メモ帳** で開き、その内容をコピーしてから、それを **[X.509 証明書]** ボックスに貼り付けます。

    d.  **[保存]** をクリックします。

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

このセクションでは、Britta Simon に ClickTime へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[ClickTime]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[ClickTime]** を選択します。

    ![アプリケーションの一覧の ClickTime のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-clicktime-test-user"></a>ClickTime のテスト ユーザーの作成

Azure AD ユーザーが ClickTime にログインできるようにするには、そのユーザーを ClickTime にプロビジョニングする必要があります。  
ClickTime の場合、プロビジョニングは手動で行います。

> [!NOTE]
> ClickTime から提供されている他の ClickTime ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ClickTime** テナントにログインします。

1. 上部のツールバーで、 **[Company]** をクリックし、 **[People]** をクリックします。

    ![スクリーンショットは、[Company]\(会社\) と [People]\(ユーザー\) が選択されている ClickTime テナントを示しています。](./media/clicktime-tutorial/tic777282.png "ユーザー")

1. **[Add Person]** をクリックします。

    ![Add Person](./media/clicktime-tutorial/tic777283.png "[Add Person]")

1. [New Person] セクションで、次の手順を実行します。

    ![スクリーンショットは、この手順の情報を追加できる [Add Person]\(ユーザーの追加\) セクションを示しています。](./media/clicktime-tutorial/tic777284.png "ユーザー")

    a.  **[フル ネーム]** ボックスに、ユーザーの氏名 (**Britta Simon** など) を入力します。 

    b.  **[電子メール アドレス]** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    > [!NOTE]
    > 必要に応じて、新しいユーザー オブジェクトの追加プロパティを設定できます。

    c.  **[保存]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ClickTime] タイルをクリックすると、SSO を設定した ClickTime に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)