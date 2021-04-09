---
title: 'チュートリアル: Azure Active Directory と TOPdesk - Public の統合 | Microsoft Docs'
description: Azure Active Directory と TOPdesk - Public の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: 3688dc2db67c0c1343a74be4652cb52466ef762e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520440"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>チュートリアル: Azure Active Directory と TOPdesk - Public の統合

このチュートリアルでは、TOPdesk - Public と Azure Active Directory (Azure AD) を統合する方法について説明します。
TOPdesk - Public と Azure AD の統合には、次の利点があります。

* TOPdesk - Public にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで TOPdesk - Public (シングル サインオン) に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

TOPdesk - Public と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* TOPdesk - Public でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TOPdesk - Public では、**SP** によって開始される SSO がサポートされます

## <a name="adding-topdesk---public-from-the-gallery"></a>ギャラリーから TOPdesk - Public を追加する

Azure AD への TOPdesk - Public の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから TOPdesk - Public を追加する必要があります。

**ギャラリーから TOPdesk - Public を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**TOPdesk - Public**」と入力して、結果パネルで **[TOPdesk - Public]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の TOPdesk - Public](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、TOPdesk - Public で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと TOPdesk - Public 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

TOPdesk - Public で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[TOPdesk - Public のシングル サインオンの構成](#configure-topdesk---public-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[TOPdesk - Public のテスト ユーザーの作成](#create-topdesk---public-test-user)** - TOPdesk - Public で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

TOPdesk - Public で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Microsoft Azure portal](https://portal.azure.com/) の **TOPdesk - Public** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4.  **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    >[!NOTE]
    >チュートリアルで後述する **[Configure TOPdesk - Public Single Sign-On]**(TOPdesk - Public シングル サインオンの構成) セクションから **サービス プロバイダー メタデータ ファイル** を取得します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。
    
    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    ![[TOPdesk - Public のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    d. **[サインオン URL]** ボックスに、`https://<companyname>.topdesk.net` という形式で URL を入力します。

    e. **[Identifier URL]\(識別子 URL\)** ボックスに、TOPdesk 構成から取得できる TOPdesk メタデータ URL を入力します。 これには次のパターンを使用する必要があります: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. **[応答 URL]** ボックスに、`https://<companyname>.topdesk.net/tas/public/login/verify` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > **[識別子]** と **[応答 URL]** の値が自動的に入力されない場合は、手動で入力する必要があります。 [識別子] については前述のパターンに従い、チュートリアルで後述する **[Configure TOPdesk - Public Single Sign-On]**(TOPdesk - Public シングル サインオンの構成) セクションから [応答 URL] の値を取得します。 この **[サインオン URL]** の値は実際のものとは異なるので、実際のサインオン URL で更新する必要があります。 この値を取得するには、[TOPdesk - Public クライアント サポート チーム](https://help.topdesk.com/saas/enterprise/user/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Set up TOPdesk - Public]**(TOPdesk - Public のセットアップ) セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-topdesk---public-single-sign-on"></a>TOPdesk - Public シングル サインオンの構成

1. **TOPdesk - Public** 会社サイトに管理者としてサインインします。

2. [**TOPdesk**] メニューで、[**設定**] をクリックします。
   
    ![[設定]](./media/topdesk-public-tutorial/ic790598.png "設定")

3. [**ログイン設定**] をクリックします。
   
    ![ログイン設定](./media/topdesk-public-tutorial/ic790599.png "[Login Settings]")

4. [**ログイン設定**] メニューを展開し、[**全般**] をクリックします。
   
    ![全般](./media/topdesk-public-tutorial/ic790600.png "全般")

5. [**SAML ログイン**] 構成セクションの [**公開**] セクションで、次の手順を実行します。
   
    ![技術設定](./media/topdesk-public-tutorial/ic790601.png "技術設定")
   
    a. [**ダウンロード**] をクリックして公開メタデータ ファイルをダウンロードし、コンピューターのローカルに保存します。
   
    b. ダウンロードしたメタデータ ファイルを開いて、**AssertionConsumerService** ノードを探します。

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. **[AssertionConsumerService]** の値をコピーし、**[基本的な SAML 構成]** セクションの **[応答 URL]** テキストボックスにこの値を貼り付けます。      
   
6. 証明書ファイルを作成するには、次の手順を実行します。
    
    ![[MSSQLSERVER のプロトコルのプロパティ]](./media/topdesk-public-tutorial/ic790606.png "Certificate")
    
    a. Azure Portal からダウンロードしたメタデータ ファイルを開きます。
    
    b. **RoleDescriptor** ノードを展開します。ここには **fed:ApplicationServiceType** の **xsi:type** があります。
    
    c. **X509Certificate** ノードの値をコピーします。
    
    d. コピーした **X509Certificate** の値をコンピューターのローカルのファイルに保存します。

7. [**公開**] セクションで、[**追加**] をクリックします。
    
    ![SAML ログイン](./media/topdesk-public-tutorial/ic790625.png "[Public]")

8. [**SAML 構成アシスタント**] ダイアログ ページで、次の手順を実行します。
    
    ![SAML 構成アシスタント](./media/topdesk-public-tutorial/ic790608.png "[SAML configuration assistant]")
    
    a. Azure Portal からダウンロードしたメタデータ ファイルをアップロードするには、**[Federation Metadata]** で **[Browse]** をクリックします。

    b. 証明書ファイルをアップロードするには、[**証明書 (RSA)**] の下の [**参照**] をクリックします。

    c. TOPdesk サポート チームから取得したロゴ ファイルをアップロードするには、[**ロゴ アイコン**] の下の [**参照**] をクリックします。

    d. **[User name attribute]** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    e. [**表示名**] テキストボックスに、構成の名前を入力します。

    f. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TOPdesk - Public へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[TOPdesk - Public]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[TOPdesk - Public]** を選択します。

    ![アプリケーションの一覧の TOPdesk - Public のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-topdesk---public-test-user"></a>TOPdesk - Public のテスト ユーザーの作成

Azure AD ユーザーが TOPdesk - Public にサインインできるようにするには、そのユーザーを TOPdesk - Public にプロビジョニングする必要があります。 TOPdesk - Public の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **TOPdesk - Public** 企業サイトに管理者としてサインオンします。

2. 上部のメニューで、**[TOPdesk]\>[New]\>[Support Files]\>[Person]** の順にクリックします。
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "人物")

3. [New Person] ダイアログで、次の手順を実行します。
   
    ![新しいユーザー](./media/topdesk-public-tutorial/ic790629.png "新しいユーザー")
   
    a. [General] タブをクリックします。

    b. **[Surname]** ボックスに、ユーザーの姓を入力します (この例では Simon)。
 
    c. アカウントの [**サイト**] を選択します。
 
    d. **[保存]** をクリックします。

> [!NOTE]
> 他の TOPdesk - Public ユーザー アカウントの作成ツールまたは TOPdesk - Public から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [TOPdesk - Public] タイルをクリックすると、SSO を設定した TOPdesk - Public に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)