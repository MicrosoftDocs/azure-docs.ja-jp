---
title: 'チュートリアル: Azure Active Directory と OrgChart Now の統合 | Microsoft Docs'
description: Azure Active Directory と OrgChart Now の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6b90be8e951c0e30c99d0bb16f85e49cf991f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522327"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>チュートリアル: Azure Active Directory と OrgChart Now の統合

このチュートリアルでは、OrgChart Now と Azure Active Directory (Azure AD) を統合する方法について説明します。
OrgChart Now と Azure AD の統合には、次の利点があります。

* OrgChart Now にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して OrgChart Now に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

OrgChart Now と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* OrgChart Now でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* OrgChart Now では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-orgchart-now-from-the-gallery"></a>ギャラリーから OrgChart Now を追加

Azure AD への OrgChart Now の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OrgChart Now を追加する必要があります。

**ギャラリーから OrgChart Now を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**OrgChart Now**」と入力し、結果パネルから **[OrgChart Now]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の OrgChart Now](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、OrgChart Now で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと OrgChart Now 内の関連ユーザー間にリンク関係が確立されている必要があります。

OrgChart Now で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[OrgChart Now シングル サインオンの構成](#configure-orgchart-now-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[OrgChart Now のテスト ユーザーの作成](#create-orgchart-now-test-user)** - OrgChart Now で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

OrgChart Now で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **OrgChart Now** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[OrgChart Now のドメインと URL] のシングル サインオン情報](common/idp-identifier.png)

    **[識別子]** テキスト ボックスに、`https://sso2.orgchartnow.com` という URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![image](common/both-preintegrated-signon.png)

    **[サインオン URL]** ボックスに、`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com` という形式で URL を入力します。

    > [!NOTE]
    > `<YourEntityID>` は、 **[OrgChart Now のセットアップ]** セクションでコピーする **Azure AD の ID** です (このチュートリアルで後ほど説明します)。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[OrgChart Now のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-orgchart-now-single-sign-on"></a>OrgChart Now のシングル サインオンを構成する

**OrgChart Now** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [OrgChart Now サポート チーム](mailto:ocnsupport@officeworksoftware.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に OrgChart Now へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[OrgChart Now]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[OrgChart Now]** を選択します。

    ![アプリケーションの一覧の [OrgChart Now] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-orgchart-now-test-user"></a>OrgChart Now のテスト ユーザーの作成

Azure AD ユーザーが OrgChart Now にログインできるようにするには、そのユーザーを OrgChart Now にプロビジョニングする必要があります。 

1. OrgChart Now では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 OrgChart Now にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。 Just-In-Time ユーザー プロビジョニング機能では、認識されている IDP から SSO 要求が届いたとき、SAML アサーションの電子メールがユーザーの一覧に見つからない場合にのみ **読み取り専用** ユーザーが作成されます。 この自動プロビジョニング機能のために、OrgChart Now で「**全般**」というタイトルのアクセス グループを作成する必要があります。 次の手順でアクセス グループを作成してください。

    a. UI の右上隅にある **歯車** をクリックし、**[グループの管理]** オプションに進みます。

    ![OrgChart Now のグループ](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. **[追加]** アイコンを選択し、グループに「**全般**」という名前を付け、**[OK]** をクリックします。 

    ![OrgChart Now の [追加]](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. 全般 (読み取り) ユーザーがアクセスできるフォルダーを選択します。

    ![OrgChart Now のフォルダー](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. 管理者ユーザーのみが変更できるようにフォルダーを **ロックします**。 **[OK]** をクリックします。

    ![OrgChart Now のロック機能](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. **管理者** ユーザーと **読み取り/書き込み** ユーザーを作成するには、SSO 経由で特権レベルへのアクセスを取得する目的でユーザーを手動作成する必要があります。 ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

    a. セキュリティ管理者として OrgChart Now にログインします。

    b.  右上隅にある **[設定]** をクリックし、**[ユーザーの管理]** に移動します。

    ![OrgChart Now の設定](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. **[追加]** をクリックし、次の手順を実行します。

    ![OrgChart Now の管理](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * **[ユーザー ID]** テキストボックスに **brittasimon\@contoso.com** のようなユーザー ID を入力します。

    * **[Email Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    * **[追加]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [OrgChart Now] タイルをクリックすると、SSO を設定した OrgChart Now に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)