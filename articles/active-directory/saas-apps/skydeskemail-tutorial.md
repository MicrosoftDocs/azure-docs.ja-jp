---
title: 'チュートリアル: Azure Active Directory と SkyDesk Email の統合 | Microsoft Docs'
description: Azure Active Directory と SkyDesk Email の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 30e94c9737241ff49c29898adcc5e50c6b73a9b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516034"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>チュートリアル: Azure Active Directory と SkyDesk Email の統合

このチュートリアルでは、SkyDesk Email と Azure Active Directory (Azure AD) を統合する方法について説明します。
SkyDesk Email と Azure AD の統合には、次の利点があります。

* SkyDesk Email にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで SkyDesk Email に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と SkyDesk Email の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SkyDesk Email でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SkyDesk Email では、**SP** Initiated SSO がサポートされます

## <a name="adding-skydesk-email-from-the-gallery"></a>ギャラリーからの SkyDesk Email の追加

Azure AD への SkyDesk Email の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SkyDesk Email を追加する必要があります。

**ギャラリーから SkyDesk Email を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SkyDesk Email**」と入力し、結果パネルで **[SkyDesk Email]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の SkyDesk Email](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SkyDesk Email で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SkyDesk Email 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

SkyDesk Email で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SkyDesk Email のシングル サインオンの構成](#configure-skydesk-email-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Skydesk Email のテスト ユーザーの作成](#create-skydesk-email-test-user)** - Skydesk Email で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SkyDesk Email で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SkyDesk Email** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[SkyDesk Email のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://mail.skydesk.jp/portal/<companyname>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[SkyDesk Email クライアント サポート チーム](https://www.skydesk.jp/apps/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[SkyDesk Email のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-skydesk-email-single-sign-on"></a>SkyDesk Email のシングル サインオンの構成

1. 別の Web ブラウザーから、管理者として SkyDesk Email アカウントにサインオンします。

1. 上部のメニューで、 **[設定]** をクリックし、 **[組織]** を選択します。

    ![[設定] メニューから [組織] が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. 左側のパネルで **[ドメイン]** をクリックします。

    ![コントロール パネルから [ドメイン] が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. **[ドメインの追加]** をクリックします。

    ![[ドメインの追加] が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. 自分のドメイン名を入力し、ドメインを確認します。

    ![[ドメインの追加] タブのスクリーンショット。ここでドメインを入力することができます。](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. 左側のパネルで **[SAML Authentication (SAML 認証)]** をクリックします。

    ![コントロール パネルから [SAML Authentication]\(SAML 認証\) が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. **[SAML 認証]** ダイアログ ページで、次の手順に従います。

    ![[SAML Authentication Details]\(SAML 認証の詳細\) ダイアログ ボックスのスクリーンショット。ここで、説明されている値を入力できます。](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > SAML ベースの認証を使用するには、**ドメインが検証済み** または **ポータル URL** が設定済みである必要があります。 一意の名前でポータル URL を設定できます。

    ![名前を入力する [Portal U R L]\(ポータル U R L\) のスクリーンショット。](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. **[ログイン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[ログアウト URL]** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    c. **[パスワード変更 URL]** は省略可能なので、空白のままにします。

    d. **[Get Key From File (ファイルからキーを取得)]** をクリックし、Azure portal からダウンロードした証明書を選択してから、 **[開く]** をクリックして証明書をアップロードします。

    e. **[アルゴリズム]** として **[RSA]** を選択します。

    f. **[OK]** をクリックして変更を保存します。

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

このセクションでは、Britta Simon に SkyDesk Email へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[SkyDesk Email]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SkyDesk Email]** を選択します。

    ![アプリケーションの一覧の SkyDesk Email のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-skydesk-email-test-user"></a>SkyDesk Email のテスト ユーザーの作成

このセクションでは、SkyDesk Email で Britta Simon というユーザーを作成します。

SkyDesk Email 内の左側のパネルで **[User Access (ユーザー アクセス)]** をクリックし、自分のユーザー名を入力します。

![コントロール パネルから [User Access]\(ユーザー アクセス\) が選択された画面のスクリーンショット。](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> ユーザーを一括で作成する必要がある場合は、[SkyDesk Email クライアント サポート チーム](https://www.skydesk.jp/apps/support/)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SkyDesk Email] タイルをクリックすると、SSO を設定した SkyDesk Email に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)