---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Elium の統合 | Microsoft Docs
description: Azure Active Directory と Elium の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7f0e9d0c97b9325a30de3cb8c6ce10a3ba8489f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454115"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Elium の統合

このチュートリアルでは、Elium と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Elium を統合すると、次のことができます。

* Elium にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Elium に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Elium でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Elium では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Elium では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-elium-from-the-gallery"></a>ギャラリーからの Elium の追加

Azure AD への Elium の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Elium を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Elium**」と入力します。
1. 結果のパネルから **[Elium]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Elium の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Elium に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Elium の関連ユーザーとの間にリンク関係を確立する必要があります。

Elium に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Elium SSO の構成](#configure-elium-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Elium のテスト ユーザーの作成](#create-elium-test-user)** - Elium で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Elium** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<platform-domain>.elium.com/login/saml2/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<platform-domain>.elium.com/login/saml2/acs` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<platform-domain>.elium.com/login/saml2/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 `https://<platform-domain>.elium.com/login/saml2/metadata` でダウンロードできる **SP メタデータ ファイル** からこれらの値を取得します。これについては、このチュートリアルで後ほど説明します。

1. Elium アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Elium アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| ----------------|
    | email   |User.mail |
    | first_name| User.givenname |
    | last_name| User.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > これらは、既定の要求です。 **電子メール要求のみが必須です**。 JIT プロビジョニングの場合も、電子メール要求のみが必須です。 その他のカスタム要求は、顧客プラットフォームによって異なる場合があります。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Elium のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Elium へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Elium]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-elium-sso"></a>Elium の SSO の構成

1. Elium 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Elium のセットアップ]** をクリックすると、Elium アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Elium にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Elium を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Elium 企業サイトにサインインして、次の手順を実行します。

1. 右上隅の **[User profile]\(ユーザー プロファイル\)** をクリックし、 **[Settings]\(設定\)** を選択します。

    ![Elium のシングル サインオンの構成 01](./media/elium-tutorial/elium-01.png)

1. **[Advanced]\(詳細\)** で **[Security]\(セキュリティ\)** を選択します。

    ![Elium のシングル サインオンの構成 02](./media/elium-tutorial/elium-02.png)

1. **[シングル サインオン (SSO)]** セクションにスクロールし、次の手順に従います。

    ![Elium のシングル サインオンの構成 03](./media/elium-tutorial/elium-03.png)

    a. **[Verify that SAML2 authentication works for your account]\(SAML2 認証がアカウントに対して動作することを確認する\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** テキスト ボックスに貼り付けます。

    > [!NOTE]
    > SSO を構成した後で、次の URL で既定のリモート ログイン ページにいつでもアクセスできます: `https://<platform_domain>/login/regular/login` 

    b. **[Enable SAML2 federation]\(SAML2 フェデレーションを有効にする\)** チェック ボックスをオンにします。

    c. **[JIT Provisioning]\(JIT プロビジョニング\)** チェック ボックスをオンにします。

    d. **[ダウンロード]** をクリックして **SP メタデータ** を開きます。

    e. **SP メタデータ** ファイルで **entityID** を検索し、**entityID** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** テキストボックスに貼り付けます。 

    ![Elium のシングル サインオンの構成 04](./media/elium-tutorial/elium-04.png)

    f. **SP メタデータ** ファイルで **AssertionConsumerService** を検索し、**Location** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキストボックスに貼り付けます。

    ![Elium のシングル サインオンの構成 05](./media/elium-tutorial/elium-05.png)

    g. Azure Portal からダウンロードしたメタデータ ファイルをメモ帳で開き、内容をコピーして、 **[IdP Metadata]\(IdP メタデータ\)** テキストボックスに貼り付けます。

    h. **[保存]** をクリックします。

### <a name="create-elium-test-user"></a>Elium のテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Elium に作成します。 Elium では、**Just-In-Time プロビジョニング** がサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Elium に存在しない場合は、Elium にアクセスしようとしたときに新しいユーザーが作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Elium サポート チーム](mailto:support@elium.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 
 
#### <a name="sp-initiated"></a>SP Initiated:
 
* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Elium のサインオン URL にリダイレクトされます。  
 
* Elium のサインオン URL に直接移動し、そこからログイン フローを開始します。
 
#### <a name="idp-initiated"></a>IDP Initiated:
 
* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Elium に自動的にサインインされます 
 
また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで Elium タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Elium に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

Elium を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。