---
title: 'チュートリアル: Azure Active Directory と Tableau Server の統合 | Microsoft Docs'
description: Azure Active Directory と Tableau Server の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: ed8e63a49702e8ba951990f8d7ff19b8f058fa74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089397"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>チュートリアル: Azure Active Directory と Tableau Server の統合

このチュートリアルでは、Tableau Server と Azure Active Directory (Azure AD) を統合する方法について説明します。
Tableau Server と Azure AD の統合には、次の利点があります。

* Tableau Server にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Tableau Server に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Tableau Server の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Tableau Server でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Tableau Server では、**SP** によって開始される SSO がサポートされます

## <a name="adding-tableau-server-from-the-gallery"></a>ギャラリーから Tableau Server を追加する

Azure AD への Tableau Server の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tableau Server を追加する必要があります。

**ギャラリーから Tableau Server を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Tableau Server**」と入力し、結果ウィンドウで **[Tableau Server]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Tableau Server](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Tableau Server で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Tableau Server 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Tableau Server で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Tableau Server シングル サインオンの構成](#configure-tableau-server-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Tableau Server のテスト ユーザーの作成](#create-tableau-server-test-user)** - Tableau Server で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Tableau Server で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Tableau Server** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Tableau Server ドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://azure.<domain name>.link` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://azure.<domain name>.link` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://azure.<domain name>.link/wg/saml/SSO/index.html` のパターンを使用して URL を入力します

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 [Tableau Server Configiuration]\(Tableau Server の構成) ページから入手した実際の URL と識別子で値を更新します。これについてはこのチュートリアルで後ほど説明します。

5. Tableau Server アプリケーションは、カスタム要求**ユーザー名**を要求します。ユーザー名は次のように定義する必要があります。 これは、一意のユーザー識別子要求の代わりにユーザー識別子として使用されています。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性と要求]** セクションで管理できます。 **[編集]** ボタンをクリックして、 **[ユーザー属性と要求]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性と要求]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name | ソース属性 | 名前空間 |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** 値を入力します。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

8. **[Tableau Server のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-tableau-server-single-sign-on"></a>Tableau Server のシングル サインオンを構成する

1. アプリケーションに合わせて SSO を構成するには、管理者として Tableau Server テナントにサインインする必要があります。

2. **[構成]** タブで、 **[User Identity & Access]\(ユーザー ID とアクセス\)** を選択し、 **[Authentication Method]\(認証方法\)** タブを選択します。

    ![Configure single sign-on](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. **[CONFIGURATION]\(構成\)** ページで、次の手順を実行します。

    ![Configure single sign-on](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. **[Authentication Method]\(認証方法\)** として SAML を選択します。

    b. **[Enable SAML Authentication for the server]\(サーバーの SAML 認証を有効にする\)** のチェック ボックスをオンにします。

    c. [Tableau Server return URL]\(Tableau Server の戻り先 URL\): Tableau Server ユーザーがアクセスする URL (<http://tableau_server> など)。 `http://localhost` の使用は推奨されません。 末尾にスラッシュが付いている URL (例: `http://tableau_server/`) はサポートされていません。 **Tableau Server の戻り先 URL** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます

    d. [SAML entity ID]: IdP に対して Tableau Server のインストールを一意に識別するエンティティ ID。 必要に応じてこの欄にも Tableau Server URL を入力できますが、使用する Tableau Server URL にする必要はありません。 **SAML エンティティ ID** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます

    e. **[Download XML Metadata File]\(XML メタデータ ファイルのダウンロード\)** をクリックし、テキスト エディター アプリケーションで開きます。 Http Post で Index 0 の [Assertion Consumer Service URL] を探し、URL をコピーします。 これを、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます

    f. Azure Portal からダウンロードしたフェデレーション メタデータ ファイルを検索し、 **[SAML Idp metadata file]\(SAML Idp メタデータ ファイル\)** でアップロードします。

    g. ユーザー名、表示名、メール アドレスを IdP が保持するために使用する属性の名前を入力します。

    h. **[保存]**

    > [!NOTE]
    > 顧客は任意の証明書を Tableau Server の SAML SSO 構成でアップロードする必要があります。SSO フローではその証明書は無視されます。 Tableau Server で SAML を構成する方法について不明な点がある場合は、[SAML の構成](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm)に関する記事を参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Tableau Server へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Tableau Server]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Tableau Server]** を選択します。

    ![アプリケーションの一覧の Tableau Server のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-tableau-server-test-user"></a>Tableau Server のテスト ユーザーの作成

このセクションの目的は、Tableau Server で Britta Simon というユーザーを作成することです。 Tableau Server 内のすべてのユーザーをプロビジョニングする必要があります。

また、ユーザーのユーザー名は、Azure AD のカスタム属性 **username** で構成した値と一致する必要があります。 正しい対応付けがあれば、統合で Azure AD シングル サインオンの構成が機能します。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、組織の Tableau Server 管理者に問い合わせてください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Tableau Server] タイルをクリックすると、SSO を設定した Tableau Server に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

