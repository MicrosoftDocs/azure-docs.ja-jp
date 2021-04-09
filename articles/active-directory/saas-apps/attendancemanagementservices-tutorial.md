---
title: 'チュートリアル: Azure Active Directory と Attendance Management Services の統合 | Microsoft Docs'
description: Azure Active Directory と Attendance Management Services の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 1f404d3613f9de8daadc4bb2ceb39282cf3b619e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688996"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>チュートリアル: Azure Active Directory と Attendance Management Services の統合

このチュートリアルでは、Attendance Management Services と Azure Active Directory (Azure AD) を統合する方法について説明します。
Attendance Management Services と Azure AD の統合には、次の利点があります。

* Attendance Management Services にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Attendance Management Services に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Attendance Management Services と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Attendance Management Services でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Attendance Management Services では、**SP** によって開始される SSO がサポートされます

## <a name="adding-attendance-management-services-from-the-gallery"></a>ギャラリーから Attendance Management Services を追加する

Azure AD への Attendance Management Services の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Attendance Management Services を追加する必要があります。

**ギャラリーから Attendance Management Services を追加するには、次の手順を実行します:**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Attendance Management Services**」と入力して結果パネルから **[Attendance Management Services]** を選択し、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの Attendance Management Services](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Attendance Management Services で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Attendance Management Services の関連ユーザーの間で、リンク関係が確立されている必要があります。

Attendance Management Services で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Attendance Management Services シングル サインオンの構成](#configure-attendance-management-services-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Attendance Management Services テスト ユーザーの作成](#create-attendance-management-services-test-user)** - Attendance Management Services で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Attendance Management Services で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Attendance Management Services** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Attendance Management Services のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://id.obc.jp/<tenant information >/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Attendance Management Services クライアント サポート チーム](https://www.obcnet.jp/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Attendance Management Services のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Attendance Management Services シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Attendance Management Services 企業サイトにサインオンします。

1. **[セキュリティ管理] セクション** の **[SAML 認証]** をクリックします。

    ![このスクリーンショットは、非ラテン文字が使用されたページで [SAML 認証] が選択されている状態を示しています。](./media/attendancemanagementservices-tutorial/user1.png)

1. 次の手順に従います。

    ![このスクリーンショットは、この手順で説明されているタスクを実行できるウィンドウを示しています。](./media/attendancemanagementservices-tutorial/user2.png)

    a. **[SAML 認証を利用する]** を選択します。

    b. **[Identifier]\(識別子\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    c. **[Authentication endpoint URL]\(認証エンドポイント URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[ファイル選択]** ボタンをクリックして、Azure AD からダウンロードした証明書をアップロードします。

    e. **[パスワード認証を無効する]** を選択します。

    f. **[登録]** をクリックします

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

このセクションでは、Britta Simon に Attendance Management Services へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Attendance Management Services]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Attendance Management Services]** を選択します。

    ![アプリケーション一覧の [Attendance Management Services] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-attendance-management-services-test-user"></a>Attendance Management Services テスト ユーザーの作成

Azure AD ユーザーが Attendance Management Services にサインインできるようにするには、ユーザーが Attendance Management Services にプロビジョニングされる必要があります。 Attendance Management Services の場合、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Attendance Management Services 企業サイトにサインインします。

1. **[セキュリティ管理] セクション** の **[利用者管理]** をクリックします。

    ![このスクリーンショットは、非ラテン文字が使用されたページで [利用者管理] が選択されている状態を示しています。](./media/attendancemanagementservices-tutorial/user5.png)

1. **[新規登録]** をクリックします。

    ![このスクリーンショットは、プラス記号オプションの選択を示しています。](./media/attendancemanagementservices-tutorial/user3.png)

1. **[OBCiD 情報]** セクションで、次の手順を実行します。

    ![このスクリーンショットは、説明されているタスクを実行できるウィンドウを示しています。](./media/attendancemanagementservices-tutorial/user4.png)

    a. **[OBCiD]** ボックスに、ユーザーのメール アドレスを入力します (例: `BrittaSimon@contoso.com`)。

    b. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    c. **[登録]** をクリックします

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Attendance Management Services] タイルをクリックすると、SSO を設定した Attendance Management Services に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)