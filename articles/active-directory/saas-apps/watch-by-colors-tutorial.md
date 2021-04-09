---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Watch by Colors の統合 | Microsoft Docs
description: Azure Active Directory と Watch by Colors の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: fae276049ec3ff6c4bf96be38ff0d2a952731a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636784"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Watch by Colors の統合

このチュートリアルでは、Watch by Colors と Azure Active Directory (Azure AD) を統合する方法について説明します。 Watch by Colors を Azure AD と統合すると、次のことができます。

* Watch by Colors にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Watch by Colors に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Watch by Colors でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Watch by Colors では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-watch-by-colors-from-the-gallery"></a>ギャラリーからの Watch by Colors の追加

Azure AD への Watch by Colors の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Watch by Colors を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Watch by Colors**」と入力します。
1. 結果パネルで **[Watch by Colors]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Watch by Colors の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Watch by Colors に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Watch by Colors の関連ユーザーとの間にリンク関係を確立する必要があります。

Watch by Colors に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Watch by Colors SSO の構成](#configure-watch-by-colors-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Watch by Colors のテスト ユーザーの作成](#create-watch-by-colors-test-user)** - Watch by Colors で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Watch by Colors** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは **IDP** 開始モードで事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.colorscorporation.com/login`」と入力します。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に Watch by Colors へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Watch by Colors]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-watch-by-colors-sso"></a>Watch by Colors SSO の構成

1. Watch by Colors 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Watch by Colors のセットアップ]** をクリックすると、Watch by Colors アプリケーションに移動します。 そこから、管理者の資格情報を入力して Watch by Colors にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Watch by Colors を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Watch by Colors 企業サイトにサインインして、次の手順を実行します。

4. ページの右上隅で、 **[profile]\(プロファイル\)**  >  **[Account Settings]\(アカウント設定\)**  >  **[SSO (Single Sign On)]\(SSO (シングル サインオン)\)** の順にクリックします。

    ![SSO が無効になっている [Account Settings]\(アカウント設定\) ページを示すスクリーンショット。](./media/watch-by-colors-tutorial/config01.png)

5. **[SSO (Single Sign On)]\(SSO (シングル サインオン)\)** ページで、次の手順を実行します。

    ![[SAML Setup]\(SAML のセットアップ\) タブを示すスクリーンショット。ここで、SAML を有効にできます。](./media/watch-by-colors-tutorial/config02.png)

    a. **[Enable SAML]\(SAML を有効にする\)** を **[ON]\(オン\)** に切り替えます。

    b. **[URL]** ボックスに、Azure portal からコピーした **フェデレーション メタデータ URL** を貼り付けます。

    c. **[Import]\(インポート\)** をクリックすると、ページの後続のフィールドに自動的に値が入力されます。

    d. **[保存]** をクリックします。

### <a name="create-watch-by-colors-test-user"></a>Watch by Colors のテスト ユーザーの作成

Azure AD ユーザーが Watch by Colors にサインインできるようにするには、そのユーザーを Watch by Colors にプロビジョニングする必要があります。 Watch by Colors では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Watch by Colors にサインインします。

1. ページの右上隅で、 **[profile]\(プロファイル\)**  >  **[Users]\(ユーザー\)**  >  **[Add User]\(ユーザーの追加\)** の順にクリックします。

    ![[Users]\(ユーザー\) ページを示すスクリーンショット。](./media/watch-by-colors-tutorial/config03.png)

1. **[User Details]\(ユーザーの詳細\)** ページで、次の手順を実行します。

    ![[User Details]\(ユーザーの詳細\) ページを示すスクリーンショット。ここで説明されている値を入力できます。](./media/watch-by-colors-tutorial/config04.png)

    a. **[名]** ボックスに、ユーザーの名を入力します (例: **B**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    c. **[電子メール]** ボックスに、ユーザーのメール アドレスを入力します (例: `B.Simon@contoso.com`)。

    d. **[Password]\(パスワード\)** ボックスにパスワードを入力します。

    e. 組織に従って **アカウントのアクセス許可** を選択します。

    f. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Watch by Colors] タイルをクリックすると、SSO を設定した Watch by Colors に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD を使用して Watch by Colors を試す](https://aad.portal.azure.com/)