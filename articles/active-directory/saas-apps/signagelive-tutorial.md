---
title: チュートリアル:Azure Active Directory と Signagelive の統合 | Microsoft Docs
description: Azure Active Directory と Signagelive の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.openlocfilehash: 78324cfa58a8ac015b085052bdec7e3793befc1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348417"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>チュートリアル:Azure Active Directory と Signagelive の統合

このチュートリアルでは、Signagelive と Azure Active Directory (Azure AD) を統合する方法について説明します。
Signagelive と Azure AD の統合には、次の利点があります。

* Signagelive にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Signagelive に自動的にサインイン (シングル サインオン) するようにすることができます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](../manage-apps/what-is-single-sign-on.md)に関する記事をご覧ください。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Signagelive と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD 環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。
* Signagelive でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Signagelive では、SP-initiated SSO がサポートされます。

## <a name="add-signagelive-from-the-gallery"></a>ギャラリーからの Signagelive の追加

Azure AD への Signagelive の統合を構成するには、まず、ギャラリーからマネージド SaaS アプリの一覧に Signagelive を追加します。

ギャラリーから Signagelive を追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Signagelive**」と入力します。 

     ![結果リストの Signagelive](common/search-new-app.png)

5. 結果ウィンドウから **[Signagelive]** を選択し、 **[追加]** ボタンを選択して、アプリケーションを追加します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Signagelive で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Signagelive 内の関連ユーザーとの間にリンク関係を確立する必要があります。

Signagelive で Azure AD でのシングル サインオンを構成してテストするには、まず、次の構成要素を完了します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Signagelive のシングル サインオンの構成](#configure-signagelive-single-sign-on) - アプリケーション側でシングル サインオン設定を構成します。
3. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [Signagelive のテスト ユーザーの作成](#create-a-signagelive-test-user) - Signagelive で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Signagelive での Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Signagelive** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML]** を選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** を選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Signagelive のドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Signagelive クライアント サポート チーム](mailto:support@signagelive.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択して、ご自分の要件に適した特定のオプションの **証明書 (未加工)** をダウンロードします。 その後、ご自分のコンピューター上に保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[Signagelive のセットアップ]** セクションで、必要な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-signagelive-single-sign-on"></a>Signagelive のシングル サインオンの構成

Signagelive 側でシングル サインオンを構成するには、ダウンロードした **証明書 (未加工)** と Azure portal からコピーした URL を [Signagelive サポート チーム](mailto:support@signagelive.com)に送信します。 サポート チームにより、SAML シングル サインオン接続が両方の側で確実に正しく設定されます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、この場合は、「BrittaSimon@contoso.com」と入力できます。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Signagelive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Signagelive]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Signagelive]** を選択します。

    ![アプリケーションの一覧の Signagelive リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** ボタンを選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** 一覧から、 **[Britta Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンを選択します。

### <a name="create-a-signagelive-test-user"></a>Signagelive のテスト ユーザーの作成

このセクションでは、Signagelive で Britta Simon というユーザーを作成します。 [Signagelive サポート チーム](mailto:support@signagelive.com)と連携して、Signagelive プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、アクティブにする必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、MyApps ポータルを使用してご自分の Azure AD のシングル サインオン構成をテストします。

MyApps ポータル内で **[Signagelive]** タイルを選択すると、Signagelive に自動的にサインインします。 MyApps ポータルの詳細については、[MyApps ポータルの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)