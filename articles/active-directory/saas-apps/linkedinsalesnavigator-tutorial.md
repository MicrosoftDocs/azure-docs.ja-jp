---
title: チュートリアル:Azure Active Directory と LinkedIn Sales Navigator の統合 | Microsoft Docs
description: Azure Active Directory と LinkedIn Sales Navigator の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38ce83df4d4de6d7816ee969a4fee153ad5457d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098008"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>チュートリアル:Azure Active Directory と LinkedIn Sales Navigator の統合

このチュートリアルでは、LinkedIn Sales Navigator と Azure Active Directory (Azure AD) を統合する方法について説明します。
LinkedIn Sales Navigator と Azure AD の統合には、次の利点があります。

* LinkedIn Sales Navigator にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで LinkedIn Sales Navigator に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

LinkedIn Sales Navigator と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* LinkedIn Sales Navigator でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LinkedIn Sales Navigator では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

* LinkedIn Sales Navigator では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

* LinkedIn Sales Navigator では、[**自動化された**ユーザー プロビジョニング](linkedinsalesnavigator-provisioning-tutorial.md)がサポートされます

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>ギャラリーからの LinkedIn Sales Navigator ナビゲーターの追加

Azure AD への LinkedIn Sales Navigator の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Sales Navigator を追加する必要があります。

**ギャラリーから LinkedIn Sales Navigator を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**LinkedIn Sales Navigator**」と入力し、結果ウィンドウで **[LinkedIn Sales Navigator]** を選んでから、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の LinkedIn Sales Navigator](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、LinkedIn Sales Navigator で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと LinkedIn Sales Navigator 内の関連ユーザー間にリンク関係が確立されている必要があります。

LinkedIn Sales Navigator で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[LinkedIn Sales Navigator のシングル サインオンの構成](#configure-linkedin-sales-navigator-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[LinkedIn Sales Navigator のテスト ユーザーの作成](#create-linkedin-sales-navigator-test-user)** - LinkedIn Sales Navigatorで Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

LinkedIn Sales Navigator で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **LinkedIn Sales Navigator** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[LinkedIn Sales Navigator のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、**エンティティ ID** の値を入力します。エンティティ ID の値は、このチュートリアルで後述する Linkedin Portal からコピーします。

    b. **[応答 URL]** ボックスに、**Assertion Consumer Access (ACS) URL** の値を入力します。Assertion Consumer Access (ACS) URL の値は、このチュートリアルで後述する LinkedIn Portal からコピーします。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[LinkedIn Sales Navigator のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator` という形式で URL を入力します。

6. LinkedIn Sales Navigator アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをSAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 LinkedIn Sales Navigator アプリケーションでは、nameidentifier が **user.mail** にマップされると想定されているため、[編集] アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

7. その他に、LinkedIn Sales Navigator アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの [ユーザー要求] セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name | ソース属性|
    | --- | --- |
    | email| User.mail |
    | department| user.department |
    | firstname| User.givenname |
    | lastname| User.surname |

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

9. **[LinkedIn Sales Navigator のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-linkedin-sales-navigator-single-sign-on"></a>LinkedIn Sales Navigator のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として **LinkedIn Sales Navigator** テナントにサインオンします。

1. **[Account Center (アカウント センター)]** で、 **[Settings (設定)]** の下の **[Global Settings (グローバル設定)]** をクリックします。 さらに、ドロップダウン リストから **[Sales Navigator]** を選択します。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. **[OR Click Here to load and copy individual fields from the form (または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする)]** をクリックして、次の手順を実行します。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. **[Entity ID]\(エンティティ ID\)** をコピーして、Azure portal の **[基本的な SAML 構成]** にある **[識別子]** ボックスに貼り付けます。

    b. **Assertion Consumer Access (ACS) URL** をコピーし、Azure portal で **[基本的な SAML 構成]** の **[応答 URL]** ボックスに貼り付けます。

1. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 **[Upload XML file]\(XML ファイルのアップロード\)** オプションをクリックして、Azure portal からダウンロードした XML ファイルをアップロードします。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. **[ON (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[Not Connected (未接続)]** から **[Connected (接続済み)]** に変更されます

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

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

このセクションでは、Britta Simon に LinkedIn Sales Navigator へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[LinkedIn Sales Navigator]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[LinkedIn Sales Navigator]** を選択します。

    ![アプリケーションの一覧の LinkedIn Sales Navigator のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-linkedin-sales-navigator-test-user"></a>LinkedIn Sales Navigator のテスト ユーザーの作成

Linked Sales Navigator アプリケーションでは、ジャストインタイム (JIT) ユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーション内に自動的に作成されます。 **[Automatically assign licenses (ライセンスを自動的に割り当てる)]** をアクティブ化して、ユーザーにライセンスを割り当てます。

   ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [LinkedIn Sales Navigator] タイルをクリックすると、SSO を設定した LinkedIn Sales Navigator に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

