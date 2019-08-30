---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday の統合 | Microsoft Docs
description: Azure Active Directory と Workday の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fe63ba810724216b1b356896b621f1e5b021bbf
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday の統合

このチュートリアルでは、Workday と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Workday を統合すると、次のことができます。

* Workday にアクセスする Azure AD を制御する。
* ユーザーが自分の Azure AD アカウントを使用して Workday に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure portal) でアカウントを管理する。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Workday でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Workday では、**SP** によって開始される SSO がサポートされます。

## <a name="adding-workday-from-the-gallery"></a>ギャラリーからの Workday の追加

Azure AD への Workday の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workday を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Workday**」と入力します。
1. 結果ウィンドウで **[Workday]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Workday の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Workday に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Workday の関連ユーザーの間で、リンク関係を確立する必要があります。

Workday で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Workday の構成](#configure-workday)** - アプリケーション側で SSO 設定を構成します。
    1. **[Workday テスト ユーザーの作成](#create-workday-test-user)** - Workday で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Workday** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml2.flex` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`http://www.workday.com` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml.htmld` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 応答 URL には必ずサブドメインを入れます (例: www、wd2、wd3、wd3-impl、wd5、wd5-impl)。
    > `http://www.myworkday.com` のような URL は動作しますが、`http://myworkday.com` は動作しません。 これらの値を取得するには、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. Workday アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Workday アプリケーションでは、**nameidentifier** が **user.mail** や **UPN** などにマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > ここでは、既定値として UPN (user.userprincipalname) を使用して名前 ID をマップしています。 SSO を正常に動作させるには、Workday アカウント (メール、UPN など) の実際のユーザー ID を使用して名前 ID をマップする必要があります。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[署名]** オプションを要件に応じて変更するには、 **[編集]** ボタンをクリックして **[SAML 署名証明書]** ダイアログを開きます。

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. **[署名オプション]** で **[SAML 応答とアサーションへの署名]** を選択します。

    b. **[保存]**

1. **[Workday のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

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

このセクションでは、B. Simon に Workday へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Workday]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-workday"></a>Workday の構成

1. 別の Web ブラウザー ウィンドウで、Workday 企業サイトに管理者としてサインインします。

2. ホーム ページの左上にある **[Search]\(検索\)** ボックスで、「**Edit Tenant Setup – Security**」(テナントのセットアップの編集 - セキュリティ) という名前を検索します。

    ![テナントのセキュリティの編集](./media/workday-tutorial/IC782925.png "Edit Tenant Security")

3. **[リダイレクト URL]** セクションで、次の手順を実行します。

    ![リダイレクト URL](./media/workday-tutorial/IC7829581.png "Redirection URLs")

    a. **[行の追加]** をクリックします。

    b. **[ログイン リダイレクトの URL]** 、 **[Timeout Redirect URL]\(タイムアウト リダイレクト URL\)** 、および **[Mobile Redirect URL]\(モバイル リダイレクト URL\)** テキストボックスに、Azure portal の **[Workday のセットアップ]** セクションからコピーした **[ログイン URL]** を貼り付けます。

    c. **[Logout Redirect URL]\(ログアウト リダイレクトの URL\)** テキスト ボックスに、Azure portal の **[Workday のセットアップ]** セクションからコピーした **[ログアウト URL]** を貼り付けます。

    d. **[Used for Environments]\(環境に使用\)** テキストボックスで、環境名を選択します。  

   > [!NOTE]
   > [環境] 属性の値が、テナント URL の値に関連付けられます。  
   > \- Workday テナント URL のドメイン名が impl で始まる場合 (例: *https:\//impl.workday.com/\<テナント\>/login-saml2.flex*)、 **[Environment]\(環境\)** 属性を [Implementation]\(実装\) に設定する必要があります。  
   > \- ドメイン名が impl 以外で始まる場合は、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせて、対応する **[Environment]\(環境\)** の値を取得してください。

4. **[SAML 設定]** セクションで、次の手順を実行します。

    ![SAML のセットアップ](./media/workday-tutorial/IC782926.png "SAML Setup")

    a.  **[Enable SAML Authentication]** を選択します。

    b.  **[行の追加]** をクリックします。

5. **[SAML Identity Providers]\(SAML ID プロバイダー\)** セクションで、次の手順を実行します。

    ![SAML ID プロバイダー](./media/workday-tutorial/IC7829271.png "SAML Identity Providers")

    a. **[Identity Provider Name]\(ID プロバイダー名\)** テキスト ボックスに、プロバイダー名を入力します (例:*SPInitiatedSSO*)。

    b. Azure portal の **[Workday の構成]** セクションで **Azure AD 識別子**の値をコピーし、 **[Issuer]\(発行者\)** ボックスに貼り付けます。

    ![SAML ID プロバイダー](./media/workday-tutorial/IC7829272.png "SAML Identity Providers")

    c. Azure portal の **[Workday の構成]** セクションで**ログアウト URL** の値をコピーし、 **[Logout Response URL]\(ログアウト応答 URL\)** ボックスに貼り付けます。

    d. Azure portal の **[Workday の構成]** セクションで**ログイン URL** の値をコピーし、 **[IdP SSO Service URL]\(IDP SSP サービス URL\)** ボックスに貼り付けます。

    e. **[Used for Environments]\(環境に使用\)** テキストボックスで、環境名を選択します。

    f. **[ID プロバイダーの公開鍵証明書]** をクリックし、 **[作成]** をクリックします。

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

    a.  **[サービス プロバイダー ID]** ボックスに、「 **http://www.workday.com** 」と入力します。

    b. **[SP によって開始された認証要求を圧縮しない]** を選択します。

    c. **[認証要求署名方法]** として **[SHA256]** を選択します。

    ![認証要求署名方法](./media/workday-tutorial/WorkdaySSOConfiguration.png "Authentication Request Signature Method") 

    d. Click **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > シングル サインオンが正しく設定されていることを確認してください。 誤った設定のシングル サインオンを有効にした場合、資格情報を使用してアプリケーションに入ることができず、ロックアウトされてしまいます。このような状況に備えて、Workday には、ユーザーが [Workday URL]/login.flex?redirect=n 形式で通常のユーザー名とパスワードを使用してサインインできる、バックアップ用ログイン URL が用意されています

### <a name="create-workday-test-user"></a>Workday テスト ユーザーの作成

このセクションでは、Workday で B.Simon というユーザーを作成します。 [Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)と連携し、Workday プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Workday] タイルを選択すると、SSO を設定した Workday に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD を使用して Workday を試す](https://aad.portal.azure.com)
