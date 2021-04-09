---
title: 'チュートリアル: Azure Active Directory と Front の統合 | Microsoft Docs'
description: Azure Active Directory と Front の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 3edd1e9bf3b42b3d4ad0c7c533dedb23ea05e5ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92450868"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>チュートリアル: Azure Active Directory と Front の統合

このチュートリアルでは、Front と Azure Active Directory (Azure AD) を統合する方法について説明します。
Front と Azure AD の統合には、次の利点があります。

* Front にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Front に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Front と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Front でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Front では、**IDP** Initiated SSO がサポートされます

## <a name="adding-front-from-the-gallery"></a>ギャラリーからの Front の追加

Azure AD への Front の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Front を追加する必要があります。

**ギャラリーから Front を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Front**」と入力し、結果パネルで **[Front]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Front](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Front で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Front 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Front で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Front のシングル サインオンの構成](#configure-front-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Front のテスト ユーザーの作成](#create-front-test-user)** - Front で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Front で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Front** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[Front のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<companyname>.frontapp.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<companyname>.frontapp.com/sso/saml/callback` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 この値を取得するには、[Front クライアント サポート チーム](mailto:support@frontapp.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Front のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-front-single-sign-on"></a>Front のシングル サインオンの構成

1. Front テナントに管理者としてサインオンします。

2. **[Settings (設定)] \(左のサイド バーの下にある歯車アイコン)、[Preferences (ユーザー設定)]** の順に移動します。
   
    ![[Settings]\(設定\) (歯車アイコン) が選択され、[Preferences]\(ユーザー設定\) が強調表示されているところを示すスクリーンショット。](./media/front-tutorial/tutorial_front_000.png)

3. **[シングル サインオン]** のリンクをクリックします。
   
    ![[Single Sign On]\(シングル サイン オン\) リンクが選択されている [Company preferences]\(会社の設定\) セクションを示すスクリーンショット。](./media/front-tutorial/tutorial_front_001.png)

4. **[Single Sign On]\(シングル サインオン\)** のドロップダウン リストで、 **[SAML]** を選択します。
   
    ![[S A M L] が選択されている [Single Sign On]\(シングル サイン オン\) ドロップダウン リストを示すスクリーンショット。](./media/front-tutorial/tutorial_front_002.png)

5. **[Entry Point]\(エントリ ポイント\)** ボックスに、Azure AD アプリケーションの構成ウィザードの **[ログイン URL]** の値を入力します。
    
    ![[Entry Point]\(エントリ ポイント\) ボックスを示すスクリーンショット。](./media/front-tutorial/tutorial_front_003.png)

6. ダウンロードした **証明書 (Base64)** ファイルをメモ帳で開き、その内容をクリップボードにコピーし、 **[Signing certificate]** ボックスに貼り付けます。
    
    ![[Signing certificate]\(署名証明書\) が強調表示され、テキスト ボックスが淡色表示されているところを示すスクリーンショット。](./media/front-tutorial/tutorial_front_004.png)

7. **[Service provider settings]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/front-tutorial/tutorial_front_005.png)

    a. **Entity ID** の値をコピーして Azure Portal の **[Front のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。

    b. **ACS URL** の値をコピーして Azure Portal の **[Front のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。
    
8. **[保存]** ボタンをクリックします。

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

このセクションでは、Britta Simon に Front へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Front]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Front]** を選択します。

    ![アプリケーションの一覧の Front のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-front-test-user"></a>Front テスト ユーザーの作成

このセクションでは、Front で Britta Simon というユーザーを作成します。 [Front クライアント サポート チーム](mailto:support@frontapp.com)と連携し、Front プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Front] タイルをクリックすると、SSO を設定した Front に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)