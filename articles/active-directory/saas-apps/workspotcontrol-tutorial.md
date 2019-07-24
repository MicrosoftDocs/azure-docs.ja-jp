---
title: チュートリアル:Azure Active Directory と Workspot Control の統合 | Microsoft Docs
description: Azure Active Directory と Workspot Control の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97f375c6f48d3dc497eb59e76f19fc64cf906b56
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886509"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>チュートリアル:Azure Active Directory と Workspot Control の統合

このチュートリアルでは、Workspot Control と Azure Active Directory (Azure AD) を統合する方法について説明します。

Workspot Control と Azure AD の統合には、次の利点があります。

- Workspot Control にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Workspot Control にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Workspot Control の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workspot Control のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Workspot Control の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-workspot-control-from-the-gallery"></a>ギャラリーからの Workspot Control の追加
Azure AD への Workspot Control の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workspot Control を追加する必要があります。

**ギャラリーから Workspot Control を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. 検索ボックスに「**Workspot Control**」と入力し、結果ウィンドウで **[Workspot Control]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Workspot Control で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Workspot Control ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workspot Control の関連ユーザーの間で、リンク関係が確立されている必要があります。

Workspot Control で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Workspot Control テスト ユーザーの作成](#create-a-workspot-control-test-user)** - Workspot Control で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Workspot Control アプリケーションでシングル サインオンを構成します。

**Workspot Control で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. [Azure portal](https://portal.azure.com/) の **Workspot Control** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードを選択して、シングル サインオンを有効にします。

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. **[識別子]** ボックスに、`https://<INSTANCENAME>-saml.workspot.com/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<INSTANCENAME>-saml.workspot.com/saml/assertion` のパターンを使用して URL を入力します

    c. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    **[サインオン URL]** ボックスに、`https://<INSTANCENAME>-saml.workspot.com/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Workspot Control クライアント サポート チーム](mailto:support@workspot.com)に問い合わせてください。 

5. **[Set up Single Sign-On with SAML]\(SAML でのシングル サインオンの設定\)** ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[Download]\(ダウンロード\)** をクリックして**証明書 (Base64)** をダウンロードし、コンピューターに保存します。

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. **[Set up Workspot Control]\(Workspot Control の設定\)** セクションで、要件に従って適切な URL をコピーします。

    URL は次のように表示されている場合があることに注意してください。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. 異なる Web ブラウザー ウィンドウで、セキュリティ管理者として Workspot Control にログインします。

8. ページの上部にあるツール バーで、 **[Setup]\(セットアップ\)** をクリックし、 **[SAML]** に移動します。

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. **[Security Assertion Markup Language Configuration]\(Security Assertion Markup Language の構成\)** ページで、次の手順のようにします。
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. **[Entity ID]\(エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。   

    b. **[Signon Service URL]\(サインオン サービス URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. **[Logout Service URL]\(ログアウト サービス URL\)** テキスト ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。 

    d. **[Update File]\(ファイルの更新\)** ボタンをクリックして、Azure portal からダウンロードした base 64 でエンコードされた証明書を X.509 証明書にアップロードします。

    e. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順のようにします。

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-workspot-control-test-user"></a>Workspot Control のテスト ユーザーを作成する

Azure AD ユーザーが Workspot Control にログインできるようにするには、ユーザーを Workspot Control にプロビジョニングする必要があります。 Workspot Control では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Workspot Control にログインします。

2. ページの上部にあるツール バーで、 **[Users]\(ユーザー\)** をクリックし、 **[Add User]\(ユーザーの追加\)** に移動します。

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. **[Add a New User]\(新しいユーザーの追加\)** ページで、次の手順を実行します。

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (**Brittasimon\@contoso.com** など) を入力します。

    d. **[Role]\(ロール\)** ドロップダウンから適切なユーザー ロールを選択します。

    e. **[Group]\(グループ\)** ドロップダウンから適切なユーザー グループを選択します。

    f. **[ユーザーの追加]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Workspot Control へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[Workspot Control]** を選択します。

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Workspot Control] タイルをクリックすると、自動的に Workspot Control アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
