---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Perimeter 81 の統合 | Microsoft Docs
description: Azure Active Directory と Perimeter 81 の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: cd6ba1da92a19a1f73fc67c0165bfb19b3bb77aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363851"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeter-81"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Perimeter 81 の統合

このチュートリアルでは、Perimeter 81 と Azure Active Directory (Azure AD) を統合する方法について説明します。 Perimeter 81 と Azure AD を統合すると、次のことができます。

* Perimeter 81 にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Perimeter 81 に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Perimeter 81 サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Perimeter 81 では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Perimeter 81 では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-perimeter-81-from-the-gallery"></a>ギャラリーからの Perimeter 81 の追加

Azure AD への Perimeter 81 の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Perimeter 81 を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Perimeter 81**」と入力します。
1. 結果のパネルから **[Perimeter 81]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-perimeter-81"></a>Perimeter 81 の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Perimeter 81 に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Perimeter 81 の関連ユーザーとの間にリンク関係を確立する必要があります。

Perimeter 81 に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Perimeter 81 の SSO の構成](#configure-perimeter-81-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Perimeter 81 のテスト ユーザーの作成](#create-perimeter-81-test-user)** - Perimeter 81 で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Perimeter 81** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`urn:auth0:perimeter81:<SUBDOMAIN>` の形式で値を入力します。

    b. **[応答 URL]** ボックスに、`https://auth.perimeter81.com/login/callback?connection=<SUBDOMAIN>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.perimeter81.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Perimeter 81 クライアント サポート チーム](mailto:support@perimeter81.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Perimeter 81 のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B. Simon に Perimeter 81 へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Perimeter 81]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-perimeter-81-sso"></a>Perimeter 81 の SSO の構成

1. Perimeter 81 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Perimeter 81 のセットアップ]** をクリックすると、Perimeter 81 アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Perimeter 81 にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Perimeter 81 を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Perimeter 81 企業サイトに管理者としてサインインします。

4. **[設定]** に移動し、 **[ID プロバイダー]** をクリックします。

    ![Perimeter 81 の設定](./media/perimeter-81-tutorial/settings.png)

5. **[Add Provider]\(プロバイダーの追加\)** ボタンをクリックします。

    ![Perimeter 81 のプロバイダーの追加](./media/perimeter-81-tutorial/add-provider.png)

6. **[SAML 2.0 Identity Providers]\(SAML 2.0 ID プロバイダー\)** を選択し、 **[続行]** ボタンをクリックします。

    ![Perimeter 81 の ID プロバイダーの追加](./media/perimeter-81-tutorial/add-identity-provider.png)

7. **[SAML 2.0 Identity Providers]\(SAML 2.0 ID プロバイダー\)** セクションで、次の手順を実行します。

    ![Perimeter 81 の SAML の設定](./media/perimeter-81-tutorial/setting-up-saml.png)

    a. **[Sign In URL]\(サインイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[Domain Aliases]\(ドメイン エイリアス\)** ボックスに、実際のドメイン エイリアスの値を入力します。

    c. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[X509 Signing Certificate]\(X509 署名証明書\)** ボックスに貼り付けます。

    > [!NOTE]
    > または、 **[Upload PEM/CERT File]\(PEM または CERT ファイルのアップロード\)** をクリックして、Azure portal からダウンロードした **証明書 (Base64)** をアップロードすることもできます。
    
    d. **[Done]** をクリックします。

### <a name="create-perimeter-81-test-user"></a>Perimeter 81 のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Perimeter 81 に作成します。 Perimeter 81 では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Perimeter 81 にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Perimeter 81 のサインオン URL にリダイレクトされます。  

* Perimeter 81 のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Perimeter 81 に自動的にサインインされます。

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Perimeter 81] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Perimeter 81 に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Perimeter 81 を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
