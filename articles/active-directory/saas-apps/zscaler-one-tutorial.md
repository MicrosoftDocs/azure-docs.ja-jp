---
title: 'チュートリアル: Azure Active Directory と Zscaler One の統合 | Microsoft Docs'
description: Azure Active Directory と Zscaler One の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 3f825e247aff5c8fc53eb8610f33bd8e0b7fce3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735644"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>チュートリアル: Azure Active Directory と Zscaler One の統合

このチュートリアルでは、Zscaler One と Azure Active Directory (Azure AD) を統合する方法について説明します。
Zscaler One と Azure AD の統合には、次の利点があります。

* Zscaler One にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Zscaler One に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。


## <a name="prerequisites"></a>前提条件

Zscaler One と Azure AD の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Zscaler One でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zscaler One では、**SP** によって開始される SSO がサポートされます

* Zscaler One では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-zscaler-one-from-the-gallery"></a>ギャラリーからの Zscaler One の追加

Azure AD への Zscaler One の統合を構成するには、管理対象の SaaS アプリ一覧に Zscaler One をギャラリーから追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zscaler One**」と入力します。
1. 結果のパネルから **[Zscaler One]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-one"></a>Zscaler One の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zscaler One に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Zscaler One の関連ユーザーとの間にリンク関係を確立する必要があります。

Zscaler One に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Zscaler One の SSO の構成](#configure-zscaler-one-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zscaler One のテスト ユーザーの作成](#create-zscaler-one-test-user)** - Zscaler One で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zscaler One** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、ユーザーが Zscaler One アプリケーションへのサインオンに使用する URL を入力します。

    > [!NOTE]
    > 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Zscaler One クライアント サポート チーム](https://www.zscaler.com/company/contact)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. Zscaler One アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

6. その他に、Zscaler One アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。
    
    | 名前 | ソース属性 |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。
    
    f. **[保存]** をクリックします。

    > [!NOTE]
    > Azure AD でロールを構成する方法については、[こちら](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)をクリックしてください。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Set up Zscaler One]\(Zscaler One のセットアップ\)** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に Zscaler One へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Zscaler One]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. 前述のロールを設定した場合、 **[ロールの選択]** ボックスの一覧からそれを選択できます。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-zscaler-one-sso"></a>Zscaler One の SSO の構成

1. Zscaler One 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **[My Apps Secure Sign-in browser extension]\(My Apps Secure Sign-in ブラウザー拡張機能\)** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Set up Zscaler One]\(Zscaler One のセットアップ\)** をクリックすると、Zscaler One アプリケーションに移動します。 そこから、管理者資格情報を提供して Zscaler One にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![SSO の設定](common/setup-sso.png)

3. Zscaler One を手動で設定する場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Zscaler One 企業サイトにサインインして、次の手順を実行します。

4. **[Administration]\(管理\) > [Authentication]\(認証\) > [Authentication Settings]\(認証の設定\)** に移動し、次の手順を実行します。
   
    ![Zscaler One サイトのスクリーンショット。説明されている手順が示されています。](./media/zscaler-one-tutorial/ic800206.png "管理")

    a. [Authentication Type]\(認証の種類\)で **[SAML]** を選択します。

    b. **[Configure SAML]** をクリックします。

5. **[Edit SAML]\(SAML の編集)** ウィンドウで次の手順を実行して、[保存] をクリックします。  
            
    ![ユーザーと認証の管理](./media/zscaler-one-tutorial/ic800208.png "[ユーザーと認証の管理]")
    
    a. **[SAML Portal URL]\(SAML ポータル URL\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。

    b. **[Login Name Attribute]\(ログイン名属性\)** テキスト ボックスに **NameID** の値を入力します。

    c. **アップロード** をクリックして、Azure portal からダウンロードした Azure SAML 署名証明書を **Public SSL Certificate\(パブリック SSL 証明書\)** にアップロードします。

    d. **[SAML 自動プロビジョニングを有効にする]** を選択します。

    e. displayName 属性に SAML 自動プロビジョニングを有効にするには、 **[User Display Name Attribute]\(ユーザー表示名属性\)** テキスト ボックスに **displayName** の値を入力します。

    f. memberOf 属性に SAML 自動プロビジョニングを有効にするには、 **[Group Name Attribute]\(グループ名属性\)** テキスト ボックスに **memberOf** の値を入力します。

    g. department 属性に SAML 自動プロビジョニングを有効にするには、 **[Department Name Attribute]\(部署名属性\)** テキスト ボックスに **department** の値を入力します。

    h. **[保存]** をクリックします。

6. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![[Activate]\(アクティブ化\) が選択された [Configure User Authentication]\(ユーザー認証の構成\) ダイアログ ボックスを示すスクリーンショット。](./media/zscaler-one-tutorial/ic800207.png)

    a. 左下の **[Activation]\(アクティブ化\)** メニューにポインターを置きます。

    b. **[アクティブ化]** をクリックします。

## <a name="configuring-proxy-settings"></a>プロキシ設定の構成
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer でプロキシ設定を構成するには

1. **Internet Explorer** を開始します。

2. **[ツール]** メニューの **[インターネット オプション]** を選択し、 **[インターネット オプション]** ダイアログを開きます。   

    ![[インターネット オプション]](./media/zscaler-one-tutorial/ic769492.png "[インターネット オプション]")

3. **[接続]** タブをクリックします。   

    ![接続](./media/zscaler-one-tutorial/ic769493.png "接続")

4. **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5. [プロキシ サーバー] セクションで、次の手順を実行します。   
   
    ![[プロキシ サーバー]](./media/zscaler-one-tutorial/ic769494.png "プロキシ サーバー")

    a. **[LAN にプロキシ サーバーを使用する]** をオンにします。

    b. [アドレス] ボックスに「**gateway.Zscaler One.net**」と入力します。

    c. [ポート] ボックスに「 **80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。

    e. **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

7. **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

### <a name="create-zscaler-one-test-user"></a>Zscaler One のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Zscaler One に作成します。 Zscaler One では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Zscaler One にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Zscaler One のサポート チーム](https://www.zscaler.com/company/contact)にお問い合わせください。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zscaler One のサインオン URL にリダイレクトされます。 

* Zscaler One のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zscaler One] タイルをクリックすると、Zscaler One のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Zscaler One を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
