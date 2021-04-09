---
title: 'チュートリアル: Azure Active Directory と IdeaScale の統合 | Microsoft Docs'
description: Azure Active Directory と IdeaScale の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: ef04bf9f6d9ca6ff5089d2826c05a5acfb759b04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001662"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>チュートリアル: Azure Active Directory と IdeaScale の統合

このチュートリアルでは、IdeaScale と Azure Active Directory (Azure AD) を統合する方法について説明します。
IdeaScale と Azure AD の統合には、次の利点があります。

* IdeaScale にアクセスする Azure AD ユーザーを制御できます
* ユーザーが自分の Azure AD アカウントを使用して IdeaScale に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と IdeaScale の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* IdeaScale でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* IdeaScale では、**SP** Initiated SSO がサポートされます

## <a name="adding-ideascale-from-the-gallery"></a>ギャラリーからの IdeaScale の追加

Azure AD への IdeaScale の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に IdeaScale を追加する必要があります。

**ギャラリーから IdeaScale を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**IdeaScale**」と入力し、結果パネルで **[IdeaScale]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の IdeaScale](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、IdeaScale で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと IdeaScale 内の関連ユーザー間にリンク関係が確立されている必要があります。

IdeaScale で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[IdeaScale のシングル サインオンの構成](#configure-ideascale-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[IdeaScale のテスト ユーザーの作成](#create-ideascale-test-user)** - IdeaScale で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

IdeaScale で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **IdeaScale** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[IdeaScale のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.ideascale.com`

    b. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のパターンで URL を入力します。
    
    ```http
    http://<companyname>.ideascale.com
    https://<companyname>.ideascale.com
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[IdeaScale クライアント サポート チーム](https://support.ideascale.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[IdeaScale のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-ideascale-single-sign-on"></a>IdeaScale のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、IdeaScale 企業サイトに管理者としてログインします。

2. **[コミュニティの設定]** に移動します。

    ![[コミュニティの設定]](./media/ideascale-tutorial/ic790847.png "[コミュニティの設定]")

3. **[セキュリティ] \> [シングル サインオン設定]** の順にクリックします。

    ![[Security]\(セキュリティ\) メニューで選択されている [Single Signon Settings]\(シングル サインオン設定\) を示すスクリーンショット。](./media/ideascale-tutorial/ic790848.png "[シングル サインオンの設定]")

4. **[シングル サインオンのタイプ]** で **[SAML 2.0]** を選択します。

    ![[シングル サインオンのタイプ]](./media/ideascale-tutorial/ic790849.png "シングル サインオンのタイプ")

5. **[シングル サインオンの設定]** ダイアログで、次の手順を実行します。

    ![[Single Signon Settings]\(シングル サインオンの設定\) ダイアログ ボックスを示すスクリーンショット。](./media/ideascale-tutorial/ic790850.png "[シングル サインオンの設定]")

    a. **[SAML IdP Entity ID]\(SAML IdP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    b. Azure portal からダウンロードしたメタデータ ファイルをメモ帳で開き、内容をコピーして、 **[SAML IdP Metadata]\(SAML IdP メタデータ\)** テキストボックスに貼り付けます。

    c. **[Logout Success URL]\(ログアウト成功 URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[変更を保存]** をクリックします。

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

このセクションでは、Britta Simon に IdeaScale へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[IdeaScale]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[IdeaScale]** を選択します。

    ![アプリケーションの一覧の IdeaScale のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-ideascale-test-user"></a>IdeaScale のテスト ユーザーの作成

Azure AD ユーザーが IdeaScale にログインできるようにするには、そのユーザーを IdeaScale にプロビジョニングする必要があります。 IdeaScale の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **IdeaScale** 企業サイトに管理者としてログインします。

2. **[コミュニティの設定]** に移動します。

    ![[コミュニティの設定]](./media/ideascale-tutorial/ic790847.png "[コミュニティの設定]")

3. **[基本設定] \> [メンバ管理]** の順にクリックします。

4. **[Add Member]** をクリックします。

    ![[メンバー管理]](./media/ideascale-tutorial/ic790852.png "メンバ管理")

5. [新しいメンバーの追加] セクションで、次の手順を実行します。

    ![[新しいメンバーの追加]](./media/ideascale-tutorial/ic790853.png "新しいメンバーの追加")

    a. **[電子メール アドレス]** ボックスに、プロビジョニングする有効な Azure AD アカウントのメール アドレスを入力します。

    b. **[変更を保存]** をクリックします。

    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

> [!NOTE]
> 他の IdeaScale ユーザー アカウント作成ツールや、IdeaScale から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [IdeaScale] タイルをクリックすると、SSO を設定した IdeaScale に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)