---
title: 'チュートリアル: Azure Active Directory と BitaBIZ の統合 | Microsoft Docs'
description: Azure Active Directory と BitaBIZ の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: f564032873be6e4c70426d48c4576371862ea35d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673615"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>チュートリアル: Azure Active Directory と BitaBIZ の統合

このチュートリアルでは、BitaBIZ と Azure Active Directory (Azure AD) を統合する方法について説明します。
BitaBIZ と Azure AD の統合には、次の利点があります。

* BitaBIZ にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して BitaBIZ に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

BitaBIZ と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* BitaBIZ でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* BitaBIZ では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-bitabiz-from-the-gallery"></a>ギャラリーからの BitaBIZ の追加

Azure AD への BitaBIZ の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BitaBIZ を追加する必要があります。

**ギャラリーから BitaBIZ を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**BitaBIZ**」と入力し、結果パネルで **[BitaBIZ]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの BitaBIZ](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、BitaBIZ で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと BitaBIZ 内の関連ユーザー間にリンク関係が確立されている必要があります。

BitaBIZ で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[BitaBIZ のシングル サインオンの構成](#configure-bitabiz-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[BitaBIZ テスト ユーザーの作成](#create-bitabiz-test-user)** - BitaBIZ で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

BitaBIZ で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **BitaBIZ** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[BitaBIZ のドメインと URL] のシングル サインオン情報](common/idp-identifier.png)

    **[識別子]** ボックスに、`https://www.bitabiz.com/<instanceId>` の形式で URL を入力します。

    > [!NOTE]
    > 上記の URL の値は、単なる例です。 この値は、実際の識別子に置き換えてください。これについては後で説明します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![image](common/both-preintegrated-signon.png)

    **[サインオン URL]** テキスト ボックスに、URL として「`https://www.bitabiz.com/dashboard`」と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[BitaBIZ のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-bitabiz-single-sign-on"></a>BitaBIZ のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として BitaBIZ テナントにサインオンします。

2. **[SETUP ADMIN]\(管理設定\)** をクリックします。

    ![スクリーンショットは、[Setup Admin]\(管理設定\) が選択されているブラウザー ウィンドウの一部を示します。](./media/bitabiz-tutorial/settings1.png)

3. **[値の追加]** セクションで **[Microsoft integrations]\(Microsoft 統合\)** をクリックします。

    ![スクリーンショットは、[Microsoft integrations]\(Microsoft 統合\) が選択されている [値の追加] を示します。](./media/bitabiz-tutorial/settings2.png)

4. 下にスクロールして、 **[Microsoft Azure AD (シングル サインオンを有効にする)]** セクションに移動し、次の手順を実行します。

    ![スクリーンショットは、この手順で説明されている情報を入力する [Microsoft Azure AD] セクションを示します。](./media/bitabiz-tutorial/settings3.png)

    a. **[Entity ID]\(エンティティ ID\) (Azure AD では "識別子")** ボックスの値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。 

    b. **[Azure AD Single Sign-On Service URL]\(Azure AD のシングル サインオンのサービス URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。

    c. **[Azure AD SAML Entity ID]\(Azure AD SAML エンティティ ID\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** を貼り付けます。

    d. ダウンロードした **証明書 (Base64)** ファイルをメモ帳で開き、その内容をクリップボードにコピーし、 **[Azure AD Signing Certificate (Base64 encoded)]\(Azure AD 署名証明書 (Base64 でエンコード済み)\)** ボックスに貼り付けます。

    e. ビジネス メール ドメイン名 (mycompany.com) を **[ドメイン名]** ボックスに追加して、SSO を、このメール ドメインと共に (必須ではありません) 会社のユーザーに割り当てます。

    f. BitaBIZ アカウントの **[SSO enabled]\(SSO 有効\)** をオンします。

    g. **[Save Azure AD configuration]\(Azure AD 構成を保存する\)** をクリックして、SSO 構成をアクティブ化します。

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

このセクションでは、Britta Simon に BitaBIZ へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[BitaBIZ]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[BitaBIZ]** を選択します。

    ![アプリケーションの一覧の BitaBIZ リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-bitabiz-test-user"></a>BitaBIZ テスト ユーザーの作成

Azure AD ユーザーが BitaBIZ にログインできるようにするには、そのユーザーを BitaBIZ にプロビジョニングする必要があります。  
BitaBIZ の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. BitaBIZ の企業サイトに管理者としてログインします。

2. **[SETUP ADMIN]\(管理設定\)** をクリックします。

    ![スクリーンショットは、[Setup Admin]\(管理設定\) が選択されているブラウザー ウィンドウの一部を示します。](./media/bitabiz-tutorial/settings1.png)

3. **[組織]** セクションの **[ユーザーの追加]** をクリックします。

    ![スクリーンショットは、[ユーザーの追加] が選択されている [組織] セクションを示します。](./media/bitabiz-tutorial/user1.png)

4. **[Add new employee]\(新しい従業員の追加\)** をクリックします。

    ![スクリーンショットは、[Add new employee]\(新しい従業員の追加\) が選択されている [ユーザーの追加] を示します。](./media/bitabiz-tutorial/user2.png)

5. **[Add new employee]\(新しい従業員の追加\)** ダイアログ ページで、次の手順に従います。

    ![スクリーンショットは、この手順で説明されている情報を入力するページを示します。](./media/bitabiz-tutorial/user3.png)

    a. **[名]** ボックスに、ユーザーの名を入力します (この例では Britta)。

    b. **[姓]** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    d. **[Date of employment]\(雇用日\)** で日付を選択します。

    e. ユーザーに対して設定できる他の任意のユーザー属性があります。 詳細については、[従業員の設定ドキュメント](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee)を参照してください。

    f. **[Save employee]\(従業員を保存\)** をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [BitaBIZ] タイルをクリックすると、SSO を設定した BitaBIZ に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)