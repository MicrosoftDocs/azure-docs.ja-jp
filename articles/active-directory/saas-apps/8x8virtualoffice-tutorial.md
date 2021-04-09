---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と 8x8 の統合 | Microsoft Docs
description: Azure Active Directory と 8x8 の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 81a7efea268600e661981b35f79149fe814ef084
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と 8x8 の統合

このチュートリアルでは、8x8 と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と 8x8 を統合すると、次のことができます。

* 8x8 にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して 8x8 に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* 8x8 サブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* 8x8 では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-8x8-from-the-gallery"></a>ギャラリーからの 8x8 の追加

Azure AD への 8x8 の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に 8x8 を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**8x8**」と入力します。
1. 結果パネルで **[8x8]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>8x8 の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、8x8 に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと 8x8 の関連ユーザーとの間にリンク関係を確立する必要があります。

8x8 に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[8x8 の SSO の構成](#configure-8x8-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[8x8 のテスト ユーザーの作成](#create-8x8-test-user)** - 8x8 で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **8x8** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** テキスト ボックスに、`https://sso.8x8.com/saml2` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://sso.8x8.com/saml2` という URL を入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。 この証明書は、チュートリアルの後半の「**8x8 の SSO の構成**」セクションで使用します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[8x8 のセットアップ]** セクションで、URL をコピーします。これらの URL 値は、チュートリアルの後半で使用します。

    ![構成 URL のコピー](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

このセクションでは、B.Simon に 8x8 へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[8x8]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-8x8-sso"></a>8x8 の SSO の構成

このチュートリアルの次の部分は、8x8 で使用しているサブスクリプションの種類によって異なります。

* 管理に Configuration Manager を使用している 8x8 エディションおよび X シリーズのお客様の場合は、「[8x8 管理コンソールの構成](#configure-8x8-admin-console)」を参照してください。
* 管理に Account Manager を使用している Virtual Office のお客様の場合は、「[8x8 Account Manager の構成](#configure-8x8-account-manager)」を参照してください。

### <a name="configure-8x8-admin-console"></a>8x8 Admin Console の構成

1. 8x8 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[8x8 のセットアップ]** をクリックすると、8x8 アプリケーションに移動します。 そこから、管理者の資格情報を入力して 8x8 にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. 8x8 を手動で設定する場合は、管理者として 8x8 [Admin Console](https://admin.8x8.com/) にサインインします。

1. ホーム ページで、 **[Identity Management]\(ID 管理\)** をクリックします。

    ![[Identity Management]\(ID 管理\) タイルが強調表示されているスクリーンショット。](./media/8x8virtualoffice-tutorial/configure1.png)

1. **[Single Sign On (SSO)]\(シングル サインオン (SSO)\)** をオンにし、 **[Microsoft Azure AD]** を選択します。

    ![[Single Sign On (SSO)]\(シングル サインオン (SSO)\) と [Microsoft Azure AD] オプションが強調表示されているスクリーンショット。](./media/8x8virtualoffice-tutorial/configure2.png)

1. 3 つの URL と署名証明書を、Azure AD の **[SAML でシングル サインオンをセットアップします]** ページから、8x8 Admin Console の **[Microsoft Azure AD SAML Settings]\(Microsoft Azure AD SAML 設定\)** セクションにコピーします。

    ![8x8 Admin Console](./media/8x8virtualoffice-tutorial/configure3.png)

    a. **ログイン URL** を **[IDP Login URL]\(IDP ログイン URL\)** にコピーします。

    b. **Azure AD 識別子** を **[IDP Issuer URL/URN]\(IDP 発行者 URL/URN\)** にコピーします。

    c. **ログアウト URL** を **[IDP Logout URL]\(IDP ログアウト URL\)** にコピーします。

    d. **証明書 (Base64)** をダウンロードし、 **[Certificate]\(証明書\)** にアップロードします。

    e. **[保存]** をクリックします。

### <a name="configure-8x8-account-manager"></a>8x8 Account Manager の構成

1. 8x8 Virtual Office テナントに管理者としてサインオンします。

1. アプリケーション パネルで **[Virtual Office Account Mgr (Virtual Office アカウント マネージャー)]** を選択します。

    ![[Virtual Office Account Mgr]\(Virtual Office アカウント マネージャー\) タイルが強調表示されているスクリーンショット。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. 管理する **[Business (ビジネス)]** アカウントを選択し、 **[Sign In (サインイン)]** をクリックします。

    ![[Business]\(ビジネス\) オプションと [Sign In]\(サインイン\) ボタンが強調表示されているスクリーンショット。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. メニュー リストの **[ACCOUNTS]\(アカウント\)** タブをクリックします。

    ![メニュー リストの [ACCOUNTS]\(アカウント\) タブが強調表示されているスクリーンショット。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. アカウントの一覧で **[Single Sign On (シングル サインオン)]** をクリックします。

    ![[Single Sign On]\(シングル サインオン\) オプションが強調表示されているスクリーンショット。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. [Authentication methods]\(認証方法\) の **[Single Sign On]\(シングル サインオン\)** を選択し、 **[SAML]** をクリックします。

    ![[Single Sign On]\(シングル サインオン\) の [SAML] が強調表示されているスクリーンショット。](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. **[SAML Single Sign-On]\(SAML シングル サインオン\)** セクションで、次の手順に従います。

    ![Configure On App Side](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. **[Sign In URL]\(サインイン URL\)** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[Sign Out URL]\(サインアウト URL\)** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    c. **[Issuer URL]\(発行者 URL\)** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    d. **[Browse]\(参照\)** ボタンをクリックして、Azure portal からダウンロードした証明書をアップロードします。

    e. **[保存]** ボタンをクリックします。

### <a name="create-8x8-test-user"></a>8x8 のテスト ユーザーの作成

このセクションでは、8x8 で Britta Simon というユーザーを作成します。 [8x8 サポート チーム](https://www.8x8.com/about-us/contact-us)と連携して、8x8 プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる 8x8 のサインオン URL にリダイレクトされます。  

* 8x8 のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した 8x8 に自動的にサインインされます 

また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで 8x8 タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した 8x8 に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

8x8 を構成したら、ご自分の組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。