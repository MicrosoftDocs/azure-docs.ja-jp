---
title: チュートリアル:Azure Active Directory と Teamphoria の統合 | Microsoft Docs
description: Azure Active Directory と Teamphoria の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 05387d2fd9b38ffa8137668e01b6d7cff3346ac8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089008"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>チュートリアル:Azure Active Directory と Teamphoria の統合

このチュートリアルでは、Teamphoria と Azure Active Directory (Azure AD) を統合する方法について説明します。
Teamphoria と Azure AD の統合には、次の利点があります。

* Teamphoria にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Teamphoria に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Teamphoria と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Teamphoria でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Teamphoria では、**SP** によって開始される SSO がサポートされます

## <a name="adding-teamphoria-from-the-gallery"></a>ギャラリーからの Teamphoria の追加

Azure AD への Teamphoria の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Teamphoria を追加する必要があります。

**ギャラリーから Teamphoria を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Teamphoria**」と入力し、結果パネルで **[Teamphoria]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リスト内の Teamphoria](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Teamphoria で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Teamphoria 内の関連ユーザー間にリンク関係が確立されている必要があります。

Teamphoria で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Teamphoria シングル サインオンの構成](#configure-teamphoria-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Teamphoria のテスト ユーザーの作成](#create-teamphoria-test-user)** - Teamphoria 内で Britta Simon に対応するユーザーを作成し、Azure AD のユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Teamphoria で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Teamphoria** アプリケーション統合ページで、 **[シングルサインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Teamphoria のドメインと URL] のシングル サインオン情報](common/sp-intiated.png)

    **[サインオン URL]** ボックスに、`https://<sub-domain>.teamphoria.com/login` の形式で URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 この値は実際のサインオン URL で更新する必要があります。 シングル サインオン URL を取得するには、[Teamphoria クライアント サポート チーム](https://www.teamphoria.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Teamphoria のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-teamphoria-single-sign-on"></a>Teamphoria シングル サインオンの構成

1. **Teamphoria** 側でシングル サインオンを構成するには、Teamphoria アプリケーションに管理者としてログインします。

1. 左のツールバーの [Configure] タブの **[ADMIN SETTINGS]** オプションに移動し、 **[SINGLE SIGN-ON]** をクリックして SSO 構成ウィンドウを開きます。

    ![Configure single sign-on](./media/teamphoria-tutorial/admin_sso_configure.png)

1. 右上隅の **[ADD NEW IDENTITY PROVIDER (新しい ID プロバイダーの追加)]** をクリックして、SSO の設定を追加するためのフォームを開きます。

    ![Configure single sign-on](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. 以下の説明に従って、フィールドに詳細を入力します。

    ![Configure single sign-on](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **表示名**:管理者ページでのプラグインの表示名を入力します。

    b. **ボタン名**: SSO を使用してログインするためのログイン ページに表示されるタブの名前。

    c. **証明書**: Azure portal から先ほどダウンロードした証明書をメモ帳で開き、内容をコピーして、こちらのボックス内に貼り付けます。

    d. **エントリ ポイント**: 先ほど Azure portal からコピーした**ログイン URL** を貼り付けます。

    e. オプションを **[ON (オン)]** に切り替え、 **[保存]** をクリックします。

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

このセクションでは、Britta Simon に Teamphoria へのアクセスを　許可することにより、Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Teamphoria]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Teamphoria]** を選択します。

    ![アプリケーションの一覧内の Teamphoria のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-teamphoria-test-user"></a>Teamphoria テスト ユーザーの作成

Azure AD ユーザーが Teamphoria にサインインできるようにするには、そのユーザーを Teamphoria にプロビジョニングする必要があります。 Teamphoria の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Teamphoria 企業サイトに管理者としてサインインします。

1. 左のツールバーの **[管理]** タブで**管理者**設定をクリックし、 **[ユーザー]** をクリックして、ユーザーの管理者ページを開きます。

    ![従業員の追加](./media/teamphoria-tutorial/admin_manage_users.png)

1. **[MANUAL INVITE (手動招待)]** オプションをクリックします。

    ![[ユーザーの招待]](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. このページで、次の操作を実行します。

    ![[ユーザーの招待]](./media/teamphoria-tutorial/manual_user_invite.png)

    a. **[EMAIL ADDRESS]\(メール アドレス\)** ボックスに、BrittaSimon のようなユーザーの**メール アドレス**を入力します。

    b. **[FIRST NAME]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    c. **[LAST NAME]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    d. **[INVITE 1 USER (1 ユーザーを招待)]** をクリックします。 ユーザーをシステムに作成するには、そのユーザーが招待を受け入れる必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Teamphoria] タイルをクリックすると、SSO を設定した Teamphoria に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

