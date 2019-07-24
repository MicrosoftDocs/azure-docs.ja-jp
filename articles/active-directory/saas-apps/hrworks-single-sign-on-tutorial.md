---
title: チュートリアル:Azure Active Directory と HRworks Single Sign-On の統合 | Microsoft Docs
description: Azure Active Directory と HRworks Single Sign-On の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 06a10cab81b1253658f505b3cd3f2c520ef9cea8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266343"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>チュートリアル:Azure Active Directory と HRworks Single Sign-On の統合

このチュートリアルでは、HRworks Single Sign-On と Azure Active Directory (Azure AD) を統合する方法について説明します。
HRworks Single Sign-On と Azure AD の統合には、次の利点があります。

* HRworks Single Sign-On にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで HRworks Single Sign-On に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

HRworks Single Sign-On と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* HRworks Single Sign-On でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* HRworks Single Sign-On では、**SP** Initiated SSO がサポートされます

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>ギャラリーからの HRworks Single Sign-On の追加

Azure AD への HRworks Single Sign-On の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に HRworks Single Sign-On を追加する必要があります。

**ギャラリーから HRworks Single Sign-On を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**HRworks Single Sign-On**」と入力し、結果パネルで **[HRworks Single Sign-On]** を選択してから **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の HRworks Single Sign-On](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、HRworks Single Sign-On で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと HRworks Single Sign-On 内の関連ユーザー間にリンク関係が確立されている必要があります。

HRworks Single Sign-On で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[HRworks Single Sign-On シングル サインオンの構成](#configure-hrworks-single-sign-on-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[HRworks Single Sign-On テスト ユーザーの作成](#create-hrworks-single-sign-on-test-user)** - HRworks Single Sign-On で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

HRworks Single Sign-On で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **HRworks Single Sign-On** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[HRworks Single Sign-On のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://login.hrworks.de/?companyId=<companyId>&directssologin=true` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[HRworks Single Sign-On クライアント サポート チーム](mailto:nadja.sommerfeld@hrworks.de)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[HRworks Single Sign-On のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>HRworks Single Sign-On シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、管理者として HRworks Single Sign-On にサインインします。

2. メニュー バーの左側で **[Administrator]\(管理者\)** > **[Basics]\(基本\)** > **[Security]\(セキュリティ\)** > **[Single Sign-on]\(シングル サインオン\)** の順にクリックし、以下の手順を実行します。

       ![Configure single sign-on](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. **[Use Single Sign-on]\(シングル サインオンを使用する\)** をオンにします。

    b. **[Meta data input method]\(メタ データの入力方法\)** として **[XML Metadata]\(XML メタデータ\)** を選択します。

    c. **[Value for NameID]\(NameID の値\)** として **[Individual NameID identifier]\(個別の NameID 識別子\)** を選択します。

    d. Azure portal からダウンロードしたメタデータ XML をメモ帳で開き、その内容をコピーして  **[Metadata]\(メタデータ\)**  ボックスに貼り付けます。

    e. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、BrittaSimon@contoso.com のようなユーザー名を入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に HRworks Single Sign-On へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから **[HRworks Single Sign-On]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[HRworks Single Sign-On]** を選択します。

    ![アプリケーションの一覧の HRworks Single Sign-On のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** をクリックします。

### <a name="create-hrworks-single-sign-on-test-user"></a>HRworks Single Sign-On のテスト ユーザーの作成

Azure AD ユーザーが HRworks Single Sign-On にサインインできるようにするには、ユーザーを HRworks Single Sign-On にプロビジョニングする必要があります。 HRworks Single Sign-On では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として HRworks Single Sign-On にサインインします。

2. メニュー バーの左側で、**[Administrator]\(管理者\)** > **[Persons]\(人\)** > **[Persons]\(人\)** > **[New person]\(新しい人\)** の順にクリックします。

     ![Configure single sign-on](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. ポップアップで、**[Next]\(次へ\)** をクリックします。

    ![Configure single sign-on](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. **[Create new person with country for legal terms]\(法的条項のための国を指定した新しい人の作成\)** ポップアップで、**[First name]\(名\)**、**[Last name]\(姓\)** などのそれぞれの詳細を入力し、**[Create]\(作成\)** をクリックします。
    
    ![Configure single sign-on](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで HRworks Single Sign-On のタイルをクリックすると、SSO を設定した HRworks Single Sign-On に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

