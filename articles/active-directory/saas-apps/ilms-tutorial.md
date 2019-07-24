---
title: チュートリアル:Azure Active Directory と iLMS の統合 | Microsoft Docs
description: Azure Active Directory と iLMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9bcb465f76e09675333e6e608249cba11f722e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274657"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>チュートリアル:Azure Active Directory と iLMS の統合

このチュートリアルでは、iLMS と Azure Active Directory (Azure AD) を統合する方法について説明します。
iLMS と Azure AD の統合には、次の利点があります。

* iLMS にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して iLMS に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

iLMS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* iLMS でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* iLMS では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-ilms-from-the-gallery"></a>ギャラリーからの iLMS の追加

Azure AD への iLMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iLMS を追加する必要があります。

**ギャラリーから iLMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**iLMS**」と入力し、結果パネルで **[iLMS]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の iLMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、iLMS で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと iLMS 内の関連ユーザー間にリンク関係が確立されている必要があります。

iLMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[iLMS シングル サインオンの構成](#configure-ilms-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[iLMS のテスト ユーザーの作成](#create-ilms-test-user)** - iLMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

iLMS で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **iLMS** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[iLMS のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、iLMS 管理ポータルで [SAML settings]\(SAML 設定\) の **[Service Provider]\(サービス プロバイダー\)** セクションからコピーした **[Identifier]\(識別子\)** の値を貼り付けます。

    b. **[応答 URL]** ボックスに、iLMS 管理ポータルで [SAML settings]\(SAML 設定\) の **[Service Provider]\(サービス プロバイダー\)** セクションからコピーした `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx` という形式の **[Endpoint (URL)]\(エンドポイント (URL)\)** の値を貼り付けます。

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[iLMS のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、iLMS 管理ポータルで [SAML settings]\(SAML 設定\) の **[Service Provider]\(サービス プロバイダー\)** セクションからコピーした `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx` という形式の **[Endpoint (URL)]\(エンドポイント (URL)\)** の値を貼り付けます。

6. iLMS アプリケーションは、JIT プロビジョニングを有効にするために特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]**  アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > これらの属性をマップするには、iLMS で **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** を有効にする必要があります。 属性の構成を理解するには、[こちら](http://support.inspiredelearning.com/customer/portal/articles/2204526)の手順に従ってください。

7. その他に、iLMS アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name | ソース属性|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

9. **[iLMS のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-ilms-single-sign-on"></a>iLMS のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、**iLMS 管理者ポータル**に管理者としてログインします。

2. **[Settings (設定)]** タブの **[SSO:SAML]** をクリックして SAML 設定を開き、次の手順を実行します。

    ![Configure single sign-on](./media/ilms-tutorial/1.png)

3. **[Service Provider (サービス プロバイダー)]** セクションを展開し、**[Identifier (識別子)]** と **[Endpoint (URL) (エンドポイント (URL))]** の値をコピーします。

    ![Configure single sign-on](./media/ilms-tutorial/2.png) 

4. **[Identity Provider (ID プロバイダー)]** セクションで、**[Import Metadata (メタデータのインポート)]** をクリックします。

5. Azure portal で **[SAML 署名証明書]** セクションからダウンロードした**フェデレーション メタデータ** ファイルを選択します。

    ![Configure single sign-on](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. JIT プロビジョニングを有効にして未認識のユーザーの iLMS アカウントを作成する場合は、次の手順に従います。

    a. **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** をクリックします。

    ![Configure single sign-on](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Azure AD の属性を ILMS の属性にマップします。 属性欄に、属性名または既定値を指定します。

    c. **[Business Rules (ビジネス ルール)]** タブに移動し、次の手順を実行します。

    ![Configure single sign-on](./media/ilms-tutorial/5.png)

    d. シングル サインオンの時点で存在していないリージョン、事業部、および部署を作成するには、**[Create Un-recognized Regions, Divisions and Departments (未認識のリージョン、事業部、および部署)]** をオンにします。

    e. シングル サインオンのたびにユーザー プロファイルが更新されるように指定するには、**[Update User Profile During Sign-in (サインイン中にユーザー プロファイルを更新する)]** をオンにします。

    f. **[Update Blank Values for Non Mandatory Fields in User Profile]\(ユーザー プロファイルの必須フィールド以外の空白の値を更新する\)** オプションをオンにした場合、サインイン時に空白であるオプションのプロファイル フィールドによって、ユーザーの iLMS プロファイルのオプションのフィールドが空白の値を含むように更新されます。

    g. エラー通知メールを受信する場合は、**[Send Error Notification Email (エラー通知メールを送信する)]** をオンにし、ユーザーのメール アドレスを入力します。

7. **[Save (保存)]** ボタンをクリックして、設定を保存します。

    ![Configure single sign-on](./media/ilms-tutorial/save.png)

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

このセクションでは、Britta Simon に iLMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[iLMS]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[iLMS]** を選択します。

    ![アプリケーションの一覧の [iLMS] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-ilms-test-user"></a>iLMS のテスト ユーザーの作成

アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。 iLMS 管理ポータルの SAML 構成設定で **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** チェックボックスをオンにした場合は、JIT が機能します。

ユーザーを手動で作成する必要がある場合は、以下の手順に従います。

1. iLMS 企業サイトに管理者としてログインします。

2. **[Users]\(ユーザー\)** タブの **[Register User]\(ユーザーの登録\)** をクリックして **[Register User]\(ユーザーの登録\)** ページを開きます。

   ![従業員の追加](./media/ilms-tutorial/3.png)

3. **[Register User]\(ユーザーの登録\)** ページで次の手順を実行します。

    ![従業員の追加](./media/ilms-tutorial/create_testuser_add.png)

    a. **[First Name]** ボックスに、ユーザーの名を入力します (この例では Britta)。

    b. **[Last Name]** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    c. **[Email ID]\(電子メール ID\)** ボックスに、ユーザーの電子メール アドレスを BrittaSimon@contoso.com のように入力します。

    d. **[Region (リージョン)]** ボックスの一覧からリージョンの値を選択します。

    e. **[Division (事業部)]** ボックスの一覧からリージョンの値を選択します。

    f. **[Department (部署)]** ボックスの一覧から部署の値を選択します。

    g. **[Save]** をクリックします。

    > [!NOTE]
    > **[Send welcome email (ようこそメールの送信)]** チェックボックスをオンにすることで、ユーザーに登録メールを送信できます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [iLMS] タイルをクリックすると、SSO を設定した iLMS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)