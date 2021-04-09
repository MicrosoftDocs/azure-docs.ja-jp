---
title: 'チュートリアル: Azure Active Directory と Yodeck の統合 | Microsoft Docs'
description: Azure Active Directory と Yodeck の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 68e961336d7e86f9983fea49044137894ab0f985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895117"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>チュートリアル: Azure Active Directory と Yodeck の統合

このチュートリアルでは、Yodeck と Azure Active Directory (Azure AD) を統合する方法について説明します。
Yodeck と Azure AD の統合には、次の利点があります。

* Yodeck にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Yodeck に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Yodeck と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Yodeck でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Yodeck では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-yodeck-from-the-gallery"></a>ギャラリーからの Yodeck の追加

Azure AD への Yodeck の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Yodeck を追加する必要があります。

**ギャラリーから Yodeck を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Yodeck**」と入力し、結果パネルで **[Yodeck]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Yodeck](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Yodeck で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Yodeck 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Yodeck で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Yodeck のシングル サインオンの構成](#configure-yodeck-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Yodeck のテスト ユーザーの作成](#create-yodeck-test-user)** - Yodeck で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Yodeck で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Yodeck** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Yodeck Domain and URLs]\(Yodeck のドメインと URL) のシングル サインオン情報](common/idp-identifier.png)

    **[識別子]** テキスト ボックスに、`https://app.yodeck.com/api/v1/account/metadata/` という URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![スクリーンショットは、[メタデータ ファイルをアップロードする] リンクを含む、[基本的な SAML 構成] を示しています。](common/both-preintegrated-signon.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.yodeck.com/login`」と入力します。

6. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Yodeck のシングル サインオンの構成

1. **Yodeck** 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![[拡張機能のインストール] ボタンを示すスクリーンショット。](./media/target-process-tutorial/install_extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Yodeck のセットアップ]** をクリックすると、Yodeck アプリケーションに移動します。 そこから、管理者の資格情報を入力して Yodeck にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

    **アプリケーションを手動で構成する場合は、次の手順を実行します。**

1. 別の Web ブラウザー ウィンドウで、Yodeck 企業サイトに管理者としてサインインします。

1. ページの右上隅にある **[ユーザー設定]** オプションをクリックし、**[アカウント設定]** を選択します。

    ![ユーザーに対して [アカウント設定] が選択されていることを示すスクリーンショット。](./media/yodeck-tutorial/configure1.png)

1. **[SAML]** をクリックし、次の手順を実行します。

    ![[SAML] タブを示すスクリーンショット。ここで以下の手順を行います。](./media/yodeck-tutorial/configure2.png)

    a. **[URL からインポートする]** を選択します。

    b. **[URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付け、**[インポート]** をクリックします。
    
    c. **[アプリのフェデレーション メタデータ URL]** をインポートすると、残りのフィールドが自動的に設定されます。

    d. **[保存]** をクリックします。

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

このセクションでは、Britta Simon に Yodeck へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Yodeck]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Yodeck]** を選択します。

    ![アプリケーションの一覧の Yodeck のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-yodeck-test-user"></a>Yodeck のテスト ユーザーの作成

Azure AD ユーザーが Yodeck にサインインできるようにするには、ユーザーを Yodeck にプロビジョニングする必要があります。 Yodeck の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Yodeck 企業サイトに管理者としてサインインします。

2. ページの右上隅にある [ユーザー設定] オプションをクリックし、[ユーザー] を選択します。

    ![ユーザーに対して選択された [Users]\(ユーザー\) を示すスクリーンショット。](./media/yodeck-tutorial/user1.png)

3. **[+User]** をクリックして **[ユーザーの詳細]** タブを開きます。

    ![[Users]\(ユーザー\) ボタンを示すスクリーンショット。](./media/yodeck-tutorial/user2.png)

4. [**User Details**] ダイアログ ページで、以下の手順を実行します。

    ![[User Details]\(ユーザーの詳細\) タブを示すスクリーンショット。ここで以下の手順を行います。](./media/yodeck-tutorial/user3.png)

    a. **[名]** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (brittasimon@contoso.com など) を入力します。

    d. 組織の要件に従って、適切な **[アカウントのアクセス許可]** オプションを選択します。
    
    e. **[保存]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Yodeck] タイルをクリックすると、SSO を設定した Yodeck に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)