---
title: チュートリアル:Azure Active Directory と Velpic SAML の統合 | Microsoft Docs
description: Azure Active Directory と Velpic SAML の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8431e2c45cc3bfdfa08dd0078220ab2d290309
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565718"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>チュートリアル:Azure Active Directory と Velpic SAML の統合

このチュートリアルでは、Velpic SAML と Azure Active Directory (Azure AD) を統合する方法について説明します。
Velpic SAML と Azure AD の統合には、次の利点があります。

* Velpic SAML にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Velpic SAML に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Velpic SAML と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Velpic SAML でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Velpic SAML では、**SP** によって開始される SSO がサポートされます

## <a name="adding-velpic-saml-from-the-gallery"></a>ギャラリーからの Velpic SAML の追加

Azure AD への Velpic SAML の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Velpic SAML を追加する必要があります。

**ギャラリーから Velpic SAML を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Velpic SAML**」と入力し、結果ウィンドウで **[Velpic SAML]** を選択してから **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Velpic SAML](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーを基に、Velpic SAML で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Velpic SAML 内の関連ユーザー間にリンク関係が確立されている必要があります。

Velpic SAML で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Velpic SAML シングル サインオンの構成](#configure-velpic-saml-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Velpic SAML テスト ユーザーの作成](#create-velpic-saml-test-user)** - Velpic SAML で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Velpic SAML で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Velpic SAML** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Velpic SAML のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<sub-domain>.velpicsaml.net`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > サインオン URL は Velpic SAML チームによって提供され、識別子の値は Velpic SAML 側の SSO プラグインの構成時に入手できます。 Velpic SAML アプリケーションのページからこの値をコピーして、ここに貼り付けてください。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Velpic SAML のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-velpic-saml-single-sign-on"></a>Velpic SAML シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、Velpic SAML 企業サイトに管理者としてサインインします。

2. **[Manage] \(管理)** タブをクリックして **[Integration] \(統合)** セクションに移動します。ここで、**[Plugins] \(プラグイン)** をクリックして、サインイン用の新しいプラグインを作成する必要があります。

    ![プラグイン](./media/velpicsaml-tutorial/velpic_1.png)

3. **[Add plugin] \(プラグインの追加)** をクリックします。
    
    ![プラグイン](./media/velpicsaml-tutorial/velpic_2.png)

4. [Add plugin] \(プラグインの追加) ページで **[SAML]** タイルをクリックします。
    
    ![プラグイン](./media/velpicsaml-tutorial/velpic_3.png)

5. 新しい SAML プラグインの名前を入力し、**[追加]** をクリックします。

    ![プラグイン](./media/velpicsaml-tutorial/velpic_4.png)

6. 詳細を次のように入力します。

    ![プラグイン](./media/velpicsaml-tutorial/velpic_5.png)

    a. **[名前]** テキストボックスに、SAML プラグインの名前を入力します。

    b. **[Issuer URL]\(発行者 URL\)** ボックスに、Azure portal の **[サインオンの構成]** ウィンドウからコピーした **Azure AD 識別子**を貼り付けます。

    c. **[Provider Metadata Config] \(Provider メタデータ構成)** で、Azure Portal からダウンロードしたメタデータ XML ファイルをアップロードします。

    d. **Auto create new users \(新規ユーザーの自動作成)** チェックボックスをオンにして、SAML のジャストインタイム プロビジョニングを有効にすることもできます。 Velpic にユーザーが存在せず、このチェックボックスがオンになっていない場合は、Azure からのログインに失敗します。 このチェックボックスがオンになっている場合、ユーザーは、ログイン時に Velpic に自動的にプロビジョニングされます。 

    e. テキストボックスから**シングル サインオン URL** をコピーして、Azure Portal に貼り付けます。
    
    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Velpic SAML へのアクセス権を付与して、Azure のシングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Velpic SAML]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧から **[Velpic SAML]** を選択します。

    ![アプリケーションの一覧の Velpic SAML のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-velpic-saml-test-user"></a>Velpic SAML テスト ユーザーの作成

アプリケーションは、ジャスト イン タイムのユーザー プロビジョニングをサポートしているため、通常、この手順は必要ありません。 自動ユーザー プロビジョニングが有効でない場合は、次の手順にしたがって手動でユーザーを作成することができます。

Velpic SAML 企業サイトに管理者としてサインインし、次の手順に従います。
    
1. [Manage] \(管理) タブをクリックして [Users] \(ユーザー) セクションに移動し、[New] \(新規) をクリックしてユーザーを追加します。

    ![add user](./media/velpicsaml-tutorial/velpic_7.png)

2. **[Create New User] \(新しいユーザーの作成)** ダイアログ ページで、次の手順を実行します。

    ![ユーザー](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. **[First Name]\(名\)** ボックスに、ユーザーの名 Britta を入力します。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓 Simon を入力します。

    c. **[User Name] \(ユーザー名)** テキストボックスに、Britta Simon のユーザーを入力します。

    d. **[Email]\(電子メール\)** ボックスに、Brittasimon@contoso.com アカウントのメール アドレスを入力します。

    e. その他の情報は省略可能です。必要に応じて入力してください。
    
    f. **[保存]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

1. アクセス パネルで [Velpic SAML] タイルをクリックすると、Velpic SAML アプリケーションのログイン ページが表示されます。 サインイン ページに **[Log In With Azure AD] \(Azure AD でログイン)** と表示されます。

    ![プラグイン](./media/velpicsaml-tutorial/velpic_6.png)

1. **[Log In With Azure AD] \(Azure AD でログイン)** をクリックし、Azure AD アカウントを使用して Velpic にログインします。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

