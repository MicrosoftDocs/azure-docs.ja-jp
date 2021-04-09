---
title: 'チュートリアル: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の統合| Microsoft Docs'
description: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 5e777a27e793b1f4ef5489248c170824d69d615c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92517686"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>チュートリアル: Azure Active Directory と Veritas Enterprise Vault.cloud SSO の統合

このチュートリアルでは、Veritas Enterprise Vault.cloud SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。
Veritas Enterprise Vault.cloud SSO と Azure AD の統合には、次の利点があります。

* Veritas Enterprise Vault.cloud SSO にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Veritas Enterprise Vault.cloud SSO に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Veritas Enterprise Vault.cloud SSO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Veritas Enterprise Vault.cloud SSO でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Veritas Enterprise Vault.cloud SSO では、**SP** によって開始される SSO がサポートされます

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>ギャラリーからの Veritas Enterprise Vault.cloud SSO の追加

Azure AD への Veritas Enterprise Vault.cloud SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Veritas Enterprise Vault.cloud SSO を追加する必要があります。

**ギャラリーから Veritas Enterprise Vault.cloud SSO を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Veritas Enterprise Vault.cloud SSO**」と入力し、結果パネルから **[Veritas Enterprise Vault.cloud SSO]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の Veritas Enterprise Vault.cloud SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Veritas Enterprise Vault.cloud SSO で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Veritas Enterprise Vault.cloud SSO の関連ユーザーの間で、リンク関係が確立されている必要があります。

Veritas Enterprise Vault.cloud SSO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Veritas Enterprise Vault.cloud SSO シングル サインオンの構成](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Veritas Enterprise Vault.cloud SSO テスト ユーザーの作成](#create-veritas-enterprise-vaultcloud-sso-test-user)** - Veritas Enterprise Vault.cloud SSO で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Veritas Enterprise Vault.cloud SSO で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Veritas Enterprise Vault.cloud SSO** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Veritas Enterprise Vault.cloud SSO のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>` という形式で URL を入力します。

    b. **[識別子]** ボックスでは、データセンターに応じた URL を使用します。

    | データセンター| URL |
    |----------|----|
    | 北米| `https://auth.lax.archivecloud.net` |
    | ヨーロッパ | `https://auth.ams.archivecloud.net` |
    | アジア太平洋| `https://auth.syd.archivecloud.net`|

    c. **[応答 URL]** ボックスでは、データセンターに応じた URL を使用します。

    | データセンター| URL |
    |----------|----|
    | 北米| `https://auth.lax.archivecloud.net` |
    | ヨーロッパ | `https://auth.ams.archivecloud.net` |
    | アジア太平洋| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Veritas Enterprise Vault.cloud SSO クライアント サポート チーム](https://www.veritas.com/support/.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Veritas Enterprise Vault.cloud SSO のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Veritas Enterprise Vault.cloud SSO シングル サインオンの構成

**Veritas Enterprise Vault.cloud SSO** 側にシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** および Azure portal からコピーした適切な URL を [Veritas Enterprise Vault.cloud SSO サポート チーム](https://www.veritas.com/support/.html)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Veritas Enterprise Vault.cloud SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Veritas Enterprise Vault.cloud SSO]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Veritas Enterprise Vault.cloud SSO]** を選択します。

    ![アプリケーションの一覧の Veritas Enterprise Vault.cloud SSO のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Veritas Enterprise Vault.cloud SSO テスト ユーザーの作成

このセクションでは、Veritas Enterprise Vault.cloud SSO で Britta Simon というユーザーを作成します。 [Veritas Enterprise Vault.cloud SSO サポート チーム](https://www.veritas.com/support/.html)と連携して、Veritas Enterprise Vault.cloud SSO プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Veritas Enterprise Vault.cloud SSO] タイルをクリックすると、SSO を設定した Veritas Enterprise Vault.cloud SSO に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)