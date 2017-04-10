---
title: "チュートリアル: Azure Active Directory と Soonr Workplace の統合 | Microsoft Docs"
description: "Azure Active Directory と Soonr Workplace の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 181760d0bff399790143e7313be760e14a2e019e
ms.openlocfilehash: 4cba977d3bc302fdf7a85a71098043098ac6de12


---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>チュートリアル: Azure Active Directory と Soonr Workplace の統合

このチュートリアルの目的は、Soonr Workplace と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
Soonr Workplace と Azure AD の統合には、次の利点があります。

- Soonr Workplace にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Soonr Workplace にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Soonr Workplace の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Soonr Workplace でのシングル サインオンが有効なサブスクリプション


> [!NOTE] 
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。  
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Soonr Workplace の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-soonr-workplace-from-the-gallery"></a>ギャラリーからの Soonr Workplace の追加
Azure AD への Soonr Workplace の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Soonr Workplace を追加する必要があります。

**ギャラリーから Soonr Workplace を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
 
    ![アプリケーション][4]

6. 検索ボックスに、「 **Soonr Workplace**」と入力します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_01.png)

7. 結果ウィンドウで **[Soonr Workplace]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Soonr Workplace で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Soonr Workplace ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Soonr Workplace の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Soonr Workplace の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

Soonr Workplace で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Soonr Workplace テスト ユーザーの作成](#creating-a-soonr-workplace-test-user)** - Soonr Workplace で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Soonr Workplace アプリケーションでシングル サインオンを構成します。


**Soonr Workplace で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Soonr Workplace** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On][6] 

2. **[ユーザーの Soonr Workplace へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_03.png) 

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_04.png) 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<server-name>.soonr.com/singlesignon/saml/SSO` のパターンを使用して URL を入力します。

    b. **[次へ]**をクリックします。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値は実際のサインオン URL で更新する必要があります。 この値を取得するには、Soonr Workplace サポート チームに問い合わせてください。

4. **[Soonr Workplace でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_05.png) 

5. アプリケーション用に構成された SSO を入手するには、Soonr Workplace サポート チームに連絡し、次のものを情報として提供してください。 

    •  ダウンロードした**メタデータ** ファイル

    •  **発行者の URL**

    •  **SAML SSO URL**

    •  **シングル サインアウト サービス URL**

    >[!NOTE]
    >このアプリケーションは <a href="https://azure.microsoft.com/en-us/marketplace/partners/autotask-corporataion/autotask/">Autotask Workplace</a> に置き換わるため、Azure AD でアプリケーションを構成する場合は、<a href="https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-autotaskworkplace-tutorial">この</a>チュートリアルを参照してください。
   
6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。

    ![Azure AD のシングル サインオン][10]

7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
  
    ![Azure AD のシングル サインオン][11]



### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/create_aaduser_05.png) 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。

    c. **[次へ]**をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/create_aaduser_06.png) 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。

    d. **[ロール]** 一覧で **[ユーザー]** を選択します。

    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-soonr-tutorial/create_aaduser_08.png) 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。

    b. ページの下部にある **[完了]**」を参照してください。   



### <a name="creating-a-soonr-workplace-test-user"></a>Soonr Workplace テスト ユーザーの作成

このセクションの目的は、Soonr Workplace で Britta Simon というユーザーを作成することです。 Soonr Workplace サポート チームと協力して、プラットフォームにユーザーを作成します。 Soonr へのサポート チケットは<a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">こちら</a>から上げることができます。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Soonr Workplace へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**Britta Simon を Soonr Workplace に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **Soonr Workplace**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_50.png) 

1. 上部のメニューで **[ユーザー]**をクリックします。

    ![ユーザーの割り当て][203] 

1. ユーザーの一覧で **[Britta Simon]**を選択します。

2. 下部にあるツール バーで **[割り当て]**をクリックします。

    ![ユーザーの割り当て][205]



### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [Soonr Workplace] タイルをクリックすると、自動的に Soonr Workplace アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


