---
title: チュートリアル:Azure Active Directory と Knowledge Anywhere LMS の統合 | Microsoft Docs
description: Azure Active Directory と Knowledge Anywhere LMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.openlocfilehash: f39952c74006964155fd23920c85506cac13a878
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892721"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledge-anywhere-lms"></a>チュートリアル:Azure Active Directory と Knowledge Anywhere LMS の統合

このチュートリアルでは、Knowledge Anywhere LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。
Knowledge Anywhere LMS と Azure AD の統合には、次の利点があります。

* Knowledge Anywhere LMS にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Knowledge Anywhere LMS に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Knowledge Anywhere LMS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Knowledge Anywhere LMS でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Knowledge Anywhere LMS では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Knowledge Anywhere LMS では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>ギャラリーからの Knowledge Anywhere LMS の追加

Azure AD への Knowledge Anywhere LMS の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Knowledge Anywhere LMS を追加する必要があります。

**ギャラリーから Knowledge Anywhere LMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Knowledge Anywhere LMS**」と入力し、結果パネルで **[Knowledge Anywhere LMS]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Knowledge Anywhere LMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Knowledge Anywhere LMS で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Knowledge Anywhere LMS 内の関連ユーザーの間にリンク関係が確立されている必要があります。

Knowledge Anywhere LMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Knowledge Anywhere LMS のシングル サインオンの構成](#configure-knowledge-anywhere-lms-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Knowledge Anywhere LMS のテスト ユーザーの作成](#create-knowledge-anywhere-lms-test-user)** - Knowledge Anywhere LMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Knowledge Anywhere LMS で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Knowledge Anywhere LMS** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Knowledge Anywhere LMS のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<CLIENTNAME>.knowledgeanywhere.com/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL に値を置き換えます。実際の値については後で説明します。

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Knowledge Anywhere LMS のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<CLIENTNAME>.knowledgeanywhere.com/` という形式で URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[Knowledge Anywhere LMS クライアント サポート チーム](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Knowledge Anywhere LMS のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-knowledge-anywhere-lms-single-sign-on"></a>Knowledge Anywhere LMS のシングル サインオンの構成

1. 別のブラウザー ウィンドウで、Knowledge Anywhere LMS の管理ポータルを開きます。

2. **[Site]\(サイト\)** タブを選択します。

    ![Knowledge Anywhere LMS の構成](./media/knowledge-anywhere-lms-tutorial/configure1.png)

3. **[SAML Settings]\(SAML 設定\)** タブを選択します。

    ![Knowledge Anywhere LMS の構成](./media/knowledge-anywhere-lms-tutorial/configure2.png)

4. **[Add New]\(新規追加\)** をクリックします。

    ![Knowledge Anywhere LMS の構成](./media/knowledge-anywhere-lms-tutorial/configure3.png)

5. **[Add/Update SAML Settings]\(SAML 設定の追加/更新\)** ページで、次の手順を実行します。

    ![Knowledge Anywhere LMS の構成](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. 所属する組織に応じて [IDP Name]\(IDP 名\) に入力します (例: `Azure`)。

    b. **[IDP Entity ID]\(IDP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    c. **[IDP URL]\(IDP URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. Azure Portal からダウンロードした証明書ファイルをメモ帳で開き、その内容をコピーして **[Certificate]\(証明書\)** ボックスに貼り付けます。

    e. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    f. **[Domain]\(ドメイン\)** には、ボックスの一覧から **[Main Site]\(メイン サイト\)** を選択します。

    g. **[SP Entity ID]\(SP エンティティ ID\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]** ボックスに貼り付けます。

    h. **[SP Response(ACS) URL]\(SP 応答 (ACS) URL\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[応答 URL]** ボックスに貼り付けます。

    i. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Knowledge Anywhere LMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 内で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Knowledge Anywhere LMS]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Knowledge Anywhere LMS]** を選択します。

    ![アプリケーションの一覧の Knowledge Anywhere LMS リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-knowledge-anywhere-lms-test-user"></a>Knowledge Anywhere LMS のテスト ユーザーの作成

このセクションでは、Knowledge Anywhere LMS に Britta Simon というユーザーを作成します。 Knowledge Anywhere LMS では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Knowledge Anywhere LMS にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Knowledge Anywhere LMS] タイルをクリックすると、SSO を設定した Knowledge Anywhere LMS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)