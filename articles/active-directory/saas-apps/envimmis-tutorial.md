---
title: 'チュートリアル: Azure Active Directory と Envi MMIS の統合 | Microsoft Docs'
description: Azure Active Directory と Envi MMIS の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 6ccf755a73cafa4b855f602aa18246d710e5e1ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454013"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>チュートリアル: Azure Active Directory と Envi MMIS の統合

このチュートリアルでは、Envi MMIS と Azure Active Directory (Azure AD) を統合する方法について説明します。
Envi MMIS と Azure AD の統合には、次の利点があります。

* Envi MMIS にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Envi MMIS に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Envi MMIS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Envi MMIS でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Envi MMIS では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます

## <a name="adding-envi-mmis-from-the-gallery"></a>ギャラリーからの Envi MMIS の追加

Azure AD への Envi MMIS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Envi MMIS を追加する必要があります。

**ギャラリーから Envi MMIS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Envi MMIS**」と入力し、結果ウィンドウで **Envi MMIS** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Envi MMIS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Envi MMIS で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Envi MMIS 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Envi MMIS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Envi MMIS シングル サインオンの構成](#configure-envi-mmis-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Envi MMIS のテスト ユーザーの作成](#create-envi-mmis-test-user)** - Envi MMIS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Envi MMIS で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Envi MMIS** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[識別子]、[応答 U R L]、および [保存] ボタンが強調表示されている [基本的な S A M L 構成] を示すスクリーンショット。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://www.<CUSTOMER DOMAIN>.com/Account` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://www.<CUSTOMER DOMAIN>.com/Account/Acs` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Envi MMIS のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://www.<CUSTOMER DOMAIN>.com/Account` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Envi MMIS クライアント サポート チーム](mailto:support@ioscorp.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Envi MMIS のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-envi-mmis-single-sign-on"></a>Envi MMIS シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Envi MMIS サイトに管理者としてサインインします。

2. **[マイ ドメイン]** タブをクリックします。

    ![[My Domain]\(マイ ドメイン\) が選択されている [User]\(ユーザー\) メニューを示すスクリーンショット。](./media/envimmis-tutorial/configure1.png)

3. **[編集]** をクリックします。

    ![選択された [Edit]\(編集\) ボタンを示すスクリーンショット。](./media/envimmis-tutorial/configure2.png)

4. **[Use remote authentication]\(リモート認証を使用する\)** チェック ボックスをオンにして、 **[Authentication Type]\(認証の種類\)** ドロップダウンから **[HTTP Redirect]\(HTTP リダイレクト\)** を選びます。

    ![[Use remote authentication]\(リモート認証を使用する\) がオンになり、[H T T P Redirect]\(H T T P リダイレクト\) が選択されている [Details]\(詳細\) タブを示すスクリーンショット。](./media/envimmis-tutorial/configure3.png)

5. **[Resources]\(リソース\)** タブを選び、 **[Upload Metadata]\(メタデータのアップロード\)** をクリックします。

    ![[Upload Metadata]\(メタデータのアップロード\) アクションが選択された [Resources]\(リソース\) タブを示すスクリーンショット。](./media/envimmis-tutorial/configure4.png)

6. **[Upload Metadata]\(メタデータのアップロード\)** ポップアップで、次の手順を実行します。

    ![[File]\(ファイル\) オプションが選択され、ファイルの選択アイコンと [OK] ボタンが強調表示されている [Upload Metadata]\(メタデータのアップロード\) ポップアップを示すスクリーンショット。](./media/envimmis-tutorial/configure5.png)

    a. **[Upload From]\(アップロード元\)** ドロップダウンから **[File]\(ファイル\)** オプションを選びます。

    b. **ファイル選択アイコン** を選ぶことにより、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。

    c. **[OK]** をクリックします。

7. ダウンロードしたメタデータ ファイルをアップロードした後、フィールドは自動的に設定されます。 **[Update]\(更新\)** をクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Envi MMIS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Envi MMIS]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Envi MMIS]** を選択します。

    ![アプリケーションの一覧の Envi MMIS のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-envi-mmis-test-user"></a>Envi MMIS のテスト ユーザーの作成

Azure AD ユーザーが Envi MMIS にサインインできるようにするには、そのユーザーを Envi MMIS にプロビジョニングする必要があります。 Envi MMIS の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Envi MMIS 企業サイトに管理者としてサインインします。

2. **[User List]\(ユーザー一覧\)** タブをクリックします。

    ![[User List]\(ユーザー一覧\) が選択されている [User]\(ユーザー\) メニューを示すスクリーンショット。](./media/envimmis-tutorial/user1.png)

3. **[Add User]\(ユーザーの追加\)** ボタンをクリックします。

    ![[Add User]\(ユーザーの追加\) ボタンが選択されている [User]\(ユーザー\) セクションを示すスクリーンショット。](./media/envimmis-tutorial/user2.png)

4. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![従業員の追加](./media/envimmis-tutorial/user3.png)

    a. **[ユーザー名]** ボックスに、Britta Simon のアカウントのユーザー名を入力します (例: **brittasimon\@contoso.com**)。
    
    b. **[First Name]\(名\)** テキスト ボックスに、Britta Simon の名を入力します (**Britta**)。

    c. **[Last Name]\(姓\)** テキスト ボックスに、Britta Simon の名を入力します (**Simon**)。

    d. ユーザーの役職を **[Title]\(役職\)** テキスト ボックスに入力します。
    
    e. **[メール アドレス]** ボックスに、Britta Simon のアカウントのメール アドレスを入力します (例: **brittasimon\@contoso.com**)。

    f. **[SSO User Name]\(SSO ユーザー名\)** ボックスに、Britta Simon のアカウントのユーザー名を入力します (例: **brittasimon\@contoso.com**)。

    g. **[保存]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Envi MMIS] タイルをクリックすると、SSO を設定した Envi MMIS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)