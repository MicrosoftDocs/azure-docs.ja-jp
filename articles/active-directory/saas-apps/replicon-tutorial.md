---
title: チュートリアル:Azure Active Directory と Replicon の統合 | Microsoft Docs
description: Azure Active Directory と Replicon の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 8915d780e79fa219428c54bad5458ab5966df6c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688503"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>チュートリアル:Azure Active Directory と Replicon の統合

このチュートリアルでは、Replicon と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Replicon を統合すると、次のことができます。

* Replicon にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Replicon に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* Replicon でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Replicon では、**SP** によって開始される SSO がサポートされます。

## <a name="adding-replicon-from-the-gallery"></a>ギャラリーからの Replicon の追加

Azure AD への Replicon の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Replicon を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Replicon**」と入力します。
1. 結果ウィンドウで **[Replicon]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Replicon に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Replicon の関連ユーザーとの間にリンク関係を確立する必要があります。

Replicon で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Replicon SSO の構成](#configure-replicon-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Replicon のテスト ユーザーの作成](#create-replicon-test-user)** - Replicon で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Replicon** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    1. **[サインオン URL]** ボックスに、`https://global.replicon.com/!/saml2/<client name>/sp-sso/post` という形式で URL を入力します。

    1. **[識別子]** ボックスに、`https://global.replicon.com/!/saml2/<client name>` という形式で URL を入力します。

    1. **[応答 URL]** ボックスに、`https://global.replicon.com/!/saml2/<client name>/sso/post` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Replicon クライアント サポート チーム](https://www.replicon.com/customerzone/contact-support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML 署名証明書]** の編集/ペン アイコンをクリックして設定を編集します。

    ![署名アルゴリズム](common/signing-algorithm.png)

    1. **[署名オプション]** として **[SAML アサーションへの署名]** を選択します。

    1. **[署名アルゴリズム]** として **[SHA-256]** を選択します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Replicon の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Replicon 企業サイトに管理者としてサインインします。

2. SAML 2.0 を構成するには、次の手順に従います。

    ![SAML 認証の有効化](./media/replicon-tutorial/ic777805.png "SAML 認証の有効化")

    a. **[EnableSAML Authentication2]** ダイアログを表示するには、URL の企業キーの後に次のパスを追加します。`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * 完全な URL のスキーマを次に示します。`https://na2.replicon.com/<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. **+** をクリックして、 **[v20Configuration]** セクションを展開します。

   c. **+** をクリックして、 **[metaDataConfiguration]** セクションを展開します。

   d. xmlSignatureAlgorithm に **[SHA256]** を選択します。

   e. **[Choose File]** をクリックして、ID プロバイダー メタデータ XML ファイルを選択し、 **[送信]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Replicon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Replicon]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-replicon-test-user"></a>Replicon テスト ユーザーの作成

このセクションの目的は、Replicon で B.Simon というユーザーを作成することです。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. Web ブラウザー ウィンドウで、Replicon 企業サイトに管理者としてサインインします。

2. **[Administration] \> [Users]** に移動します。

    ![ユーザー](./media/replicon-tutorial/ic777806.png "ユーザー")

3. **[+Add User]** をクリックします。

    ![ユーザーの追加](./media/replicon-tutorial/ic777807.png "ユーザーの追加")

4. **[User Profile]** セクションで、次の手順に従います。

    ![ユーザー プロファイル](./media/replicon-tutorial/ic777808.png "User Profile")

    a. **[Login Name]\(ログイン名\)** ボックスに、プロビジョニングする Azure AD ユーザーの Azure AD メール アドレスを入力します (例: `B.Simon@contoso.com`)。

    > [!NOTE]
    > [Login Name]\(ログイン名\) は Azure AD のユーザーのメール アドレスと一致する必要があります。

    b. **[Authentication Type]** として **[SSO]** を選択します。

    c. [Authentication ID]\(認証 ID\) を [Login Name]\(ログイン名\) (ユーザーの Azure AD のメール アドレス) と同じ値に設定します

    d. **[Department]** テキストボックスに、ユーザーの所属を入力します。

    e. **[Employee Type]** として **[Administrator]** を選択します。

    f. **[Save User Profile]** をクリックします。

> [!NOTE]
> Replicon から提供されている他の Replicon ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Replicon] タイルを選択すると、SSO を設定した Replicon に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)