---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday の統合 | Microsoft Docs
description: Azure Active Directory と Workday の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday の統合

このチュートリアルでは、Workday と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Workday を統合すると、次のことができます。

* Workday にアクセスする Azure AD を制御する。
* ユーザーが自分の Azure AD アカウントを使用して Workday に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Workday でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Workday では、**SP** によって開始される SSO がサポートされます。

* Workday Mobile アプリケーションを Azure AD に構成して SSO を有効にできるようになりました。 構成方法の詳細については、こちらの[リンク](workday-mobile-tutorial.md)を参照してください。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-workday-from-the-gallery"></a>ギャラリーからの Workday の追加

Azure AD への Workday の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workday を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Workday**」と入力します。
1. 結果ウィンドウで **[Workday]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Workday の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Workday に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Workday の関連ユーザーの間で、リンク関係を確立する必要があります。

Workday に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Workday の構成](#configure-workday)** - アプリケーション側で SSO 設定を構成します。
    1. **[Workday テスト ユーザーの作成](#create-workday-test-user)** - Workday で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Workday** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml2.flex` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml.htmld` のパターンを使用して URL を入力します

    c. **[ログアウト URL]** テキスト ボックスに、`https://impl.workday.com/<tenant>/login-saml.htmld` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、応答 URL、ログアウト URL で更新します。 応答 URL には必ずサブドメインを入れます (例: www、wd2、wd3、wd3-impl、wd5、wd5-impl)。
    > `http://www.myworkday.com` のような URL は動作しますが、`http://myworkday.com` は動作しません。 これらの値を取得するには、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Workday アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Workday アプリケーションでは、**nameidentifier** が **user.mail** や **UPN** などにマップされると想定されているため、**[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

    > [!NOTE]
    > ここでは、既定値として UPN (user.userprincipalname) を使用して名前 ID をマップしています。 SSO を正常に動作させるには、Workday アカウント (メール、UPN など) の実際のユーザー ID を使用して名前 ID をマップする必要があります。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[署名]** オプションを要件に応じて変更するには、**[編集]** ボタンをクリックして **[SAML 署名証明書]** ダイアログを開きます。

    ![Certificate](common/edit-certificate.png) 

    ![[SAML Signing Certificate (SAML 署名証明書)]](./media/workday-tutorial/signing-option.png)

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
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-workday"></a>Workday の構成

1. 別の Web ブラウザー ウィンドウで、Workday 企業サイトに管理者としてサインインします。

1. ホーム ページの左上にある **[Search]\(検索\)** ボックスで、「**Edit Tenant Setup – Security**」(テナントのセットアップの編集 - セキュリティ) という名前を検索します。

    ![テナントのセキュリティの編集](./media/workday-tutorial/IC782925.png "テナントのセキュリティの編集")


1. [**SAML Setup**] セクションで、以下の手順を実行します。

    ![[SAML 設定]](./media/workday-tutorial/IC782926.png "[SAML 設定]")

    a.  **[SAML 認証を有効にする]** を選択します。

    b.  **[行の追加]** をクリックします。

1. **[SAML Identity Providers]\(SAML ID プロバイダー\)** セクションで、新しく作成した行に対して次の操作を実行します。

    a. 以下に示されているフィールドで、次の操作を実行します。

    ![SAML Identity Providers\(SAML ID プロバイダー\) 1](./media/workday-tutorial/IC7829271.png "SAML ID プロバイダー")

    * **[Identity Provider Name]\(ID プロバイダー名\)** テキスト ボックスに、プロバイダー名を入力します (例:*SPInitiatedSSO*)。

    * Azure portal の **[Workday の構成]** セクションで **Azure AD 識別子** の値をコピーし、**[Issuer]\(発行者\)** ボックスに貼り付けます。

    * Azure portal からダウンロードした **証明書** をメモ帳で開き、その内容を **[x.509 Certificate]\(x.509 証明書\)** テキストボックスに貼り付けます。

    b. 以下に示されているフィールドで、次の操作を実行します。

    ![SAML Identity Providers\(SAML ID プロバイダー\) 2](./media/workday-tutorial/saml-identity-provider-2.png "SAML ID プロバイダー")

    * **[Enable IDP Initiated Logout]\(IDP によって開始されたログアウトを有効にする\)** チェックボックスをオンにします。

    * **[Logout Response URL]\(ログアウト応答 URL\)** テキストボックスに、「 **http://www.workday.com** 」と入力します。

    * **[Logout Request URL]\(ログアウト要求 URL\)** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    * **[SP Initiated]\(SP によって開始\)** チェックボックスをオンにします。

    * **[サービス プロバイダー ID]** ボックスに、「**http://www.workday.com**」と入力します。


    * [**Do Not Deflate SP-initiated Authentication Request**] を選択します。

    c. 以下に示されているフィールドで、次の操作を実行します。

    ![SAML Identity Providers\(SAML ID プロバイダー\) 3](./media/workday-tutorial/saml-identity-provider-3.png "SAML ID プロバイダー")

    * Azure portal の **[Workday の構成]** セクションで **ログイン URL** の値をコピーし、**[IdP SSO Service URL]\(IDP SSP サービス URL\)** ボックスに貼り付けます。

    * **[Used for Environments]\(環境に使用\)** テキストボックスで、ドロップダウンから適切な環境名を選択します。

1. 下図の場所で次の手順を行います。

    ![Workday](./media/workday-tutorial/service-provider.png "SAML ID プロバイダー")

    a. **[Service Provider ID (Will be Deprecated)]\(サービス プロバイダー ID (非推奨になる予定)\)** テキストボックスに、「 **http://www.workday.com** 」と入力します。

    b. **[IDP SSO Service URL (Will be Deprecated)]\(IDP SSO サービスの URL (非推奨になる予定)\)** テキストボックスに、**ログイン URL** 値を入力します。

    c. **[Do Not Deflate SP-initiated Authentication Request (Will be Deprecated)]\(SP によって開始された認証要求を圧縮しない (非推奨になる予定)\)** を選択します。

    d. **[Authentication Request Signature Method]\(認証要求署名方法\)** として **[SHA256]** を選択します。

    e. **[OK]** をクリックします。

    > [!NOTE]
    > シングル サインオンが正しく設定されていることを確認してください。 誤った設定のシングル サインオンを有効にした場合、資格情報を使用してアプリケーションに入ることができず、ロックアウトされてしまいます。このような状況に備えて、Workday には、ユーザーが [Workday URL]/login.flex?redirect=n 形式で通常のユーザー名とパスワードを使用してサインインできる、バックアップ用ログイン URL が用意されています

### <a name="create-workday-test-user"></a>Workday テスト ユーザーの作成

1. 自分の Workday 企業サイトに管理者としてサインインします。

1. 右上隅の **[Profile]\(プロファイル\)** をクリックし、 **[Home]\(ホーム\)** を選択し、 **[Applications]\(アプリケーション\)** タブの **[Directory]\(ディレクトリ\)** をクリックします。 

1. **[Directory]\(ディレクトリ\)** ページで、[View]\(表示\) タブの **[Find Workers]\(従業員の検索\)** を選択します。

    ![Find workers\(従業員の検索\)](./media/workday-tutorial/user-directory.png)

1.  **[Find Workers]** \(従業員の検索\) ページで、結果からユーザーを選択します。

1. 次のページで、 **[Job]\(ジョブ\)、[Worker Security]\(従業員のセキュリティ\)** を選択します。ここで **[Workday account]\(Workday アカウント\)** が、Azure Active Directory の **[名前 ID]** 値として一致している必要があります。

    ![Worker Security\(従業員のセキュリティ\)](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Workday テスト ユーザーを作成する方法の詳細については、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始する Workday のサインオン URL にリダイレクトされます。 

2. Workday のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネル上で [Workday] タイルをクリックすると、SSO を設定した Workday に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Workday を構成したら、自分の組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。