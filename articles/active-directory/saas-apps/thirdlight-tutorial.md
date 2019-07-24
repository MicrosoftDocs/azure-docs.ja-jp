---
title: チュートリアル:Azure Active Directory と ThirdLight の統合 | Microsoft Docs
description: Azure Active Directory と ThirdLight の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: caee6bad1b944b6d1396ea2e26f163629b3c444f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59269879"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>チュートリアル:Azure Active Directory と ThirdLight の統合

このチュートリアルでは、ThirdLight と Azure Active Directory (Azure AD) を統合する方法について説明します。
ThirdLight と Azure AD の統合には、次の利点があります。

* ThirdLight にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで ThirdLight に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ThirdLight と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Thirdlight でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ThirdLight では、**SP** Initiated SSO がサポートされます

## <a name="adding-thirdlight-from-the-gallery"></a>ギャラリーからの ThirdLight の追加

Azure AD への ThirdLight の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ThirdLight を追加する必要があります。

**ギャラリーから ThirdLight を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**ThirdLight**」と入力し、結果ウィンドウで **ThirdLight** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の ThirdLight](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、ThirdLight で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと ThirdLight 内の関連ユーザー間にリンク関係が確立されている必要があります。

ThirdLight で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[ThirdLight のシングル サインオンの構成](#configure-thirdlight-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[ThirdLight のテスト ユーザーの作成](#create-thirdlight-test-user)** - ThirdLight で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

ThirdLight で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **ThirdLight** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[ThirdLight のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.thirdlight.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.thirdlight.com/saml/sp`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[ThirdLight クライアント サポート チーム](https://www.thirdlight.com/support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[ThirdLight のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-thirdlight-single-sign-on"></a>ThirdLight のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、ThirdLight 企業サイトに管理者としてサインインします。

1. **[Configuration]\>[System Administration]** の順に移動し、**[SAML2]** をクリックします。

    ![System Administration](./media/thirdlight-tutorial/ic805843.png "System Administration")

1. SAML2 構成セクションで、次の手順を実行します。
  
    ![SAML シングル サインオン](./media/thirdlight-tutorial/ic805844.png "SAML Single Sign-On")

    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Enable SAML2 Single Sign-On]** を選択します。

    b. **[Source for IdP Metadata]** として、**[Load IdP Metadata from XML]** を選択します。

    c. Azure portal からダウンロードしたメタデータ ファイルを開き、内容をコピーしてから、**[IdP Metadata XML]** テキストボックスに貼り付けます。

    d. **[Save SAML2 settings]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ThirdLight へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[ThirdLight]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[ThirdLight]** を選択します。

    ![アプリケーションの一覧の ThirdLight のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-thirdlight-test-user"></a>ThirdLight のテスト ユーザーの作成

Azure AD ユーザーが ThirdLight にサインインできるようにするには、そのユーザーを ThirdLight にプロビジョニングする必要があります。  
ThirdLight の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ThirdLight** 企業サイトに管理者としてサインインします。

1. **[Users]** タブに移動します。

1. **[Users and Groups]** を選択します。

1. **[Add new User]** ボタンをクリックします。

1. プロビジョニングする有効な AAD アカウントの **[Username]、[Name or Description]、[Email]、[Choose a Preset or Group of New Members]** を入力します。

1. **Create** をクリックしてください。

> [!NOTE]
> Thirdlight から提供されている他の Thirdlight ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [ThirdLight] タイルをクリックすると、SSO を設定した ThirdLight に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
