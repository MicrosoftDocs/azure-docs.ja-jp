---
title: 'チュートリアル: Azure Active Directory と Image Relay の統合 | Microsoft Docs'
description: Azure Active Directory と Image Relay の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: b76c2e346adb6c2afd146b0d73c8f20165145bac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460361"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>チュートリアル: Azure Active Directory と Image Relay の統合

このチュートリアルでは、Image Relay と Azure Active Directory (Azure AD) を統合する方法について説明します。
Image Relay と Azure AD の統合には、次の利点があります。

* Image Relay にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Image Relay に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Image Relay の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Image Relay でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Image Relay では、**SP** によって開始される SSO がサポートされます

## <a name="adding-image-relay-from-the-gallery"></a>ギャラリーからの Image Relay の追加

Azure AD への Image Relay の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Image Relay を追加する必要があります。

**ギャラリーから ImageRelay を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Image Relay**」と入力し、結果パネルで **[Image Relay]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Image Relay](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Image Relay で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Image Relay 内の関連ユーザー間にリンク関係が確立されている必要があります。

Image Relay で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Image Relay シングル サインオンの構成](#configure-image-relay-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Image Relay のテスト ユーザーの作成](#create-image-relay-test-user)** - Image Relay で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Image Relay で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Image Relay** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Image Relay のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.imagerelay.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Image Relay クライアント サポート チーム](http://support.imagerelay.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Image Relay のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-image-relay-single-sign-on"></a>Image Relay のシングル サインオンの構成

1. 別のブラウザー ウィンドウで、管理者として Image Relay 企業サイトにサインインします。

2. 上部にあるツール バーで **[Users & Permissions]** ワークロードをクリックします。

    ![ツール バーで [Users & Permissions]\(ユーザーとアクセス許可\) が選択されているスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. **[Create New Permission (新しいアクセス許可を作成)]** をクリックします。

    ![アクセス許可のタイトルを入力するためのテキスト ボックスと、アクセス許可の種類を選択するためのオプションを示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. **[Single Sign On Settings]** ワークロードで、 **[This Group can only sign-in via Single Sign On]** チェックボックスをオンにして **[Save]** をクリックします。

    ![オプションを選択できる [Single Sign On Settings]\(シングル サイン オンの設定\) を示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. **[Account Settings (アカウントの設定)]** に移動します。

    ![[Account Settings]\(アカウントの設定\) ツール バー オプションを示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. **[Single Sign On Settings (シングル サインオンの設定)]** ワークロードに移動します。

    ![[Single Sign On Settings]\(シングル サインオンの設定\) メニュー オプションを示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. **[SAML Settings (SAML の設定)]** ダイアログで、次の手順を実行します。

    ![情報を入力できる [SAML Settings]\(SAML の設定\) ダイアログ ボックスを示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    b. **[ログアウト URL]** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    c. **[Name Id Format]** として **[urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress]** を選択します。

    d. **[Binding Options for Requests from the Service Provider (Image Relay)]** で **[POST Binding]** を選択します。

    e. **[x.509 Certificate]** の下にある **[Update Certificate]** をクリックします。

    ![証明書を更新するためのオプションを示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、 **[x.509 Certificate]\(x.509 証明書\)** ボックスに貼り付けます。

    ![x dot 509 証明書を示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. **[Just-In-Time User Provisioning]** で、 **[Enable Just-In-Time User Provisioning]** をオンにします。

    ![有効にするコントロールが選択されている [Just-In-Time User Provisioning]\(Just-In-Time ユーザー プロビジョニング\) セクションを示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. シングル サインオンによるサインインのみを許可するアクセス許可グループを選択します ( **[SSO Basic]** など)。

    ![[S S O Basic] が選択されている [Just-In-Time User Provisioning]\(Just-In-Time ユーザー プロビジョニング\) セクションを示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. **[保存]** をクリックします。

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

このセクションでは、Britta Simon に Image Relay へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Image Relay]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Image Relay]** を選択します。

    ![アプリケーションの一覧の [Image Relay] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-image-relay-test-user"></a>Image Relay の テスト ユーザーの作成

このセクションの目的は、Image Relay で Britta Simon というユーザーを作成することです。

**Image Relay で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Image Relay 企業サイトに管理者としてログインします。

2. **[Users & Permissions]** に移動して **[Create SSO User]** を選択します。

    ![メニューで選択されている [Create S S O User]\(S S O ユーザーの作成\) を示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. プロビジョニングするユーザーの **[Email]\(電子メール\)** 、 **[First Name]\(名\)** 、 **[Last Name]\(姓\)** 、 **[Company]\(会社\)** を入力し、シングル サインオンのみでサインインできるアクセス許可グループ ([SSO Basic]\(SSO Basic\) など) を選択します。

    ![必要な情報を入力できる [Create a S S O User]\(S S O ユーザーの作成\) ページを示すスクリーンショット。](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. **Create** をクリックしてください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Image Relay] タイルをクリックすると、SSO を設定した Image Relay に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)