---
title: 'チュートリアル: Azure Active Directory と Zscaler Beta の統合 | Microsoft Docs'
description: Azure Active Directory と Zscaler Beta の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735592"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>チュートリアル: Azure Active Directory と Zscaler Beta の統合

このチュートリアルでは、Zscaler Beta と Azure Active Directory (Azure AD) を統合する方法について説明します。
Zscaler Beta を Azure AD に統合すると、次のことを実行できます。

* Zscaler Beta にアクセスするユーザーを Azure AD で管理する。
* ユーザーが Azure AD アカウントを使用して Zscaler Beta に自動的にサインインできるようにする。 このアクセス制御はシングル サインオン (SSO) と呼ばれます。
* Azure portal を使用して 1 つの中央サイトでアカウントを管理する。

## <a name="prerequisites"></a>前提条件

Zscaler Beta と Azure AD の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンを使用する Zscaler Beta サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zscaler Beta では、**SP** Initiated SSO がサポートされます。
* Zscaler Beta では、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-zscaler-beta-from-the-gallery"></a>ギャラリーからの Zscaler Beta の追加

Azure AD への Zscaler Beta の統合を構成するには、管理対象の SaaS アプリ一覧に Zscaler Beta をギャラリーから追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zscaler Beta**」と入力します。
1. 結果パネルから **[Zscaler Beta]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Zscaler Beta の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zscaler Beta に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Zscaler Beta の関連ユーザーとの間にリンク関係を確立する必要があります。

Zscaler Beta に対する Azure AD SSO を構成してテストするには、次の手順を実行します。


1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zscaler Beta の SSO の構成](#configure-zscaler-beta-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zscaler Beta のテスト ユーザーの作成](#create-zscaler-beta-test-user)** - Zscaler Beta で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zscaler Beta** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、ユーザーが Zscaler Beta アプリケーションへのサインインに使用する URL を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 実際のサインオン URL 値でこの値を更新します。 この値を取得するには、[Zscaler Beta クライアント サポート チーム](https://www.zscaler.com/company/contact)に問い合わせてください。

5. Zscaler Beta アプリケーションでは、特定の形式の SAML アサーションを予期しています。 カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** を選択して **[ユーザー属性]** ダイアログ ボックスを開きます。

    ![[ユーザー属性] ダイアログ ボックス](common/edit-attribute.png)

6. Zscaler Beta アプリケーションでは、いくつかの追加の属性が SAML 応答で返されることを予期しています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、次の手順を実行して、以下の表に示すように SAML トークン属性を追加します。
    
    | Name | ソース属性 | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. **[新しい要求の追加]** を選択して **[ユーザー要求の管理]** ダイアログ ボックスを開きます。

    b. **[名前]** ボックスに、その行に表示される属性名を入力します。

    c. **[名前空間]** ボックスは空白のままにします。

    d. **[ソース]** で **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** を選択します。

    g. **[保存]** を選択します。

    > [!NOTE]
    > Azure AD でロールを構成する方法については、[こちら](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)をクリックしてください。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** を選択して **証明書 (Base64)** をダウンロードします。 それを自分のコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

8. **[Set up Zscaler Beta]\(Zscaler Beta の設定\)** セクションで、要件に従って必要な URL をコピーします。

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

このセクションでは、B.Simon に Zscaler Beta へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Zscaler Beta]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. 前述のロールを設定した場合、 **[ロールの選択]** ボックスの一覧からそれを選択できます。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zscaler-beta-sso"></a>Zscaler Beta の SSO の構成

1. Zscaler Beta 内での構成を自動化するには、**[拡張機能のインストール]** を選択して **[アプリによるセキュリティで保護されたサインイン拡張機能]** をインストールします。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後は、**[Zscaler Beta の設定]** を選択すると Zscaler Beta アプリケーションが表示されます。 そこから、管理者資格情報を入力して Zscaler Beta にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Zscaler Beta を手動で設定するには、新しい Web ブラウザー ウィンドウを開きます。 Zscaler Beta の会社サイトに管理者としてサインインし、次の手順を実行します。

4. **[Administration]\(管理\)**  >  **[Authentication]\(認証\)**  >  **[Authentication Settings]\(認証設定\)** の順に選択し、次の手順を実行します。
   
    ![管理](./media/zscaler-beta-tutorial/ic800206.png "管理")

    a. **[Authentication Type]\(認証タイプ\)** で **[SAML]** を選択します。

    b. **[Configure SAML]\(SAML の構成\)** を選択します。

5. **[Edit SAML]\(SAML の編集\)** ウィンドウで、次の手順に従います。 
            
    ![ユーザーと認証の管理](./media/zscaler-beta-tutorial/ic800208.png "[ユーザーと認証の管理]")
    
    a. **[SAML Portal URL]\(SAML ポータル URL\)** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。

    b. **[Login Name Attribute]\(ログイン名属性\)** ボックスに **NameID** の値を入力します。

    c. **[パブリック SSL 証明書]** ボックスで、**[アップロード]** を選択して、Azure portal からダウンロードした Azure SAML 署名証明書をアップロードします。

    d. **[SAML 自動プロビジョニングを有効にする]** を切り替えます。

    e. displayName 属性で SAML 自動プロビジョニングを有効にするには、**[User Display Name Attribute]\(ユーザー表示名属性\)** ボックスに **displayName** の値を入力します。

    f. memberOf 属性で SAML 自動プロビジョニングを有効にするには、**[Group Name Attribute]\(グループ名属性\)** ボックスに **memberOf** の値を入力します。

    g. department 属性で SAML 自動プロビジョニングを有効にするには、**[Department Name Attribute]\(部署名属性\)** ボックスに **department** の値を入力します。

    h. **[保存]** を選択します。

6. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![[アクティブ化] メニューと [アクティブ化] ボタン](./media/zscaler-beta-tutorial/ic800207.png)

    a. 左下の **[Activation]\(アクティブ化\)** メニューにポインターを置きます。

    b. **[アクティブ化]** を選びます。

## <a name="configure-proxy-settings"></a>プロキシ設定の構成
Internet Explorer でプロキシ設定を構成するには、次の手順に従ってください。

1. **Internet Explorer** を開始します。

2. **[ツール]** メニューの **[インターネット オプション]** を選択し、**[インターネット オプション]** ダイアログ ボックスを開きます。 
    
     ![[インターネット オプション] ダイアログ ボックス](./media/zscaler-beta-tutorial/ic769492.png "[インターネット オプション]")

3. **[接続]** タブを選択します。 
  
     ![[接続] タブ](./media/zscaler-beta-tutorial/ic769493.png "接続")

4. **[LAN の設定]** を選択して **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログ ボックスを開きます。

5. **[プロキシ サーバー]** セクションで、次の手順に従います。 
   
    ![[プロキシ サーバー] セクション](./media/zscaler-beta-tutorial/ic769494.png "プロキシ サーバー")

    a. **[LAN にプロキシ サーバーを使用する]** チェック ボックスを選択します。

    b. **[アドレス]** ボックスに「**gateway.Zscaler Beta.net**」と入力します。

    c. **[ポート]** ボックスに「**80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]** チェック ボックスを選択します。

    e. **[OK]** を選択して **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログ ボックスを閉じます。

6. **[OK]** を選択して **[インターネット オプション]** ダイアログ ボックスを閉じます。

### <a name="create-zscaler-beta-test-user"></a>Zscaler Beta テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Zscaler Beta に作成します。 Zscaler Beta では、**Just-In-Time ユーザー プロビジョニング** がサポートされています。この設定は既定で有効になっています。 このセクションでは実行することはありません。 Zscaler Beta にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成するには、[Zscaler Beta サポート チーム](https://www.zscaler.com/company/contact)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zscaler Beta のサインオン URL にリダイレクトされます。 

* Zscaler Beta のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zscaler Beta] タイルをクリックすると、Zscaler Beta のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Zscaler Beta を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
