---
title: チュートリアル:Azure Active Directory と Everbridge の統合 | Microsoft Docs
description: Azure Active Directory と Everbridge の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103250"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>チュートリアル:Azure Active Directory と Everbridge の統合

このチュートリアルでは、Everbridge と Azure Active Directory (Azure AD) を統合する方法について説明します。
Azure AD と Everbridge を統合すると、次のことができます。

* Everbridge にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Everbridge に自動的にサインインできるようにする。 このアクセス制御はシングル サインオン (SSO) と呼ばれます。
* Azure portal を使用して 1 つの中央サイトでアカウントを管理する。
サービスとしてのソフトウェア (SaaS) アプリと Azure AD との統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」をご覧ください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Everbridge の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンを使用する Everbridge サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Everbridge では、IDP によって開始される SSO がサポートされます。

## <a name="add-everbridge-from-the-azure-marketplace"></a>Azure Marketplace から Everbridge を追加する

Azure AD への Everbridge の統合を構成するには、Azure Marketplace からマネージド SaaS アプリの一覧に Everbridge を追加します。

Azure Marketplace から Everbridge を追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] ボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Everbridge**」と入力します。 結果パネルで **[Everbridge]** を選択し、 **[追加]** を選択します。

     ![結果リスト内の Everbridge](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon というテスト ユーザーに基づいて、Everbridge で Azure AD のシングル サインオンを構成してテストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Everbridge の関連ユーザーとの間にリンク関係を確立します。

Everbridge で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了します。

- [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
- [Everbridge を Everbridge Manager Portal シングル サインオンとして構成](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) - アプリケーション側でシングル サインオン設定を構成します。
- [Everbridge を Everbridge Member Portal シングル サインオンとして構成](#configure-everbridge-as-everbridge-member-portal-single-sign-on) - アプリケーション側でシングル サインオン設定を構成します。
- [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
- [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
- [Everbridge テスト ユーザーの作成](#create-an-everbridge-test-user) - Everbridge で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
- [シングル サインオンのテスト](#test-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Everbridge で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Everbridge** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** を選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

    >[!NOTE]
    >Azure portal と Everbridge ポータルの両方で、アプリケーションを Manager Portal *または* Member Portal として構成します。

4. **Everbridge** アプリケーションを **Everbridge Manager Portal** として構成するには、 **[基本的な SAML 構成]** セクションで次の手順に従います。

    ![Everbridge のドメインと URL のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。`https://sso.everbridge.net/<API_Name>`

    b. **[応答 URL]** ボックスに、次の形式で URL を入力します。`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL の値でこれらの値を更新します。 これらの値を取得するには、[Everbridge サポート チーム](mailto:support@everbridge.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **Everbridge** アプリケーションを **Everbridge Member Portal** として構成するには、 **[基本的な SAML 構成]** セクションで次の手順に従います。

  * IDP 開始モードでアプリケーションを構成する場合は、次の手順に従います。

     ![IDP 開始モードでの Everbridge のドメインと URL のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. **[応答 URL]** ボックスに、次の形式で URL を入力します。`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * アプリケーションを SP 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順に従います。

     ![SP 開始モードでの Everbridge のドメインと URL のシングル サインオン情報](common/both-signonurl.png)

     a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL の値で更新してください。 これらの値を取得するには、[Everbridge サポート チーム](mailto:support@everbridge.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択して**フェデレーション メタデータ XML** をダウンロードします。 これをコンピューターに保存します。

    ![証明書のダウンロード リンク](common/metadataxml.png)

7. **[Set up Everbridge]\(Everbridge の設定\)** セクションで、要件に従って必要な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    - ログイン URL
    - Azure AD 識別子
    - ログアウト URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Everbridge を Everbridge Manager Portal シングル サインオンとして構成

**Everbridge Manager Portal** アプリケーションとしての **Everbridge** で SSO を構成するには、次の手順に従います。
 
1. 別の Web ブラウザー ウィンドウで、Everbridge に管理者としてサインインします。

1. 上部のメニューで **[Settings]\(設定)\** タブを選択します。 **[Serucity]\(セキュリティ)\** の下で **[Single Sign-On]\(シングル サインオン)\** を選択します。
   
     ![Configure single sign-on](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. **[Name]\(名前)\** ボックスに、識別子プロバイダーの名前を入力します。 たとえば、自分の会社名などです。
   
     b. **[API Name]\(API 名)\** ボックスに、API の名前を入力します。
   
     c. **[Choose File]\(ファイルの選択)\** を選択して、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。
   
     d. **[SAML Identity Location]\(SAML ID の場所\)** で、 **[Identity is in the NameIdentifier element of the Subject statement]\(ID を Subject ステートメントの NameIdentifier 要素にする\)** をオンにします。
   
     e. **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。
   
     f. **[Service Provider initiated Request Binding]\(サービス プロバイダーが開始した要求のバインド)\** で **[HTTP Redirect]\(HTTP リダイレクト)\** を選択します。

     g. **[保存]** を選択します。

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Everbridge を Everbridge Member Portal シングル サインオンとして構成

**Everbridge Member Portal** としての **Everbridge** でシングル サインオンを構成するには、ダウンロードした**フェデレーション メタデータ XML** を [Everbridge のサポート チーム](mailto:support@everbridge.com)に送信します。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

Azure portal で Britta Simon というテスト ユーザーを作成するには、次の手順に従います。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] のリンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** ボックスに「`brittasimon@yourcompanydomain.extension`」と入力します。 例: BrittaSimon@contoso.com。

    c. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

Britta Simon に Everbridge へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  > **[Everbridge]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Everbridge]** を選択します。

    ![アプリケーションの一覧の Everbridge のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ダイアログ ボックス](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。 画面の下部にある **[選択]** を選択します。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** を選択します。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-an-everbridge-test-user"></a>Everbridge テスト ユーザーの作成

このセクションでは、Everbridge で Britta Simon というテスト ユーザーを作成します。 Everbridge プラットフォームにユーザーを追加するには、[Everbridge サポート チーム](mailto:support@everbridge.com)と連携します。 シングル サインオンを使用する前に、Everbridge でユーザーを作成して有効化しておく必要があります。 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Everbridge] タイルを選択すると、SSO を設定した Everbridge アカウントに自動的にサインインできます。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

