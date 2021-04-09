---
title: 'チュートリアル: Azure Active Directory と InsideView の統合 | Microsoft Docs'
description: このチュートリアルでは、Azure Active Directory と InsideView の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: b8f12b6d55cb11c0cfeda5331072127ea4a696bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460106"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>チュートリアル: Azure Active Directory と InsideView の統合

このチュートリアルでは、InsideView と Azure Active Directory (Azure AD) を統合する方法について説明します。
この統合には、次の利点があります。

* Azure AD を使用して、InsideView にアクセスできるユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントを使用して InsideView に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

InsideView と Azure AD の統合を構成するには、以下が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンが有効な InsideView サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* InsideView では、IdP Initiated SSO がサポートされます。

## <a name="add-insideview-from-the-gallery"></a>ギャラリーからの InsideView の追加

Azure AD への InsideView の統合を設定するには、ギャラリーからマネージド SaaS アプリの一覧に InsideView を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに「**InsideView**」と入力します。 検索結果で **[InsideView]** を選択し、**[追加]** を選択します。

    ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、InsideView に対する Azure AD シングル サインオンを構成およびテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと InsideView の対応するユーザーの間で、関係を確立する必要があります。

InsideView に対する Azure AD シングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[InsideView シングル サインオンを構成](#configure-insideview-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. **[InsideView テスト ユーザーを作成](#create-an-insideview-test-user)** して、Azure AD の対応するユーザーにリンクさせます。
6. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

InsideView に対する Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の InsideView アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集** アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を実行します。

    ![[基本的な SAML 構成] ダイアログ ボックス](common/idp-reply.png)

    **[応答 URL]** ボックスに、次のパターンで URL を入力します。

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > この値は、プレースホルダーです。 実際の応答 URL を使用する必要があります。 この値を取得するには、[InsideView サポート チーム](mailto:support@insideview.com)に連絡してください。 また、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、要件に従って **[証明書 (未加工)]** の横にある **[ダウンロード]** リンクを選択し、証明書をコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificateraw.png)

6. **[Set up InsideView]\(InsideView の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **[ログイン URL]** 。

    1. **[Azure AD 識別子]** 。

    1. **[ログアウト URL]** 。

### <a name="configure-insideview-single-sign-on"></a>InsideView シングル サインオンの構成

1. 新しい Web ブラウザー ウィンドウで、InsideView 企業サイトに管理者としてサインインします。

1. ウィンドウ上部で **[Admin]\(管理\)**、**[SingleSignOn Settings]\(シングル サインオンの設定\)**、**[Add SAML]\(SAML の追加\)** の順に選択します。
   
   ![SAML シングル サインオンの設定](./media/insideview-tutorial/ic794135.png "SAML シングル サインオンの設定")

1. **[Add a New SAML]\(新しい SAML の追加\)** セクションで、次の手順を実行します。

    ![[Add a New SAML]\(新しい SAML の追加\) セクション](./media/insideview-tutorial/ic794136.png "[Add a New SAML]\(新しい SAML の追加\) セクション")

    1. **[STS Name]\(STS 名\)** ボックスに、構成の名前を入力します。

    1. **[SamlP/WS-Fed Unsolicited EndPoint]\(SamlP/WS-Fed 未承諾エンドポイント\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    1. Azure portal からダウンロードした未加工の証明書を開きます。 証明書の内容をクリップボードにコピーしてから、その内容を **[STS Certificate]\(STS 証明書\)** ボックスに貼り付けます。

    1. **[Crm User Id Mapping]\(Crm ユーザー ID マッピング\)** ボックスに、「**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`**」と入力します。

    1. **[Crm Email Mapping]\(Crm メール マッピング\)** ボックスに、「**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`**」と入力します。

    1. **[Crm First Name Mapping]\(Crm 名マッピング\)** ボックスに、「**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`**」と入力します。

    1. **[Crm lastName Mapping]\(Crm 姓マッピング\)** ボックスに、「**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`**」と入力します。  

    1. **[保存]** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal で、左側のウィンドウの **[Azure Active Directory]** を選択し、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[すべてのユーザー] を選択する](common/users.png)

2. ウィンドウの上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] を選択する](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに「**BrittaSimon@\<yourcompanydomain>.\<extension>** 」と入力します (例: BrittaSimon@contoso.com)。

    1. **[パスワードを表示]** を選択し、 **[パスワード]** ボックス内の値を書き留めます。

    1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に InsideView へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[InsideView]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、**[InsideView]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、ウィンドウの下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 ウィンドウの下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-an-insideview-test-user"></a>InsideView のテスト ユーザーの作成

Azure AD ユーザーが InsideView にサインインできるようにするには、それらを InsideView に追加する必要があります。 手動で追加する必要があります。

InsideView でユーザーまたは連絡先を作成するには、[InsideView サポート チーム](mailto:support@insideview.com)にお問い合わせください。

> [!NOTE]
> InsideView から提供されているユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネルで [InsideView] タイルを選択すると、SSO を設定した InsideView インスタンスに自動的にサインインします。 アクセス パネルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)