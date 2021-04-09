---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と ThousandEyes の統合 | Microsoft Docs
description: Azure Active Directory と ThousandEyes の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: aa3c5115a5255d30decbc66691878ffbe2579a06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514590"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と ThousandEyes の統合

このチュートリアルでは、ThousandEyes と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と ThousandEyes を統合すると、次のことができます。

* ThousandEyes にアクセスできるユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して ThousandEyes に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ThousandEyes でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* ThousandEyes では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* ThousandEyes では、[**自動化された** ユーザー プロビジョニング](./thousandeyes-provisioning-tutorial.md)がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-thousandeyes-from-the-gallery"></a>ギャラリーからの ThousandEyes の追加

Azure AD への ThousandEyes の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ThousandEyes を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**ThousandEyes**」と入力します。
1. 結果のパネルから **[ThousandEyes]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>ThousandEyes の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、ThousandEyes に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと ThousandEyes の関連ユーザーとの間にリンク関係を確立する必要があります。

ThousandEyes で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ThousandEyes の SSO の構成](#configure-thousandeyes-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[ThousandEyes のテスト ユーザーの作成](#create-thousandeyes-test-user)** - ThousandEyes で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **ThousandEyes** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.thousandeyes.com/login/sso`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[ThousandEyes のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に ThousandEyes へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[ThousandEyes]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-thousandeyes-sso"></a>ThousandEyes の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として **ThousandEyes** 企業サイトにサインオンします。

2. 上部のメニューで **[Settings]** をクリックします。

    ![[Settings]\(設定\) が選択されている ThousandEyes サイトを示すスクリーンショット。](./media/thousandeyes-tutorial/ic790066.png "設定")

3. ページの下部にある **[Account]**

    ![[Settings]\(設定\) メニューから [Account]\(アカウント\) が選択されていることを示すスクリーンショット。](./media/thousandeyes-tutorial/ic790067.png "Account")

4. **[Security & Authentication]** タブをクリックします。

    ![[Security & Authentication]\(セキュリティと認証\)](./media/thousandeyes-tutorial/ic790068.png "[Security & Authentication]")

5. **[Setup Single Sign-On]** セクションで、次の手順を実行します。

    ![[Setup Single Sign-On]\(シングル サインオンの設定\)](./media/thousandeyes-tutorial/ic790069.png "[Setup Single Sign-On]")

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[Login Page URL]\(ログイン ページの URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。

    c. **[Logout Page URL]\(ログアウト ページの URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** を貼り付けます。

    d. **[Identity Provider Issuer]\(ID プロバイダー発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** を貼り付けます。

    e. **[Verification Certificate]\(検証証明書\)** で **[Choose file]\(ファイルの選択\)** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。

    f. **[保存]** をクリックします。

### <a name="create-thousandeyes-test-user"></a>ThousandEyes テスト ユーザーの作成

このセクションの目的は、ThousandEyesで Britta Simon というユーザーを作成することです。 ThousandEyes では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](thousandeyes-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. ThousandEyes 企業サイトに管理者としてサインインします。

2. **[設定]** をクリックします。

    ![[Settings]\(設定\) が選択されている ThousandEyes サイトを示すスクリーンショット。](./media/thousandeyes-tutorial/ic790066.png "設定")

3. **[アカウント]** クリックします。

    ![[Settings]\(設定\) メニューから [Account]\(アカウント\) が選択されていることを示すスクリーンショット。](./media/thousandeyes-tutorial/ic790067.png "Account")

4. **[Accounts & Users]** タブをクリックします。

    ![[Accounts & Users]\(アカウントとユーザー\)](./media/thousandeyes-tutorial/IC790073.png "[Accounts & Users]")

5. **[Add Users & Accounts]** セクションで、次の手順を実行します。

    ![[Add User Accounts]\(ユーザー アカウントの追加\)](./media/thousandeyes-tutorial/IC790074.png "ユーザー アカウントの追加")

    a. **[Name]\(名前\)** ボックスに、ユーザーの名前を入力します (**B.Simon** など)。

    b. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレス (b.simon@contoso.com など) を入力します。

    b. **[Add New User to Account]** をクリックします。

    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、アカウントを確認してアクティブにするためのリンクを含むメールが送られます。

> [!NOTE]
> ThousandEyes から提供されている他の ThousandEyes ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。


## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [ThousandEyes] タイルをクリックすると、SSO を設定した ThousandEyes に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で ThousandEyes を試す](https://aad.portal.azure.com/)

- [[ユーザー プロビジョニングの構成]](./thousandeyes-provisioning-tutorial.md)