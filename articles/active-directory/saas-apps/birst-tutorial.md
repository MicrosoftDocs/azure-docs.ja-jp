---
title: 'チュートリアル: Azure Active Directory と Birst Agile Business Analytics の統合 | Microsoft Docs'
description: Azure Active Directory と Birst Agile Business Analytics の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.openlocfilehash: 124485819bf7fab02e2d62bec46ad50468589773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674346"
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>チュートリアル: Azure Active Directory と Birst Agile Business Analytics の統合

このチュートリアルでは、Birst Agile Business Analytics と Azure Active Directory (Azure AD) を統合する方法について説明します。
Birst Agile Business Analytics と Azure AD の統合には、次の利点があります。

* Birst Agile Business Analytics にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Birst Agile Business Analytics に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Birst Agile Business Analytics の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Birst Agile Business Analytics でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Birst Agile Business Analytics では、**SP** Initiated SSO がサポートされます

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>ギャラリーから Birst Agile Business Analytics を追加する

Azure AD への Birst Agile Business Analytics の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Birst Agile Business Analytics を追加する必要があります。

**ギャラリーから Birst Agile Business Analytics を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Birst Agile Business Analytics**」と入力し、結果パネルで **[Birst Agile Business Analytics]** を選び、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の Birst Agile Business Analytics](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** という名前のテスト ユーザーに基づいて、Birst Agile Business Analytics で Azure AD のシングル サインオンを構成およびテストします。
シングル サインオンが機能するためには、Azure AD ユーザーと Birst Agile Business Analytics の関連ユーザーの間で、リンク関係が確立されている必要があります。

Birst Agile Business Analytics で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Birst Agile Business Analytics シングル サインオンの構成](#configure-birst-agile-business-analytics-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Birst Agile Business Analytics のテスト ユーザーの作成](#create-birst-agile-business-analytics-test-user)** - Azure AD でのユーザーにリンクされた、Birst Agile Business Analytics での Britta Simon の対応するユーザーを作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Birst Agile Business Analytics で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **[Birst Agile Business Analytics]** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Birst Agile Business Analytics のドメインと URL] のシングル サインオン情報](common/sp-intiated.png)

    **[サインオン URL]** ボックスに、`https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` のパターンを使用して URL を入力します。

    この URL は、Birst アカウントが存在するデータセンターによって異なります。

   * 米国のデータセンターでは、`https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` というパターンを使用します。

   * ヨーロッパのデータセンターでは、`https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` というパターンを使用します。

     > [!NOTE]
     > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Birst Agile Business Analytics クライアント サポート チーム](mailto:info@birst.com)に問い合わせてください。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Birst Agile Business Analytics のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-birst-agile-business-analytics-single-sign-on"></a>Birst Agile Business Analytics シングル サインオンの構成

**Birst Agile Business Analytics** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を [Birst Agile Business Analytics サポート チーム](mailto:info@birst.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!NOTE]
> Birst チームが適切なサーバー (**app2101** など) 上で SSO を設定できるように、この統合には SHA256 アルゴリズムが必要なことをチームに伝えてください (SHA1 はサポートされません)。

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

このセクションでは、Birst Agile Business Analytics へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Birst Agile Business Analytics]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Birst Agile Business Analytics]** を選択します。

    ![アプリケーションの一覧の Birst Agile Business Analytics のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-birst-agile-business-analytics-test-user"></a>Birst Agile Business Analytics のテスト ユーザーの作成

このセクションでは、Birst Agile Business Analytics で Britta Simon というユーザーを作成します。 [Birst Agile Business Analytics サポート チーム](mailto:info@birst.com)と連携して、Birst Agile Business Analytics プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Birst Agile Business Analytics] タイルをクリックすると、SSO を設定した Birst Agile Business Analytics に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)