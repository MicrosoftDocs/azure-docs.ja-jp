---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Confluence SAML SSO by Microsoft の統合 | Microsoft Docs
description: Azure Active Directory と Confluence SAML SSO by Microsoft の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1384a8c9cfc4da9e8757c26bdb3e92defdb73708
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743640"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Confluence SAML SSO by Microsoft の統合

このチュートリアルでは、Confluence SAML SSO by Microsoft と Azure Active Directory (Azure AD) を統合する方法について説明します。 Confluence SAML SSO by Microsoft と Azure AD を統合すると、次のことができます。

* Confluence SAML SSO by Microsoft にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Confluence SAML SSO by Microsoft に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

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

開始するには、次が必要です。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Confluence SAML SSO by Microsoft でのシングル サインオン (SSO) が有効なサブスクリプション。

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

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Confluence SAML SSO by Microsoft では、**SP** によって開始される SSO がサポートされます

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>ギャラリーからの Confluence SAML SSO by Microsoft の追加

Azure AD への Confluence SAML SSO by Microsoft の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Confluence SAML SSO by Microsoft を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Confluence SAML SSO by Microsoft**」と入力します。
1. 結果のパネルから **[Confluence SAML SSO by Microsoft]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Confluence SAML SSO by Microsoft の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Confluence SAML SSO by Microsoft に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Confluence SAML SSO by Microsoft の関連ユーザーの間で、リンク関係を確立する必要があります。

Confluence SAML SSO by Microsoft で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Confluence SAML SSO by Microsoft の SSO の構成](#configure-confluence-saml-sso-by-microsoft-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Confluence SAML SSO by Microsoft テスト ユーザーの作成](#create-confluence-saml-sso-by-microsoft-test-user)** - Confluence SAML SSO by Microsoft で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Confluence SAML SSO by Microsoft** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<domain:port>/` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 名前付き URL である場合は、ポートは省略できます。 これらの値は Confluence プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Confluence SAML SSO by Microsoft へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Confluence SAML SSO by Microsoft]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Confluence SAML SSO by Microsoft の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Confluence インスタンスに管理者としてサインインします。

1. 歯車をポイントし、 **[Add-ons]\(アドオン\)** をクリックします。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon1.png)

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=56503)からプラグインをダウンロードします。 **[Upload add-on]\(アドオンのアップロード\)** メニューを使用して、Microsoft が提供しているプラグインを手動でアップロードします。 プラグインのダウンロードは、[Microsoft サービス規約](https://www.microsoft.com/servicesagreement/)の対象です。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon12.png)

1. Confluence のリバース プロキシ シナリオまたはロード バランサー シナリオを実行するには、次の手順を実行します。

    > [!NOTE]
    > 以下の手順に従って最初にサーバーを構成した後、プラグインをインストールする必要があります。

    a. JIRA サーバー アプリケーションの **server.xml** ファイルの **connector** ポートに以下の属性を追加します。

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. プロキシ/ロード バランサーに従って、 **[システム設定]** の **[ベース URL]** を変更します。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. プラグインがインストールされると、 **[アドオンの管理]** セクションの **[User Installed]\(ユーザー インストール\)** アドオン セクションに表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon13.png)

1. 構成ページで次の手順を実行します。

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

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft のテスト ユーザーの作成

オンプレミス サーバーで Azure AD ユーザーの Confluence へのサインインを有効にするには、そのユーザーを Confluence SAML SSO by Microsoft にプロビジョニングする必要があります。 Confluence SAML SSO by Microsoft では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの Confluence にサインインします。

1. 歯車をポイントし、 **[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](./media/confluencemicrosoft-tutorial/user1.png)

1. [Users]\(ユーザー\) セクションで、 **[Add users]\(ユーザーの追加\)** タブをクリックします。 **[Add a User]\(ユーザーの追加\)** ダイアログ ページで、次の手順に従います。

    ![従業員の追加](./media/confluencemicrosoft-tutorial/user2.png)

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザー (B.Simon など) の電子メールを入力します。

    b. **[フル ネーム]** ボックスに、ユーザー (B.Simon など) の氏名を入力します。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (B.Simon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、B.Simon のパスワードを入力します。

    e. **[Confirm Password]\(パスワードの確認\)** をクリックし、パスワードを再入力します。

    f. **[追加]** ボタンをクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [Confluence SAML SSO by Microsoft] のタイルをクリックすると、SSO を設定した Confluence SAML SSO by Microsoft に自動的にサインオンされます。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Confluence SAML SSO by Microsoft を試す](https://aad.portal.azure.com/)