---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と KnowledgeOwl の統合 | Microsoft Docs
description: Azure Active Directory と KnowledgeOwl の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: c1c53da8c62acc1b4aacd2b0fa79dd968c65c13a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645639"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と KnowledgeOwl の統合

このチュートリアルでは、KnowledgeOwl を Azure Active Directory (Azure AD) と統合する方法について説明します。 Azure AD と KnowledgeOwl を統合すると、次のことができます。

* KnowledgeOwl にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して KnowledgeOwl に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* KnowledgeOwl でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* KnowledgeOwl では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* KnowledgeOwl では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-knowledgeowl-from-the-gallery"></a>ギャラリーからの KnowledgeOwl の追加

Azure AD への KnowledgeOwl の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に KnowledgeOwl を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**KnowledgeOwl**」と入力します。
1. 結果のパネルから **[KnowledgeOwl]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-knowledgeowl"></a>KnowledgeOwl の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、KnowledgeOwl に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと KnowledgeOwl の関連ユーザーとの間にリンク関係を確立する必要があります。

KnowledgeOwl に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[KnowledgeOwl の SSO の構成](#configure-knowledgeowl-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[KnowledgeOwl のテスト ユーザーの作成](#create-knowledgeowl-test-user)** - KnowledgeOwl で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **KnowledgeOwl** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、次のいずれかのパターンを使用して URL を入力します。
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. **[応答 URL]** ボックスに、次のいずれかのパターンを使用して URL を入力します。
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のいずれかのパターンを使用して URL を入力します。
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際の識別子、応答 URL、サインオン URL に更新する必要があります。

1. KnowledgeOwl アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、KnowledgeOwl アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性 | 名前空間 |
    | ------------ | -------------------- | -----|
    | ssoid | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

1. **[KnowledgeOwl のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に KnowledgeOwl へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[KnowledgeOwl]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-knowledgeowl-sso"></a>KnowledgeOwl の SSO の構成

1. 別の Web ブラウザー ウィンドウで、KnowledgeOwl 企業サイトに管理者としてサインインします。

1. **[Settings]\(設定\)** をクリックし、 **[Security]\(セキュリティ\)** を選択します。

    ![[Settings]\(設定\) メニューの [Security]\(セキュリティ\) が選択されているスクリーンショット。](./media/knowledgeowl-tutorial/configure-1.png)

1. **[SAML SSO Integration]\(SAML SSO 統合\)** までスクロールして、次の手順に従います。

    ![ここで説明されている変更を行うことができる [SAML S S O Integration]\(SAML S S O 統合\) を示すスクリーンショット。](./media/knowledgeowl-tutorial/configure-2.png)

    a. **[Enable SAML SSO]\(SAML SSO を有効にする\)** をオンにします。

    b. **[SP Entity ID]\(SP エンティティ ID\)** 値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** に貼り付けます。

    c. **[SP Login URL]\(SP ログイン URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL] および [応答 URL]** テキスト ボックスに貼り付けます。

    d. **[IdP entityID]** テキストボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    e. **[IdP Login URL]\(IdP ログイン URL\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    f. **[IdP Logout URL]\(IdP ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    g. **[Upload IdP Certificate]\(IdP 証明書のアップロード\)** をクリックして、Azure portal からダウンロードした証明書をアップロードします。

    h. **[Map SAML Attributes]\(SAML 属性のマッピング\)** をクリックして属性をマップし、次の手順に従います。

    ![ここで説明されている変更を行うことができる [Map SAML Attributes]\(SAML 属性のマッピング\) を示すスクリーンショット。](./media/knowledgeowl-tutorial/configure-3.png)

    * **[SSO ID]** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`」と入力します。
    * **[Username/Email]\(ユーザー名/電子メール\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。
    * **[First Name]\(名\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。
    * **[Last Name]\(姓\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。
    * **[保存]** をクリックします。

    i. ページの下部にある **[保存]** をクリックします。

    ![[Save]\(保存\) ボタンを示すスクリーンショット。](./media/knowledgeowl-tutorial/configure-4.png)

### <a name="create-knowledgeowl-test-user"></a>KnowledgeOwl のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを KnowledgeOwl に作成します。 KnowledgeOwl では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 KnowledgeOwl にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[KnowledgeOwl サポート チーム](mailto:support@knowledgeowl.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる KnowledgeOwl のサインオン URL にリダイレクトされます。  

* KnowledgeOwl のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した KnowledgeOwl に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [KnowledgeOwl] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した KnowledgeOwl に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

KnowledgeOwl を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。