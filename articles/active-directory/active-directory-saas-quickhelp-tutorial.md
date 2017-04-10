---
title: "チュートリアル: Azure Active Directory と QuickHelp の統合 | Microsoft Docs"
description: "Azure Active Directory と QuickHelp の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: f84a5a9e40f6dd1c98fd73ba38a1b5cae9d9cba4
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>チュートリアル: Azure Active Directory と QuickHelp の統合
このチュートリアルの目的は、QuickHelp と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
QuickHelp と Azure AD の統合には、次の利点があります。 

* QuickHelp にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に QuickHelp にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
QuickHelp と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* QuickHelp でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。  
このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの QuickHelp の追加 
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-quickhelp-from-the-gallery"></a>ギャラリーからの QuickHelp の追加
Azure AD への QuickHelp の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に QuickHelp を追加する必要があります。

**ギャラリーから QuickHelp を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **QuickHelp**」と入力します。
   
    ![アプリケーション][5]
7. 結果ウィンドウで **[QuickHelp]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、QuickHelp で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

QuickHelp で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[QuickHelp テスト ユーザーの作成](#creating-a-quickhelp-test-user)** - QuickHelp で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、QuickHelp アプリケーションでシングル サインオンを構成することです。

**QuickHelp で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **QuickHelp** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの QuickHelp へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][7] 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[アプリケーション設定の構成]][8] 
   
    a. **[サインオン URL]** ボックスに、ユーザーが QuickHelp サイトへのサインオンに使用する URL を入力します (例: * https://quickhelp.com/bsiazure/*)。
   
    > [!NOTE]
    > サインオン URL の値がわからない場合は、QuickHelp サポート チームに問い合わせてください。
    > 
    > 
   
    b. **[次へ]**をクリックします。
4. **[QuickHelp でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターでメタデータ ファイルをローカルに保存します。
   
    ![Azure AD Connect とは][9] 
5. QuickHelp 企業サイトに管理者としてサインオンします。
6. 上部のメニューで **[管理者]**をクリックします。
   
    ![[シングル サインオンの構成]][21]
7. **[QuickHelp の管理者]** メニューの **[設定]** をクリックします。
   
    ![[シングル サインオンの構成]][22]
8. **[認証設定]**をクリックします。
9. **[認証設定]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On][23]
   
    a. **SSO 型**として **WSFederation** を選びます。
   
    b. ダウンロードした Azure メタデータ ファイルをアップロードするには、**[参照]** をクリックし、目的のファイルに移動し、**[メタデータのアップロード]** をクリックします。
   
    c. **[Email]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** と入力します。
   
    d. **[First Name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** と入力します。
   
    e. **[Last Name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** と入力します。
   
    f. **[操作バー]** で、**[保存]** をクリックします。
10. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。 
    
     ![Azure AD Connect とは][10]
11. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
     ![Azure AD Connect とは][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  
ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
    e. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) 

1. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-quickhelp-test-user"></a>QuickHelp テスト ユーザーの作成
このセクションの目的は、QuickHelp で Britta Simon というユーザーを作成することです。
シングル サインオンを機能させるには、Azure AD ユーザーに対応する QuickHelp ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと QuickHelp の関連ユーザーの間で、リンク関係が確立されている必要があります。

QuickHelp では、ジャストインタイム プロビジョニングがサポートされています。 つまり、必要に応じて、ユーザー アカウントは、QuickHelp で自動的に作成され、Azure AD アカウントにリンクされます。

このセクションでは、ユーザー側で必要な操作はありません。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に QuickHelp へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**QuickHelp に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[QuickHelp]**を選択します。
   
    ![ユーザーの割り当て][202] 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [QuickHelp] タイルをクリックすると、自動的に QuickHelp アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png





