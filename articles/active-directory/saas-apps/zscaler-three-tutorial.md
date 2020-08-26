---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zscaler Three の統合 | Microsoft Docs
description: Azure Active Directory と Zscaler Three の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 00585105350e9858bbfd649a41273def162febcb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545779"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zscaler Three の統合

このチュートリアルでは、Zscaler Three と Azure Active Directory (Azure AD) を統合する方法について説明します。 Zscaler Three を Azure AD に統合すると、次のことができます。

* Zscaler Three にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントで Zscaler Three に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Zscaler Three でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Zscaler Three では、**SP** によって開始される SSO がサポートされます

* Zscaler Three では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-zscaler-three-from-the-gallery"></a>ギャラリーからの Zscaler Three の追加

Azure AD への Zscaler Three の統合を構成するには、管理対象の SaaS アプリ一覧に Zscaler Three をギャラリーから追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zscaler Three**」と入力します。
1. 結果のパネルから **[Zscaler Three]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-three"></a>Zscaler Three の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zscaler Three に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Zscaler Three の関連ユーザーとの間にリンク関係を確立する必要があります。

Zscaler Three に対する Azure AD SSO を構成してテストするには、次の項目を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zscaler Three の SSO の構成](#configure-zscaler-three-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zscaler Three のテスト ユーザーの作成](#create-zscaler-three-test-user)** - Zscaler Three で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Zscaler Three** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://login.zscalerthree.net/sfc_sso`」と入力します。

1. Zscaler Three アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/edit-attribute.png)

6. その他に、Zscaler Three アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性 |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    > [!NOTE]
    > Azure AD で役割を構成する方法については、[ここ](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)をクリックしてください

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Zscaler Three]\(Zscaler Three の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

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

このセクションでは、B.Simon に Zscaler Three へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Zscaler Three]** を選択します。
1. **[ユーザーとグループ]** ダイアログの一覧から **[Britta Simon]** などのユーザーを選択し、画面の下部にある **[選択]** ボタンをクリックします。

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. **[ロールの選択]** ダイアログで、一覧から適切なユーザー ロールを選択し、画面の下部にある **[選択]** ボタンをクリックします。

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンを選択します。

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Zscaler Three の SSO の構成

1. Zscaler Three 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **[マイ アプリによるセキュリティで保護されたサインイン拡張機能]** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Set up Zscaler Three]\(Zscaler Three のセットアップ\)** をクリックすると、Zscaler Three アプリケーションに移動します。 そこから、管理者資格情報を提供して Zscaler Three にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップ](common/setup-sso.png)

3. Zscaler Three を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、Zscaler Three の企業サイトに管理者としてサインインして、次の手順を実行します。

4. **[Administration]\(管理\) > [Authentication]\(認証\) > [Authentication Settings]\(認証の設定\)** に移動し、次の手順を実行します。
   
    ![管理](./media/zscaler-three-tutorial/ic800206.png "管理")

    a. [Authentication Type]\(認証の種類\)で **[SAML]** を選択します。

    b. **[Configure SAML]** をクリックします。

5. **[Edit SAML]\(SAML の編集)** ウィンドウで次の手順を実行して、[保存] をクリックします。  
            
    ![ユーザーと認証の管理](./media/zscaler-three-tutorial/ic800208.png "[ユーザーと認証の管理]")
    
    a. **[SAML Portal URL]\(SAML ポータル URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** を貼り付けます。

    b. **[Login Name Attribute]\(ログイン名属性\)** テキスト ボックスに **NameID** の値を入力します。

    c. **アップロード** をクリックして、Azure portal からダウンロードした Azure SAML 署名証明書を **Public SSL Certificate\(パブリック SSL 証明書\)** にアップロードします。

    d. **[SAML 自動プロビジョニングを有効にする]** を選択します。

    e. displayName 属性に SAML 自動プロビジョニングを有効にするには、 **[User Display Name Attribute]\(ユーザー表示名属性\)** テキスト ボックスに **displayName** の値を入力します。

    f. memberOf 属性に SAML 自動プロビジョニングを有効にするには、 **[Group Name Attribute]\(グループ名属性\)** テキスト ボックスに **memberOf** の値を入力します。

    g. department 属性に SAML 自動プロビジョニングを有効にするには、 **[Department Name Attribute]\(部署名属性\)** テキスト ボックスに **department** の値を入力します。

    h. **[保存]** をクリックします。

6. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![管理](./media/zscaler-three-tutorial/ic800207.png)

    a. 左下の **[Activation]\(アクティブ化\)** メニューにポインターを置きます。

    b. **[アクティブ化]** をクリックします。

## <a name="configuring-proxy-settings"></a>プロキシ設定の構成
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer でプロキシ設定を構成するには

1. **Internet Explorer**を開始します。

2. **[ツール]** メニューの **[インターネット オプション]** を選択し、 **[インターネット オプション]** ダイアログを開きます。   
    
     ![[インターネット オプション]](./media/zscaler-three-tutorial/ic769492.png "[インターネット オプション]")

3. **[接続]** タブをクリックします。   
  
     ![接続](./media/zscaler-three-tutorial/ic769493.png "接続")

4. **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5. [プロキシ サーバー] セクションで、次の手順を実行します。   
   
    ![[プロキシ サーバー]](./media/zscaler-three-tutorial/ic769494.png "プロキシ サーバー")

    a. **[LAN にプロキシ サーバーを使用する]** をオンにします。

    b. [アドレス] テキスト ボックスに「**gateway.Zscaler Three.net**」と入力します。

    c. [ポート] ボックスに「 **80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。

    e. **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

6. **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

### <a name="create-zscaler-three-test-user"></a>Zscaler Three テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Zscaler Three に作成します。 Zscaler Three では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Zscaler Three に存在しない場合は、Zscaler Three にアクセスしようとしたときに新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Zscaler Three のサポート チーム](https://www.zscaler.com/company/contact)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Zscaler Three] タイルをクリックすると、SSO を設定した Zscaler Three に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Zscaler Three を試す](https://aad.portal.azure.com/)

