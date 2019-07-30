---
title: チュートリアル:Azure Active Directory と ArcGIS Online の統合 | Microsoft Docs
description: Azure Active Directory と ArcGIS Online の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db56cd7551ef8179aeff575fdd1f2578cbee74ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106707"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>チュートリアル:Azure Active Directory と ArcGIS Online の統合

このチュートリアルでは、ArcGIS Online と Azure Active Directory (Azure AD) を統合する方法について説明します。
ArcGIS Online と Azure AD の統合には、次の利点があります。

* Azure AD で誰が ArcGIS Online にアクセスできるかを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に ArcGIS Online にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と ArcGIS Online の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* ArcGIS Online でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ArcGIS Online では、**SP** によって開始される SSO がサポートされます

## <a name="adding-arcgis-online-from-the-gallery"></a>ギャラリーからの ArcGIS Online の追加

ArcGIS Online の Azure AD への統合を構成するには、ArcGIS Online をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから ArcGIS Online を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**ArcGIS Online**」と入力し、結果パネルで **[ArcGIS Online]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の ArcGIS Online](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、ArcGIS Online で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと ArcGIS Online 内の関連ユーザー間にリンク関係が確立されている必要があります。

ArcGIS Online で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[ArcGIS Online シングル サインオンの構成](#configure-arcgis-online-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[ArcGIS Online のテスト ユーザーの作成](#create-arcgis-online-test-user)** - ArcGIS Online で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

ArcGIS Online で Azure AD のシングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **[ArcGIS Online]** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[ArcGIS Online Domain and URLs] \(ArcGIS Online のドメインと URL\) のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.maps.arcgis.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`<companyname>.maps.arcgis.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[ArcGIS Online クライアント サポート チーム](https://support.esri.com/en/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **ArcGIS Online** 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして**マイアプリによるセキュリティで保護されたサインイン拡張機能** をインストールする必要があります。

    ![image](./media/arcgis-tutorial/install_extension.png)

7. ブラウザーに拡張機能を追加した後、 **[setup ArcGIS Online]\(ArcGIS Online のセットアップ)** をクリックすると、ArcGIS Online アプリケーションに移動します。 そこから、管理者資格情報を提供して ArcGIS Online にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、「**ArcGIS Online シングル サインオンの構成**」セクションの手順が自動化されます。

### <a name="configure-arcgis-online-single-sign-on"></a>ArcGIS Online シングル サインオンの構成

1. ArcGIS Online を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として ArcGIS Online 企業サイトにログインして、次の手順を実行します。

2. **[EDIT SETTINGS] \(設定の編集)** をクリックします。

    ![Edit Settings (設定の編集)](./media/arcgis-tutorial/ic784742.png "Edit Settings")

3. **[セキュリティ]** をクリックします。

    ![Security (セキュリティ)](./media/arcgis-tutorial/ic784743.png "Security")

4. **[Enterprise Logins] [エンタープライズ ログイン]** で、 **[SET IDENTITY PROVIDER] \(ID プロバイダーの設定)** をクリックします。

    ![Enterprise Logins (エンタープライズ ログイン)](./media/arcgis-tutorial/ic784744.png "Enterprise Logins")

5. **[Set Identity Provider]** 構成ページで、次の手順に従います。

    ![Set Identity Provider (ID プロバイダーの設定)](./media/arcgis-tutorial/ic784745.png "Set Identity Provider")

    a. **[名前]** テキスト ボックスに組織の名前を入力します。

    b. **[Metadata for the Enterprise Identity Provider will be supplied using]** で、 **[A File]** を選択します。

    c. ダウンロードしたメタデータ ファイルをアップロードするには、 **[Choose file]** をクリックします。

    d. **[SET IDENTITY PROVIDER] \(ID プロバイダーの設定)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ArcGIS Online へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[ArcGIS Online]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**ArcGIS Online**」と入力して選択します。

    ![アプリケーションの一覧の ArcGIS Online のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-arcgis-online-test-user"></a>ArcGIS Online のテスト ユーザーの作成

Azure AD ユーザーが ArcGIS Online にログインできるようにするには、そのユーザーを ArcGIS Online にプロビジョニングする必要があります。  
ArcGIS Online の場合、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ArcGIS** テナントにログインします。

2. **[INVITE MEMBERS] \(メンバーの招待)** をクリックします。
   
    ![Invite Members (メンバーの招待)](./media/arcgis-tutorial/ic784747.png "Invite Members")

3. **[Add members automatically without sending an email] \(電子メールを送信せずにメンバーを自動的に追加する)** を選択し、 **[NEXT] \(次へ)** をクリックします。
   
    ![メンバーの自動追加](./media/arcgis-tutorial/ic784748.png "Add Members Automatically")

4. **[Members]** ダイアログ ページで、次の手順に従います。
   
     ![追加とレビュー](./media/arcgis-tutorial/ic784749.png "Add and review")
    
     a. プロビジョニングする有効な AAD アカウントの **[電子メール]** 、 **[名]** 、および **[姓]** を入力します。
  
     b. **[ADD AND REVIEW] \(追加とレビュー)** をクリックします。
5. 入力したデータを確認してから、 **[ADD MEMBERS] \(メンバーの追加)** をクリックします。
   
    ![メンバーの追加](./media/arcgis-tutorial/ic784750.png "Add member")
        
    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [ArcGIS Online] タイルをクリックすると、SSO を設定した ArcGIS Online に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

