---
title: チュートリアル:Azure Active Directory と Percolate の統合 | Microsoft Docs
description: Azure Active Directory と Percolate の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2110b1ec7d5d6b317341855ff19acf7975733e71
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617871"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>チュートリアル:Azure Active Directory と Percolate との統合

このチュートリアルでは、Percolate と Azure Active Directory (Azure AD) を統合する方法について説明します。
Percolate と Azure AD の統合には、次の利点があります。

* Percolate にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Percolate にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Percolate と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Percolate でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Percolate では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます

## <a name="adding-percolate-from-the-gallery"></a>ギャラリーからの Percolate の追加

Azure AD への Percolate の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Percolate を追加する必要があります。

**ギャラリーから Percolate を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Percolate**」と入力し、結果パネルで **[Percolate]** を選択してから **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Percolate](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Percolate で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Percolate 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Percolate で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Percolate シングル サインオンの構成](#configure-percolate-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Percolate テスト ユーザーの作成](#create-percolate-test-user)** - Percolate で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Percolate で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Percolate** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **IDP** 開始モードでアプリケーションを構成する場合は、アプリケーションが Azure と既に統合されているため、**[基本的な SAML 構成]** セクションで実行する必要がある手順はありません。

    ![[Percolate のドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Percolate のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://percolate.com/app/login`」と入力します。

6. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

7. **[Percolate のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-percolate-single-sign-on"></a>Percolate シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、管理者として Percolate にサインインします。

2. ホーム ページの左側で、**[Settings]\(設定\)** をクリックします。
    
    ![Configure single sign-on](./media/percolate-tutorial/configure01.png)

3. メニュー バーの左側で、**[Organization]\(組織\)** の下の **[SSO]** をクリックします。

    ![Configure single sign-on](./media/percolate-tutorial/configure02.png)

    a. **[ログイン URL]** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[x509 certificates]\(x509 証明書\)** ボックスに貼り付けます。

    d. **[Email attribute]\(メール属性\)** ボックスに、「**emailaddress**」と入力します。

    e. **[Identity provider metadata URL]\(ID プロバイダー メタデータ URL\)** フィールドは省略可能であり、Azure portal からコピーした**アプリのフェデレーション メタデータ URL** がある場合は、**[Identity provider metadata URL]\(ID プロバイダー メタデータ URL\)** ボックスに貼り付けます。

    f. **[Should AuthNRequests be signed?]\(AuthNRequests には署名が必要ですか?\)** に対して **[No]\(いいえ\)** を選択します。

    g. **[Enable SSO auto-Provisioning]\(SSO の自動プロビジョニングを有効にする\)** に対して **[No]\(いいえ\)** を選択します。

    h. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Percolate へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Percolate]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Percolate]** を選択します。

    ![アプリケーションの一覧の Percolate のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-percolate-test-user"></a>Percolate テスト ユーザーの作成

Azure AD ユーザーが Percolate にサインインできるようにするには、ユーザーを Percolate にプロビジョニングする必要があります。 Percolate では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Percolate にサインインします。

2. メニュー バーの左側で、**[Organization]\(組織\)** の下の **[Users]\(ユーザー\)** をクリックし、**[New users]\(新しいユーザー\)** に移動します。

    ![Configure single sign-on](./media/percolate-tutorial/configure03.png)

3. **[Create users]\(ユーザーの作成\)** ページで、以下の手順を実行します。

    ![Configure single sign-on](./media/percolate-tutorial/configure04.png)

    a. **[電子メール]** ボックスに、ユーザーのメール アドレスを入力します (例: brittasimon@contoso.com)。

    b. **[Fullname]\(フル ネーム\)** ボックスに、ユーザーの名前を入力します (例: **Brittasimon**)。

    c. **[Create users]\(ユーザーの作成\)** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Percolate] タイルをクリックすると、SSO を設定した Percolate に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

