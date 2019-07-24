---
title: チュートリアル:Azure Active Directory と LinkedIn Learning の統合 | Microsoft Docs
description: Azure Active Directory と LinkedIn Learning の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3717ed095c162255b0c0cdf09cac13fe2033979a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57905003"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>チュートリアル:Azure Active Directory と LinkedIn Learning の統合

このチュートリアルでは、LinkedIn Learning と Azure Active Directory (Azure AD) を統合する方法について説明します。
LinkedIn Learning と Azure AD の統合には、次の利点があります。

* LinkedIn Learning にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に LinkedIn Learning にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

LinkedIn Learning と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* LinkedIn Learning でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LinkedIn Learning では、**SP と IDP** によって開始される SSO がサポートされます
* LinkedIn Learning では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-linkedin-learning-from-the-gallery"></a>ギャラリーからの LinkedIn Learning の追加

Azure AD への LinkedIn Learning の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Learning を追加する必要があります。

**ギャラリーから LinkedIn Learning を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**LinkedIn Learning**」と入力し、結果ウィンドウで **[LinkedIn Learning]** を選んでから、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の LinkedIn Learning](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、LinkedIn Learning で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと LinkedIn Learning 内の関連ユーザー間にリンク関係が確立されている必要があります。

LinkedIn Learning で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[LinkedIn Learning シングル サインオンの構成](#configure-linkedin-learning-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[LinkedIn Learning のテスト ユーザーの作成](#create-linkedin-learning-test-user)** - LinkedIn Learning で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

LinkedIn Learning で Azure AD のシングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **LinkedIn Learning** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IdP 開始**モードで構成する場合は、次の手順を実行します。

    ![[LinkedIn Learning のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、LinkedIn ポータルからコピーした**エンティティ ID** を入力します。 

    b. **[応答 URL]** ボックスに、LinkedIn ポータルからコピーした **Assertion Consumer Service (ACS) URL** を入力します。

    c. アプリケーションを **SP 開始**モードで構成する場合は、サインオン URL を指定する **[基本的な SAML 構成]** セクションで **[追加の URL を設定します]** オプションをクリックします。 ログイン URL を作成するには、**Assertion Consumer Service (ACS) URL** をコピーして、/saml/ を /login/ で置き換えます。 これが終了すると、サインオン URL は次のパターンになります。

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![[LinkedIn Learning のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > これらの値は実際の値ではありません。 これらの値は、実際の識別子と応答 URL に更新します。これについては、このチュートリアルの「**LinkedIn Learning シングル サインオンの構成**」セクションで後述します。

5. LinkedIn Learning アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをSAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 LinkedIn Learning アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、**[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

6. その他に、LinkedIn Learning アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。
    
    | Name | ソース属性 |
    | ---------------| --------------- |
    | email  | User.mail  |
    | department  | user.department  |
    | firstname  | User.givenname  |
    | lastname  | User.surname  |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="configure-linkedin-learning-single-sign-on"></a>LinkedIn Learning シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として LinkedIn Learning テナントにサインオンします。

2. **[アカウント センター]** で、**[設定]** の下の **[グローバル設定]** をクリックします。 また、ドロップダウン リストから **[Learning - Default (ラーニング - 既定)]** を選択します。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. **[OR Click Here to load and copy individual fields from the form]\(または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする\)** をクリックし、**[エンティティ ID]** と **[Assertion Consumer Service (ACS) URL]** をコピーして、Azure portal の **[基本的な SAML 構成]** セクションに貼り付けます。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 **[Upload XML file (XML ファイルのアップロード)]** オプションをクリックして、Azure ポータルからダウンロードした XML ファイルをアップロードします。

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. **[ON (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[Not Connected (未接続)]** から **[Connected (接続済み)]** に変更されます

    ![Configure single sign-on](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LinkedIn Learning へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[LinkedIn Learning]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**LinkedIn Learning**」と入力して選択します。

    ![アプリケーションの一覧の LinkedIn Learning のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-linkedin-learning-test-user"></a>LinkedIn Learning のテスト ユーザーの作成

LinkedIn Learning アプリケーションでは、ジャストインタイムのユーザーのプロビジョニングがサポートされ、認証後に、ユーザーが自動的に、アプリケーションに作成されます。 LinkedIn Learning ポータルの管理者設定ページで、スイッチ **[Automatically Assign licenses (ライセンスを自動的に割り当てる)]** を切り替えて、ジャストインタイム プロビジョニングを有効にします。これにより、ユーザーにライセンスも割り当てられます。

   ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [LinkedIn Learning] タイルをクリックすると、SSO を設定した LinkedIn Learning に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

