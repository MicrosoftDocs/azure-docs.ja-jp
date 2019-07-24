---
title: チュートリアル:Azure Active Directory と BambooHR の統合 | Microsoft Docs
description: Azure Active Directory と BambooHR の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a054274da0c83fa5f079e05a1c648c1fc693fd7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57875976"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>チュートリアル:Azure Active Directory と BambooHR の統合

このチュートリアルでは、BambooHR と Azure Active Directory (Azure AD) を統合する方法について説明します。
BambooHR と Azure AD の統合には、次の利点があります。

* Azure AD で誰が BambooHR にアクセスできるかを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して BambooHR に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と BambooHR の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* BambooHR でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* BambooHR では、**SP** によって開始される SSO がサポートされます

## <a name="adding-bamboohr-from-the-gallery"></a>ギャラリーからの BambooHR の追加

BambooHR の Azure AD への統合を構成するには、BambooHR をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから BambooHR を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**BambooHR**」と入力し、結果パネルで **[BambooHR]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の BambooHR](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、BambooHR を使用して Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと BambooHR 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

BambooHR で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[BambooHR シングル サインオンの構成](#configure-bamboohr-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[BambooHR テスト ユーザーの作成](#create-bamboohr-test-user)** - BambooHR で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

BambooHR を使用して Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **BambooHR** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[BambooHR のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<company>.bamboohr.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`BambooHR-SAML`

    > [!NOTE]
    > **サインオン URL** は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 値を取得するには、[BambooHR クライアント サポート チーム](https://www.bamboohr.com/contact.php)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[BambooHR のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-bamboohr-single-sign-on"></a>BambooHR のシングル サインオンの構成

1. 新しいウィンドウで、BambooHR ｌ企業サイトに管理者としてサインインします。

2. ホーム ページで、次の操作を行います。
   
    ![BambooHR シングル サインオン ページ](./media/bamboo-hr-tutorial/ic796691.png "シングル サインオン")   

    a. **[アプリ]** を選択します。
   
    b. **[アプリ]** ウィンドウの **[シングル サインオン]** を選択します。
   
    c. **[SAML シングル サインオン]** を選択します。

3. **[SAML シングル サインオン]** ウィンドウで、次の手順を実行します。
   
    ![[SAML シングル サインオン] ウィンドウ](./media/bamboo-hr-tutorial/IC796692.png "[SAML シングル サインオン]")
   
    a. **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、手順 6. で Azure portal からコピーした**ログイン URL** を貼り付けます。
      
    b. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[X.509 証明書]** ボックスに貼り付けます。
   
    c. **[保存]** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、BambooHR へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[BambooHR]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**BambooHR**」と入力して選択します。

    ![アプリケーションの一覧の BambooHR リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-bamboohr-test-user"></a>BambooHR テスト ユーザーの作成

Azure AD ユーザーで BambooHR にサインインできるようにするには、次の手順に従い、それらのユーザーを BambooHR に手動で設定します。

1. **BambooHR** サイトに管理者としてサインインします。

2. 上部のツールバーで **[Settings]\(設定\)** を選択します。
   
    ![[Settings]\(設定\) ボタン](./media/bamboo-hr-tutorial/IC796694.png "[Settings]\(設定\)")

3. **[概要]** を選択します。

4. 左側のウィンドウで **[Security]\(セキュリティ\)** > **[Users]\(ユーザー\)** を選択します。

5. 設定しようとしている有効な Azure AD アカウントのユーザー名、パスワード、メール アドレスを入力します。

6. **[保存]** を選択します。
        
>[!NOTE]
>Azure AD ユーザー アカウントの設定は、BambooHR のユーザー アカウント作成ツールや API を使って行うこともできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [BambooHR] タイルをクリックすると、SSO を設定した BambooHR に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

