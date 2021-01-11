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
ms.date: 04/06/2020
ms.author: jeedes
ms.openlocfilehash: 3e1386fdfafdea3112e26ce6480c7d20061236ba
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549672"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Litmus の統合

このチュートリアルでは、Litmus と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Litmus を統合すると、次のことができます。

* Litmus にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Litmus に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Litmus でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Litmus では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Litmus を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-litmus-from-the-gallery"></a>ギャラリーからの Litmus の追加

Azure AD への Litmus の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Litmus を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Litmus**」と入力します。
1. 結果のパネルから **[Litmus]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-litmus"></a>Litmus の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Litmus に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Litmus の関連ユーザーとの間にリンク関係を確立する必要があります。

Litmus で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Litmus の SSO の構成](#configure-litmus-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Litmus のテスト ユーザーの作成](#create-litmus-test-user)** - Litmus で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Litmus** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
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

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-litmus-sso"></a>Litmus の SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として Litmus アプリケーションにサインインします。

1. 左側のナビゲーション パネルから、 **[Security]\(セキュリティ\)** をクリックします。

    ![Litmus の構成](./media/litmus-tutorial/security-img.png)

1. **[Configure SAML Authentication]\(SAML 認証の構成\)** セクションで、次の手順に従います。

    ![Litmus の構成](./media/litmus-tutorial/configure1.png)

    a. **[Enable SAML]\(SAML を有効にする\)** トグルをオンに切り替えます。

    b. プロバイダーに **[Generic]\(汎用\)** を選択します。

    c. **[Identity Provider Name]\(ID プロバイダー名\)** の名前を入力します。例: for ex. `Azure AD`

1. 次の手順に従います。

    ![Litmus の構成](./media/litmus-tutorial/configure3.png)

    a. **[SAML 2.0 Endpoint(HTTP)]\(SAML 2.0 エンドポイント (HTTP)\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. Azure portal からダウンロードした**証明書**ファイルをメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    c. **[Save SAML settings]\(SAML 設定の保存\)** をクリックします。

### <a name="create-litmus-test-user"></a>Litmus のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、管理者として Litmus アプリケーションにサインインします。

1. 左側のナビゲーション パネルから、 **[Accounts]\(アカウント\)** をクリックします。

    ![Litmus の構成](./media/litmus-tutorial/accounts-img.png)

1. **[Add New User]\(新しいユーザーの追加\)** タブをクリックします。

    ![Litmus の構成](./media/litmus-tutorial/add-new-user.png)

1. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![Litmus の構成](./media/litmus-tutorial/user-profile.png)

    a. **[Email]\(電子メール\)** テキスト ボックスに、ユーザーのメール アドレスを入力します ( **B.Simon@contoso.com** など)。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **B**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    d. **[Create User]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Litmus] タイルをクリックすると、SSO を設定した Litmus に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Litmus を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Litmus を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
