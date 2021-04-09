---
title: チュートリアル:Azure Active Directory と Confirmit Horizons の統合 | Microsoft Docs
description: Azure Active Directory と Confirmit Horizons の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b1c4ab22db3bb9cadc783357bd9963c5de3b6f98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672697"
---
# <a name="tutorial-azure-active-directory-integration-with-confirmit-horizons"></a>チュートリアル:Azure Active Directory と Confirmit Horizons の統合

このチュートリアルでは、Confirmit Horizons と Azure Active Directory (Azure AD) を統合する方法について説明します。
Confirmit Horizons と Azure AD の統合には、次の利点があります。

* Confirmit Horizons にアクセスできるユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで Confirmit Horizons に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Confirmit Horizons と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Confirmit Horizons でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Confirmit Horizons では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

* Confirmit Horizons では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-confirmit-horizons-from-the-gallery"></a>ギャラリーからの Confirmit Horizons の追加

Azure AD への Confirmit Horizons の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Confirmit Horizons を追加する必要があります。

**ギャラリーから Confirmit Horizons を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Confirmit Horizons**」と入力して、結果パネルで **[Confirmit Horizons]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Confirmit Horizons](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Confirmit Horizons で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Confirmit Horizons 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Confirmit Horizons で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Confirmit Horizons のシングル サインオンの構成](#configure-confirmit-horizons-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Confirmit Horizons のテスト ユーザーの作成](#create-confirmit-horizons-test-user)** - Confirmit Horizons で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Confirmit Horizons で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Confirmit Horizons** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[保存] アクション、[識別子] フィールド、[応答 U R L] フィールドが強調表示された [基本的な S A M L 構成] セクションを示すスクリーンショット。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.com.au/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.ca/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.hk/identity/AuthServices/<UNIQUEID>
    https://sso.us.confirmit.com/<UNIQUEID>
    ```

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.com.au/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.ca/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.hk/identity/AuthServices/<UNIQUEID>/acs
    https://sso.us.confirmit.com/<UNIQUEID>/saml/acs
    ```

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Confirmit Horizons のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.com.au/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.ca/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.hk/identity/<UNIQUEID>
    https://sso.us.confirmit.com/<UNIQUEID>
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Confirmit Horizons クライアント サポート チーム](mailto:support@confirmit.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-confirmit-horizons-single-sign-on"></a>Confirmit Horizons のシングル サインオンの構成

**Confirmit Horizons** 側でシングル サインオンを構成するには、**アプリケーション フェデレーション メタデータ URL** を [Confirmit Horizons サポート チーム](mailto:support@confirmit.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** フィールドに「 **brittasimon@yourcompanydomain.extension** 」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Confirmit Horizons へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Confirmit Horizons]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Confirmit Horizons]** を選択します。

    ![アプリケーションの一覧の Confirmit Horizons のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-confirmit-horizons-test-user"></a>Confirmit Horizons のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Confirmit Horizons に作成します。 Confirmit Horizons では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Confirmit Horizons にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Confirmit Horizons] タイルをクリックすると、SSO を設定した Confirmit Horizons に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)