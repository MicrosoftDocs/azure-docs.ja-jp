---
title: "チュートリアル: Azure Active Directory と Promapp の統合 | Microsoft Docs"
description: "Azure Active Directory と Promapp の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: ac73b5c2ece8044d9f75e017428c43259f8a9357


---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>チュートリアル: Azure Active Directory と Promapp の統合
このチュートリアルの目的は、Promapp と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
Promapp と Azure AD の統合には、次の利点があります。 

* Promapp にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に Promapp にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Active Directory クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Promapp と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Promapp でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーから Promapp を追加する 
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-promapp-from-the-gallery"></a>ギャラリーから Promapp を追加する
Azure AD への Promapp の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Promapp を追加する必要があります。

**ギャラリーから Promapp を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「 **Promapp**」と入力します。
   
    ![アプリケーション][5]
7. 結果ウィンドウで **[Promapp]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Promapp で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Promapp ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Promapp の関連ユーザーの間で、リンク関係が確立されている必要があります。  
このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Promapp の **[Username]** の値として割り当てます。

Promapp で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Promapp のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Promapp で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure AD クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Promapp アプリケーションでシングル サインオンを構成することです。

**Promapp で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure AD クラシック ポータルの **Promapp** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの Promapp へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][7] 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のシングル サインオン][8] 
   
   1. **[サインオン URL]** ボックスに、ユーザーが Promapp サイトへのサインオンに使用する URL (例: *https://companyname.promapp.com/instancename*) を入力します。
   2. **[次へ]**をクリックします。

1. **[Promapp でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Azure AD のシングル サインオン][9] 
   
   1. [証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。
   2. **[次へ]**をクリックします。
   
2. Promapp 企業サイトに管理者としてサインオンします。 
3. 上部のメニューで **[管理者]**をクリックします。 
   
    ![Azure AD Single Sign-On][12]
4. **[構成]**をクリックします。 
   
    ![Azure AD Single Sign-On][13]
5. **[Security]** ダイアログで、次の手順を実行します。
   
    ![Azure AD のシングル サインオン][14] 
   
   1. Azure クラシック ポータルの **[Promapp でのシングル サインオンの構成]** ダイアログで、**[リモート ログイン URL]** の値をコピーして、**[SSO-Login URL]** ボックスに貼り付け、**[Save]** をクリックします。
   2. **[SSO - Single Sign-on Mode]** として **[Optional]** を選択し、**[Save]** をクリックします。
   3. ダウンロードした証明書をメモ帳で開き、最初の行 (*-----BEGIN CERTIFICATE-----*) と最後の行 (*-----END CERTIFICATE-----*) を除く証明書の内容をコピーして、**[SSO-x.509 Certificate]** に貼り付け、**[Save]** をクリックします。
6. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] \(シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。 
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png)  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png)  
   
   1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   3. **[次へ]**をクリックします。
   
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png) 
   
   1. **[名]** ボックスに「**Britta**」と入力します。   
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
   
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png) 
   
   1. **[新しいパスワード]** の値を書き留めます。
   2. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-promapp-test-user"></a>Promapp のテスト ユーザーの作成
Promapp アプリケーションでは、ジャストインタイム プロビジョニングがサポートされています。
そのため、アクセス パネルを使用してアプリケーションにアクセスを試みると、必要に応じてユーザー アカウントが自動的に作成されます。  

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Promapp へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**Promapp に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Promapp]**を選択します。
   
    ![ユーザーの割り当て][202] 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [Promapp] タイルをクリックすると、自動的に Promapp アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png



<!--HONumber=Feb17_HO1-->


