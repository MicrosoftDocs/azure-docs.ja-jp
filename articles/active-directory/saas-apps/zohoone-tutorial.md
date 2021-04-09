---
title: 'チュートリアル: Azure Active Directory と Zoho One の統合 | Microsoft Docs'
description: Azure Active Directory と Zoho One の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 12ac4d9fbf30873f0392a6d767d7568129bad112
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650653"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>チュートリアル: Azure Active Directory と Zoho One の統合

このチュートリアルでは、Zoho One と Azure Active Directory (Azure AD) を統合する方法について説明します。 Zoho One と Azure AD を統合すると、次のことができます。

* Zoho One にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Zoho One に自動的にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Zoho One と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Zoho One でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zoho One では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-zoho-one-from-the-gallery"></a>ギャラリーからの Zoho One の追加

Azure AD への Zoho One の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Zoho One を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zoho One**」と入力します。
1. 結果のパネルから **[Zoho One]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Zoho One の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zoho One に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Zoho One の関連ユーザーとの間にリンク関係を確立する必要があります。

Zoho One に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zoho One SSO の構成](#configure-zoho-one-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zoho One のテスト ユーザーの作成](#create-zoho-one-test-user)** - Azure AD の B.Simon にリンクさせるために、対応するユーザーを Zoho One で作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zoho One** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`one.zoho.com` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://accounts.zoho.com/samlresponse/<saml-identifier>` のパターンを使用して URL を入力します

    > [!NOTE]
    > 上記の **[応答 URL]** の値は、実際の値ではありません。 `<saml-identifier>` の値は、このチュートリアルで後述する「**Zoho One のシングル サインオンの構成**」セクションの手順 4. で取得します。

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** ボックスに、URL `https://one.zoho.com` を入力します

5. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` という形式で URL を入力します。 

    > [!NOTE] 
    > 上記の **サインオン URL** の値は、実際の値ではありません。 この値は、このチュートリアルで後述する「**Zoho One のシングル サインオンの構成**」セクションで得られる実際のサインオン URL に置き換えてください。 

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Set up Zoho One]\(Zoho One のセットアップ\)** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Zoho One へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Zoho One]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-zoho-one-sso"></a>Zoho One SSO の構成

1. 別の Web ブラウザー ウィンドウで、Zoho One 企業サイトに管理者としてサインインします。

2. **[Organization]\(組織\)** タブで、**[SAML Authentication]\(SAML 認証\)** の **[Setup]\(設定\)** をクリックします。

    ![Zoho One、組織](./media/zoho-one-tutorial/set-up.png)

3. ポップアップ ページで、次の手順に従います。

    ![Zoho One、sig](./media/zoho-one-tutorial/save.png)

    a. **[Sign-in URL]\(サインイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[Sign-out URL]\(サインアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    c. **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードした **証明書 (Base64)** をアップロードします。

    d. **[保存]** をクリックします。

4. SAML 認証設定を保存した後、**[SAML-Identifier]\(SAML 識別子\)** の値をコピーし、`<saml-identifier>` を置き換える形で **応答 URL** に追加します (例: `https://accounts.zoho.com/samlresponse/one.zoho.com`)。こうして得られた値を **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けてください。

    ![Zoho One、SAML](./media/zoho-one-tutorial/saml-identifier.png)

5. **[Domains]\(ドメイン\)** タブに移動し、 **[Add Domain]\(ドメインの追加\)** をクリックします。

    ![Zoho One、ドメイン](./media/zoho-one-tutorial/add-domain.png)

6. **[Add Domain]\(ドメインの追加\)** ページで、次の手順に従います。

    ![Zoho One、ドメインの追加](./media/zoho-one-tutorial/add-domain-name.png)

    a. **[Domain Name]\(ドメイン名\)** ボックスに、ドメインを入力します (例: contoso.com)。

    b. **[追加]** をクリックします。

    >[!Note]
    >ドメインに追加した後、[これら](https://www.zoho.com/one/help/admin-guide/domain-verification.html)の手順に従って、ドメインを確認します。 ドメインを確認したら、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** でこのドメイン名を使用します。

### <a name="create-zoho-one-test-user"></a>Zoho One テスト ユーザーの作成

Azure AD ユーザーが Zoho One にサインインできるようにするには、そのユーザーを Zoho One にプロビジョニングする必要があります。 Zoho One では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Zoho One にサインインします。

2. **[Users]\(ユーザー\)** タブで、**ユーザー ロゴ** をクリックします。

    ![Zoho One、ユーザー](./media/zoho-one-tutorial/user.png)

3. **[Add User]** ページで、次の手順に従います。

    ![Zoho One、ユーザーの追加](./media/zoho-one-tutorial/add-user.png)
    
    a. **[Name]\(名前\)** ボックスに、ユーザーの氏名を入力します (例: **Britta Simon**)。
    
    b. **[Email Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレスを入力します (例: brittasimon@contoso.com)。

    >[!Note]
    >ドメインの一覧から、確認済みドメインを選択します。

    c. **[追加]** をクリックします。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zoho One のサインオン URL にリダイレクトされます。  

* Zoho One のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Zoho One に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Zoho One] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Zoho One に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Zoho One を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。