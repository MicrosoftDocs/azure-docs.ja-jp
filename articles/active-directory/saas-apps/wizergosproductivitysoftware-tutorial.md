---
title: 'チュートリアル: Azure Active Directory と Wizergos Productivity Software の統合 | Microsoft Docs'
description: Azure Active Directory と Wizergos Productivity Software の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: b4cddae25bbf7ff113d2ea67700e28eb81c0e7c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92638025"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>チュートリアル: Azure Active Directory と Wizergos Productivity Software の統合

このチュートリアルでは、Wizergos Productivity Software と Azure Active Directory (Azure AD) を統合する方法について説明します。
Wizergos Productivity Software と Azure AD の統合には、次の利点があります。

* Wizergos Productivity Software にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Wizergos Productivity Software に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Wizergos Productivity Software と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Wizergos Productivity Software でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Wizergos Productivity Software では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>ギャラリーからの Wizergos Productivity Software の追加

Azure AD への Wizergos Productivity Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Wizergos Productivity Software を追加する必要があります。

**ギャラリーから Wizergos Productivity Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Wizergos Productivity Software**」と入力し、結果ウィンドウで **Wizergos Productivity Software** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Wizergos Productivity Software](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Wizergos Productivity Software で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Wizergos Productivity Software 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Wizergos Productivity Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Wizergos Productivity Software シングル サインオンの構成](#configure-wizergos-productivity-software-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Wizergos Productivity Software テスト ユーザーの作成](#create-wizergos-productivity-software-test-user)** - Wizergos Productivity Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Wizergos Productivity Software で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Wizergos Productivity Software** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Wizergos Productivity Software のドメインと URL] のシングル サインオン情報](common/idp-identifier.png)

    **[識別子]** テキスト ボックスに、`https://www.wizergos.net` という URL を入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Wizergos Productivity Software のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-wizergos-productivity-software-single-sign-on"></a>Wizergos Productivity Software シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Wizergos Productivity Software テナントにサインオンします。

2. ハンバーガー メニューから **[Admin (管理者)]** を選択します。

    ![メニューから [Admin]\(管理者\) アイコンが選択されていることを示すスクリーンショット。](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

3. [Admin (管理者)] ページの左側にあるメニューで **[AUTHENTICATION (認証)]** を選択し、 **[Azure AD]** をクリックします。

    ![[AUTHENTICATION]\(認証\) から [Azure AD] が選択された状態を示すスクリーンショット。](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

4. **[AUTHENTICATION (認証)]** セクションで、次の手順に従います。

    ![[AUTHENTICATION]\(認証\) ページのスクリーンショット。ここで、説明されている値を入力することができます。](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. **[UPLOAD (アップロード)]** ボタンをクリックして、Azure AD からダウンロードした証明書をアップロードします。
    
    b. **[Issuer URL]\(発行者の URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。
    
    c. **[Single Sign-On URL]\(シングル サインオン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。
    
    d. **[Single Sign-Out URL]\(シングル サインアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。
    
    e. **[保存]** ボタンをクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Wizergos Productivity Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Wizergos Productivity Software]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Wizergos Productivity Software]** を選択します。

    ![アプリケーションの一覧の Wizergos Productivity Software のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-wizergos-productivity-software-test-user"></a>Wizergos Productivity Software テスト ユーザーの作成

このセクションでは、Wizergos Productivity Software で Britta Simon というユーザーを作成します。 [Wizergos Productivity Software サポート チーム](mailTo:support@wizergos.com)と連携して、Wizergos Productivity Software プラットフォームにユーザーを追加してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Wizergos Productivity Software] タイルをクリックすると、SSO を設定した Wizergos Productivity Software に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)