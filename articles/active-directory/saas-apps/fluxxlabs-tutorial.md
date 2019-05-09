---
title: チュートリアル:Azure Active Directory と Fluxx Labs の統合 | Microsoft Docs
description: Azure Active Directory と Fluxx Labs の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e1951a65c48c32f2ce4af722400d03c20dfa684b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565395"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>チュートリアル:Azure Active Directory と Fluxx Labs の統合

このチュートリアルでは、Fluxx Labs と Azure Active Directory (Azure AD) を統合する方法について説明します。
Fluxx Labs と Azure AD の統合には、次の利点があります。

* Fluxx Labs にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Fluxx Labs に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Fluxx Labs と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Fluxx Labs のシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Fluxx Labs では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-fluxx-labs-from-the-gallery"></a>ギャラリーからの Fluxx Labs の追加

Azure AD への Fluxx Labs の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Fluxx Labs を追加する必要があります。

**ギャラリーから Fluxx Labs を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Fluxx Labs**」と入力し、結果ウィンドウで **[Fluxx Labs]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Fluxx Labs](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Fluxx Labs で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Fluxx Labs 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Fluxx Labs で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Fluxx Labs シングル サインオンの構成](#configure-fluxx-labs-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Fluxx Labs テスト ユーザーの作成](#create-fluxx-labs-test-user)** - Fluxx Labs で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Fluxx Labs で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Fluxx Labs** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[Fluxx Labs のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    | 環境 | URL パターン|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | 運用前 | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | 環境 | URL パターン|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | 運用前 | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Fluxx Labs クライアント サポート チーム](mailto:travis@fluxxlabs.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Fluxx Labs のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Fluxx Labs でのシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、Fluxx Labs 企業サイトに管理者としてサインインします。

2. **[Settings]\(設定\)** セクションで **[Admin]\(管理者\)** を選択します。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config1.png)

3. 管理パネルで、**[Plug-ins]\(プラグイン\)** > **[Integrations]\(インテグレーション\)** の順に選択し、**[SAML SSO-(Disabled)]\(SAML SSO (無効)\)** を選択します。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config2.png)

4. [Attribute]\(属性\) セクションで、次の手順に従います。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config3.png)

    a. **[SAML SSO]** チェックボックスをオンにします。

    b. **[Request Path]\(要求パス\)** ボックスに、「**/auth/saml**」と入力します。

    c. **[Callback Path]\(コールバック パス\)** ボックスに、「**/auth/saml/callback**」と入力します。

    d. **[Assertion Consumer Service Url(Single Sign-On URL)]\(Assertion Consumer Service URL (シングル サインオン URL)\)** テキストボックスに、Azure portal で入力した **[応答 URL]** の値を入力します。

    e. **[Audience(SP Entity ID)]\(対象 (SP エンティティ ID)\)** テキストボックスに、Azure portal で入力した **[ID]** の値を入力します。

    f. **[Identity Provider SSO Target URL]\(ID プロバイダーの SSO ターゲット URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    g. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    h. **[Name identifier Format]\(名前識別子の形式\)** ボックスに、次の値を入力します。`urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`

    i. **[Save]** をクリックします。

    > [!NOTE]
    > 内容が保存されると、フィールドはセキュリティのために空白で表示されますが、値は構成内に保存されています。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Fluxx Labs へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Fluxx Labs]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Fluxx Labs]** を選択します。

    ![アプリケーションの一覧の Fluxx Labs のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-fluxx-labs-test-user"></a>Fluxx Labs テスト ユーザーを作成する

Azure AD ユーザーが Fluxx Labs にサインインできるようにするには、そのユーザーを Fluxx Labs にプロビジョニングする必要があります。 Fluxx Labs の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Fluxx Labs 企業サイトに管理者としてサインインします。

2. 次の図に示されている**アイコン**をクリックします。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config6.png)

3. ダッシュボードで、次の図に示されているアイコンをクリックして、**[New PEOPLE]\(新規ユーザー\)** カードを開きます。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config4.png)

4. **[NEW PEOPLE]\(新規ユーザー\)** セクションで、次の手順を実行します。

    ![Fluxx Labs の構成](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs では、SSO ログインの一意識別子として電子メールを使用します。 **[SSO UID]** フィールドにユーザーの電子メール アドレスを入力します。これは、SSO でのログインとして使用される電子メールアドレスと一致する電子メール アドレスです。

    b. **[Save]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Fluxx Labs] タイルをクリックすると、SSO を設定した Fluxx Labs に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

