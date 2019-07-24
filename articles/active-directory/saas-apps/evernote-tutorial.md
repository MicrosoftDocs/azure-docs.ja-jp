---
title: チュートリアル:Azure Active Directory と Evernote の統合 | Microsoft Docs
description: Azure Active Directory と Evernote の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 2ce05e904484a6d773a0132734208b87e161f960
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59499922"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>チュートリアル:Azure Active Directory と Evernote の統合

このチュートリアルでは、Evernote と Azure Active Directory (Azure AD) を統合する方法について説明します。
Evernote と Azure AD の統合には、次の利点があります。

* Evernote にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Drift にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Evernote の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Evernote でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Evernote では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-evernote-from-the-gallery"></a>ギャラリーからの Evernote の追加

Azure AD への Evernote の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Evernote を追加する必要があります。

**ギャラリーから Evernote を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Evernote**」と入力し、結果ウィンドウで **[Evernote]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Evernote](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Evernote で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Evernote 内の関連ユーザー間にリンク関係が確立されている必要があります。

Evernote で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Evernote のシングル サインオンの構成](#configure-evernote-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Evernote テスト ユーザーの作成](#create-evernote-test-user)** - Evernote で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Evernote で Azure AD のシングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Evernote** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Evernote のドメインと URL] のシングル サインオン情報](common/idp-identifier.png)

    **[識別子]** テキスト ボックスに、`https://www.evernote.com/saml2` という URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![image](common/both-preintegrated-signon.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://www.evernote.com/Login.action`」と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **署名**オプションを変更するには、**[編集]** をクリックして **[SAML 署名証明書]** ダイアログを開きます。

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. **[署名オプション]** で **[SAML 応答とアサーションへの署名]** オプションを選択します。

    b. **[保存]**

8. **[Evernote のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-evernote-single-sign-on"></a>Evernote のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Evernote 企業サイトに管理者としてログインします。

2. **[管理コンソール]** に移動します。

    ![管理コンソール](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. **[管理コンソール]** から **[セキュリティ]** に移動し、**[シングル サインオン]** を選択します。

    ![SSO の設定](./media/evernote-tutorial/tutorial_evernote_sso.png)

4. 次の値を構成します。

    ![Certificate-Setting](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **SSO を有効にします。** SSO は既定で有効になっています (SSO の要件を削除する場合は、**[シングル サインオンを無効にする]** をクリックします)

    b. Azure portal からコピーした **[ログイン URL]** の値を **[SAML HTTP Request URL]\(SAML HTTP 要求 URL\)** ボックスに貼り付けます。

    c. Azure AD からダウンロードした証明書をメモ帳で開き、"BEGIN CERTIFICATE" および "END CERTIFICATE" を含む内容をコピーして、**[X.509 証明書]** ボックスに貼り付けます。 

    d. **[変更の保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Evernote へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Evernote]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Evernote]** を選択します。

    ![アプリケーションの一覧の Evernote のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-evernote-test-user"></a>Evernote テスト ユーザーの作成

Azure AD ユーザーが Evernote にログインできるようにするには、そのユーザーを Evernote にプロビジョニングする必要があります。  
Evernote の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Evernote の企業サイトに管理者としてログインします。

2. **[管理コンソール]** をクリックします。

    ![管理コンソール](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. **[管理コンソール]** から **[ユーザーの追加]** に移動します。

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **[メール]** テキストボックスの **[チーム メンバーを追加]** にユーザー アカウントのメール アドレスを入力し、**[招待する]** をクリックします。

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. 招待が送信された後、招待を承諾するメールが Azure Active Directory アカウント保有者に届きます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Evernote] タイルをクリックすると、SSO を設定した Evernote に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

