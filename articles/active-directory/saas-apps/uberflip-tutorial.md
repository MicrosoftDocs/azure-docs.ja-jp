---
title: 'チュートリアル: Azure Active Directory と Uberflip の統合 | Microsoft Docs'
description: Azure Active Directory と Uberflip の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 6bf3eccec70619bf73397e315c6d851f753ec52f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521834"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>チュートリアル: Azure Active Directory と Uberflip の統合

このチュートリアルでは、Uberflip と Azure Active Directory (Azure AD) を統合する方法について説明します。

Uberflip と Azure AD の統合には、次の利点があります。

* Uberflip にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Uberflip に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Uberflip と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* シングル サインオンが有効な Uberflip のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

Uberflip では、次の機能をサポートしています。

* SP または IDP が起点となるシングル サインオン (SSO)。
* Just-In-Time のユーザー プロビジョニング。

## <a name="add-uberflip-from-the-azure-marketplace"></a>Azure Marketplace から Uberflip を追加する

Azure AD への Uberflip の統合を構成するには、Azure Marketplace からマネージド SaaS アプリの一覧に Uberflip を追加する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインする
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。

   ![[Azure Active Directory] オプション](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

   ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. 新しいアプリケーションを追加するには、ウィンドウの上部の **[+ 新しいアプリケーション]** を選択します。

   ![[新しいアプリケーション] オプション](common/add-new-app.png)

1. 検索ボックスに「**Uberflip**」と入力します。 検索結果で **[Uberflip]** を選択し、 **[追加]** を選択してアプリケーションを追加します。

   ![結果一覧の Uberflip](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**B Simon** というテスト ユーザーに基づいて、Uberflip で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと Uberflip 内の関連ユーザーとの間にリンクを確立する必要があります。

Uberflip で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Uberflip シングル サインオンの構成](#configure-uberflip-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
1. **[Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user)** - B. Simon で Azure AD のシングル サインオンをテストします。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B. Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Uberflip のテスト ユーザーの作成](#create-an-uberflip-test-user)** - B. Simon という Azure AD ユーザーにリンクされている B. Simon というユーザーが Uberflip に存在するようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Uberflip で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Uberflip** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン オプションを構成する](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、**編集** (鉛筆アイコン) を選択して **[基本的な SAML 構成]** ウィンドウを開きます。

   ![[基本的な SAML 構成] を示すスクリーンショット。ここに応答 U R L を入力することができます。](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、構成する SSO モードに応じて、以下のいずれかの手順に従います。

   * IDP が起点となる SSO モードでアプリケーションを構成するには、 **[応答 URL (Assertion Consumer Service URL)]** ボックスに、次のパターンを使用して URL を入力します。

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![[Uberflip のドメインと URL] のシングル サインオン情報](common/both-replyurl.png)

     > [!NOTE]
     > これは実際の値ではありません。 実際の応答 URL でこの値を更新します。 実際の値を取得するには、[Uberflip サポート チーム](mailto:support@uberflip.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** ウィンドウに示されているパターンを参照することもできます。

   * SP が起点となる SSO モードでアプリケーションを構成するには、 **[追加の URL を設定します]** を選択し、 **[サインオン URL]** ボックスに次の URL を入力します。

     `https://app.uberflip.com/users/login`

     ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/both-signonurl.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択し、特定のオプションの **フェデレーション メタデータ XML** をダウンロードします。

   ![フェデレーション メタデータ XML のダウンロード オプション](common/metadataxml.png)

1. **[Uberflip のセットアップ]** ウィンドウで、必要な URL をコピーします。

   * **ログイン URL**
   * **Azure AD 識別子**
   * **ログアウト URL**

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Uberflip シングル サインオンの構成

Uberflip 側でシングル サインオンを構成するには、ダウンロードしたフェデレーション メタデータ XML と Azure portal からコピーした適切な URL を [Uberflip サポート チーム](mailto:support@uberflip.com)に送信する必要があります。 Uberflip チームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] オプション](common/users.png)

1. 画面の上部にある **[+ 新しいユーザー]** を選択します。

    ![[新しいユーザー] オプション](common/new-user.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    ![[ユーザー] ウィンドウ](common/user-properties.png)

    1. **[名前]** ボックスに、「**BSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに「**BSimon\@\<yourcompanydomain>.\<extension>** 」と入力します。 たとえば、「**BSimon\@contoso.com**」のように入力します。

    1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

    1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に Uberflip へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Uberflip]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Uberflip]** を選択します。

    ![アプリケーションの一覧の Uberflip](common/all-applications.png)

1. 左側のウィンドウの **[管理]** で、 **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] オプション](common/users-groups-blade.png)

1. **[+ ユーザーの追加]** を選択し、 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ウィンドウの **[ユーザー]** の一覧で **[B Simon]** を選択し、ウィンドウの下部にある **[選択]** を選択します。

1. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ウィンドウで、一覧からユーザーに適したロールを選択します。 ウィンドウの下部で、 **[選択]** を選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-an-uberflip-test-user"></a>Uberflip テスト ユーザーを作成する

これで、B. Simon というユーザーが Uberflip に作成されました。 このユーザーを作成するために、何かをする必要はありません。 Uberflip では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 Uberflip に B. Simon というユーザーがまだ存在しない場合は、認証後に新しく作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Uberflip サポート チーム](mailto:support@uberflip.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

マイ アプリ ポータルで **[Uberflip]** を選択すると、シングル サインオンを設定した Uberflip サブスクリプションに自動的にサインインするはずです。 マイアプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアルの一覧](./tutorial-list.md)

* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

* [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)