---
title: チュートリアル:Azure Active Directory と Absorb LMS の統合 | Microsoft Docs
description: Azure Active Directory と Absorb LMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e86ae61a6aec6c546b36c52f3f3875cbebdc838e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861929"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>チュートリアル:Azure Active Directory と Absorb LMS の統合

このチュートリアルでは、Absorb LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。
Absorb LMS と Azure AD の統合には、次の利点があります。

* Absorb LMS にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Absorb LMS に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Absorb LMS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Absorb LMS でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Absorb LMS では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-absorb-lms-from-the-gallery"></a>ギャラリーからの Absorb LMS の追加

Azure AD への Absorb LMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Absorb LMS を追加する必要があります。

**ギャラリーから Absorb LMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Absorb LMS**」と入力し、結果ウィンドウで **[Absorb LMS]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Absorb LMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Absorb LMS で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Absorb LMS 内の関連ユーザー間にリンク関係が確立されている必要があります。

Absorb LMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Absorb LMS シングル サインオンの構成](#configure-absorb-lms-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Absorb LMS テスト ユーザーの作成](#create-absorb-lms-test-user)** - Absorb LMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Absorb LMS で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Absorb LMS** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![[Absorb LMS のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    **Absorb 5 - UI** を使用している場合は、次の構成を使用します。

    a. **[識別子]** ボックスに、`https://company.myabsorb.com/account/saml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://company.myabsorb.com/account/saml` のパターンを使用して URL を入力します

    **Absorb 5 - New Learner Experience** を使用している場合は、次の構成を使用します。

    a. **[識別子]** ボックスに、`https://company.myabsorb.com/api/rest/v2/authentication/saml` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://company.myabsorb.com/api/rest/v2/authentication/saml` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Absorb LMS クライアント サポート チーム](https://support.absorblms.com/hc/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)**  ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションから**メタデータ XML**をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Absorb LMS のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-absorb-lms-single-sign-on"></a>Absorb LMS のシングル サインオンを構成する

1. 新しい Web ブラウザー ウィンドウで、Absorb LMS 企業サイトに管理者としてサインインします。

2. 右上にある **[アカウント]** ボタンを選択します。

    ![[アカウント] ボタン](./media/absorblms-tutorial/1.png)

3. [アカウント] ウィンドウで、**[ポータルの設定]** を選択します。

    ![[ポータルの設定] リンク](./media/absorblms-tutorial/2.png)

4. **[Manage SSO Settings]\(SSO の設定の管理\)** タブを選択します。

    ![[ユーザー] タブ](./media/absorblms-tutorial/managesso.png)

5. **[Manage Single Sign-On Settings]\(シングル サインオンの設定の管理\)** ページで、次のようにします。

    ![[シングル サインオンの構成] ページ](./media/absorblms-tutorial/settings.png)

    a. **[Name]\(名前\)** テキスト ボックスに、"Azure AD Marketplace SSO" のような名前を入力します。

    b. **[Method]\(方法\)** として **[SAML]** を選択します。

    c. メモ帳で、Azure Portal からダウンロードした証明書を開きます。 **-----BEGIN CERTIFICATE-----** タグと **-----END CERTIFICATE-----** タグを削除します。 次に **[キー]** ボックスに残りのコンテンツを貼り付けます。

    d. **[モード]** ボックスで、**[Identity Provider Initiated]\(ID プロバイダー開始\)** を選択します。

    e. **[Id Property]\(ID プロパティ\)** ボックスで、Azure AD でユーザー ID として構成した属性を選択します。 たとえば、Azure AD で *userPrincipalName* が選択されている場合、**[ユーザー名]** を選択します。

    f. **[Signature Type]\(署名の種類\)** として **[Sha256]** を選択します。

    g. **[ログイン URL]** ボックスに、Azure Portal のアプリケーションの **[プロパティ]** ページにある**ユーザーのアクセス URL** を貼り付けます。

    h. **[ログアウト URL]** に、Azure Portal の **[サインオンの構成]** ウィンドウからコピーした **[サインアウト URL]** 値を貼り付けます。

    i. **[Automatically Redirect]\(自動的にリダイレクトする\)** を **[On]\(オン\)** にします。

6. **[保存]** を選択します。

    ![[Only Allow SSO Login]\(SSO ログインのみを許可する\) の切り替え](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Absorb LMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Absorb LMS]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Absorb LMS**」と入力して選択します。

    ![アプリケーションの一覧の Absorb LMS のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-absorb-lms-test-user"></a>Absorb LMS のテスト ユーザーの作成

Azure AD ユーザーが Absorb LMS にサインインするには、そのユーザーを Absorb LMS で設定する必要があります。 Absorb LMS の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. Absorb LMS 企業サイトに管理者としてサインインします。

2. **[ユーザー]** ウィンドウで、**[ユーザー]** を選択します。

    ![[ユーザー] リンク](./media/absorblms-tutorial/absorblms_userssub.png)

3. **[ユーザー]** タブを選択します。

    ![[新規追加] ドロップダウン リスト](./media/absorblms-tutorial/absorblms_createuser.png)

4. **[ユーザーの追加]** ページで、次の手順に従います。

    ![[ユーザーの追加] ページ](./media/absorblms-tutorial/user.png)

    a. **[名]** ボックスに、ユーザーの名 (たとえば、**Britta**) を入力します。

    b. **[姓]** ボックスに、ユーザーの姓 (たとえば、**Simon**) を入力します。

    c. **[ユーザー名]** ボックスに、ユーザーの氏名 (たとえば、**Britta Simon**) を入力します。

    d. **[パスワード]** ボックスに、ユーザーのパスワードを入力します。

    e. **[パスワードの確認]** ボックスに、パスワードを再度入力します。

    f. **[アクティブ]** を **[アクティブ]** に切り替えます。

5. **[保存]** を選択します。

    ![[Only Allow SSO Login]\(SSO ログインのみを許可する\) の切り替え](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > 既定では、ユーザー プロビジョニングは SSO では有効になりません。 顧客がこの機能を有効にすることを望む場合は、[こちら](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning)のドキュメントで説明されているように設定する必要があります。 また、ユーザー プロビジョニングは ACS URL が `https://company.myabsorb.com/api/rest/v2/authentication/saml` の **Absorb 5 - New Learner Experience** でのみ使用できることに注意してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Absorb LMS] タイルをクリックすると、SSO を設定した Absorb LMS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
