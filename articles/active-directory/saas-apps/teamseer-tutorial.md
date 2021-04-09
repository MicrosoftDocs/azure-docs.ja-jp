---
title: チュートリアル:Azure Active Directory と TeamSeer の統合 | Microsoft Docs
description: Azure Active Directory と TeamSeer の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 6085ba5091b2b9973354280175aeb01f93ad7e28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521171"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>チュートリアル:Azure Active Directory と TeamSeer の統合

このチュートリアルでは、TeamSeer と Azure Active Directory (Azure AD) を統合する方法について説明します。
TeamSeer と Azure AD の統合には、次の利点があります。

* TeamSeer にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TeamSeer に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

TeamSeer と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* TeamSeer でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TeamSeer では、**SP** Initiated SSO がサポートされます

## <a name="adding-teamseer-from-the-gallery"></a>ギャラリーからの TeamSeer の追加

Azure AD への TeamSeer の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に TeamSeer を追加する必要があります。

**ギャラリーから TeamSeer を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**TeamSeer**」と入力し、結果ウィンドウで **[TeamSeer]** を選択してから **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の TeamSeer](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、TeamSeer で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと TeamSeer 内の関連ユーザー間にリンク関係が確立されている必要があります。

TeamSeer で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[TeamSeer シングル サインオンの構成](#configure-teamseer-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[TeamSeer テスト ユーザーの作成](#create-teamseer-test-user)** - TeamSeer で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

TeamSeer で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **TeamSeer** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[TeamSeer のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://www.teamseer.com/<companyid>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[TeamSeer クライアント サポート チーム](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[TeamSeer のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-teamseer-single-sign-on"></a>TeamSeer シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、TeamSeer 企業サイトに管理者としてサインインします。

1. **[HR Admin]** に移動します。

    ![TeamSeer ウィンドウから [H R Admin]\(H R 管理者\) が選択された画面のスクリーンショット。](./media/teamseer-tutorial/ic789634.png "[HR Admin]")

1. **[Setup]** をクリックします。

    ![セットアップ](./media/teamseer-tutorial/ic789635.png "セットアップ")

1. **[Set up SAML provider details]** をクリックします。

    ![[Set up SAML provider details]\(SAML プロバイダーの詳細の設定\) が選択された画面のスクリーンショット。](./media/teamseer-tutorial/ic789636.png "SAML 設定")

1. SAML プロバイダーの詳細セクションで、次の手順に従います。

    ![SAML プロバイダーの詳細が表示された画面のスクリーンショット。ここで、説明されている値を入力できます。](./media/teamseer-tutorial/ic789637.png "SAML 設定")

    a. **[URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[IdP Public Certificate]\(IdP パブリック証明書\)** ボックスに貼り付けます。

1. SAML プロバイダー構成を完了するには、次の手順に従います。

    ![SAML プロバイダー構成を示すスクリーンショット。ここで、説明されている値を入力できます。](./media/teamseer-tutorial/ic789638.png "SAML 設定")

    a. **[Test Email Addresses]** に、テスト ユーザーの電子メール アドレスを入力します。
  
    b. **[Issuer]** テキスト ボックスに、サービス プロバイダーの発行元 URL を入力します。
  
    c. **[保存]** をクリックします。

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

このセクションでは、Britta Simon に TeamSeer へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[TeamSeer]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[TeamSeer]** を選択します。

    ![アプリケーションの一覧の TeamSeer のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-teamseer-test-user"></a>TeamSeer テスト ユーザーの作成

Azure AD ユーザーが TeamSeer にログインできるようにするには、ユーザーを ShiftPlanning にプロビジョニングする必要があります。 TeamSeer の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **TeamSeer** 企業サイトに管理者としてサインインします。

1. **[HR Admin]\(HR 管理者\) \> [Users]\(ユーザー\)** の順に移動し、 **[Run the New User wizard]\(新しいユーザー ウィザードの実行\)** をクリックします。

    ![[H R Admin]\(H R 管理者\) タブのスクリーンショット。ここで、実行するウィザードを選択できます。](./media/teamseer-tutorial/ic789640.png "[HR Admin]")

1. **[User Details]** セクションで、次の手順に従います。

    ![ユーザーの詳細](./media/teamseer-tutorial/ic789641.png "[ユーザーの詳細]")

    a. プロビジョニングする有効な Azure AD アカウントの **名**、**姓**、**ユーザー名 (メール アドレス)** を、対応するボックスに入力します。
  
    b. **[次へ]** をクリックします。

1. 画面の指示に従って新しいユーザーを追加し、 **[完了]** をクリックします。

> [!NOTE]
> TeamSeer から提供されている他の TeamSeer ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [TeamSeer] タイルをクリックすると、SSO を設定した TeamSeer に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)