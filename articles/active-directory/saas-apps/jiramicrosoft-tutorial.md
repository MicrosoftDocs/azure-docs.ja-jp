---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と JIRA SAML SSO by Microsoft の統合 | Microsoft Docs
description: Azure Active Directory と JIRA SAML SSO by Microsoft の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 75b8230a1027bbf3ff3d73fb35ce65107c2db7e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730674"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と JIRA SAML SSO by Microsoft の統合

このチュートリアルでは、JIRA SAML SSO by Microsoft と Azure Active Directory (Azure AD) を統合する方法について説明します。 JIRA SAML SSO by Microsoft と Azure AD を統合すると、次のことができます。

* JIRA SAML SSO by Microsoft にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に JIRA SAML SSO by Microsoft にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="description"></a>説明

Microsoft Azure Active Directory アカウントと Atlassian JIRA サーバーを使用して、シングル サインオンを有効にします。 これにより、組織のすべてのユーザーが、Azure AD の資格情報を使用して JIRA アプリケーションにサインインできます。 このプラグインは、フェデレーションに SAML 2.0 を使用します。

## <a name="prerequisites"></a>前提条件

JIRA SAML SSO by Microsoft と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
- JIRA Core と Software 6.4 から 8.14.0、または JIRA Service Desk 3.0 から 4.11.1 が、Windows 64 ビット版にインストールおよび構成されている必要があります
- JIRA サーバーの HTTPS が有効になっていること
- JIRA プラグインがサポートされているバージョンであること (下記のセクションをご覧ください)
- JIRA サーバーが認証のためにインターネット、特に Azure AD ログイン ページにアクセスでき、Azure AD からトークンを受け取れること
- 管理者の資格情報が JIRA に設定されていること
- WebSudo が JIRA で無効になっていること
- テスト ユーザーが JIRA サーバー アプリケーションで作成されていること

> [!NOTE]
> このチュートリアルの手順をテストする場合、JIRA の運用環境を使用しないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

開始するには、次が必要です。

* 必要な場合を除き、運用環境は使用しないでください。
* JIRA SAML SSO by Microsoft でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="supported-versions-of-jira"></a>サポートされている JIRA のバージョン

* JIRA Core と Software:6.4 から 8.14.0
* JIRA Service Desk 3.0.0 から 4.11.1
* JIRA は 5.2 もサポートします。 詳細については、[Microsoft Azure Active Directory single sign-on for JIRA 5.2](jira52microsoft-tutorial.md) に関する記事を参照してください。

> [!NOTE]
> JIRA プラグインは、Ubuntu Version 16.04 と Linux でも動作することに注意してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* JIRA SAML SSO by Microsoft では、**SP** によって開始される SSO がサポートされます

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>ギャラリーからの JIRA SAML SSO by Microsoft の追加

Azure AD への JIRA SAML SSO by Microsoft の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に JIRA SAML SSO by Microsoft を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**JIRA SAML SSO by Microsoft**」と入力します。
1. 結果のパネルから **[JIRA SAML SSO by Microsoft]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft"></a>JIRA SAML SSO by Microsoft の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、JIRA SAML SSO by Microsoft に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと JIRA SAML SSO by Microsoft の関連ユーザーとの間にリンク関係を確立する必要があります。

JIRA SAML SSO by Microsoft に対して Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[JIRA SAML SSO by Microsoft の SSO の構成](#configure-jira-saml-sso-by-microsoft-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[JIRA SAML SSO by Microsoft テスト ユーザーの作成](#create-jira-saml-sso-by-microsoft-test-user)** - JIRA SAML SSO by Microsoft で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **JIRA SAML SSO by Microsoft** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<domain:port>/` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<domain:port>/plugins/servlet/saml/auth` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 名前付き URL である場合は、ポートは省略できます。 これらの値は JIRA プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

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

このセクションでは、B.Simon に JIRA SAML SSO by Microsoft へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[JIRA SAML SSO by Microsoft]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>JIRA SAML SSO by Microsoft の SSO の構成

1. 別の Web ブラウザー ウィンドウで、JIRA インスタンスに管理者としてサインインします。

2. 歯車をポイントし、 **[Add-ons]\(アドオン\)** をクリックします。

    ![[Settings]\(設定\) メニューの [Add-ons]\(アドオン\) が選択されているスクリーンショット。](./media/jiramicrosoft-tutorial/addon1.png)

3. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=56506)からプラグインをダウンロードします。 **[Upload add-on]\(アドオンのアップロード\)** メニューを使用して、Microsoft が提供しているプラグインを手動でアップロードします。 プラグインのダウンロードは、[Microsoft サービス規約](https://www.microsoft.com/servicesagreement/)の対象です。

    ![[Upload add-on]\(アドオンのアップロード\) リンクが強調表示されている [Manage add-ons]\(アドオンの管理\) を示すスクリーンショット。](./media/jiramicrosoft-tutorial/addon12.png)

4. JIRA のリバース プロキシ シナリオまたはロード バランサー シナリオを実行するには、次の手順を実行します。

    > [!NOTE]
    > 以下の手順に従って最初にサーバーを構成した後、プラグインをインストールする必要があります。

    a. JIRA サーバー アプリケーションの **server.xml** ファイルの **connector** ポートに以下の属性を追加します。

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![新しい行が追加された、エディター内の server ドット x m l ファイルを示すスクリーンショット。](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. プロキシ/ロード バランサーに従って、 **[システム設定]** の **[ベース URL]** を変更します。

    ![ベース U R L を変更できる [Administration Settings]\(管理設定\) を示すスクリーンショット。](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. プラグインがインストールされると、 **[アドオンの管理]** セクションの **[User Installed]\(ユーザー インストール\)** アドオン セクションに表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![[Configure]\(構成\) が選択されている [Azure A D SAML Single Sign-on for Jira] セクションを示すスクリーンショット。](./media/jiramicrosoft-tutorial/addon14.png)

6. 構成ページで次の手順を実行します。

    ![Microsoft Azure Active Directory single sign-on for Jira の構成ページを示すスクリーンショット。](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > メタデータの解決でエラーが発生しないように、アプリに対してマップされている証明書が 1 つしかないようにします。 証明書が複数ある場合は、メタデータの解決の際に管理者に対してエラーが表示されます。

    a. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付け、 **[解決]** ボタンをクリックします。 IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

    b. **識別子、応答 URL、サインオン URL** の値をコピーして、Azure Portal の **[JIRA SAML SSO by Microsoft Domain and URLs]/(JIRA SAML SSO by Microsoft のドメインと URL\)** セクションにある **識別子、応答 URL、サインオン URL** ボックスにそれぞれ貼り付けます。

    c. ユーザーのログイン画面に表示するボタン名を **[Login Button Name]\(ログイン ボタン名\)** に入力します。
    
    d. ユーザーのログイン画面に表示するボタンの説明を **[Login Button Description]\(ログイン ボタンの説明\)** に入力します。

    e. **[SAML User ID Locations]\(SAML ユーザー ID の場所\)** で、 **[User ID is in the NameIdentifier element of the Subject statement]\(Subject ステートメントの NameIdentifier 要素内のユーザー ID\)** 、または **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択します。  この ID は JIRA ユーザー ID である必要があります。 ユーザー ID が一致しない場合、システムはユーザーのサインインを許可しません。

    > [!Note]
    > 既定の SAML ユーザー ID の場所は、名前識別子です。 属性オプションでこれを変更して、適切な属性名を入力できます。

    f. **[User ID is in an Attribute element]\(Attribute 要素内のユーザー ID\)** を選択する場合は、ユーザー ID が格納されている属性の名前を **[Attribute name]\(属性名\)** ボックスに入力します。

    g. Azure AD でフェデレーション ドメイン (ADFS など) を使用している場合は、 **[Enable Home Realm Discovery]\(ホーム領域の検出を有効にする\)** をクリックして **[Domain Name]\(ドメイン名\)** を構成します。

    h. ADFS ベースでログインする場合は、 **[Domain Name]\(ドメイン名\)** にドメイン名を入力します。

    i. ユーザーが JIRA からサインアウトしたときに Azure AD からもサインアウトさせる場合は、 **[Enable Single Sign out]\(シングル サインアウトを有効にする\)** をオンにします。
    
    j. Azure AD 資格情報でのみサインインする場合は、 **[Force Azure Login]\(Azure ログインを強制する)** チェックボックスを有効にします。
    
    > [!Note]
    > Azure ログインの強制を有効にしたときにログイン ページで管理ログインの既定のログイン フォームを有効にするには、ブラウザー URL にクエリ パラメーターを追加します。
    > `https://<domain:port>/login.jsp?force_azure_login=false`

    k. **[Save (保存)]** ボタンをクリックして、設定を保存します。

    > [!NOTE]
    > インストールとトラブルシューティングについて詳しくは、[MS JIRA SSO コネクタ管理者ガイド](./ms-confluence-jira-plugin-adminguide.md)のページをご覧ください。 [FAQ](./ms-confluence-jira-plugin-adminguide.md) もご利用いただけます。

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>JIRA SAML SSO by Microsoft テスト ユーザーの作成

オンプレミス サーバーで Azure AD ユーザーの JIRA へのサインインを有効にするには、そのユーザーを JIRA SAML SSO by Microsoft にプロビジョニングする必要があります。 JIRA SAML SSO by Microsoft では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの JIRA にサインインします。

2. 歯車をポイントし、 **[User management]\(ユーザー管理\)** をクリックします。

    ![[Settings]\(設定\) メニューの [User management]\(ユーザー管理\) が選択されているスクリーンショット。](./media/jiramicrosoft-tutorial/user1.png)

3. [Administrator Access]\(管理者アクセス\) のページにリダイレクトされるので、**パスワード** を入力し、 **[Confirm]\(確認\)** ボタンをクリックします。

    ![資格情報を入力する [Administrator Access]\(管理者アクセス\) ページを示すスクリーンショット。](./media/jiramicrosoft-tutorial/user2.png)

4. **[User management]\(ユーザー管理\)** タブ セクションで、 **[create user]\(ユーザーの作成\)** をクリックします。

    ![ユーザーを作成する [User management]\(ユーザー管理\) タブを示すスクリーンショット。](./media/jiramicrosoft-tutorial/user3.png) 

5. **[Create new user]\(新しいユーザーの作成\)** ダイアログ ページで、以下の手順を実行します。

    ![この手順の情報を入力できる [Create new user]\(新しいユーザーの作成\) ダイアログ ボックスを示すスクリーンショット。](./media/jiramicrosoft-tutorial/user4.png) 

    a. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (B.simon@contoso.com など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーのフル ネーム (B.Simon など) を入力します。

    c. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (B.simon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    e. **[Create user]\(ユーザーの作成\)** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる JIRA SAML SSO by Microsoft のサインオン URL にリダイレクトされます。 

* JIRA SAML SSO by Microsoft のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリの [JIRA SAML SSO by Microsoft] タイルをクリックすると、JIRA SAML SSO by Microsoft のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

JIRA SAML SSO by Microsoft を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。