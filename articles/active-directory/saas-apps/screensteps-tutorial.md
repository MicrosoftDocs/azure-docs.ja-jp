---
title: チュートリアル:Azure Active Directory と ScreenSteps の統合 | Microsoft Docs
description: Azure Active Directory と ScreenSteps の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 3816239798290318404980ded388b726d8134395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999792"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>チュートリアル:Azure Active Directory と ScreenSteps の統合

このチュートリアルでは、ScreenSteps と Azure Active Directory (Azure AD) を統合する方法について説明します。
ScreenSteps と Azure AD の統合には、次の利点があります。

* ScreenSteps にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで ScreenSteps に自動的にサインイン (シングル サインオン) されるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ScreenSteps と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* ScreenSteps でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ScreenSteps では、**SP** によって開始される SSO がサポートされます

## <a name="adding-screensteps-from-the-gallery"></a>ギャラリーからの ScreenSteps の追加

Azure AD への ScreenSteps の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから ScreenSteps を追加する必要があります。

**ギャラリーから ScreenSteps を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**ScreenSteps**」と入力し、結果ウィンドウで **[ScreenSteps]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの ScreenSteps](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、ScreenSteps で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと ScreenSteps 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

ScreenSteps で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[ScreenSteps のシングル サインオンの構成](#configure-screensteps-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[ScreenSteps のテスト ユーザーの作成](#create-screensteps-test-user)** - ScreenSteps で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

ScreenSteps で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **ScreenSteps** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[ScreenSteps のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://<tenantname>.ScreenSteps.com` という形式で URL を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 実際のサインオン URL に値を置き換えます。実際の値については後で説明します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[ScreenSteps のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-screensteps-single-sign-on"></a>ScreenSteps のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、ScreenSteps 企業サイトに管理者としてログインします。

1. **[Account Settings]\(アカウント設定\)** をクリックします。

    ![アカウント管理](./media/screensteps-tutorial/ic778523.png "アカウント管理")

1. **[Single Sign-on]** をクリックします。

    ![選択されている [Single Sign-on]\(シングル サインオン\) を示すスクリーンショット。](./media/screensteps-tutorial/ic778524.png "[Remote Authentication]")

1. **[シングル サインオン エンドポイントの作成]** をクリックします。

    ![リモート認証](./media/screensteps-tutorial/ic778525.png "[Remote Authentication]")

1. **[シングル サインオン エンドポイントの作成]** セクションで、次の手順に従います。

    ![認証エンドポイントの作成](./media/screensteps-tutorial/ic778526.png "[Create an Authentication Endpoint]")

    a. **[Title]** テキスト ボックスに、タイトルを入力します。

    b. **[Mode]** 一覧から **[SAML]** を選択します。

    c. **Create** をクリックしてください。

1. 新しいエンドポイントを **編集** します。

    ![エンドポイントの編集](./media/screensteps-tutorial/ic778528.png "エンドポイントを編集する")

1. **[シングル サインオン エンドポイントの編集]** セクションで、次の手順に従います。

    ![リモート認証エンドポイント](./media/screensteps-tutorial/ic778527.png "[Remote Authentication Endpoint]")

    a. **[Upload new SAML Certificate file]\(新しい SAML 証明書のアップロード\)** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。

    b. **[Remote Login URL]\(リモート ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** 値を貼り付けます。

    c. Azure portal からコピーした **ログアウト URL** を **[Log out URL]\(ログアウト URL\)** ボックスに貼り付けます。

    d. **[グループ]** を選択し、プロビジョニングされたときにユーザーを割り当てます。

    e. **[Update]** をクリックします。

    f. **SAML コンシューマー URL** をクリップボードにコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    g. **[シングル サインオン エンドポイントの編集]** に戻ります。

    h. ScreenSteps にログインするすべてのユーザーについて、このエンドポイントを使用するには、 **[アカウントの既定値にする]** ボタンをクリックします。 または、 **[サイトに追加]** ボタンをクリックして、**ScreenSteps** 内の特定のサイトにこのエンドポイントを使用することもできます。

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

このセクションでは、Britta Simon に ScreenSteps へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[ScreenSteps]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[ScreenSteps]** を選択します。

    ![アプリケーションの一覧の ScreenSteps のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-screensteps-test-user"></a>ScreenSteps のテスト ユーザーの作成

このセクションでは、ScreenSteps で Britta Simon というユーザーを作成します。 [ScreenSteps クライアント サポート チーム](https://www.screensteps.com/contact)と協力して、ScreenSteps プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [ScreenSteps] タイルをクリックすると、SSO を設定した ScreenSteps に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)