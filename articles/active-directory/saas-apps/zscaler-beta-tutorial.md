---
title: チュートリアル:Azure Active Directory と Zscaler Beta の統合 | Microsoft Docs
description: Azure Active Directory と Zscaler Beta の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07341c1ad30f1242bdff430826fdc82c45e09dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086069"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>チュートリアル:Azure Active Directory と Zscaler Beta の統合

このチュートリアルでは、Zscaler Beta と Azure Active Directory (Azure AD) を統合する方法について説明します。
Zscaler Beta を Azure AD に統合すると、次のことを実行できます。

* Zscaler Beta にアクセスするユーザーを Azure AD で管理する。
* ユーザーが Azure AD アカウントを使用して Zscaler Beta に自動的にサインインできるようにする。 このアクセス制御はシングル サインオン (SSO) と呼ばれます。
* Azure portal を使用して 1 つの中央サイトでアカウントを管理する。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD との統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」をご覧ください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Zscaler Beta と Azure AD の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンを使用する Zscaler Beta サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zscaler Beta では、SP によって開始される SSO がサポートされます。
* Zscaler Beta では、Just In Time ユーザー プロビジョニングがサポートされます

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Azure Marketplace から Zscaler Beta を追加する

Azure AD への Zscaler Beta の統合を構成するには、Zscaler Beta を Azure Marketplace からマネージド SaaS アプリの一覧に追加する必要があります。

Azure Marketplace から Zscaler Beta を追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] ボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに、「**Zscaler Beta**」と入力します。 結果のパネルから **[Zscaler Beta]** を選択して、 **[追加]** を選択します。

     ![結果一覧の Zscaler Beta](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon というテスト ユーザーに基づいて Zscaler Beta で Azure AD のシングル サインオンを構成してテストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Zscaler Beta の関連ユーザーとの間にリンク関係を確立します。

Zscaler Beta で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了してください。

- [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
- [Zscaler Beta シングル サインオンの構成](#configure-zscaler-beta-single-sign-on) - アプリケーション側でシングル サインオン設定を構成します。
- [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
- [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
- [Zscaler Beta テスト ユーザーの作成](#create-a-zscaler-beta-test-user) - Zscaler Beta で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
- [シングル サインオンのテスト](#test-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Zscaler Beta で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Zscaler Beta** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** を選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Zscaler Beta のドメインと URL] のシングル サインオン情報](common/sp-intiated.png)

    - **[サインオン URL]** ボックスに、ユーザーが Zscaler Beta アプリケーションへのサインインに使用する URL を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 実際のサインオン URL 値でこの値を更新します。 この値を取得するには、[Zscaler Beta クライアント サポート チーム](https://www.zscaler.com/company/contact)に問い合わせてください。

5. Zscaler Beta アプリケーションでは、特定の形式の SAML アサーションを予期しています。 カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]** を選択して **[ユーザー属性]**   ダイアログ ボックスを開きます。

    ![[ユーザー属性] ダイアログ ボックス](common/edit-attribute.png)

6. Zscaler Beta アプリケーションでは、いくつかの追加の属性が SAML 応答で返されることを予期しています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、次の手順を実行して、以下の表に示すように SAML トークン属性を追加します。
    
    | Name | ソース属性 | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. **[新しい要求の追加]** を選択して **[ユーザー要求の管理]** ダイアログ ボックスを開きます。

    ![ユーザー要求のダイアログ ボックス](common/new-save-attribute.png)

    ![[ユーザー要求の管理] ダイアログ ボックス](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に表示される属性名を入力します。

    c. **[名前空間]** ボックスは空白のままにします。

    d. **[ソース]** で **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** を選択します。

    g. **[保存]** を選択します。

    > [!NOTE]
    > Azure AD でロールを構成する方法については、[ロール要求の構成](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)に関する記事を参照してください。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択して**証明書 (Base64)** をダウンロードします。 それを自分のコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

8. **[Set up Zscaler Beta]\(Zscaler Beta の設定\)** セクションで、要件に従って必要な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    - ログイン URL
    - Azure AD 識別子
    - ログアウト URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Zscaler Beta シングル サインオンの構成

1. Zscaler Beta 内での構成を自動化するには、 **[拡張機能のインストール]** を選択して **[アプリによるセキュリティで保護されたサインイン拡張機能]** をインストールします。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後は、 **[Zscaler Beta の設定]** を選択すると Zscaler Beta アプリケーションが表示されます。 そこから、管理者資格情報を入力して Zscaler Beta にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Zscaler Beta を手動で設定するには、新しい Web ブラウザー ウィンドウを開きます。 Zscaler Beta の会社サイトに管理者としてサインインし、次の手順を実行します。

4. **[Administration]\(管理\)**  >  **[Authentication]\(認証\)**  >  **[Authentication Settings]\(認証設定\)** の順に選択し、次の手順を実行します。
   
    ![Administration](./media/zscaler-beta-tutorial/ic800206.png "Administration")

    a. **[Authentication Type]\(認証の種類\)** で **[SAML]** を選択します。

    b. **[Configure SAML]\(SAML の構成\)** を選択します。

5. **[Edit SAML]\(SAML の編集\)** ウィンドウで、次の手順に従います。 
            
    ![ユーザーと認証の管理](./media/zscaler-beta-tutorial/ic800208.png "Manage Users & Authentication")
    
    a. **[SAML Portal URL]\(SAML ポータル URL\)** ボックスに、Azure portal からコピーした**ログイン URL** を貼り付けます。

    b. **[Login Name Attribute]\(ログイン名属性\)** ボックスに **NameID** の値を入力します。

    c. **[パブリック SSL 証明書]** ボックスで、 **[アップロード]** を選択して、Azure portal からダウンロードした Azure SAML 署名証明書をアップロードします。

    d. **[SAML 自動プロビジョニングを有効にする]** を切り替えます。

    e. displayName 属性で SAML 自動プロビジョニングを有効にするには、 **[User Display Name Attribute]\(ユーザー表示名属性\)** ボックスに **displayName** の値を入力します。

    f. memberOf 属性で SAML 自動プロビジョニングを有効にするには、 **[Group Name Attribute]\(グループ名属性\)** ボックスに **memberOf** の値を入力します。

    g. department 属性で SAML 自動プロビジョニングを有効にするには、 **[Department Name Attribute]\(部署名属性\)** ボックスに **department** の値を入力します。

    h. **[保存]** を選択します。

6. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![[アクティブ化] メニューと [アクティブ化] ボタン](./media/zscaler-beta-tutorial/ic800207.png)

    a. 左下の **[Activation]\(アクティブ化\)** メニューにポインターを置きます。

    b. **[アクティブ化]** を選択します。

## <a name="configure-proxy-settings"></a>プロキシ設定の構成
Internet Explorer でプロキシ設定を構成するには、次の手順に従ってください。

1. **Internet Explorer**を開始します。

2. **[ツール]** メニューの **[インターネット オプション]** を選択し、 **[インターネット オプション]** ダイアログ ボックスを開きます。 
    
     ![[インターネット オプション] ダイアログ ボックス](./media/zscaler-beta-tutorial/ic769492.png "[インターネット オプション]")

3. **[接続]** タブを選択します。 
  
     ![[接続] タブ](./media/zscaler-beta-tutorial/ic769493.png "[接続]")

4. **[LAN の設定]** を選択して **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログ ボックスを開きます。

5. **[プロキシ サーバー]** セクションで、次の手順に従います。 
   
    ![[プロキシ サーバー] セクション](./media/zscaler-beta-tutorial/ic769494.png "[プロキシ サーバー]")

    a. **[LAN にプロキシ サーバーを使用する]** チェック ボックスを選択します。

    b. **[アドレス]** ボックスに「**gateway.Zscaler Beta.net**」と入力します。

    c. **[ポート]** ボックスに「**80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]** チェック ボックスを選択します。

    e. **[OK]** を選択して **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログ ボックスを閉じます。

6. **[OK]** を選択して **[インターネット オプション]** ダイアログ ボックスを閉じます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

Azure Portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] のリンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** ボックスに「`brittasimon@yourcompanydomain.extension`」と入力します。 例: BrittaSimon@contoso.com。

    c. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

Zscaler Beta へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Zscaler Beta]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Zscaler Beta**」と入力して選択します。

    ![アプリケーションの一覧の Zscaler Beta のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] ボタン](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、一覧から **[Britta Simon]** のようなユーザーを選択します。 次に、画面の下部にある **[選択]** を選択します。

    ![[ユーザーとグループ] ダイアログ ボックス](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. **ロールの選択** ダイアログ ボックスで、一覧から適切なユーザー ロールを選択します。 次に、画面の下部にある **[選択]** を選択します。

    ![[ロールの選択] ダイアログ ボックス](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

    ![[割り当ての追加] ダイアログ ボックス](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Zscaler Beta テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Zscaler Beta に作成します。 Zscaler Beta では、**Just-In-Time ユーザー プロビジョニング**がサポートされています。この設定は既定で有効になっています。 このセクションでは実行することはありません。 Zscaler Beta にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成するには、[Zscaler Beta サポート チーム](https://www.zscaler.com/company/contact)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Zscaler Beta] タイルを選択すると、SSO を設定した Zscaler Beta に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

