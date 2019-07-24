---
title: チュートリアル:Azure Active Directory と MyWorkDrive の統合 | Microsoft Docs
description: Azure Active Directory と MyWorkDrive の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: d16aa8442f71845e7b46377c6c290212f9c400a3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280504"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>チュートリアル:Azure Active Directory と MyWorkDrive の統合

このチュートリアルでは、MyWorkDrive と Azure Active Directory (Azure AD) を統合する方法について説明します。
MyWorkDrive と Azure AD の統合には、次の利点があります。

* MyWorkDrive にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで MyWorkDrive に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

MyWorkDrive と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* MyWorkDrive でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* MyWorkDrive では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-myworkdrive-from-the-gallery"></a>ギャラリーからの MyWorkDrive の追加

Azure AD への MyWorkDrive の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MyWorkDrive を追加する必要があります。

**ギャラリーから MyWorkDrive を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**MyWorkDrive**」と入力し、結果パネルで **MyWorkDrive** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの MyWorkDrive](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、MyWorkDrive で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと MyWorkDrive 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

MyWorkDrive で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[MyWorkDrive のシングル サインオンの構成](#configure-myworkdrive-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[MyWorkDrive テスト ユーザーの作成](#create-myworkdrive-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを MyWorkDrive で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

MyWorkDrive で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **MyWorkDrive** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[MyWorkDrive Domain and URLs] (MyWorkDrive のドメインと URL) のシングル サインオン情報](common/both-replyurl.png)

    **[応答 URL]** ボックスに、`https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[MyWorkDrive Domain and URLs] (MyWorkDrive のドメインと URL) のシングル サインオン情報](common/both-signonurl.png)

    **[サインオン URL]** ボックスに、`https://<SERVER.DOMAIN.COM>/Account/Login-saml` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 自社の MyWorkDrive サーバーのホスト名を入力します。たとえば、次のようになります。
    > 
    > 応答 URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > サインオン URL: `https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > これらの値に対して独自のホスト名と SSL 証明書を設定する方法がわからない場合は、[MyWorkDrive サポート チーム](mailto:support@myworkdrive.com)にお問い合わせください。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

### <a name="configure-myworkdrive-single-sign-on"></a>MyWorkDrive のシングル サインオンを構成する

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として MyWorkDrive にログインします。

2. MyWorkDrive サーバー上の管理パネルで、**[ENTERPRISE]\(エンタープライズ\)** をクリックし、次の手順を実行します。

    ![管理](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. **[SAML/ADFS SSO]\(SAML/ADFS SSO\)** を有効にします。

    b. **[SAML - Azure AD]\(SAML - Azure AD\)** を選択します

    c. **[Azure App Federation Metadata Url]\(Azure アプリのフェデレーション メタデータ URL\)** テキスト ボックスに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    d. **[保存]**

    >[!NOTE]
    >追加情報については、[MyWorkDrive の Azure AD サポートに関する記事](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/)をご覧ください。

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

このセクションでは、Britta Simon に MyWorkDrive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[MyWorkDrive]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[MyWorkDrive]** を選択します。

    ![アプリケーションの一覧の MyWorkDrive のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-myworkdrive-test-user"></a>MyWorkDrive テスト ユーザーの作成

このセクションでは、MyWorkDrive で Britta Simon というユーザーを作成します。 [MyWorkDrive サポート チーム](mailto:support@myworkdrive.com)と連携し、MyWorkDrive プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [MyWorkDrive] タイルをクリックすると、SSO を設定した MyWorkDrive に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

