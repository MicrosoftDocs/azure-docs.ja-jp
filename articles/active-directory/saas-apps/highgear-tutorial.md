---
title: チュートリアル:Azure Active Directory と HighGear の統合 | Microsoft Docs
description: Azure Active Directory と HighGear の間でシングル サインオンを構成する方法について学習します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: b3806d768e8335801e2dd097eac7a440b157de38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92444708"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>チュートリアル:Azure Active Directory と HighGear の統合

このチュートリアルでは、HighGear と Azure Active Directory (Azure AD) を統合する方法を学習できます。
HighGear と Azure AD の統合には、次の利点があります。

* HighGear にアクセスできる Azure AD ユーザーを制御できます。
* お客様のユーザーが自身の Azure AD アカウントを使用して HighGear に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と HighGear の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます
* Enterprise または Unlimited ライセンスの HighGear システム

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストする方法を学習できます。

* HighGear では、**SP と IdP** によって開始される SSO がサポートされます

## <a name="adding-highgear-from-the-gallery"></a>ギャラリーからの HighGear の追加

Azure AD への HighGear の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に HighGear を追加する必要があります。

**ギャラリーから HighGear を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**HighGear**」と入力し、結果パネルで **HighGear** を選び、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果リストの HighGear](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーを基に、お客様の HighGear システムで Azure AD のシングル サインオンを構成してテストする方法について学習できます。
シングル サインオンを機能させるには、Azure AD ユーザーとお客様の HighGear システム内の関連ユーザーとの間にリンク関係が確立されている必要があります。

お客様の HighGear システムで Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[HighGear シングル サインオンの構成](#configure-highgear-single-sign-on)** - HighGear アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[HighGear のテスト ユーザーの作成](#create-highgear-test-user)** - HighGear で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。 
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にする方法を学習できます。

お客様の HighGear システムで Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **HighGear** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** テキスト ボックスに、お客様の HighGear システムの [Single Sign-On Settings]\(シングル サインオンの設定\) ページにある **[Service Provider Entity ID]\(サービス プロバイダー エンティティ ID\)** フィールドの値を貼り付けます。

    ![[Service Provider Entity ID]\(サービス プロバイダー エンティティ ID\) フィールド](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > [Single Sign-On Settings]\(シングル サインオンの設定\) ページにアクセスするには、お客様の HighGear システムにログインする必要があります。 ログインしたら、HighGear の [Administration]\(管理\) タブにマウスを移動して、[Single Sign-On Settings]\(シングル サインオンの設定\) メニュー項目をクリックします。
    
    ![[Single Sign-On Settings]\(シングル サインオンの設定\) メニュー項目](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. **[返信 URL]** テキスト ボックスに、お客様の HighGear システムの [Single Sign-On Settings]\(シングル サインオンの設定\) ページから **[Assertion Consumer Service (ACS) URL]** の値を貼り付けます。

    ![[Assertion Consumer Service (ACS) URL] フィールド](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

     ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

     **[サインオン URL]** テキスト ボックスに、お客様の HighGear システムの [Single Sign-On Settings]\(シングル サインオンの設定\) ページにある **[Service Provider Entity ID]\(サービス プロバイダー エンティティ ID\)** フィールドの値を貼り付けます (このエンティティ ID は、SP によって開始されたサインオンに使用される HighGear システムのベース URL でもあります)。

    ![[Service Provider Entity ID]\(サービス プロバイダー エンティティ ID\) フィールド](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、お客様の HighGear システムの **[Single Sign-On Settings]\(シングル サインオンの設定\)** ページにある実際の識別子、返信 URL、サインオン URL で更新してください。 ヘルプが必要な場合は、[HighGear サポート チーム](mailto:support@highgear.com)にお問い合わせください。

4. **[Set up Single Sign-On with SAML]\(SAML でのシングル サインオンの設定\)** ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、 **[Download]\(ダウンロード\)** をクリックして **証明書 (Base64)** をダウンロードし、コンピューターに保存します。 これは、後のシングル サインオンの構成手順で必要になります。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[HighGear の設定]** セクションで、次の URL の場所をメモします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. [ログイン URL]。 この値は、次の「**HighGear シングル サインオンの構成**」の手順 2. で必要になります。

    b. [Azure AD 識別子]。 この値は、次の「**HighGear シングル サインオンの構成**」の手順 3. で必要になります。

    c. [ログアウト URL]。 この値は、次の「**HighGear シングル サインオンの構成**」の手順 4. で必要になります。

### <a name="configure-highgear-single-sign-on"></a>HighGear シングル サインオンの構成

HighGear のシングル サインオンを構成するには、お客様の HighGear システムにログインします。 ログインしたら、HighGear の [Administration]\(管理\) タブにマウスを移動して、[Single Sign-On Settings]\(シングル サインオンの設定\) メニュー項目をクリックします。

![[Single Sign-On Settings]\(シングル サインオンの設定\) メニュー項目](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. **[Identity Provider Name]\(ID プロバイダー名\)** に、ログイン ページの HighGear のシングル サインオン ボタンに表示される簡単な説明を入力します。 次に例を示します。Azure AD

2. HighGear の **[Single Sign-On (SSO) URL]\(シングル サインオン (SSO) URL\)** フィールドに、Azure の **[HighGear の設定]** セクションにある **[ログイン URL]** フィールドの値を貼り付けます。

3. HighGear の **[Identity Provider Entity ID]\(ID プロバイダー エンティティ ID\)** フィールドに、Azure の **[HighGear の設定]** セクションにある **[Azure AD 識別子]** フィールドの値を貼り付けます。

4. HighGear の **[Single Logout (SLO) URL]\(シングル ログアウト (SLO) URL\)** フィールドに、Azure の **[HighGear の設定]** セクションにある **[ログアウト URL]** フィールドの値を貼り付けます。

5. メモ帳を使用して、お客様が Azure の **[SAML 署名証明書]** セクションからダウンロードした証明書を開きます。 **[証明書 (Base64)]** 形式でダウンロードしている必要があります。 メモ帳から証明書の内容をコピーし、HighGear の **[Identity Provider Certificate]\(ID プロバイダー証明書\)** フィールドに貼り付けます。

6. お客様の HighGear 証明書をリクエストするメールを [HighGear サポート チーム](mailto:support@highgear.com)に送信します。 受け取った指示に従って、 **[HighGear Certificate]\(HighGear 証明書\)** フィールドと **[HighGear Certificate Password]\(HighGear 証明書パスワード\)** フィールドに入力します。

7. **[Save]\(保存\)** ボタンをクリックして、お客様の HighGear シングル サインオン構成を保存します。

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

このセクションでは、HighGear へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、**HighGear** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **HighGear** を選択します。

    ![アプリケーションの一覧の HighGear リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-highgear-test-user"></a>HighGear テスト ユーザーの作成

お客様のシングル サインオン構成をテストするための HighGear テスト ユーザーを作成するには、お客様の HighGear システムにログインします。

1. **[Create New Contact]\(新しい連絡先の作成\)** ボタンをクリックします。

    ![[Create New Contact]\(新しい連絡先の作成\) ボタン](media/highgear-tutorial/create-new-contact-button.png)

    お客様が作成したい連絡先の種類を選択できるメニューが表示されます。

2. **[Individual]\(個人\)** メニュー項目をクリックして、HighGear ユーザーを作成します。

    ウィンドウが右側にスライドして、新しいユーザーの情報を入力できるようになります。  
    ![新しい連絡先フォーム](media/highgear-tutorial/new-contact-form.png)

3. **[Name]\(名前\)** フィールドに、連絡先の名前を入力します。 次に例を示します。Britta Simon

4. **[More Options]\(その他のオプション\)** メニューをクリックし、 **[Account Info]\(アカウント情報\)** メニュー項目を選択します。

    ![[Account Info]\(アカウント情報\) メニュー項目をクリックする](media/highgear-tutorial/account-info-menu-item.png)

5. **[Can Log In]\(ログインできる\)** フィールドを [Yes]\(はい\) に設定します。

    **[Enable Single Sign-On]\(シングル サインオンを有効にする\)** フィールドも自動的に [Yes]\(はい\) に設定されます。

6. **[Single Sign-On User Id]\(シングル サインオン ユーザー ID\)** フィールドに、ユーザーの ID を入力します。 例: BrittaSimon@contoso.com

    [Account Info]\(アカウント情報\) セクションは次のようになります。  
    ![完成した [Account Info]\(アカウント情報\) セクション](media/highgear-tutorial/finished-account-info-section.png)

7. 連絡先を保存するために、ウィンドウの下部にある **[保存]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで HighGear タイルをクリックすると、お客様が SSO を設定した HighGear に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)