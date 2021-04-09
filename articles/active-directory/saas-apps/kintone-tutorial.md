---
title: 'チュートリアル: Azure Active Directory と Kintone の統合 | Microsoft Docs'
description: Azure Active Directory と Kintone の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 96ea3423d3c2dff2c8ba8c82b4c26d318c47211f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459052"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>チュートリアル: Azure Active Directory と Kintone の統合

このチュートリアルでは、Kintone と Azure Active Directory (Azure AD) を統合する方法について説明します。
Kintone と Azure AD の統合には、次の利点があります。

* Kintone にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Kintone にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Kintone と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Kintone でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Kintone では、**SP** によって開始される SSO がサポートされます

## <a name="adding-kintone-from-the-gallery"></a>ギャラリーからの Kintone の追加

Azure AD への Kintone の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kintone を追加する必要があります。

**ギャラリーから Kintone を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Kintone**」と入力し、結果パネルで **[Kintone]** を選択し、 **[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の Kintone](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Kintone で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Kintone 内の関連ユーザー間にリンク関係が確立されている必要があります。

Kintone で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Kintone シングル サインオンの構成](#configure-kintone-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Kintone テスト ユーザーの作成](#create-kintone-test-user)** - Kintone で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Kintone で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Kintone** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Kintone のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.kintone.com`

    b. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のパターンで URL を入力します。
    
    ```http
    https://<companyname>.cybozu.com
    https://<companyname>.kintone.com
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Kintone クライアント サポート チーム](https://www.kintone.com/contact/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Kintone のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-kintone-single-sign-on"></a>Kintone でのシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、 **Kintone** の企業サイトに管理者としてサインインします。

1. **設定** アイコンをクリックします。

    ![[設定]](./media/kintone-tutorial/ic785879.png "設定")

1. **[ユーザーとシステム管理]** をクリックします。

    ![[ユーザーとシステム管理]](./media/kintone-tutorial/ic785880.png "[ユーザーとシステム管理]")

1. **[システム管理] \> [セキュリティ]** で、 **[ログイン]** をクリックします。

    ![Login](./media/kintone-tutorial/ic785881.png "ログイン")

1. **[SAML 認証を有効にする]** を選択します。

    ![[ユーザーとシステム管理] が選択されているスクリーンショット。](./media/kintone-tutorial/ic785882.png "[SAML 認証]")

1. [SAML 承認] セクションで、次の手順に従います。

    ![SAML 認証](./media/kintone-tutorial/ic785883.png "[SAML 認証]")

    a. **[ログイン URL]** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[ログアウト URL]** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    c. Azure portal からダウンロードした証明書ファイルをアップロードするには、 **[参照]** をクリックします。

    d. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Kintone へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Kintone]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Kintone]** を選択します。

    ![アプリケーションの一覧の Kintone のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-kintone-test-user"></a>Kintone テスト ユーザーの作成

Azure AD ユーザーが Kintone にサインインできるようにするには、そのユーザーを Kintone にプロビジョニングする必要があります。 Kintone の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. **Kintone** の企業サイトに管理者としてサインインします。

1. **設定** アイコンをクリックします。

    ![[設定]](./media/kintone-tutorial/ic785879.png "設定")

1. **[ユーザーとシステム管理]** をクリックします。

    ![[ユーザーとシステム管理]](./media/kintone-tutorial/ic785880.png "ユーザーとシステム管理")

1. **[ユーザー管理]** で、 **[Departments & Users (部門とユーザー)]** をクリックします。

    ![[Department & Users]\(部門とユーザー\)](./media/kintone-tutorial/ic785888.png "部門とユーザー")

1. **[新しいユーザー]** をクリックします。

    ![[New User]\(新しいユーザー\) アクションが選択されている [Users]\(ユーザー\) セクションを示すスクリーンショット。](./media/kintone-tutorial/ic785889.png "新しいユーザー")

1. **[新しいユーザー]** セクションで、次の手順に従います。

    ![[新しいユーザー]](./media/kintone-tutorial/ic785890.png "新しいユーザー")

    a. プロビジョニングする有効な Azure AD アカウントの **表示名**、**ログイン名**、**新しいパスワード**、**パスワードの確認**、**メール アドレス**、その他の詳細を該当するボックスに入力します。

    b. **[保存]** をクリックします。

> [!NOTE]
> 他の Kintone ユーザー アカウント作成ツールや、Kintone から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Kintone] タイルをクリックすると、SSO を設定した Kintone に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)