---
title: "チュートリアル: Azure Active Directory と SciQuest Spend Director の統合 | Microsoft Docs"
description: "Azure Active Directory と SciQuest Spend Director の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: b2eb87a88721a3fd1ecdfa573c6a6d49ed02d344
ms.openlocfilehash: 84b707668dc45e92e6151f422f1c919f638533b1
ms.lasthandoff: 02/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>チュートリアル: Azure Active Directory と SciQuest Spend Director の統合
このチュートリアルの目的は、SciQuest Spend Director と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
SciQuest Spend Director と Azure AD の統合には、次の利点があります。 

* SciQuest Spend Director にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に SciQuest Spend Director にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
SciQuest Spend Director と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* SciQuest Spend Director でのシングル サインオンが有効なサブスクリプション

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

1. ギャラリーからの SciQuest Spend Director の追加 
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>ギャラリーからの SciQuest Spend Director の追加
Azure AD への SciQuest Spend Director の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SciQuest Spend Director を追加する必要があります。

**ギャラリーから SciQuest Spend Director を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに「 **sciQuest spend director**」と入力します。
   
    ![アプリケーション][5]

7. 結果ウィンドウで **[SciQuest Spend Director]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、SciQuest Spend Director で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SciQuest Spend Director ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SciQuest Spend Director の関連ユーザーの間で、リンクの関係が確立されている必要があります。  
このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、SciQuest Spend Director の **[Username]** の値として割り当てます。

SciQuest Spend Director で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[単一の Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SciQuest Spend Director のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - SciQuest Spend Director で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-single-sign-on"></a>単一の Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、SciQuest Spend Director アプリケーションでシングル サインオンを構成することです。

**SciQuest Spend Director で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **SciQuest Spend Director** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][8]

2. **[ユーザーの SciQuest Spend Director へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][9]

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。 
   
    ![[アプリケーション設定の構成]][10]
   
     a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、次のパターンを使用して、ユーザーが SciQuest Spend Director アプリケーションへのサインオンに使用する URL を入力します。*https://.*sciquest.com/**。
   
     b. **[応答 URL]** ボックスに、**[サインオン URL]** ボックスに入力したのと同じ値を入力します。 
   
     c. **[次へ]**をクリックします。

4. **[SciQuest Spend Director でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。
   
    ![What is Azure AD Connect][11]

5. 上記のダウンロードしたメタデータを使用してこの認証方法を有効にする方法については、SciQuest のサポートにお問い合わせください。

6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。 
   
    ![Azure AD Connect とは][15]

7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD Connect とは][100] 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD Connect とは][101] 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD Connect とは][102] 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![What is Azure AD Connect][103] 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
    ![What is Azure AD Connect][104] 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD Connect とは][105]  

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![What is Azure AD Connect][106]   
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-sciquest-spend-director-test-user"></a>SciQuest Spend Director のテスト ユーザーの作成
このセクションの目的は、SciQuest Spend Director で Britta Simon というユーザーを作成することです。

テスト アカウントを作成するには、SciQuest Spend Director のサポート チームに連絡し、テスト アカウントの詳細を伝える必要があります。

または、SciQuest Spend Director でサポートされているシングル サインオン機能であるジャストインタイム プロビジョニングを使用することもできます。  
ジャストインタイム プロビジョニングが有効な場合、ユーザーが存在しなければ、シングル サインオンの試行中に SciQuest Spend Director によりユーザーが自動的に作成されます。 この機能により、対応するシングル サインオン ユーザーを手動で作成する必要がなくなります。

ジャストインタイム プロビジョニングを有効にするには、SciQuest Spend Director のサポート チームに連絡する必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に SciQuest Spend Director へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![Azure AD Connect とは][200]

**SciQuest Spend Director に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルのディレクトリ ビューで、トップ メニューにある **[アプリケーション]** をクリックしてアプリケーション ビューを開きます。
   
    ![Azure AD Connect とは][201]

2. アプリケーションの一覧で **[SciQuest Spend Director]**を選択します。
   
    ![Azure AD Connect とは][202]

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![Azure AD Connect とは][203]

4. ユーザーの一覧で **[Britta Simon]**を選択します。
   
    ![Azure AD Connect とは][204]

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![Azure AD Connect とは][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで SciQuest Spend Director のタイルをクリックすると、自動的に SciQuest Spend Director アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_20.png


