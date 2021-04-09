---
title: チュートリアル:Azure Active Directory と Work.com の統合 | Microsoft Docs
description: Azure Active Directory と Work.com の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: a77b6472ea91a6ba4b6e31d1b7739aae236a1783
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636155"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>チュートリアル:Azure Active Directory と Work.com の統合

このチュートリアルでは、Work.com と Azure Active Directory (Azure AD) を統合する方法について説明します。
Work.com と Azure AD の統合には、次の利点があります。

* Work.com にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Work.com に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Work.com と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Work.com でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Work.com では、**SP** によって開始される SSO がサポートされます

## <a name="adding-workcom-from-the-gallery"></a>ギャラリーからの Work.com の追加

Azure AD への Work.com の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Work.com を追加する必要があります。

**ギャラリーから Work.com を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Work.com**」と入力し、結果パネルで **[Work.com]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Work.com](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Work.com で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Work.com 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Work.com で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Work.com のシングル サインオンの構成](#configure-workcom-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Work.com のテスト ユーザーの作成](#create-workcom-test-user)** - Work.com で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

>[!NOTE]
>シングル サインオンを構成する場合でも、カスタムの Work.com ドメイン名をセットアップする必要があります。 少なくともドメイン名を定義し、ドメイン名をテストしてから、組織全体にデプロイする必要があります。

Work.com で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Work.com** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Work.com のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`http://<companyname>.my.salesforce.com` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Work.com クライアント サポート チーム](https://help.salesforce.com/articleView?id=000159855&type=3)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Work.com のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-workcom-single-sign-on"></a>Work.com のシングル サインオンの構成

1. 管理者として Work.com テナントにサインインします。

2. **[セットアップ]** に移動します。
   
    ![ユーザー メニューから [Setup]\(セットアップ\) が選択されていることを示すスクリーンショット。](./media/work-com-tutorial/ic794108.png "セットアップ")

3. 左側のナビゲーション ウィンドウの **[管理]** セクションで、 **[ドメインの管理]** をクリックして関連するセクションを展開し、 **[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。 
   
    ![[Administer]\(管理\) ペインの [Domain Management]\(ドメインの管理\) から [My Domain]\(マイ ドメイン\) が選択されていることを示すスクリーンショット。](./media/work-com-tutorial/ic767825.png "[マイ ドメイン]")

4. ドメインが正しく設定されていることを確認するには、“**ステップ 4 ユーザーへのデプロイ**” で “**マイ ドメインの設定**” を確認します。
   
    ![ユーザーにデプロイされたドメイン](./media/work-com-tutorial/ic784377.png "ユーザーにデプロイされたドメイン")

5. Work.com テナントにサインインします。

6. **[セットアップ]** に移動します。
    
    ![ユーザー メニューから [Setup]\(セットアップ\) が選択されていることを示すスクリーンショット。](./media/work-com-tutorial/ic794108.png "セットアップ")

7. **[セキュリティ制御]** メニューを展開表示し、 **[シングル サインオンの設定]** をクリックします。
    
    ![シングル サインオンの設定](./media/work-com-tutorial/ic794113.png "[シングル サインオンの設定]")

8. **[シングル サインオンの設定]** ダイアログ ページで、次の手順を実行します。
    
    ![[SAML Enabled]\(SAML の有効化\)](./media/work-com-tutorial/ic781026.png "[SAML Enabled]")
    
    a. **[SAML Enabled]** を選択します。
    
    b. **[新規作成]** をクリックします。

9. **[SAML シングル サインオンの設定]** セクションで、次の手順に従います。
    
    ![[SAML Single Sign-On Setting]\(SAML シングル サインオンの設定\)](./media/work-com-tutorial/ic794114.png "SAML シングル サインオンの設定")
    
    a. **[名前]** テキスト ボックスに、構成の名前を入力します。  
       
    > [!NOTE]
    > **[名前]** の値を指定すると、 **[API 名]** ボックスが自動的に入力されます。
    
    b. **[Issuer]\(発行者\)** テキスト ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。
    
    c. **[Browse]\(参照\)** をクリックして、Azure Portal からダウンロードした証明書をアップロードします。
    
    d. **[Entity Id]\(エンティティ ID\)** ボックスに、「`https://salesforce-work.com`」と入力します。
    
    e. **[SAML ID の種類]** として、 **[アサーションにはユーザー オブジェクトからのフェデレーション ID が含まれます]** を選択します。
    
    f. **[SAML ID の場所]** として、 **[ID を Subject ステートメントの NameIdentifier 要素にする]** チェックボックスをオンにします。
    
    g. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    h. **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** テキスト ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。
    
    i. **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP POST]** を選択します。
    
    j. **[保存]** をクリックします。

10. Work.com クラシック ポータルで、左側のナビゲーション ウィンドウの **[ドメインの管理]** をクリックして関連するセクションを展開し、 **[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。 
    
    ![[Domain Management]\(ドメインの管理\) から [My Domain]\(マイ ドメイン\) が選択されていることを示すスクリーンショット。](./media/work-com-tutorial/ic794115.png "[マイ ドメイン]")

11. **[マイ ドメイン]** ページの **[Login Page Branding (ログイン ページのブランド)]** をクリックして、 **[編集]** をクリックします。
    
    ![[Login Page Branding]\(ログイン ページのブランド\) セクションを示すスクリーンショット。ここで、[edit]\(編集\) を選択できます。](./media/work-com-tutorial/ic767826.png "ログイン ページのブランド")

12. **[Login Page Branding (ログイン ページのブランド)]** ページの **[認証サービス]** セクションに、 **[SAML SSO 設定]** の名前が表示されます。 それを選んで、 **[保存]** をクリックします。
    
    ![[Login Page Branding]\(ログイン ページのブランド\) を示すスクリーンショット。ここで、設定の名前 (P P E) を選択できます。](./media/work-com-tutorial/ic784366.png "ログイン ページのブランド")

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Work.com へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Work.com]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Work.com]** を選択します。

    ![アプリケーションの一覧の Work.com のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-workcom-test-user"></a>Work.com のテスト ユーザーの作成

Azure Active Directory ユーザーがサインインできるように、Azure Active Directory ユーザーを Work.com に対してプロビジョニングする必要があります。 Work.com の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。

1. Work.com 企業サイトに管理者としてサインオンします。

2. **[セットアップ]** に移動します。
   
    ![ユーザー メニューから [Setup]\(セットアップ\) が選択されていることを示すスクリーンショット。](./media/work-com-tutorial/ic794108.png "セットアップ")

3. **[ユーザーの管理]\>[ユーザー]** に移動します。
   
    ![ユーザーの管理](./media/work-com-tutorial/IC784369.png "[Manage Users]")

4. **[新しいユーザー]** をクリックします。
   
    ![[All Users]\(すべてのユーザー\)](./media/work-com-tutorial/IC794117.png "すべてのユーザー")

5. [User Edit]\(ユーザーの編集\) セクションで、次の手順に従って、プロビジョニングする有効な Azure AD アカウントの属性を関連するボックスに入力します。
   
    ![[User Edit]\(ユーザーの編集\)](./media/work-com-tutorial/ic794118.png "ユーザーの編集")
   
    a. **[First Name]\(名\)** ボックスに、ユーザーの **名** (**Britta**) を入力します。
    
    b. **[Last Name]\(姓\)** ボックスに、ユーザーの **姓** (**Simon**) を入力します。
    
    c. **[Alias]\(エイリアス\)** ボックスに、ユーザーの **名前** (**BrittaS**) を入力します。
    
    d. **[Email]\(メール\)** ボックスに、ユーザーの **メール アドレス** (Brittasimon@contoso.com) を入力します。
    
    e. **[User Name]\(ユーザー名\)** ボックスに、ユーザーのユーザー名 (Brittasimon@contoso.com など) を入力します。
    
    f. **[Nick Name]\(ニック ネーム\)** ボックスに、ユーザーの **ニック ネーム** (**Simon**) を入力します。
    
    g. **[Role]\(役割\)** 、 **[User License]\(ユーザー ライセンス\)** 、 **[Profile]\(プロファイル\)** を選択します。
    
    h. **[保存]** をクリックします。  
      
    > [!NOTE]
    > アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。
    > 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Work.com] タイルをクリックすると、SSO を設定した Work.com に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)