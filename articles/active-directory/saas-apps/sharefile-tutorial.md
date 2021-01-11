---
title: 'チュートリアル: Azure Active Directory と Citrix ShareFile の統合 | Microsoft Docs'
description: Azure Active Directory と Citrix ShareFile の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/18/2020
ms.author: jeedes
ms.openlocfilehash: cfbb704799a1884c689bd0de547526a33f1ba7ce
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651900"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>チュートリアル: Azure Active Directory と Citrix ShareFile の統合

このチュートリアルでは、Citrix ShareFile と Azure Active Directory (Azure AD) を統合する方法について説明します。
Citrix ShareFile と Azure AD の統合には、次の利点があります。

* Citrix ShareFile にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが Azure AD アカウントで自動的に Citrix ShareFile にサインインできるようにします (シングル サインオン)。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Citrix ShareFile の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Citrix ShareFile でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Citrix ShareFile では、**SP** によって開始される SSO がサポートされます
* Citrix ShareFile を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-citrix-sharefile-from-the-gallery"></a>ギャラリーからの Citrix ShareFile の追加

Azure AD への Citrix ShareFile の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Citrix ShareFile を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Citrix ShareFile**」と入力します。
1. 結果のパネルから **[Citrix ShareFile]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Citrix ShareFile で Azure AD シングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Citrix ShareFile 内の関連ユーザー間にリンク関係が確立されている必要があります。

Citrix ShareFile で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Citrix ShareFile の SSO の構成](#configure-citrix-sharefile-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Citrix ShareFile テスト ユーザーの作成](#create-citrix-sharefile-test-user)** - Citrix ShareFile で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Citrix ShareFile** アプリケーション統合ページ上で、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.sharefile.com/saml/login` という形式で URL を入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. **[応答 URL]** ボックスに、 のパターンを使用して URL を入力します。
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得する場合は、[Citrix ShareFile クライアント サポート チーム](https://www.citrix.co.in/products/citrix-content-collaboration/support.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Citrix ShareFile のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

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

このセクションでは、Citrix ShareFile へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Citrix ShareFile]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-citrix-sharefile-sso"></a>Citrix ShareFile の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として **Citrix ShareFile** の会社サイトにログインします。

1. 左側のメニューで、 **[Settings]\(設定\) アイコン** ->  **[Security]\(セキュリティ\)**  ->  **[Login & Security Policy]\(ログインとセキュリティ ポリシー\)** をクリックします。
   
    ![アカウント管理](./media/sharefile-tutorial/settings-security.png "[アカウント管理]")

1. [**Single Sign-On/ SAML 2.0 Configuration**] ダイアログ ページの [**Basic Settings**] で、以下の手順を実行します。
   
    ![シングル サインオン](./media/sharefile-tutorial/saml-configuration.png "シングル サインオン")
   
    a. **[Enable SAML]\(SAML を有効にする\)** で **[はい]** を選択します。

    b. **[ShareFile Issuer/ Entity ID]\(ShareFile 発行者/エンティティ ID\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスにある **[Identifier URL]\(識別子 URL\)** ボックスに貼り付けます。
    
    c. **[Your IDP Issuer/ Entity ID]\(IDP 発行者/エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    d. **[X.509 Certificate]\(X.509証明書\)** フィールドの横の **[変更]** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。
    
    e. **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。
    
    f. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。

5. Citrix ShareFile 管理ポータルで [**Save**] をクリックします。

## <a name="create-citrix-sharefile-test-user"></a>Citrix ShareFile テスト ユーザーの作成

1. **Citrix ShareFile** テナントにログインします。

2. **[People]\(ユーザー\)**  ->  **[Manage Users Home]\(ユーザーの管理ホーム\)**  ->  **[Create New Users]\(新しいユーザーの作成\)**  ->  **[Create Employee]\(従業員の作成\)** の順にクリックします。
   
    ![従業員の作成](./media/sharefile-tutorial/create-user.png "従業員の作成")

3. **[Basic Information]** セクションで、次の手順を実行します。
   
    ![基本情報](./media/sharefile-tutorial/user-form.png "基本情報")
   
    a. **[名]** ボックスに、ユーザーの**名**を、「**Britta**」と入力します。
   
    b.  **[姓]** ボックスに、ユーザーの**姓**を、「**Simon**」と入力します。
   
    c. **[メール アドレス]** テキストボックスに、Britta Simon のメール アドレスとして「**brittasimon\@contoso.com**」と入力します。

4. **[ユーザーの追加]** をクリックします。
  
    >[!NOTE]
    >Azure AD のアカウント所有者が電子メールを受信し、リンクをたどって自分のアカウントを確認すると、アカウントがアクティブになります。Azure AD ユーザー アカウントのプロビジョニングには、他の Citrix ShareFile ユーザー アカウント作成ツールまたは Citrix ShareFile が提供する API を使用できます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Citrix ShareFile] タイルをクリックすると、SSO を設定した Citrix ShareFile に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

