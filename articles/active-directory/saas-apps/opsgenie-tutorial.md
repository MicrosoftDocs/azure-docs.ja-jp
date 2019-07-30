---
title: チュートリアル:Azure Active Directory と OpsGenie の統合 | Microsoft Docs
description: Azure Active Directory と OpsGenie の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 446ac54d84f7b2b3bf3aaf6eaf5536f0dfb804fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095749"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>チュートリアル:Azure Active Directory と OpsGenie の統合

このチュートリアルでは、OpsGenie と Azure Active Directory (Azure AD) を統合する方法について説明します。
OpsGenie と Azure AD の統合には、次の利点があります。

* OpsGenie にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して OpsGenie に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と OpsGenie の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* OpsGenie でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* OpsGenie では、**SP** によって開始される SSO がサポートされます

## <a name="adding-opsgenie-from-the-gallery"></a>ギャラリーからの OpsGenie の追加

Azure AD への OpsGenie の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OpsGenie を追加する必要があります。

**ギャラリーから OpsGenie を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**OpsGenie**」と入力し、結果パネルで **OpsGenie** を選び、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果一覧の OpsGenie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**B. Simon** というテスト ユーザーに基づいて、OpsGenie で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと OpsGenie 内の関連ユーザー間にリンク関係が確立されている必要があります。

OpsGenie で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[OpsGenie シングル サインオンの構成](#configure-opsgenie-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B. Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B. Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[OpsGenie テスト ユーザーの作成](#create-opsgenie-test-user)** - OpsGenie で B. Simon に対応するユーザーを作成し、Azure AD の B. Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

OpsGenie で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **OpsGenie** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[OpsGenie のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.opsgenie.com/auth/login`」と入力します。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

6. **[OpsGenie のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-opsgenie-single-sign-on"></a>OpsGenie シングル サインオンの構成

1. 別のブラウザー インスタンスを開き、管理者として OpsGenie にログインします。

2. **[設定]** をクリックし、 **[シングル サインオン]** タブをクリックします。
   
    ![OpsGenie シングル サインオン](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. SSO を有効にするには、 **[有効]** を選択します。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. **[プロバイダー]** セクションで、 **[Azure Active Directory]** タブをクリックします。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. [Azure Active Directory] ダイアログ ページで、次の手順に従います。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. **[SAML 2.0 Endpoint]\(SAML 2.0 エンドポイント\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。
    
    b. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付けます。
    
    c. **[変更を保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure portal で B. Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに、「**B. Simon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **bsimon@yourcompanydomain.extension** と入力します。  
    たとえば、BSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に OpsGenie へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[OpsGenie]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[OpsGenie]** を選択します。

    ![アプリケーションの一覧の OpsGenie のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] リストで **[B. Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-opsgenie-test-user"></a>OpsGenie テスト ユーザーの作成

このセクションの目的は、OpsGenie で B. Simon というユーザーを作成することです。 

1. Web ブラウザー ウィンドウで、OpsGenie テナントに管理者としてログインします。

2. 左パネルの **[ユーザー]** をクリックし、ユーザー リストに移動します。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. **[ユーザーの追加]** をクリックします。

4. **[Add User]** ダイアログで、次の手順を実行します。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. **[電子メール]** テキストボックスに、Azure Active Directory の B. Simon の電子メール アドレスを入力します。
   
    b. **[フル ネーム]** ボックスに、「**B. Simon**」と入力します。
   
    c. **[Save]** をクリックします。 

>[!NOTE]
>B. Simon にプロファイルの設定方法が記載されたメールが届きます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [OpsGenie] タイルをクリックすると、SSO を設定した OpsGenie に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

