---
title: チュートリアル:Azure Active Directory と JIRA SAML SSO by Microsoft (V5.2) の統合 | Microsoft Docs
description: Azure Active Directory と JIRA SAML SSO by Microsoft (V5.2) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b304bb35ea69906fc9576f45733134387be1b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099511"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>チュートリアル:Azure Active Directory と JIRA SAML SSO by Microsoft (V5.2) の統合

このチュートリアルでは、JIRA SAML SSO by Microsoft (V5.2) と Azure Active Directory (Azure AD) を統合する方法について説明します。
JIRA SAML SSO by Microsoft (V5.2) と Azure AD の統合には、次の利点があります。

* JIRA SAML SSO by Microsoft (V5.2) にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して JIRA SAML SSO by Microsoft (V5.2) に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="description"></a>説明

Microsoft Azure Active Directory アカウントと Atlassian JIRA サーバーを使用して、シングル サインオンを有効にします。 これにより、組織のすべてのユーザーが、Azure AD の資格情報を使用して JIRA アプリケーションにサインインできます。 このプラグインは、フェデレーションに SAML 2.0 を使用します。

## <a name="prerequisites"></a>前提条件

JIRA SAML SSO by Microsoft (V5.2) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- JIRA Core と Software 5.2が Windows 64 ビット版にインストールされて構成されている
- JIRA サーバーの HTTPS が有効になっていること
- JIRA プラグインがサポートされているバージョンであること (下記のセクションをご覧ください)
- JIRA サーバーが認証のためにインターネット、特に Azure AD ログイン ページにアクセスでき、Azure AD からトークンを受け取れること
- 管理者の資格情報が JIRA に設定されていること
- WebSudo が JIRA で無効になっていること
- テスト ユーザーが JIRA サーバー アプリケーションで作成されていること

> [!NOTE]
> このチュートリアルの手順をテストする場合、JIRA の運用環境を使用しないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、1 か月の試用版を[無料試用版の提供](https://azure.microsoft.com/pricing/free-trial/)のページで入手できます。

## <a name="supported-versions-of-jira"></a>サポートされている JIRA のバージョン

* JIRA Core と Software:5.2
* JIRA では、6.0 から 7.12 もサポートされています。 詳細については、[JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md) をクリックしてください。

> [!NOTE]
> JIRA プラグインは、Ubuntu Version 16.04 でも動作することに注意してください

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* JIRA SAML SSO by Microsoft (V5.2) では、**SP** によって開始される SSO がサポートされます

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>ギャラリーからの JIRA SAML SSO by Microsoft (V5.2) の追加

Azure AD への JIRA SAML SSO by Microsoft (V5.2) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に JIRA SAML SSO by Microsoft (V5.2) を追加する必要があります。

**ギャラリーから JIRA SAML SSO by Microsoft (V5.2) を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**JIRA SAML SSO by Microsoft (V5.2)** 」と入力し、結果パネルで **[JIRA SAML SSO by Microsoft (V5.2)]** を選び、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの JIRA SAML SSO by Microsoft (V5.2)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、JIRA SAML SSO by Microsoft (V5.2) で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと JIRA SAML SSO by Microsoft (V5.2) 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

JIRA SAML SSO by Microsoft (V5.2) で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[JIRA SAML SSO by Microsoft (V5.2) のシングル サインオンの構成](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[JIRA SAML SSO by Microsoft (V5.2) テスト ユーザーの作成](#create-jira-saml-sso-by-microsoft-v52-test-user)** - JIRA SAML SSO by Microsoft (V5.2) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

JIRA SAML SSO by Microsoft (V5.2) で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **JIRA SAML SSO by Microsoft (V5.2)** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[JIRA SAML SSO by Microsoft (V5.2) のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<domain:port>/` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 名前付き URL である場合は、ポートは省略できます。 これらの値は JIRA プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>JIRA SAML SSO by Microsoft (V5.2) のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、JIRA インスタンスに管理者としてサインインします。

2. 歯車をポイントし、 **[Add-ons]\(アドオン\)** をクリックします。

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon1.png)

3. [アドオン] タブ セクションで、 **[アドオンの管理]** をクリックします。

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon7.png)

4. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=56521)からプラグインをダウンロードします。 **[Upload add-on]\(アドオンのアップロード\)** メニューを使用して、Microsoft が提供しているプラグインを手動でアップロードします。 プラグインのダウンロードは、[Microsoft サービス規約](https://www.microsoft.com/servicesagreement/)の対象です。

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon12.png)

5. プラグインがインストールされると、 **[User Installed]\(ユーザー インストール\)** アドオン セクションに表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon13.png)

6. 構成ページで次の手順を実行します。

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > メタデータの解決でエラーが発生しないように、アプリに対してマップされている証明書が 1 つしかないようにします。 証明書が複数ある場合は、メタデータの解決の際に管理者に対してエラーが表示されます。

    a. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付け、 **[解決]** ボタンをクリックします。 IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

    b. **識別子、応答 URL、サインオン URL** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]、[応答 URL]、[サインオン URL]** ボックスにそれぞれ貼り付けます。

    c. ユーザーのログイン画面に表示するボタン名を **[Login Button Name]\(ログイン ボタン名\)** に入力します。

    d. **[SAML User ID Locations]\(SAML ユーザー ID の場所\)** で、 **[User ID is in the NameIdentifier element of the Subject statement]\(Subject ステートメントの NameIdentifier 要素内のユーザー ID\)** 、または **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択します。  この ID は JIRA ユーザー ID である必要があります。 ユーザー ID が一致しない場合、システムはユーザーのサインインを許可しません。

    > [!Note]
    > 既定の SAML ユーザー ID の場所は、名前識別子です。 属性オプションでこれを変更して、適切な属性名を入力できます。

    e. **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択する場合は、ユーザー ID が格納されている属性の名前を **[Attribute name]\(属性名\)** ボックスに入力します。 

    f. Azure AD でフェデレーション ドメイン (ADFS など) を使用している場合は、 **[Enable Home Realm Discovery]\(ホーム領域の検出を有効にする\)** をクリックして **[Domain Name]\(ドメイン名\)** を構成します。

    g. ADFS ベースでログインする場合は、 **[Domain Name]\(ドメイン名\)** にドメイン名を入力します。

    h. ユーザーが JIRA からサインアウトしたときに Azure AD からもサインアウトさせる場合は、 **[Enable Single Sign out]\(シングル サインアウトを有効にする\)** をオンにします。 

    i. **[Save (保存)]** ボタンをクリックして、設定を保存します。

    > [!NOTE]
    > インストールとトラブルシューティングについて詳しくは、[MS JIRA SSO コネクタ管理者ガイド](../ms-confluence-jira-plugin-adminguide.md)のページをご覧ください。[よくあるご質問](../ms-confluence-jira-plugin-faq.md)も役立ちます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon\@yourcompanydomain.extension`」と入力します。 たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に JIRA SAML SSO by Microsoft (V5.2) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[JIRA SAML SSO by Microsoft (V5.2)]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[JIRA SAML SSO by Microsoft (V5.2)]** を選択します。

    ![アプリケーションの一覧の [JIRA SAML SSO by Microsoft (V5.2)] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>JIRA SAML SSO by Microsoft (V5.2) テスト ユーザーの作成

Azure AD ユーザーが JIRA オンプレミス サーバーにサインインできるようにするには、そのユーザーを JIRA オンプレミス サーバーにプロビジョニングする必要があります。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの JIRA にサインインします。

2. 歯車をポイントし、 **[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](./media/jira52microsoft-tutorial/user1.png)

3. [Administrator Access]\(管理者アクセス\) のページにリダイレクトされるので、**パスワード**を入力し、 **[Confirm]\(確認\)** ボタンをクリックします。

    ![従業員の追加](./media/jira52microsoft-tutorial/user2.png)

4. **[User management]\(ユーザー管理\)** タブ セクションで、 **[create user]\(ユーザーの作成\)** をクリックします。

    ![従業員の追加](./media/jira52microsoft-tutorial/user3.png) 

5. **[Create new user]\(新しいユーザーの作成\)** ダイアログ ページで、以下の手順を実行します。

    ![従業員の追加](./media/jira52microsoft-tutorial/user4.png)

    a. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    e. **[Create user]\(ユーザーの作成\)** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [JIRA SAML SSO by Microsoft (V5.2)] タイルをクリックすると、SSO を設定した JIRA SAML SSO by Microsoft (V5.2) に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
