---
title: "チュートリアル: Azure Active Directory と @Task| Microsoft Docs の統合"
description: "Azure Active Directory と @Task の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: cba595704af8e48506a25d592770ec273cb9bd30


---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>チュートリアル: Azure Active Directory と @Task の統合
このチュートリアルの目的は、@Task と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
@Task と Azure AD の統合には、次の利点があります。 

* @Task にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで @Task に自動的にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
@Task, と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* @Task でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。  
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの @Task の追加 
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-task-from-the-gallery"></a>ギャラリーからの @Task の追加
Azure AD への @Task の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に @Task を追加する必要があります。

**ギャラリーから @Task を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1] 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2] 
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3] 
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4] 
6. 検索ボックスに、「**@Task**」と入力します。
   
    ![アプリケーション][5] 
7. 結果ウィンドウで **[@Task]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、@Task で Azure AD のシングル サインオンを構成し、テストする方法を説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する @Task ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと @Task の関連ユーザーの間で、リンク関係が確立されている必要があります。   
このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を @Task の **[Username (ユーザー名)]** の値として割り当てます。

@Task, で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[@Tasktest ユーザーの作成](#creating-a-halogen-software-test-user)** - @Taskthat でBritta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、@Task アプリケーションでシングル サインオンを構成することです。

**@Task, で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure クラシック ポータルの **@Task** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの @Task へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][7] 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[アプリケーション設定の構成]][8] 
   
     a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、ユーザーが @Task アプリケーションへのサインオンに使用する URL (例: *https://<Tenant name>.attask-ondemand.com*) を入力します。
   
     b. **[次へ]**をクリックします。
4. **[@Task でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターのローカルに保存して、**[次へ]** をクリックします。
   
    ![Azure AD Connect とは][9] 
5. @Task 企業サイトに管理者としてサインオンします。
6. **[Single Sign On Configuration]**に移動します。
7. **[Single Sign-On (シングル サインオン)]** ダイアログ ボックスで、次の手順を実行します。
   
    ![Configure Single Sign-On][23]
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[Type]** で **[SAML 2.0]** を選択します。
   
    b. **サービス プロバイダー ID** を選択します。
   
    c. Azure クラシック ポータルで、**[リモート ログイン URL]** の値をコピーし、**[Login Portal URL]** ボックスに貼り付けます。
   
    d. Azure クラシック ポータルで、**[シングル サインアウト サービス URL]** の値をコピーし、**[Sign-Out URL]** ボックスに貼り付けます。
   
    e. Azure クラシック ポータルで、**[パスワードの URL の変更]** の値をコピーし、**[Change Password URL]** ボックスに貼り付けます。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[保存]**をクリックします。
8. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。 
   
    ![Azure AD Connect とは][10]
9. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD Connect とは][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。

    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. **[完了]** をクリックします。   

### <a name="creating-an-task-test-user"></a>@Task テスト ユーザーの作成
このセクションの目的は、@Task で Britta Simon というユーザーを作成することです。

**@Task, で Britta Simon というユーザーを作成するには、次の手順を実行します。**

1. @Task 企業サイトに管理者としてサインオンします。
2. 上部のメニューで **[People]**をクリックします。
3. **[New Person]**をクリックします。 
4. [New Person] ダイアログで、次の手順を実行します。
   
    ![@Task テスト ユーザーを作成する][21] 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「Britta」と入力します。
   
    b. **[姓]** ボックスに「Simon」と入力します。
   
    c. **[Email Address]** ボックスに、Britta Simon の Azure Active Directory の電子メール アドレスを入力します。
   
    d. **[Add Person]**をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に @Task へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**@Task, に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[@Task]** 選択します。
   
    ![ユーザーの割り当て][202] 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで @Task のタイルをクリックすると、@Task アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png









<!--HONumber=Dec16_HO5-->


