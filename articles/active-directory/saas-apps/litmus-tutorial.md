---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Litmus の統合 | Microsoft Docs
description: Azure Active Directory と Litmus の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: f2168715d59f9698cba58b7e91bbc897a8cd94f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727251"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Litmus の統合

このチュートリアルでは、Litmus と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Litmus を統合すると、次のことができます。

* Litmus にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Litmus に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Litmus でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Litmus では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-litmus-from-the-gallery"></a>ギャラリーからの Litmus の追加

Azure AD への Litmus の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Litmus を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Litmus**」と入力します。
1. 結果のパネルから **[Litmus]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-litmus"></a>Litmus の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Litmus に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Litmus の関連ユーザーとの間にリンク関係を確立する必要があります。

Litmus に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Litmus の SSO の構成](#configure-litmus-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Litmus のテスト ユーザーの作成](#create-litmus-test-user)** - Litmus で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Litmus** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://litmus.com/sessions/new`」と入力します。

1. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

1. **[Litmus のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Litmus へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Litmus]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-litmus-sso"></a>Litmus の SSO の構成

1. Litmus 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして、**My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Litmus のセットアップ]** をクリックすると、Litmus アプリケーションに移動します。 そこから、管理者の資格情報を入力して Litmus にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Litmus を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Litmus 企業サイトに管理者としてサインインします。

1. 左側のナビゲーション パネルから、 **[Security]\(セキュリティ\)** をクリックします。

    ![[セキュリティ] 項目が選択されている画面のスクリーンショット。](./media/litmus-tutorial/security-img.png)

1. **[Configure SAML Authentication]\(SAML 認証の構成\)** セクションで、次の手順に従います。

    ![説明されている値を入力できる [Configure SAML Authentication]\(SAML 認証の構成\) セクションを示すスクリーンショット。](./media/litmus-tutorial/configure1.png)

    a. **[Enable SAML]\(SAML を有効にする\)** トグルをオンに切り替えます。

    b. プロバイダーに **[Generic]\(汎用\)** を選択します。

    c. **[Identity Provider Name]\(ID プロバイダー名\)** の名前を入力します。例: 例: `Azure AD`

1. 次の手順に従います。

    ![説明されている値を入力できるセクションを示すスクリーンショット。](./media/litmus-tutorial/configure3.png)

    a. **[SAML 2.0 Endpoint(HTTP)]\(SAML 2.0 エンドポイント (HTTP)\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. Azure portal からダウンロードした **証明書** ファイルをメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    c. **[Save SAML settings]\(SAML 設定の保存\)** をクリックします。

### <a name="create-litmus-test-user"></a>Litmus のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、管理者として Litmus アプリケーションにサインインします。

1. 左側のナビゲーション パネルから、 **[Accounts]\(アカウント\)** をクリックします。

    ![[Accounts]\(アカウント\) 項目が選択されているスクリーンショット。](./media/litmus-tutorial/accounts-img.png)

1. **[Add New User]\(新しいユーザーの追加\)** タブをクリックします。

    ![[Add New User]\(新しいユーザーの追加\) 項目が選択されているスクリーンショット。](./media/litmus-tutorial/add-new-user.png)

1. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![説明されている値を入力できる [Add User]\(ユーザーの追加\) セクションを示すスクリーンショット。](./media/litmus-tutorial/user-profile.png)

    a. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (例: **B.Simon\@contoso.com**) を入力します

    b. **[First Name]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **B**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    d. **[Create User]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Litmus のサインオン URL にリダイレクトされます。

* Litmus のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Litmus に自動的にサインインされます

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Litmus] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Litmus に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Litmus を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。