---
title: 'チュートリアル: Azure Active Directory と Procore SSO の統合 | Microsoft Docs'
description: Azure Active Directory と Procore SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 13f8f1067ce7c9fe55160400d20ec0b20788c17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515289"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>チュートリアル: Azure Active Directory と Procore SSO の統合

このチュートリアルでは、Procore SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。
Procore SSO と Azure AD を統合すると、次の利点が得られます。

* Procore SSO にアクセス可能な Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Procore SSO に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Procore SSO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Procore SSO でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Procore SSO では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-procore-sso-from-the-gallery"></a>ギャラリーからの Procore SSO の追加

Azure AD への Procore SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Procore SSO を追加する必要があります。

**ギャラリーから Procore SSO を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Procore SSO**」と入力し、結果パネルから **[Procore SSO]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リスト内の Procore SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーを基に、Procore SSO で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Procore SSO 内の関連ユーザー間にリンク関係が確立されている必要があります。

Procore SSO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Procore SSO のシングル サインオンの構成](#configure-procore-sso-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Procore SSO のテスト ユーザーの作成](#create-procore-sso-test-user)** - Procore SSO 内で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Procore SSO で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) 上の **Procore SSO** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[Procore SSO のドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Procore SSO のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-procore-sso-single-sign-on"></a>Procore SSO のシングル サインオンを構成する

1. **Procore SSO** 側のシングル サインオンを構成するために、Procore の企業サイトに管理者としてサインインします。

2. [TOOLBOX] \(ツールボックス) ドロップ ダウンから **[Admin] \(管理)** をクリックして、SSO 設定ページを開きます。

    ![[Directory]\(ディレクトリ\) が選択された Procore の企業サイトを示すスクリーンショット。](./media/procoresso-tutorial/procore_tool_admin.png)

3. 以下の説明に従って、各ボックスに値を貼り付けます。

    ![[Add a Person]\(ユーザーの追加\) ダイアログ ボックスを示すスクリーンショット。](./media/procoresso-tutorial/procore_setting_admin.png) 

    a. **[Single Sign On Issuer URL]\(シングル サインオン発行者 URL\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    b. **[SAML Sign On Target URL]\(SAML サインオン ターゲット URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. 次に、先ほど Azure portal からダウンロードした **フェデレーション メタデータの XML** を開いて、**X509Certificate** という名前のタグ内の証明書をコピーします。 コピーした値を、 **[Single Sign On x509 Certificate] \(シングル サインオン x509 証明書)** ボックスに貼り付けます。

4. **[Save Changes] \(変更を保存)** をクリックします。

5. 上記の設定後、Procore へのログインで経由する **ドメイン名** (例: **contoso.com**) を [Procore のサポート チーム](https://support.procore.com/)に送信して、このドメインのフェデレーション SSO を有効化してもらう必要があります。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Procore SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Procore SSO]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Procore SSO]** を選択します。

    ![アプリケーションの一覧の [Procore SSO] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-procore-sso-test-user"></a>Procore SSO のテスト ユーザーを作成する

以下の手順に従って、Procore SSO の側で Procore テスト ユーザーを作成します。

1. Procore 企業サイトに管理者としてサインインします。    

2. [TOOLBOX] \(ツールボックス) ドロップ ダウンで **[Directory] \(ディレクトリ)** をクリックして、企業のディレクトリ ページを開きます。

    ![ツールボックスから [Directory]\(ディレクトリ\) が選択された Procore の企業サイトを示すスクリーンショット。](./media/procoresso-tutorial/Procore_sso_directory.png)

3. **[Add a Person] \(ユーザーの追加)** オプションをクリックしてフォームを開き、次の手順を実行します。

    ![ユーザー情報を入力できる [Add a person to Boylan Construction]\(Boylan Construction にユーザーを追加する\) を示すスクリーンショット。](./media/procoresso-tutorial/Procore_user_add.png)

    a. **[First Name] \(名)** テキストボックスに、ユーザーの名を入力します (この例では **Britta**).

    b. **[Last Name] \(姓)** テキストボックスに、ユーザーの姓を入力します (この例では **Simon**).

    c. **[Email Address] \(メール アドレス)** ボックスに、ユーザーのメール アドレスを入力します (この例では BrittaSimon@contoso.com)。

    d. **[Permission Template] \(アクセス許可テンプレート)** で **[Apply Permission Template Later] \(アクセス許可テンプレートは後で適用する)** を選択します。

    e. **Create** をクリックしてください。

4. 新しく追加された連絡先の詳細を確認して更新します。

    ![ユーザー設定を確認できる編集ページを示すスクリーンショット。](./media/procoresso-tutorial/Procore_user_check.png)

5. メールによる招待状が必要な場合は **[Save and Send Invitiation]\(保存して招待状を送信\)** を、直接保存する場合は **[Save]\(保存\)** をクリックして、ユーザー登録を完了します。
    
    ![招待状を保存して送信できる [Current Project Settings]\(現在のプロジェクトの設定\) を示すスクリーンショット。](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Procore SSO] タイルをクリックすると、SSO を設定した Procore SSO に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)