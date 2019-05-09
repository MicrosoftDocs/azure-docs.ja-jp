---
title: チュートリアル:Azure Active Directory と MOVEit Transfer - Azure AD integration の統合 | Microsoft Docs
description: Azure Active Directory と MOVEit Transfer - Azure AD integration の間でシングル サインオンを構成する方法について確認します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: d7020299bbd52f5e7ba22809847815cb04048cb6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259407"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>チュートリアル:Azure Active Directory と MOVEit Transfer - Azure AD integration の統合

このチュートリアルでは、MOVEit Transfer - Azure AD integration と Azure Active Directory (Azure AD) を統合する方法について説明します。
MOVEit Transfer - Azure AD integration と Azure AD の統合には、次の利点があります。

* MOVEit Transfer - Azure AD integration にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して MOVEit Transfer - Azure AD integration に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と MOVEit Transfer - Azure AD integration の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* MOVEit Transfer - Azure AD integration でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* MOVEit Transfer - Azure AD integration では、**SP** によって開始される SSO がサポートされます

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>ギャラリーからの MOVEit Transfer - Azure AD integration の追加

Azure AD への MOVEit Transfer - Azure AD integration の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MOVEit Transfer - Azure AD integration を追加する必要があります。

**ギャラリーから MOVEit Transfer - Azure AD integration を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**MOVEit Transfer - Azure AD integration**」と入力し、結果パネルで **MOVEit Transfer - Azure AD integration** を選び、**[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の MOVEit Transfer - Azure AD integration](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、MOVEit Transfer - Azure AD integration で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと MOVEit Transfer - Azure AD integration 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

MOVEit Transfer - Azure AD integration で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[MOVEit Transfer - Azure AD integration のシングル サインオンの構成](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[MOVEit Transfer - Azure AD integration のテスト ユーザーの作成](#create-moveit-transfer---azure-ad-integration-test-user)** - MOVEit Transfer - Azure AD integration で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

MOVEit Transfer - Azure AD integration で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **MOVEit Transfer - Azure AD integration** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル**がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、**[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子**と **[応答 URL]** の値が、**[基本的な SAML 構成]** セクションに自動的に設定されます。

    ![MOVEit Transfer - Azure AD integration のドメインと URL のシングル サインオン情報](common/sp-identifier-reply.png)

    **[サインオン URL]** ボックスに、`https://contoso.com` という形式で URL を入力します。

    > [!NOTE]
    > **[サインオン URL]** は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[MOVEit Transfer - Azure AD integration クライアント サポート](https://community.ipswitch.com/s/support) チームに問い合わせてください。 **サービス プロバイダー メタデータ ファイル**は、チュートリアルの「**MOVEit Transfer - Azure AD integration のシングル サインオンの構成**」セクションで後述する**サービス プロバイダー メタデータ URL** からダウンロードできます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[MOVEit Transfer - Azure AD integration のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>MOVEit Transfer - Azure AD integration のシングル サインオンの構成

1. MOVEit Transfer テナントに管理者としてサインオンします。

2. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]** をクリックします。

    ![アプリ側の [Settings]\(設定\) セクション](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. **[Security Policies]\(セキュリティ ポリシー\) -> [User Auth]\(ユーザー認証\)** にある **[Single Signon]\(シングル サインオン\)** リンクをクリックします。

    ![アプリ側のセキュリティ ポリシー](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. メタデータ URL リンクをクリックし、メタデータ ドキュメントをダウンロードします。

    ![サービス プロバイダー メタデータ URL](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * **[基本的な SAML 構成]** セクションで、**entityID** が**識別子**と一致することを確認します。
   * **AssertionConsumerService** の場所 URL が **[基本的な SAML 構成]** セクションの **[応答 URL]** と一致していることを確認します。
    
     ![アプリ側でのシングル サインオンの構成](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. **[Add Identity Provider (ID プロバイダーの追加)]** ボタンをクリックして新しいフェデレーション ID プロバイダーを追加します。

    ![ID プロバイダーの追加](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. **[Browse]\(参照\)** をクリックして Azure Portal からダウンロードしたメタデータ ファイルを選び、**[Add Identity Provider]\(ID プロバイダーの追加\)** をクリックしてダウンロードしたファイルをアップロードします。

    ![SAML ID プロバイダー](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. **[Edit Federated Identity Provider Settings (フェデレーション ID プロバイダーの設定の編集)]** ページの **[Enabled (有効)]** で **[Yes (はい)]** を選択し、**[Save (保存)]** をクリックします。

    ![フェデレーション ID プロバイダーの設定](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. **[Edit Federated Identity Provider User Settings]\(フェデレーション ID プロバイダー ユーザーの設定の編集\)** ページで、次の操作を実行します。
    
    ![フェデレーション ID プロバイダーの設定の編集](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. **[Login name (ログイン名)]** として **[SAML NameID]** を選択します。
    
    b. **[Full name]\(フル ネーム\)** として **[Other]\(その他\)** を選び、**[Attribute name]\(属性名\)** ボックスに値「`http://schemas.microsoft.com/identity/claims/displayname`」を入力します。
    
    c. **[Email]\(電子メール\)** として **[Other]\(その他\)** を選び、**[Attribute name]\(属性名\)** ボックスに値「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」を入力します。
    
    d. **[Auto-create account on signon (サインオン時にアカウントを自動作成する)]** で **[Yes (はい)]** を選択します。
    
    e. **[保存]** ボタンをクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、MOVEit Transfer - Azure AD integration へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[MOVEit Transfer - Azure AD integration]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[MOVEit Transfer - Azure AD integration]** を選択します。

    ![アプリケーション一覧の MOVEit Transfer - Azure AD integration リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit Transfer - Azure AD integration のテスト ユーザーの作成

このセクションの目的は、MOVEit Transfer - Azure AD integration で Britta Simon というユーザーを作成することです。 MOVEit Transfer - Azure AD integration では、Just-In-Time プロビジョニングがサポートされています。この設定は有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合は、MOVEit Transfer - Azure AD integration へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[MOVEit Transfer - Azure AD integration クライアント サポート チーム](https://community.ipswitch.com/s/support)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [MOVEit Transfer - Azure AD integration] タイルをクリックすると、SSO を設定した MOVEit Transfer - Azure AD integration に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

