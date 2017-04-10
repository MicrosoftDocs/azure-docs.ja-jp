---
title: "チュートリアル: Azure Active Directory と Trello の統合 | Microsoft Docs"
description: "Azure Active Directory と Trello の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 36cc4206b81312f8f4ff287cd36d9dba56611fde


---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>チュートリアル: Azure Active Directory と Trello の統合
このチュートリアルでは、Trello と Azure Active Directory (Azure AD) を統合する方法について説明します。

Trello と Azure AD の統合には、次の利点があります:

* Trello にアクセスする Azure AD ユーザーを制御できます
* ユーザーが自分の Azure AD アカウントで自動的に Trello にサインオン (シングル サインオン) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Trello と Azure AD の統合を構成するには、次のものが必要です:

* Azure AD サブスクリプション
* **Trello** でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Trello の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-trello-from-the-gallery"></a>ギャラリーからの Trello の追加
Azure AD への Trello の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Trello を追加する必要があります。

**ギャラリーから Trello を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに、「 **Trello**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/tutorial_trello_01.png)

7. 結果ウィンドウで **[Trello]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/tutorial_trello_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Trello で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Trello ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Trello の関連ユーザーの間で、リンク関係が確立されている必要があります。
このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Trello の **[Username]** の値として割り当てます。 Trello で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Trello テスト ユーザーの作成](#creating-a-the-funding-portal-test-user)** - Trello で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Trello アプリケーションでシングル サインオンを構成することです。

Trello アプリケーションは SAML アサーションを使用し、特定の属性を含みます。 このアプリケーションには、次の属性を構成してください。 この属性の値は、アプリケーションの **[属性]** タブから管理できます。 次のスクリーンショットはその例です。

![[シングル サインオンの構成]](./media/active-directory-saas-trello-tutorial/tutorial_trello_03.png) 

**Trello で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Trello** アプリケーション統合ページで、上部のメニューの **[属性]** をクリックします。
   
    ![Configure Single Sign-On][5]

2. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。

    | 属性名 | 属性値 |
    | --- | --- |    
    | User.Email | User.mail |
    | User.FirstName | User.givenname |
    | User.LastName | User.surname |

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-trello-tutorial/tutorial_trello_05.png)

    b. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[属性値]** 一覧から、その行に対して表示される値を選択します。

    d. **[完了]** をクリックします。 ページの下部にある **[変更の適用]** をクリックします。

1. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]][6]

2. クラシック ポータルの **Trello** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][7] 

3. **[ユーザーの Trello へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-trello-tutorial/tutorial_trello_06.png)

4. **[アプリケーション設定の構成]** ダイアログ ページで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-trello-tutorial/tutorial_trello_07.png)

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [応答 URL] ボックスに、次の形式で URL を入力します: `https://trello.com/auth/saml/consume/<enterprise>`

    b. **[次へ]**をクリックします。

    > [!NOTE]
    > Trello から **\<enterprise\>** スラグを取得する必要があります。 スラグ値を取得していない場合は、 <mailto:support@trello.com> のTrello サポート チームにお問い合わせいただき、エンタープライズのスラグを取得してください。
    > 
    > 

1. **[アプリケーション設定の構成]** ダイアログ ページで、**SP 開始モード**でアプリケーションを構成する場合は、**[詳細設定を表示します (オプション)]** をクリックし、**サインオン URL** を入力します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-trello-tutorial/tutorial_trello_08.png)
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://trello.com/auth/saml/consume/<enterprise>` のパターンを使用して URL を入力します。
   
    b. **[次へ]** をクリックします。

2. **[Trello でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-trello-tutorial/tutorial_trello_09.png)

3. お使いのアプリケーション用に構成された SSO を取得するには、 [Trello Enterprise SSO 構成](https://trello.com/sso-configuration) ページに移動し、Trello のサポート チームにサイン オン URL を送付し、ダウンロードした認証書を添付します。
4. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]

5. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-trello-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部**[完了]**をクリックします。   

### <a name="creating-a-trello-test-user"></a>Trello テスト ユーザーの作成
このセクションでは、Trello で Britta Simon というユーザーを作成します。 このセクションでは、Trello で Britta Simon というユーザーを作成します。 Trello では、Just-In-Time プロビジョニングがサポートされており、Azure AD から初めてサイン インする時に新しいアカウントが作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Trello へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Trello に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Trello]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-trello-tutorial/tutorial_trello_10.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 

4. [すべてのユーザー] の一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Trello] タイルをクリックすると、自動的に Trello アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-trello-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-trello-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-trello-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-trello-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-trello-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trello-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trello-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


