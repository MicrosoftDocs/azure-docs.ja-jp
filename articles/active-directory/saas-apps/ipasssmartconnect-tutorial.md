---
title: チュートリアル:Azure Active Directory と iPass SmartConnect の統合 | Microsoft Docs
description: Azure Active Directory と iPass SmartConnect の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 9417d7b957d69dc802ecb2f9f78723eb7aba08ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099849"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>チュートリアル:Azure Active Directory と iPass SmartConnect の統合

このチュートリアルでは、iPass SmartConnect と Azure Active Directory (Azure AD) を統合する方法について説明します。
iPass SmartConnect と Azure AD の統合には、次の利点があります。

* iPass SmartConnect にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで iPass SmartConnect に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

iPass SmartConnect と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* iPass SmartConnect でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* iPass SmartConnect では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* iPass SmartConnect では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>ギャラリーから iPass SmartConnect を追加する

Azure AD への iPass SmartConnect の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iPass SmartConnect を追加する必要があります。

**ギャラリーから iPass SmartConnect を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**iPass SmartConnect**」と入力し、結果パネルで **[iPass SmartConnect]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果一覧の iPass SmartConnect](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、iPass SmartConnect で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと iPass SmartConnect 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

iPass SmartConnect で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[iPass SmartConnect のシングル サインオンの構成](#configure-ipass-smartconnect-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[iPass SmartConnect のテスト ユーザーの作成](#create-ipass-smartconnect-test-user)** - iPass SmartConnect で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

iPass SmartConnect で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **iPass SmartConnect** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[iPass SmartConnect のドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[iPass SmartConnect のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://om-activation.ipass.com/ClientActivation/ssolanding.go`」と入力します。

6. iPass SmartConnect アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name |  ソース属性|
    | ---------------| ----------|
    | firstName | User.givenname |
    | lastName | User.surname |
    | email | user.userprincipalname |
    | username | user.userprincipalname |
    | | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

9. **[iPass SmartConnect のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-ipass-smartconnect-single-sign-on"></a>iPass SmartConnect のシングル サインオンの構成

**iPass SmartConnect** 側でシングル サインオンを構成するには、ダウンロードした**フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [iPass SmartConnect サポート チーム](mailto:help@ipass.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

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

このセクションでは、Britta Simon に iPass SmartConnect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[iPass SmartConnect]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[iPass SmartConnect]** を選択します。

    ![[アプリケーション] 一覧の iPass SmartConnect リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-ipass-smartconnect-test-user"></a>iPass SmartConnect のテスト ユーザーの作成

このセクションでは、iPass SmartConnect で Britta Simon というユーザーを作成します。  [iPass SmartConnect サポート チーム](mailto:help@ipass.com) と協力して、iPass SmartConnect プラットフォームで許可リストに追加する必要のあるユーザーまたはドメインを追加します。 ドメインがチームによって追加された場合、ユーザーは iPass SmartConnect プラットフォームに自動的にプロビジョニングされます。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

**SP によって開始されたフローでアプリケーションをテストするには、次の手順を実行します。**

a. Windows の iPass SmartConnect クライアントを[こちら](https://om-activation.ipass.com/ClientActivation/ssolanding.go)からダウンロードします。

![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/testing3.png)

b. クライアントをインストールし、起動します。

c. **[開始]** をクリックします。

![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Azure ユーザー名とドメインを入力します。 **[続行]** をクリックします。 これは Azure ログイン ページにリダイレクトされます。

![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/testing2.png) 

e. 認証に成功すると、クライアントのアクティブ化が開始されます。 クライアントがアクティブになります。

**IdP によって開始されたフローでアプリケーションをテストするには、次の手順を実行します。**

a. [https://myapps.microsoft.com](https://myapps.microsoft.com) にログインします。

b. iPass SmartConnect アプリをクリックします。

c. SSA ページが表示されます。 **[Download App for Windows]\(Windows 向けアプリをダウンロードする\)** をクリックし、iPass SmartConnect クライアントをインストールします。

![[アプリケーション] 一覧の iPass SmartConnect リンク](./media/ipasssmartconnect-tutorial/testing4.png)

d. インストール後、最初に起動したクライアントは、利用規約に同意するとアクティブ化を自動的に開始します。

e. アクティブ化が開始しない場合、SSA ページの [アクティブ化] ボタンをクリックしてアクティブ化を開始してください。

f. クライアントがアクティブになります。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)