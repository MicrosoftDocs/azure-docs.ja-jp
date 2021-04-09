---
title: 'チュートリアル: Azure Active Directory と Clear Review の統合 | Microsoft Docs'
description: Azure Active Directory と Clear Review の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: e971b02fb87440d4833c2eeba8897143f97a20dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97670545"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>チュートリアル: Azure Active Directory と Clear Review の統合

このチュートリアルでは、Clear Review と Azure Active Directory (Azure AD) を統合する方法について説明します。
Clear Review と Azure AD の統合には、次の利点があります。

* Clear Review にアクセスするユーザーを Azure AD 上でコントロールできます。
* ユーザーが自分の Azure AD アカウントを使用して Clear Review に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Clear Review の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Clear Review でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Clear Review では、**SP および IDP** によって開始される SSO がサポートされます

## <a name="adding-clear-review-from-the-gallery"></a>ギャラリーからの Clear Review の追加

Azure AD への Clear Review の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Clear Review を追加する必要があります。

**ギャラリーから Clear Review を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Clear Review**」と入力し、結果パネルで **[Clear Review]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Clear Review](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Clear Review で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Clear Review 内の関連ユーザー間にリンク関係が確立されている必要があります。

Clear Review で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Clear Review シングル サインオンの構成](#configure-clear-review-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Clear Review のテスト ユーザーの作成](#create-clear-review-test-user)** - Clear Review で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Clear Review で Azure AD シングル サインオンを構成するには、次の手順に従います

1. [Azure portal](https://portal.azure.com/) の **Clear Review** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<customer name>.clearreview.com/sso/metadata/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<customer name>.clearreview.com/sso/acs/` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<customer name>.clearreview.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Clear Review クライアント サポート チーム](https://clearreview.com/contact/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. Clear Review アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Clear Review アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

7. **[ユーザー属性とクレーム]** セクションで、次の手順に従います。

    a. **[名前識別子の値]** の右側にある **[編集] アイコン** をクリックします。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes & Claims]\(ユーザー属性と要求\) を示しています。](./media/clearreview-tutorial/attribute02.png)

    ![スクリーンショットは、説明されている値を入力できる [ユーザー要求の管理] ダイアログ ボックスを示しています。](./media/clearreview-tutorial/attribute01.png)

    b. **[ソース属性]** の一覧から、その行の **user.mail** 属性値を選択します。

    c. **[保存]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

9. **[Clear Review のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-clear-review-single-sign-on"></a>Clear Review シングル サインオンの構成

1. **Clear Review** 側でシングル サインオンを構成するために、**Clear Review** のポータルを管理者の資格情報で開きます。

2. 左側のナビゲーションから **[Admin]\(管理者\)** を選択します。

    ![[Admin]\(管理者\) が選択されている Clear Review ポータルのスクリーンショット。](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. ページの下部の **[Integrations]\(統合\)** セクションで、 **[Single Sign-On Settings]\(シングル サインオンの設定\)** の右側にある **[Change]\(変更\)** をクリックします。

    ![シングル サインオンの [Change]\(変更\) ボタンのスクリーンショット。](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. **[Single Sign-On Settings]\(シングル サインオンの設定\)** ページで、次の手順を実行します。

    ![このスクリーンショットは、[Single Sign-On Settings]\(シングル サインオンの設定\) ページを示しています。ここで、この手順の情報を入力します。](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. **[Issuer URL]\(発行者の URL\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    b. **[SAML Endpoint]\(SAML エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。  

    c. **[SLO Endpoint]\(SLO エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。  

    d. ダウンロードした証明書をメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。   

    e. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Clear Review へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Clear Review]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Clear Review]** を選択します。

    ![アプリケーションの一覧の Clear Review のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-clear-review-test-user"></a>Clear Review のテスト ユーザーの作成

このセクションでは、Clear Review で Britta Simon というユーザーを作成します。 Clear Review [サポート チーム](https://clearreview.com/contact/)と連携し、Clear Review プラットフォームにユーザーを追加してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Clear Review] タイルをクリックすると、SSO を設定した Clear Review に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)