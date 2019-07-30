---
title: チュートリアル:Azure Active Directory と Confluence SAML SSO by Microsoft の統合 | Microsoft Docs
description: Azure Active Directory と Confluence SAML SSO by Microsoft の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35aebb1ec7b2b4eb1753e6b4aa79d583483d0c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105074"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>チュートリアル:Azure Active Directory と Confluence SAML SSO by Microsoft の統合

このチュートリアルでは、Confluence SAML SSO by Microsoft と Azure Active Directory (Azure AD) を統合する方法について説明します。
Confluence SAML SSO by Microsoft と Azure AD の統合には、次の利点があります。

* Confluence SAML SSO by Microsoft にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Confluence SAML SSO by Microsoft に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="description"></a>説明:

Microsoft Azure Active Directory アカウントと Atlassian Confluence サーバーを使用して、シングル サインオンを有効にします。 これにより、組織のすべてのユーザーが、Azure AD の資格情報を使用して Confluence アプリケーションにサインインできます。 このプラグインは、フェデレーションに SAML 2.0 を使用します。

## <a name="prerequisites"></a>前提条件

Confluence SAML SSO by Microsoft と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 64 ビットの Windows サーバー (オンプレミスまたはクラウド IaaS インフラストラクチャ) にインストールされている Confluence サーバー アプリケーション
- Confluence サーバーの HTTPS が有効になっていること
- Confluence プラグインがサポートされているバージョンであること (下記のセクションをご覧ください)
- Confluence サーバーが認証のためにインターネット、特に Azure AD ログイン ページにアクセスでき、Azure AD からトークンを受け取れること
- 管理者の資格情報が Confluence に設定されていること
- WebSudo が Confluence で無効になっていること
- テスト ユーザーが Confluence サーバー アプリケーションで作成されていること

> [!NOTE]
> このチュートリアルの手順をテストする場合、Confluence の運用環境を使用しないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、1 か月の試用版を[無料試用版の提供](https://azure.microsoft.com/pricing/free-trial/)のページで入手できます。

## <a name="supported-versions-of-confluence"></a>サポートされている Confluence のバージョン

現時点では、次のバージョンの Confluence がサポートされています。

- Confluence: 5.0 から 5.10
- Confluence: 6.0.1
- Confluence: 6.1.1
- Confluence: 6.2.1
- Confluence: 6.3.4
- Confluence: 6.4.0
- Confluence: 6.5.0
- Confluence: 6.6.2
- Confluence: 6.7.0
- Confluence: 6.8.1
- Confluence: 6.9.0
- Confluence: 6.10.0
- Confluence: 6.11.0
- Confluence: 6.12.0
- Confluence: 6.15.3

> [!NOTE]
> Confluence プラグインは、Ubuntu Version 16.04 でも動作することに注意してください

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Confluence SAML SSO by Microsoft では、**SP** によって開始される SSO がサポートされます

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>ギャラリーからの Confluence SAML SSO by Microsoft の追加

Azure AD への Confluence SAML SSO by Microsoft の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Confluence SAML SSO by Microsoft を追加する必要があります。

**ギャラリーから Confluence SAML SSO by Microsoft を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Confluence SAML SSO by Microsoft**」と入力し、結果パネルで **[Confluence SAML SSO by Microsoft]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リストの Confluence SAML SSO by Microsoft](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Confluence SAML SSO by Microsoft で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Confluence SAML SSO by Microsoft 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Confluence SAML SSO by Microsoft で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Confluence SAML SSO by Microsoft のシングル サインオンの構成](#configure-confluence-saml-sso-by-microsoft-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Confluence SAML SSO by Microsoft テスト ユーザーの作成](#create-confluence-saml-sso-by-microsoft-test-user)** - Confluence SAML SSO by Microsoft で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Confluence SAML SSO by Microsoft で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Confluence SAML SSO by Microsoft** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Confluence SAML SSO by Microsoft のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<domain:port>/` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 名前付き URL である場合は、ポートは省略できます。 これらの値は Confluence プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

4. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-confluence-saml-sso-by-microsoft-single-sign-on"></a>Confluence SAML SSO by Microsoft のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Confluence インスタンスに管理者としてサインインします。

2. 歯車をポイントし、 **[Add-ons]\(アドオン\)** をクリックします。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon1.png)

3. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=56503)からプラグインをダウンロードします。 **[Upload add-on]\(アドオンのアップロード\)** メニューを使用して、Microsoft が提供しているプラグインを手動でアップロードします。 プラグインのダウンロードは、[Microsoft サービス規約](https://www.microsoft.com/servicesagreement/)の対象です。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon12.png)

4. プラグインがインストールされると、 **[アドオンの管理]** セクションの **[User Installed]\(ユーザー インストール\)** アドオン セクションに表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon13.png)

5. 構成ページで次の手順を実行します。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon53.png)

    > [!TIP]
    > メタデータの解決でエラーが発生しないように、アプリに対してマップされている証明書が 1 つしかないようにします。 証明書が複数ある場合は、メタデータの解決の際に管理者に対してエラーが表示されます。

    1. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付け、 **[解決]** ボタンをクリックします。 IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

    1. **識別子、応答 URL、サインオン URL** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]、[応答 URL]、[サインオン URL]** ボックスにそれぞれ貼り付けます。

    1. ユーザーのログイン画面に表示するボタン名を **[Login Button Name]\(ログイン ボタン名\)** に入力します。

    1. **[SAML User ID Locations]\(SAML ユーザー ID の場所\)** で、 **[User ID is in the NameIdentifier element of the Subject statement]\(Subject ステートメントの NameIdentifier 要素内のユーザー ID\)** 、または **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択します。  この ID は Confluence ユーザー ID である必要があります。 ユーザー ID が一致しない場合、システムはユーザーのサインインを許可しません。 

       > [!Note]
       > 既定の SAML ユーザー ID の場所は、名前識別子です。 属性オプションでこれを変更して、適切な属性名を入力できます。
    
    1. **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択する場合は、ユーザー ID が格納されている属性の名前を **[Attribute name]\(属性名\)** ボックスに入力します。 

    1. Azure AD でフェデレーション ドメイン (ADFS など) を使用している場合は、 **[Enable Home Realm Discovery]\(ホーム領域の検出を有効にする\)** をクリックして **[Domain Name]\(ドメイン名\)** を構成します。
    
    1. ADFS ベースでログインする場合は、 **[Domain Name]\(ドメイン名\)** にドメイン名を入力します。

    1. ユーザーが Confluence からサインアウトしたときに Azure AD からもサインアウトさせる場合は、 **[Enable Single Sign out]\(シングル サインアウトを有効にする\)** をオンにします。 

    1. Azure AD 資格情報でのみサインインする場合は、 **[Force Azure Login]\(Azure ログインを強制する)** チェックボックスを有効にします。
    
       > [!Note]
       > Azure ログインの強制を有効にしたときにログイン ページで管理ログインの既定のログイン フォームを有効にするには、ブラウザー URL にクエリ パラメーターを追加します。
       > `https://<domain:port>/login.action?force_azure_login=false`
    
    1. **[Save (保存)]** ボタンをクリックして、設定を保存します。

       > [!NOTE]
       > インストールとトラブルシューティングについて詳しくは、[MS Confluence SSO コネクタ管理者ガイド](../ms-confluence-jira-plugin-adminguide.md)のページをご覧ください。 [FAQ](../ms-confluence-jira-plugin-faq.md) もご利用いただけます。

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

このセクションでは、Britta Simon に Confluence SAML SSO by Microsoft へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Confluence SAML SSO by Microsoft]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Confluence SAML SSO by Microsoft]** を選択します。

    ![アプリケーションの一覧の Confluence SAML SSO by Microsoft リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft のテスト ユーザーの作成

オンプレミス サーバーで Azure AD ユーザーの Confluence へのサインインを有効にするには、そのユーザーを Confluence SAML SSO by Microsoft にプロビジョニングする必要があります。 Confluence SAML SSO by Microsoft では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの Confluence にサインインします。

2. 歯車をポイントし、 **[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](./media/confluencemicrosoft-tutorial/user1.png) 

3. [Users]\(ユーザー\) セクションで、 **[Add users]\(ユーザーの追加\)** タブをクリックします。 **[Add a User]\(ユーザーの追加\)** ダイアログ ページで、次の手順に従います。

    ![従業員の追加](./media/confluencemicrosoft-tutorial/user2.png) 

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーのメール (Britta Simon など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、Britta Simon のパスワードを入力します。

    e. **[Confirm Password]\(パスワードの確認\)** をクリックし、パスワードを再入力します。

    f. **[追加]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [Confluence SAML SSO by Microsoft] のタイルをクリックすると、SSO を設定した Confluence SAML SSO by Microsoft に自動的にサインオンされます。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

