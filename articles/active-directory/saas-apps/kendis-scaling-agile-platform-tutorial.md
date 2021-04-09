---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Kendis - Azure AD Integration の統合 | Microsoft Docs
description: Azure Active Directory と Kendis - Azure AD Integration の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: jeedes
ms.openlocfilehash: 8409a4d897ea9b20528a5b30273819e6962774cb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184493"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis---azure-ad-integration"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Kendis - Azure AD Integration の統合

このチュートリアルでは、Kendis - Azure AD Integration と Azure Active Directory (Azure AD) を統合する方法について説明します。 Kendis - Azure AD Integration を Azure AD と統合すると、次のことが可能になります。

* Kendis - Azure AD Integration にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Kendis - Azure AD Integration に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Kendis - Azure AD Integration サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Kendis - Azure AD Integration では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Kendis - Azure AD Integration では、**Just In Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-kendis---azure-ad-integration-from-the-gallery"></a>ギャラリーからの Kendis - Azure AD Integration の追加

Azure AD への Kendis - Azure AD Integration の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Kendis - Azure AD Integration を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Kendis - Azure AD Integration**」と入力します。
1. 結果パネルから **[Kendis - Azure AD Integration]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-kendis---azure-ad-integration"></a>Kendis - Azure AD Integration の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Kendis - Azure AD Integration に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Kendis - Azure AD Integration の関連ユーザーとの間にリンク関係を確立する必要があります。

Kendis - Azure AD Integration に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Kendis - Azure AD Integration の SSO の構成](#configure-kendis-azure-ad-integration-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Kendis - Azure AD Integration のテスト ユーザーの作成](#create-kendis-azure-ad-integration-test-user)** - Kendis - Azure AD Integration で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Kendis - Azure AD Integration** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.kendis.io` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.kendis.io/login/saml` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.kendis.io/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Kendis - Azure AD Integration クライアント サポート チーム](mailto:support@kendis.io)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Kendis - Azure AD Integration のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Kendis - Azure AD Integration へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Kendis - Azure AD Integration]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-kendis-azure-ad-integration-sso"></a>Kendis - Azure AD Integration の SSO の構成

1. Kendis - Azure AD Integration 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Kendis - Azure AD Integration のセットアップ]** をクリックすると、Kendis - Azure AD Integration アプリケーションに移動します。 そこから、管理者の資格情報を入力して Kendis - Azure AD Integration にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Kendis - Azure AD Integration を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Kendis - Azure AD Integration 企業サイトに管理者としてサインインします。

4. **[Settings]\(設定)\、[SAML Configurations]\(SAML 構成\)** に移動します。

    ![SAML 構成の設定](./media/kendis-scaling-agile-platform-tutorial/settings.png)

5. ページの下部にある **[Edit]\(編集\)** ボタンをクリックして、次の手順を実行します。

    ![SAML 構成](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. **[Callback URL]\(コールバック URL\)** の値をコピーし、その値を Azure portal の [基本的な SAML 構成] セクションの **[応答 URL]** ボックスに貼り付けます。

    b. **[Identity Provider Single Sign On URL]\(ID プロバイダーのシングル サインオン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c.  **[Identity Provider Issuer]\(ID プロバイダーの発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子 (エンティティ ID)** の値を貼り付けます。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    e. オプションのリストから **[Default Group]\(既定のグループ\) を選択** します。 

    f. **[保存]** をクリックします。

### <a name="create-kendis-azure-ad-integration-test-user"></a>Kendis - Azure AD Integration のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Kendis - Azure AD Integration に作成します。 Kendis - Azure AD Integration では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Kendis - Azure AD Integration にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Kendis - Azure AD Integration のサインオン URL にリダイレクトされます。  

* Kendis - Azure AD Integration のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Kendis - Azure AD Integration に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Kendis - Azure AD Integration] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Kendis - Azure AD Integration に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Kendis - Azure AD Integration を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。