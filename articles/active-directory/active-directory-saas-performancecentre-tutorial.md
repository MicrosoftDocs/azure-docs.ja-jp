---
title: "チュートリアル: Azure Active Directory と PerformanceCentre の統合 | Microsoft Docs"
description: "Azure Active Directory と PerformanceCentre の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: 801c0f7085034cc3a3ed74722a11204f6421c27d


---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>チュートリアル: Azure Active Directory と PerformanceCentre の統合
このチュートリアルの目的は、PerformanceCentre と Azure Active Directory (Azure AD) を統合する方法を説明することです。  

PerformanceCentre と Azure AD の統合には、次の利点があります。 

* PerformanceCentre にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に PerformanceCentre にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Active Directory クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
PerformanceCentre と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* PerformanceCentre でのシングル サインオンが有効なサブスクリプション

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

*  ギャラリーからの PerformanceCentre の追加 
*  Azure AD シングル サインオンの構成とテスト

## <a name="adding-performancecentre-from-the-gallery"></a>ギャラリーからの PerformanceCentre の追加
Azure AD への PerformanceCentre の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PerformanceCentre を追加する必要があります。

**ギャラリーから PerformanceCentre を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **PerformanceCentre**」と入力します。
   
    ![アプリケーション][5]
7. 結果ウィンドウで **[PerformanceCentre]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、PerformanceCentre で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する PerformanceCentre ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと PerformanceCentre の関連ユーザーの間で、リンクの関係が確立されている必要があります。  

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を PerformanceCentre の **[Username]** の値として割り当てます。

**PerformanceCentre で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。**

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[PerformanceCentre のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - PerformanceCentre で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure AD クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、PerformanceCentre アプリケーションでシングル サインオンを構成することです。

**PerformanceCentre で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD クラシック ポータルの **PerformanceCentre** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの PerformanceCentre へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][7] 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のシングル サインオン][8] 
   
   1. **[サインオン URL]** ボックスに、ユーザーが PerformanceCentre サイトへのサインオンに使用する URL を入力します (例: *http://companyname.performancecentre.com/saml/SSO*)。
   2. **[次へ]**をクリックします。
4. **[PerformanceCentre でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Azure AD のシングル サインオン][9] 
   
  * **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
5. **PerformanceCentre** 企業サイトに管理者としてサインオンします。
6. 左側にあるタブで、 **[Configure]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. 左側にあるタブで、**[Miscellaneous]**、**[Single Sign On]** の順にクリックします。
   
    ![Azure AD のシングル サインオン][11]
8. **[Protocol]** で **[SAML]** を選択します。
   
    ![Azure AD のシングル サインオン][12]
9. ダウンロードしたメタデータ ファイルをメモ帳で開き、ファイルの内容をコピーして、**[Identity Provider Metadata]** ボックスに貼り付け、**[Save]** をクリックします。
   
    ![Azure AD のシングル サインオン][13]
10. **[Entity Base URL]** と **[Entity ID URL]** の値が正しいことを確認します。
    
     ![Azure AD のシングル サインオン][14]
11. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] \(シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。 
    
     ![Azure AD のシングル サインオン][15]
12. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
     ![Azure AD のシングル サインオン][16]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  
   
   1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   3. **[次へ]**をクリックします。
   
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
   
   1. **[名]** ボックスに「**Britta**」と入力します。  
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
   
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
   
    1. **[新しいパスワード]** の値を書き留めます。
    2. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-performancecentre-test-user"></a>PerformanceCentre のテスト ユーザーの作成
このセクションの目的は、PerformanceCentre で Britta Simon というユーザーを作成することです。

**PerformanceCentre で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. PerformanceCentre 企業サイトに管理者としてサインオンします。
2. 左側のメニューで、**[Interrelate]**、**[Create Participant]** の順にクリックします。
   
    ![[Create User]][400]
3. **[Interrelate - Create Participant]** ダイアログ ボックスで、次の手順を実行します。
   
    ![[Create User]][401]
   
   1. 関連するテキスト ボックスに Britta Simon の必要な属性を入力します。
   > [!IMPORTANT]
   > PerformanceCentre での Britta の User Name 属性は、Azure AD でのユーザー名と同じにする必要があります。
   > 
   > 
 
   2. **[ロールの選択]** で **[クライアント管理者]** を選択します。
   3. **[保存]**をクリックします。   


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に PerformanceCentre へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**PerformanceCentre に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[PerformanceCentre]**を選択します。
   
    ![ユーザーの割り当て][202]
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  

アクセス パネルで PerformanceCentre のタイルをクリックすると、自動的に PerformanceCentre アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png






<!--HONumber=Feb17_HO1-->


