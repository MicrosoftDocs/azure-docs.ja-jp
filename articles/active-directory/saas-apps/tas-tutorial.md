---
title: チュートリアル:Azure Active Directory と TAS の統合 | Microsoft Docs
description: Azure Active Directory と TAS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 129b6e69-e3b4-41d7-9ab5-a2ddd0068f76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: efd64830c0d9afa83838adef96cf1c103b4485e8
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943272"
---
# <a name="tutorial-azure-active-directory-integration-with-tas"></a>チュートリアル:Azure Active Directory と TAS の統合

このチュートリアルでは、TAS と Azure Active Directory (Azure AD) を統合する方法について説明します。
TAS と Azure AD の統合には、次の利点があります。

* TAS にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TAS に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

TAS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* TAS でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TAS では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-tas-from-the-gallery"></a>ギャラリーからの TAS の追加

Azure AD への TAS の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に TAS を追加する必要があります。

**ギャラリーから TAS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**TAS**」と入力し、結果パネルで **[TAS]** を選択してから **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の TAS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、TAS で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと TAS 内の関連ユーザー間にリンク関係が確立されている必要があります。

TAS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[TAS シングル サインオンの構成](#configure-tas-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[TAS テスト ユーザーの作成](#create-tas-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを TAS で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

TAS で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **TAS** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[TAS のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://taseu.combtas.com/<DOMAIN>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://taseu.combtas.com/<ENVIRONMENTNAME>/AssertionService.aspx` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[TAS のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://taseu.combtas.com/<DOMAIN>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらを実際の識別子、応答 URL、およびサインオン URL に値を置き換えます。実際の値については、チュートリアルの後の方で説明します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[TAS のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-tas-single-sign-on"></a>TAS シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、TAS に管理者としてログインします。

2. メニューの左側で **[Settings]\(設定\)** をクリックして、 **[Administrator]\(管理者\)** に移動し、 **[Manage Single sign on]\(シングル サインオンの管理\)** をクリックします。

    ![TAS の構成](./media/tas-tutorial/configure01.png)

3. **[Manage Single sign on]\(シングル サインオンの管理\)** ページで、次の手順を実行します。

    ![TAS の構成](./media/tas-tutorial/configure02.png)

    a. **[Name]\(名前\)** ボックスに環境の名前を入力します。
    
    b. **[Authentication Type]\(認証の種類\)** として **[SAML2]** を選択します。

    c. **[Enter URL]\(URL の入力\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[Enter Certification]\(証明書の入力\)** ボックスに貼り付けます。

    e. **[Enter New IP]\(新しい IP の入力\)** ボックスに、IP アドレスを入力します。

    >[!NOTE]
    > IP アドレスを取得するには、[TAS サポート チーム](mailto:support@combtas.com)にお問い合わせください。

    f. **シングル サインオン** URL をコピーして、Azure portal の **[基本的な SAML 構成]** の **[識別子 (エンティティ ID)]** および **[サインオン URL]** ボックスに貼り付けます。 URL は、大文字と小文字が区別され、スラッシュ (/) で終わる必要があることに注意してください。

    g. セットアップ ページの**アサーション サービス** URL をコピーし、Azure portal の **[基本的な SAML 構成]** の **[応答 URL]** ボックスに貼り付けます。

    h. **[Insert SSO row]\(SSO 行の挿入\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TAS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[TAS]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[TAS]** を選択します。

    ![アプリケーションの一覧の TAS のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-tas-test-user"></a>TAS テスト ユーザーの作成

このセクションでは、TAS で Britta Simon というユーザーを作成します。 [TAS サポート チーム](mailto:support@combtas.com)と連携し、TAS プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [TAS] タイルをクリックすると、SSO を設定した TAS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

