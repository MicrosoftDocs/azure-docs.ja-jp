---
title: チュートリアル:Azure Active Directory と Adaptive Insights の統合 | Microsoft Docs
description: Azure Active Directory と Adaptive Insights の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42c86ec262cd9d3d3db3035d252429e44c1208f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884882"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>チュートリアル:Azure Active Directory と Adaptive Insights の統合

このチュートリアルでは、Adaptive Insights と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adaptive Insights と Azure AD の統合には、次の利点があります。

- Adaptive Insights にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで Adaptive Insights に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Adaptive Insights と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adaptive Insights でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Adaptive Insights の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adaptive-insights-from-the-gallery"></a>ギャラリーからの Adaptive Insights の追加
Azure AD への Adaptive Insights の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Adaptive Insights を追加する必要があります。

**ギャラリーから Adaptive Insights を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. 検索ボックスに「**Adaptive Insights**」と入力し、結果パネルで **Adaptive Insights** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Adaptive Insights で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Adaptive Insights ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Adaptive Insights の関連ユーザーの間で、リンク関係が確立されている必要があります。

Adaptive Insights で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Adaptive Insights テスト ユーザーの作成](#create-an-adaptive-insights-test-user)** - Adaptive Insights で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にし、Adaptive Insights アプリケーションでシングル サインオンを構成します。

**Adaptive Insights で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. [Azure portal](https://portal.azure.com/) の **Adaptive Insights** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードを選択して、シングル サインオンを有効にします。

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. **[応答 URL]** ボックスに、`https://login.adaptiveinsights.com:443/samlsso/<unique-id>` のパターンを使用して URL を入力します。

    >[!NOTE]
    > [識別子 (エンティティ ID)] と [応答 URL] の値は、Adaptive Insights の **[SAML SSO 設定]** ページから取得できます。
 
5. **[Set up Single Sign-On with SAML]\(SAML でのシングル サインオンの設定\)** ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[Download]\(ダウンロード\)** をクリックして**証明書 (Base64)** をダウンロードし、コンピューターに保存します。

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. **[Set up Adaptive Insights]\(Adaptive Insights の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    URL は次のように表示されている場合があることに注意してください。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. 別の Web ブラウザー ウィンドウで、Adaptive Insights 企業サイトに管理者としてログインします。

8. **[Admin]** に移動します。

    ![管理](./media/adaptivesuite-tutorial/IC805644.png "Admin")

9. **[ユーザーとロール]** セクションで、**[SAML SSO 設定の管理]** をクリックします。

    ![SAML SSO 設定の管理](./media/adaptivesuite-tutorial/IC805645.png "Manage SAML SSO Settings")

10. **[SAML SSO 設定]** ページで、次の手順を実行します。

    ![SAML SSO 設定](./media/adaptivesuite-tutorial/IC805646.png "SAML SSO Settings")

    a. **[ID プロバイダー名]** テキスト ボックスに、構成の名前を入力します。

    b. Azure portal からコピーした **Azure AD 識別子**の値を **[Identity provider entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに貼り付けます。

    c. Azure portal からコピーした**ログイン URL** の値を **[Identity provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに貼り付けます。

    d. Azure portal からコピーした**ログアウト URL** の値を **[Custom logout URL]\(カスタム ログアウト URL\)** ボックスに貼り付けます。

    e. ダウンロードした証明書をアップロードするには、 **[ファイルの選択]** をクリックします。

    f. 次のように選択します。

    * **[SAML ユーザー ID]** では、**[ユーザーの Adaptive Insights ユーザー名]** を選択します。

    * **[SAML ユーザー ID の場所]** では、**[サブジェクトの NameID 内のユーザー ID ]** を選択します。

    * **[SAML NameID 形式]** では、**[電子メール アドレス]** を選択します。

    * **[SAML を有効にする]** では、**[SAML SSO を許可して、Adaptive Insights に直接ログインする]** を選択します。

    g. **Adaptive Insights の SSO URL** をコピーし、Azure portal の **[Adaptive Insights のドメインと URL]** セクションの **[識別子 (エンティティ ID)]** ボックスと **[応答 URL]** ボックスに貼り付けます。

    h. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順のようにします。

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-an-adaptive-insights-test-user"></a>Adaptive Insights テスト ユーザーの作成

Azure AD ユーザーが Adaptive Insights にログインできるようにするには、ユーザーを Adaptive Insights にプロビジョニングする必要があります。 Adaptive Insights の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。** 

1. **Adaptive Insights** 企業サイトに管理者としてログインします。
2. **[Admin]** に移動します。

   ![管理者](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. **[ユーザーとロール]** セクションで **[ユーザーの追加]** をクリックします。

   ![ユーザーの追加](./media/adaptivesuite-tutorial/IC805648.png "Add User")
   
4. **[新しいユーザー]** セクションで、次の手順に従います。

   ![送信](./media/adaptivesuite-tutorial/IC805649.png "Submit")

   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの **[名前]**、**[ログイン]**、**[電子メール]**、**[パスワード]** を入力します。

   b. **[ロール]** を選択します。

   c. **[送信]** をクリックします。

>[!NOTE]
>他の Adaptive Insights ユーザー アカウント作成ツールまたは Adaptive Insights から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adaptive Insights へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[Adaptive Insights]** を選択します。

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adaptive Insights] タイルをクリックすると、自動的に Adaptive Insights アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
