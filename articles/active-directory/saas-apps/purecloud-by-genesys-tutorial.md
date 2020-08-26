---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と PureCloud by Genesys の統合 | Microsoft Docs
description: Azure Active Directory と PureCloud by Genesys の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 0d2953710c559deb094761410e6d46401e103d8b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553289"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と PureCloud by Genesys の統合

このチュートリアルでは、PureCloud by Genesys と Azure Active Directory (Azure AD) を統合する方法について説明します。 その後、次の操作を実行できます。

* PureCloud by Genesys にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して PureCloud by Genesys に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 お持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* PureCloud by Genesys でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* PureCloud by Genesys では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

> [!NOTE]
> このアプリケーションの ID は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>ギャラリーからの PureCloud by Genesys の追加

Azure AD への PureCloud by Genesys の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に PureCloud by Genesys を追加する必要があります。 そのためには、次の手順に従います。

1. 職場または学校アカウントを使用するか、個人用 Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**PureCloud by Genesys**」と入力します。
1. 結果のパネルから **[PureCloud by Genesys]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>PureCloud by Genesys の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、PureCloud by Genesys に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと PureCloud by Genesys の関連ユーザーとの間にリンク関係を確立する必要があります。

PureCloud by Genesys に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[PureCloud by Genesys の SSO の構成](#configure-purecloud-by-genesys-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[PureCloud by Genesys のテスト ユーザーの作成](#create-purecloud-by-genesys-test-user)** - PureCloud by Genesys で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、これらの手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **PureCloud by Genesys** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** のペン アイコンを選択して設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、自分のリージョンに対応する URL を入力します。

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. **[応答 URL]** ボックスに、自分のリージョンに対応する URL を入力します。

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    **[サインオン URL]** ボックスに、自分のリージョンに対応する URL を入力します。
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. PureCloud by Genesys アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、PureCloud by Genesys アプリケーションでは、次の表に示すとおり、いくつかの属性が SAML 応答で返されることが想定されています。 これらの属性も値が事前に設定されますが、必要に応じてそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[PureCloud by Genesys のセットアップ]** セクションで、要件に基づいて適切な 1 つ以上の URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに、username@companydomain.extension という形式でユーザー名を入力します。 (例: `B.Simon@contoso.com`)。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、PureCloud by Genesys へのアクセスを許可して、B.Simon が Azure シングル サインオンを使用できるように設定します。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[PureCloud by Genesys]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスの [ユーザー] の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンを選択します。
1. SAML アサーションにロール値が必要な場合は、 **[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** ボタンを選択します。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンを選択します。

## <a name="configure-purecloud-by-genesys-sso"></a>PureCloud by Genesys の SSO の構成

1. 別の Web ブラウザー ウィンドウで、PureCloud by Genesys に管理者としてサインインします。

1. 上部の **[Admin] (管理)** を選択し、 **[Integrations] (統合)** の **[Single Sign-on] (シングル サインオン)** に移動します。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure01.png)

1. **[ADFS/Azure AD (Premium)]** タブに切り替えて、次の手順に従います。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. **[Browse] (参照)** を選択して、Azure portal からダウンロードした base 64 でエンコードされた証明書を **[ADFS Certificate] (ADFS 証明書)** にアップロードします。

    b. **[ADFS Issuer URI] (ADFS 発行者の URI)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[Target URI] (ターゲット URI)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[Relying Party Identifier] (証明書利用者識別子)** の値については、Azure portal に移動し、**PureCloud by Genesys** アプリケーション統合ページで **[プロパティ]** タブを選択して、 **[アプリケーション ID]** の値をコピーします。 それを **[Relying Party Identifier] (証明書利用者識別子)** ボックスに貼り付けます。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. **[保存]** を選択します。

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud by Genesys のテスト ユーザーの作成

Azure AD ユーザーが PureCloud by Genesys にサインインできるようにするには、それらを PureCloud by Genesys にプロビジョニングする必要があります。 PureCloud by Genesys では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順のようにします。**

1. PureCloud by Genesys に管理者としてログインします。

1. 上部の **[Admin] (管理)** を選択し、 **[People & Permissions] (ユーザーとアクセス許可)** の **[People] (ユーザー)** に移動します。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure03.png)

1. **[People] (ユーザー)** ページで、 **[Add Person] (ユーザーの追加)** を選択します。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure04.png)

1. **[Add People to the Organization] (組織へのユーザーの追加)** ダイアログボックスで、次の手順に従います。

    ![Configure single sign-on](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. **[Full Name] (フル ネーム)** ボックスに、ユーザーの名前を入力します。 次に例を示します。**B.simon**。

    b. **[Email] (メール)** ボックスに、ユーザーのメール アドレスを入力します。 例: **b.simon\@contoso.com**。

    c. **［作成］** を選択します

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで **[PureCloud by Genesys]** タイルを選択すると、SSO を設定した PureCloud by Genesys アカウントに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure AD の統合方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure AD のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure AD の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で PureCloud by Genesys を試す](https://aad.portal.azure.com/)
