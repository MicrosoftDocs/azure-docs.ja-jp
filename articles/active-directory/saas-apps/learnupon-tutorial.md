---
title: 'チュートリアル: Azure Active Directory と LearnUpon の統合 | Microsoft Docs'
description: Azure Active Directory と LearnUpon の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 297395fbd0a7fd3fc8959eed5c0aeabd7b8e5751
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458695"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>チュートリアル: Azure Active Directory と LearnUpon の統合

このチュートリアルでは、LearnUpon と Azure Active Directory (Azure AD) を統合する方法について説明します。
LearnUpon と Azure AD の統合には、次の利点があります。

* LearnUpon にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LearnUpon に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

LearnUpon と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* LearnUpon でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。


* LearnUpon では、**IDP** によって開始される SSO がサポートされます

* LearnUpon では、**Just-In-Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-learnupon-from-the-gallery"></a>ギャラリーからの LearnUpon の追加

Azure AD への LearnUpon の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LearnUpon を追加する必要があります。

**ギャラリーから LearnUpon を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**LearnUpon**」と入力し、結果パネルで **LearnUpon** を選び、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リストの LearnUpon](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、LearnUpon で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと LearnUpon 内の関連ユーザー間にリンク関係が確立されている必要があります。

LearnUpon で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[LearnUpon シングル サインオンの構成](#configure-learnupon-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[LearnUpon テスト ユーザーの作成](#create-learnupon-test-user)** - LearnUpon で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

LearnUpon で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **LearnUpon** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[LearnUpon のドメインと URL] のシングル サインオン情報](common/idp-reply.png)

    **[応答 URL]** ボックスに、`https://<companyname>.learnupon.com/saml/consumer` のパターンを使用して URL を入力します

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[LearnUpon クライアント サポート チーム](https://www.learnupon.com/features/support/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページで、 **[拇印]** を確認します。これが LearnUpon SAML 設定に追加されます。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[LearnUpon のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-learnupon-single-sign-on"></a>LearnUpon シングル サインオンの構成

1. 別のブラウザー インスタンスを開き、管理者アカウントを使用して LearnUpon にサインインします。

1. **[settings (設定)]** タブをクリックします。

    ![設定タブのスクリーンショット。](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. **[Single Sign On - SAML (シングル サインオン - SAML)]** 、 **[General Settings (全般設定)]** の順にクリックし、SAML 設定を構成します。
   
    ![[Single Sign On - SAML]\(シングル サインオン - SAML\) で [全般設定] が選択された状態を示したスクリーンショット。](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. **[General Settings (全般設定)]** セクションで、次の手順に従います。
   
    ![ここで説明されている値を入力できる [全般設定] セクションのスクリーンショット。](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. **[Enabled]** を選択します。

    b. **[Version]\(バージョン\)** として **[2.0]** を選択します。

    c. **[Skip conditions]\(条件をスキップする\)** で **[いいえ]** を選択します。

    d. **[SAML Token POST param name (SAML トークン POST パラメーター名)]** ボックスに、前述の SAML コンシューマー URL に対する POST 要求パラメーターの名前を入力します。ここには、確認と認証の対象である SAML アサーションが含まれます (**SAMLResponse** など)。

    e. **[Name Identifier Format]\(名前識別子形式\)** テキストボックスに、SAML アサーション内のユーザー ID (メール アドレス) の場所を示す値を入力します (例: `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`)。
  
    f. Azure Portal のログイン画面でアップロード済みアイコンをクリックした際のユーザーの移動先を示す値を、 **[Identify Provider Location]\(プロバイダーの場所を特定\)** ボックスに入力します。
  
    g. **[Sign Out URL]\(サインアウト URL\)** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    h. **[Manage finger prints (指紋の管理)]** をクリックし、ダウンロードした証明書の指紋をアップロードします。

1. **[User Settings (ユーザー設定)]** をクリックし、次の手順を実行します。

     ![ここで説明されている値を入力できる [ユーザー設定] セクションのスクリーンショット。](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. **[First Name Identifier Format]\(名識別子形式\)** テキストボックスに、SAML アサーション内のユーザーの名の場所を示す値を入力します (例: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`)。
  
    b. **[Last Name Identifier Format]\(姓識別子形式\)** テキストボックスに、SAML アサーション内のユーザーの姓の場所を示す値を入力します (例: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`)。

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

このセクションでは、Britta Simon に LearnUpon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[LearnUpon]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[LearnUpon]** を選択します。

    ![アプリケーションの一覧の [LearnUpon] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-learnupon-test-user"></a>LearnUpon テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを LearnUpon に作成します。 LearnUpon では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 LearnUpon にユーザーがまだ存在していない場合は、認証後に新規に作成されます。 ユーザーを手動で作成する必要がある場合は、[LearnUpon のサポート チーム](https://www.learnupon.com/features/support/)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [LearnUpon] タイルをクリックすると、SSO を設定した LearnUpon に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)