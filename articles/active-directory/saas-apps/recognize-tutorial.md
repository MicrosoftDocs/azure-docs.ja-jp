---
title: チュートリアル:Azure Active Directory と Recognize の統合 | Microsoft Docs
description: Azure Active Directory と Recognize の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 82d231eff4690dc060c01d41356933693d08c443
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093252"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>チュートリアル:Azure Active Directory と Recognize の統合

このチュートリアルでは、Recognize と Azure Active Directory (Azure AD) を統合する方法について説明します。
Recognize と Azure AD の統合には、次の利点があります。

* Recognize にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Recognize に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Recognize の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Recognize でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Recognize では、**SP** Initiated SSO がサポートされます

## <a name="adding-recognize-from-the-gallery"></a>ギャラリーからの Recognize の追加

Azure AD への Recognize の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Recognize を追加する必要があります。

**ギャラリーから Recognize を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Recognize**」と入力し、結果パネルで **Recognize** を選択してから **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Recognize](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Recognize で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Recognize 内の関連ユーザー間にリンク関係が確立されている必要があります。

Recognize で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Recognize シングル サインオンの構成](#configure-recognize-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Recognize テスト ユーザーの作成](#create-recognize-test-user)** - Recognize で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Recognize で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Recognize** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4.  **[基本的な SAML 構成]**  セクションで、 **サービス プロバイダー メタデータ ファイル**がある場合は、次の手順に従います。

    >[!NOTE]
    >チュートリアルの「**Recognize シングルサインオンの構成**」セクションで、**サービス プロバイダー メタデータ ファイル**を取得します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子**の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    ![[Recognize のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

     **[サインオン URL]** ボックスに、`https://recognizeapp.com/<your-domain>/saml/sso` の形式で URL を入力します。

    > [!Note]
    > **識別子**の値が自動的に設定されない場合は、チュートリアルの後の方で説明する「**Recognize シングル サインオンの構成**」セクションの「SSO 設定」セクションでサービス プロバイダー メタデータ URL を開いて、識別子の値を取得します。 サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Recognize クライアント サポート チーム](mailto:support@recognizeapp.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Recognize のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-recognize-single-sign-on"></a>Recognize シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Recognize テナントにサインインします。

2. 右上にある **[Menu (メニュー)]** をクリックします。 **[Company Admin (会社の管理者)]** に移動します。
   
    ![アプリ側でのシングル サインオンの構成](./media/recognize-tutorial/tutorial_recognize_000.png)

3. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]** をクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/recognize-tutorial/tutorial_recognize_001.png)

4. **[SSO Settings (SSO 設定)]** セクションで、次の手順に従います。
   
    ![アプリ側でのシングル サインオンの構成](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. **[Enable SSO (SSO を有効にする)]** を **[ON]** にします。

    b. **[IDP Entity ID]\(IDP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。
    
    c. **[Sso target url]\(SSO ターゲット URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。
    
    d. **[Slo target url]\(SLO ターゲット URL\)** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。 
    
    e. ダウンロードした**証明書 (Base64)** ファイルをメモ帳で開き、その内容をクリップボードにコピーし、 **[証明書]** ボックスに貼り付けます。
    
    f. **[Save settings (設定の保存)]** ボタンをクリックします。 

5. **[SSO Settings (SSO 設定)]** セクションに、**Service Provider Metadata url** の URL をコピーします。
   
    ![アプリ側でのシングル サインオンの構成](./media/recognize-tutorial/tutorial_recognize_003.png)

6. 空白のブラウザーで**メタデータ URL リンク**を開き、メタデータ ドキュメントをダウンロードします。 次に、ファイルから EntityDescriptor 値 (entityID) をコピーし、Azure portal の **[基本的な SAML 構成]** の **[識別子]** ボックスに貼り付けます。
    
    ![アプリ側でのシングル サインオンの構成](./media/recognize-tutorial/tutorial_recognize_004.png)

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

このセクションでは、Britta Simon に Recognize へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Recognize]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Recognize]** を選択します。

    ![アプリケーションの一覧の Recognize のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-recognize-test-user"></a>Recognize テスト ユーザーの作成

Azure AD ユーザーが Recognize にログインできるようにするには、そのユーザーを Recognize にプロビジョニングする必要があります。 Recognize の場合、プロビジョニングは手動で行います。

このアプリは SCIM プロビジョニングをサポートしていませんが、ユーザーをプロビジョニングする代替ユーザー同期があります。 

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Recognize 企業サイトに管理者としてサインインします。

2. 右上にある **[Menu (メニュー)]** をクリックします。 **[Company Admin (会社の管理者)]** に移動します。

3. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]** をクリックします。

4. **[User Sync (ユーザー同期)]** セクションで、次の手順に従います。
   
    ![New User](./media/recognize-tutorial/tutorial_recognize_005.png "New User")
   
    a. **[Sync Enabled (同期の有効化)]** で **[ON]** を選択します。
   
    b. **[Choose sync provider (同期プロバイダーの選択)]** で、 **[Microsoft / Office 365]** を選択します。
   
    c. **[Run User Sync (ユーザー同期の実行)]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Recognize] タイルをクリックすると、SSO を設定した Recognize に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

