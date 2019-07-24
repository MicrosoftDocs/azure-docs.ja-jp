---
title: チュートリアル:Azure Active Directory と StatusPage の統合 | Microsoft Docs
description: Azure Active Directory と StatusPage の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 49f77da5843bc2438ea7f82475b4faf753b66f09
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565206"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>チュートリアル:Azure Active Directory と StatusPage の統合

このチュートリアルでは、StatusPage と Azure Active Directory (Azure AD) を統合する方法について説明します。
StatusPage と Azure AD の統合には、次の利点があります。

* StatusPage にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで StatusPage に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

StatusPage と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* StatusPage でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* StatusPage では、**IDP** Initiated SSO がサポートされます

## <a name="adding-statuspage-from-the-gallery"></a>ギャラリーからの StatusPage の追加

Azure AD への StatusPage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に StatusPage を追加する必要があります。

**ギャラリーから StatusPage を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**StatusPage**」と入力し、結果パネルで **[StatusPage]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の StatusPage](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、StatusPage で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと StatusPage 内の関連ユーザー間にリンク関係が確立されている必要があります。

StatusPage で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[StatusPage のシングル サインオンの構成](#configure-statuspage-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[StatusPage のテスト ユーザーの作成](#create-statuspage-test-user)** - StatusPage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

StatusPage で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **StatusPage** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[StatusPage のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > シングル サインオンを構成するために必要なメタデータは、StatusPage サポート チーム ( [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)) に連絡して入手してください。 
    >
    > a. メタデータから発行者の値をコピーし、**[識別子]** ボックスに貼り付けます。
    >
    > b. メタデータから応答 URL をコピーし、**[応答 URL]** ボックスに貼り付けます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[StatusPage のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-statuspage-single-sign-on"></a>StatusPage のシングル サインオンの構成

1. 別のブラウザー ウィンドウで、管理者として StatusPage 企業サイトにサインインします。

1. メイン ツール バーで、 **[Manage Account]** をクリックします。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. **[Single Sign-on]** タブをクリックします。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. [SSO Setup] ページで、次の手順に従います。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. **[SSO Target URL]\(SSO ターゲット URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[Certificate]** ボックスに貼り付けます。

    c. **[構成の保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に StatusPage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[StatusPage]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[StatusPage]** を選択します。

    ![アプリケーションの一覧の StatusPage のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-statuspage-test-user"></a>StatusPage のテスト ユーザーの作成

このセクションの目的は、StatusPage で Britta Simon というユーザーを作成することです。

StatusPage では、ジャストインタイム プロビジョニングがサポートされています。 この機能は、「[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)」で既に有効にしています。

**StatusPage で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. StatusPage 企業サイトに管理者としてサインオンします。

1. 上部のメニューで **[Manage Account]** をクリックします。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. **[チーム メンバー]** タブをクリックします。
  
    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. **[チーム メンバーの追加]** をクリックします。
  
    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. プロビジョニングする有効なユーザーの**電子メール アドレス**、**名**、**姓**を、対応するテキスト ボックスに入力します。 

    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. **[Role]** で **[Client Administrator]** を選択します。

1. **[アカウントの作成]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [StatusPage] タイルをクリックすると、SSO を設定した StatusPage に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
