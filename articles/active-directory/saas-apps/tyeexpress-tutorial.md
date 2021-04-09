---
title: 'チュートリアル: Azure Active Directory と T&E Express の統合 | Microsoft Docs'
description: Azure Active Directory と T&E Express の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: f44a67b4943c6897f82aedf47d493b36e5e2d3b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521888"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>チュートリアル: Azure Active Directory と T&E Express の統合

このチュートリアルでは、T&E Express と Azure Active Directory (Azure AD) を統合する方法について説明します。
T&E Express と Azure AD の統合には、次の利点があります。

* T&E Express にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで T&E Express に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

T&E Express と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* T&E Express でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* T&E Express では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-te-express-from-the-gallery"></a>ギャラリーから T&E Express を追加する

Azure AD への T&E Express の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に T&amp;amp;E Express を追加する必要があります。

**ギャラリーから T&E Express を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**T&E Express**」と入力し、結果パネルで **[T&E Express]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の T&E Express](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、T&E Express で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと T&E Express 内の関連ユーザー間にリンク関係が確立されている必要があります。

T&E Express で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[T&E Express のシングル サインオンの構成](#configure-te-express-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[T&E Express テスト ユーザーの作成](#create-te-express-test-user)** - T&E Express で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

T&E Express で Azure AD シングル サインオンを構成するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) の **T&E Express** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[T&E Express のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<domain>.tyeexpress.com` の形式で URL として値を入力します。

    b. **[応答 URL]** ボックスに、`https://<domain>.tyeexpress.com/authorize/samlConsume.aspx` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、[T&E Express クライアント サポート チーム](https://www.tyeexpress.com/contacto.aspx)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[T&E Express の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-te-express-single-sign-on"></a>T&E Express のシングル サインオンの構成

1. **T&E Express** 側でシングル サインオンを構成するには、管理者の資格情報を使用して、SAML シングル サインオンなしで T&E Express アプリケーションにログインします。

1. **[管理者]** タブで、 **[SAML domain (SAML ドメイン)]** をクリックして、SAML 設定ページを開きます。

    ![[Admin]\(管理者\) メニューから [SAML Domain]\(SAML ドメイン\) が選択されていることを示すスクリーンショット。](./media/tyeexpress-tutorial/tye-SAML.png)

1. **[Activar (有効化)]** オプションを **[No]** から **[SI (はい)]** にして選択します。 **[Identity Provider Metadata]\(ID プロバイダーのメタデータ\)** ボックスに、Azure portal からダウンロードしたメタデータ XML を貼り付けます。

    ![[Dominio SAML] ページを示すスクリーンショット。ここでメタデータを入力できます。](./media/tyeexpress-tutorial/tyeAdmin.png)

1. **[Guardar (保存)]** ボタンをクリックして、設定を保存します。

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

このセクションでは、Britta Simon に T&E Express へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[T&E Express]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[T&E Express]** を選択します。

    ![アプリケーションの一覧の [T&E Express] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-te-express-test-user"></a>T&E Express テスト ユーザーの作成

Azure AD ユーザーが T&E Express にログインできるようにするには、そのユーザーを T&E Express にプロビジョニングする必要があります。 T&E Express の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. T&E Express 企業サイトに管理者としてログインします。

1. 管理者タグで、[ユーザー] をクリックして、[ユーザー] マスター ページを開きます。

    ![[Admin]\(管理者\) メニューから [Users]\(ユーザー\) が選択されていることを示すスクリーンショット。](./media/tyeexpress-tutorial/tye-adminusers.png)

1. ホーム ページをクリックして、 **[+]** をクリックしてユーザーを追加します。

    ![ユーザーを追加するプラス記号アイコンを示すスクリーンショット。](./media/tyeexpress-tutorial/tye-usershome.png)

1. フォームに必要なすべての必須事項を入力して、保存ボタンをクリックし、詳細を保存します。

    ![[User Information]\(ユーザー情報\) セクションを示すスクリーンショット。ここで適切な値を入力できます。](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![[Approvers]\(承認者\) と [Assistant]\(アシスタント\) のセクションを示すスクリーンショット。ここで適切な値を入力できます。](./media/tyeexpress-tutorial/tye-userssave.png)

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [T&E Express] タイルをクリックすると、SSO を設定した T&E Express に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)