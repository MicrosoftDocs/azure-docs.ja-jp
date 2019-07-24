---
title: チュートリアル:Azure Active Directory と Workday の統合 | Microsoft Docs
description: Azure Active Directory と Workday の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 8451fd692409933803f5f8023f1e1161c3a97daf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278532"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>チュートリアル:Azure Active Directory と Workday の統合

このチュートリアルでは、Workday と Azure Active Directory (Azure AD) を統合する方法について説明します。
Workday と Azure AD の統合には、次の利点があります。

* Workday にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Workday に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Workday と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Workday でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Workday では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-workday-from-the-gallery"></a>ギャラリーからの Workday の追加

Azure AD への Workday の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workday を追加する必要があります。

**ギャラリーから Workday を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Workday**」と入力し、結果ウィンドウで **[Workday]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Workday](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Workday で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Workday 内の関連ユーザー間にリンク関係が確立されている必要があります。

Workday で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Workday シングル サインオンの構成](#configure-workday-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Workday テスト ユーザーの作成](#create-workday-test-user)** - Workday で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Workday で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Workday** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Workday のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、`https:\//impl.workday.com/<tenant>/login-saml2.flex` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://www.workday.com` の形式で URL を入力します。

5. **[追加の URL を設定します]** をクリックして、次の手順のようにします。

    ![[Workday のドメインと URL] のシングル サインオン情報](./media/workday-tutorial/reply.png)

    **[応答 URL]** ボックスに、`https:\//impl.workday.com/<tenant>/login-saml.htmld` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 応答 URL には必ずサブドメインを入れます (例: www、wd2、wd3、wd3-impl、wd5、wd5-impl)。
    > `http://www.myworkday.com` のような URL は動作しますが、`http://myworkday.com` は動作しません。 これらの値を取得するには、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. Workday アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Workday アプリケーションでは、**nameidentifier** が **user.mail** や **UPN** などにマップされると想定されているため、**[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > ここでは、既定値として UPN (user.userprincipalname) を使用して名前 ID をマップしています。 SSO を正常に動作させるには、Workday アカウント (メール、UPN など) の実際のユーザー ID を使用して名前 ID をマップする必要があります。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Workday のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-workday-single-sign-on"></a>Workday のシングル サインオンを構成する

1. 別の Web ブラウザー ウィンドウで、Workday 企業サイトに管理者としてログインします。

2. ホーム ページの左上にある **[Search]\(検索\)** ボックスで、「**Edit Tenant Setup – Security**」(テナントのセットアップの編集 - セキュリティ) という名前を検索します。

    ![テナントのセキュリティの編集](./media/workday-tutorial/IC782925.png "Edit Tenant Security")

3. **[リダイレクト URL]** セクションで、次の手順を実行します。

    ![リダイレクト URL](./media/workday-tutorial/IC7829581.png "Redirection URLs")

    a. **[行の追加]** をクリックします。

    b. **[Login Redirect URL]\(ログイン リダイレクト URL\)** ボックスと **[Mobile Redirect URL]\(モバイル リダイレクト URL\)** ボックスに、Azure portal の **[基本的な SAML 構成]** セクションで入力した**サインオン URL** を入力します。

    c. Azure portal の **[Workday の構成]** セクションで **[ログアウト URL]** をコピーし、**[Logout Redirect URL]\(ログアウト リダイレクト URL\)** ボックスに貼り付けます。

    d. **[Used for Environments]\(環境に使用\)** テキストボックスで、環境名を選択します。  

   > [!NOTE]
   > [環境] 属性の値が、テナント URL の値に関連付けられます。  
   > - Workday テナント URL のドメイン名が impl で始まる場合 (例: *https:\//impl.workday.com/\<テナント\>/login-saml2.flex*)、**[Environment]\(環境\)** 属性を [Implementation]\(実装\) に設定する必要があります。  
   > - ドメイン名が impl 以外で始まる場合は、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせて、対応する **[Environment]\(環境\)** の値を取得してください。

4. **[SAML 設定]** セクションで、次の手順を実行します。

    ![SAML のセットアップ](./media/workday-tutorial/IC782926.png "SAML Setup")

    a.  **[Enable SAML Authentication]** を選択します。

    b.  **[行の追加]** をクリックします。

5. **[SAML Identity Providers]\(SAML ID プロバイダー\)** セクションで、次の手順を実行します。

    ![SAML ID プロバイダー](./media/workday-tutorial/IC7829271.png "SAML Identity Providers")

    a. **[Identity Provider Name]\(ID プロバイダー名\)** テキスト ボックスに、プロバイダー名を入力します (例:*SPInitiatedSSO*)。

    b. Azure portal の **[Workday の構成]** セクションで **Azure AD 識別子**の値をコピーし、**[Issuer]\(発行者\)** ボックスに貼り付けます。

    ![SAML ID プロバイダー](./media/workday-tutorial/IC7829272.png "SAML Identity Providers")

    c. Azure portal の **[Workday の構成]** セクションで**ログアウト URL** の値をコピーし、**[Logout Response URL]\(ログアウト応答 URL\)** ボックスに貼り付けます。

    d. Azure portal の **[Workday の構成]** セクションで**ログイン URL** の値をコピーし、**[IdP SSO Service URL]\(IDP SSP サービス URL\)** ボックスに貼り付けます。

    e. **[Used for Environments]\(環境に使用\)** テキストボックスで、環境名を選択します。

    f. **[ID プロバイダーの公開鍵証明書]** をクリックし、**[作成]** をクリックします。

    ![作成](./media/workday-tutorial/IC782928.png "作成")

    g. **[x509 公開鍵の作成]** をクリックします。

    ![作成](./media/workday-tutorial/IC782929.png "作成")

6. **[x509 公開鍵の表示]** セクションで、次の手順を実行します。

    ![x509 公開鍵の表示](./media/workday-tutorial/IC782930.png "x509 公開鍵の表示")

    a. **[Name]\(名前\)** テキスト ボックスに、証明書の名前を入力します (例:*PPE\_SP*)。

    b. **[有効期間の開始日]** テキスト ボックスに、証明書の有効期間の開始日を示す属性の値を入力します。

    c.  **[有効期間の終了日]** テキスト ボックスに、証明書の有効期間の終了日を示す属性の値を入力します。

    > [!NOTE]
    > 有効期間の開始日と終了日は、ダウンロードした証明書をダブルクリックして確認できます。  日付は **[詳細]** タブに表示されます。
    >
    >

    d.  Base 64 でエンコードされた証明書をメモ帳で開き、その内容をコピーします。

    e.  **[証明書]** テキスト ボックスに、クリップボードの内容を貼り付けます。

    f.  Click **OK**.

7. 次の手順に従います。

    ![SSO 構成](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO configuration")

    a.  **[サービス プロバイダー ID]** ボックスに、「**https://www.workday.com**」と入力します。

    b. **[SP によって開始された認証要求を圧縮しない]** を選択します。

    c. **[認証要求署名方法]** として **[SHA256]** を選択します。

    ![認証要求署名方法](./media/workday-tutorial/WorkdaySSOConfiguration.png "Authentication Request Signature Method") 

    d. Click **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > シングル サインオンが正しく設定されていることを確認してください。 誤った設定のシングル サインオンを有効にした場合、資格情報を使用してアプリケーションに入ることができず、ロックアウトされてしまいます。このような状況に備えて、Workday には、ユーザーが [Workday URL]/login.flex?redirect=n 形式で通常のユーザー名とパスワードを使用してサインインできる、バックアップ用ログイン URL が用意されています

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

このセクションでは、Britta Simon に Workday へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Workday]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Workday]** を選択します。

    ![アプリケーションの一覧の Workday のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-workday-test-user"></a>Workday テスト ユーザーの作成

このセクションでは、Workday で Britta Simon というユーザーを作成します。 [Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)と連携し、Workday プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Workday] タイルをクリックすると、SSO を設定した Workday に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

