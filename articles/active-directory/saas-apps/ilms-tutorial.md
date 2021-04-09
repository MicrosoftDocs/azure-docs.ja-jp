---
title: チュートリアル:Azure Active Directory と iLMS の統合 | Microsoft Docs
description: Azure Active Directory と iLMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.openlocfilehash: 7289fe2ec1f39679dcec95f1f48a6efa9ed0cdfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460344"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>チュートリアル:iLMS と Azure Active Directory の統合

このチュートリアルでは、iLMS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と iLMS を統合すると、次のことができます。

* iLMS にアクセスする Azure AD を制御する。
* ユーザーが自分の Azure AD アカウントを使用して iLMS に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* iLMS でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 iLMS では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-ilms-from-the-gallery"></a>ギャラリーからの iLMS の追加

Azure AD への iLMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iLMS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**iLMS**」と入力します。
1. 結果のパネルから **[iLMS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**Britta Simon** というテスト ユーザーを使用して、iLMS で Azure AD の SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと iLMS の関連ユーザーとの間にリンク関係を確立する必要があります。

iLMS で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[iLMS SSO の構成](#configure-ilms-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[iLMS のテスト ユーザーの作成](#create-ilms-test-user)** - iLMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **iLMS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、iLMS 管理ポータルで [SAML settings]\(SAML 設定\) の **[Service Provider]\(サービス プロバイダー\)** セクションからコピーした **[Identifier]\(識別子\)** の値を貼り付けます。

    b. **[応答 URL]** ボックスに、iLMS 管理ポータルで [SAML settings]\(SAML 設定\) の **[Service Provider]\(サービス プロバイダー\)** セクションからコピーした `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx` という形式の **[Endpoint (URL)]\(エンドポイント (URL)\)** の値を貼り付けます。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、iLMS 管理ポータルで [SAML settings]\(SAML 設定\) の **[Service Provider]\(サービス プロバイダー\)** セクションからコピーした `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx` という形式の **[Endpoint (URL)]\(エンドポイント (URL)\)** の値を貼り付けます。

1. iLMS アプリケーションは、JIT プロビジョニングを有効にするために特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    > [!NOTE]
    > これらの属性をマップするには、iLMS で **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** を有効にする必要があります。 属性の構成を理解するには、[こちら](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon)の手順に従ってください。

1. その他に、iLMS アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | 名前 | ソース属性|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[iLMS のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-ilms-sso"></a>iLMS の SSO の構成

1. 別の Web ブラウザー ウィンドウで、**iLMS 管理者ポータル** に管理者としてサインインします。

2. **[Settings (設定)]** タブの **[SSO:SAML]** をクリックして SAML 設定を開き、次の手順を実行します。

    ![i L M S 設定タブを示すスクリーンショット (S S O SAML を選択可能)。](./media/ilms-tutorial/1.png)

3. **[Service Provider (サービス プロバイダー)]** セクションを展開し、 **[Identifier (識別子)]** と **[Endpoint (URL) (エンドポイント (URL))]** の値をコピーします。

    ![値を取得できる [SAML Settings]\(SAML 設定\) を示すスクリーンショット。](./media/ilms-tutorial/2.png) 

4. **[Identity Provider (ID プロバイダー)]** セクションで、 **[Import Metadata (メタデータのインポート)]** をクリックします。

5. Azure portal で **[SAML 署名証明書]** セクションからダウンロードした **フェデレーション メタデータ** ファイルを選択します。

    ![メタデータ ファイルを選択できる [SAML Settings]\(SAML 設定\) を示すスクリーンショット。](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. JIT プロビジョニングを有効にして未認識のユーザーの iLMS アカウントを作成する場合は、次の手順に従います。

    a. **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** をクリックします。

    ![[Create Un-recognized User Account]\(未認識のユーザー アカウントの作成\) オプションを示すスクリーンショット。](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Azure AD の属性を ILMS の属性にマップします。 属性欄に、属性名または既定値を指定します。

    c. **[Business Rules (ビジネス ルール)]** タブに移動し、次の手順を実行します。

    ![この手順での情報を入力できる [Business Rules]\(ビジネス ルール\) 設定を示すスクリーンショット。](./media/ilms-tutorial/5.png)

    d. シングル サインオンの時点で存在していないリージョン、事業部、および部署を作成するには、 **[Create Un-recognized Regions, Divisions and Departments (未認識のリージョン、事業部、および部署)]** をオンにします。

    e. シングル サインオンのたびにユーザー プロファイルが更新されるように指定するには、 **[Update User Profile During Sign-in (サインイン中にユーザー プロファイルを更新する)]** をオンにします。

    f. **[Update Blank Values for Non Mandatory Fields in User Profile]\(ユーザー プロファイルの必須フィールド以外の空白の値を更新する\)** オプションをオンにした場合、サインイン時に空白であるオプションのプロファイル フィールドによって、ユーザーの iLMS プロファイルのオプションのフィールドが空白の値を含むように更新されます。

    g. エラー通知メールを受信する場合は、 **[Send Error Notification Email (エラー通知メールを送信する)]** をオンにし、ユーザーのメール アドレスを入力します。

7. **[Save (保存)]** ボタンをクリックして、設定を保存します。

    ![[Save]\(保存\) ボタンを示すスクリーンショット。](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`Britta Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に iLMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[iLMS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-ilms-test-user"></a>iLMS のテスト ユーザーの作成

アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。 iLMS 管理ポータルの SAML 構成設定で **[Create Un-recognized User Account (未認識のユーザー アカウントの作成)]** チェックボックスをオンにした場合は、JIT が機能します。

ユーザーを手動で作成する必要がある場合は、以下の手順に従います。

1. iLMS 企業サイトに管理者としてサインインします。

2. **[Users]\(ユーザー\)** タブの **[Register User]\(ユーザーの登録\)** をクリックして **[Register User]\(ユーザーの登録\)** ページを開きます。

   ![[Register User]\(ユーザーの登録\) を選択できる i L M S 設定タブを示すスクリーンショット。](./media/ilms-tutorial/3.png)

3. **[Register User]\(ユーザーの登録\)** ページで次の手順を実行します。

    ![指定された情報を入力できる [Register User]\(ユーザーの登録\) ページを示すスクリーンショット。](./media/ilms-tutorial/create_testuser_add.png)

    a. **[First Name]** ボックスに、ユーザーの名を入力します (この例では Britta)。

    b. **[Last Name]** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    c. **[Email ID]\(電子メール ID\)** ボックスに、ユーザーの電子メール アドレスを BrittaSimon@contoso.com のように入力します。

    d. **[Region (リージョン)]** ボックスの一覧からリージョンの値を選択します。

    e. **[Division (事業部)]** ボックスの一覧からリージョンの値を選択します。

    f. **[Department (部署)]** ボックスの一覧から部署の値を選択します。

    g. **[保存]** をクリックします。

    > [!NOTE]
    > **[Send welcome email (ようこそメールの送信)]** チェックボックスをオンにすることで、ユーザーに登録メールを送信できます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで iLMS タイルを選択すると、SSO を設定した iLMS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)