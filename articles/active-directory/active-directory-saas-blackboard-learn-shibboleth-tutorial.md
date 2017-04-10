---
title: "チュートリアル: Azure Active Directory と Blackboard Learn - Shibboleth の統合 | Microsoft Docs"
description: "Azure Active Directory と Blackboard Learn - Shibboleth の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: e435cbb4-c0f0-400e-943c-5c923fa8ddf2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 0fd15c1d01959194ff25ac7f93c015a8edd38057


---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>チュートリアル: Azure Active Directory と Blackboard Learn - Shibboleth との統合
このチュートリアルでは、Blackboard Learn - Shibboleth を Azure Active Directory (Azure AD) と統合する方法について説明します。

Blackboard Learn - Shibboleth と Azure AD の統合には、次の利点があります。

* Blackboard Learn - Shibboleth にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Blackboard Learn - Shibboleth にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Blackboard Learn - Shibboleth と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Blackboard Learn - Shibboleth でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Blackboard Learn - Shibboleth の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-blackboard-learn---shibboleth-from-the-gallery"></a>ギャラリーからの Blackboard Learn - Shibboleth の追加
Azure AD への Blackboard Learn - Shibboleth の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Blackboard Learn - Shibboleth を追加する必要があります。

**ギャラリーから Blackboard Learn - Shibboleth を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに、「 **Blackboard Learn - Shibboleth**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_01.png)

7. 結果ウィンドウで **[Blackboard Learn - Shibboleth]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Blackboard Learn - Shibboleth で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Blackboard Learn - Shibboleth ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Blackboard Learn - Shibboleth の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Blackboard Learn - Shibboleth の **[Username]** の値として割り当てます。

Blackboard Learn - Shibboleth で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Blackboard Learn - Shibboleth のテスト ユーザーの作成](#creating-a-blackboard-learn-shibboleth-test-user)** - Blackboard Learn - Shibboleth で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Blackboard Learn - Shibboleth アプリケーションでシングル サインオンを構成します。

**Blackboard Learn - Shibboleth で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Blackboard Learn - Shibboleth** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 

2. **[ユーザーの Blackboard Learn - Shibboleth へのアクセスを設定してください]** ページで、**[Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_03.png) 

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_04.png) 
   
    a. **[サインオン URL]** ボックスに、ユーザーが Blackboard Learn - Shibboleth アプリケーションへのサインオンに使用する URL を入力します。その際、**https://\<yourblackoardlearnserver\>.blackboardlearn.com/Shibboleth.sso/Login** の形式を使用します。
   
    b. **[識別子]** ボックスに、**https://\<yourblackoardlearnserver\>.blackboardlearn.com/shibboleth-sp** の形式で URL を入力します。
   
    c. **[応答 URL]** ボックスに、**https://\<yourblackoardlearnserver\>.blackboardlearn.com/Shibboleth.sso/SAML2/POST** の形式で URL を入力します。
   
    > [!NOTE]
    > これらの値はすべて、Blackboard Learn パートナーによって提供されるフェデレーション メタデータ ドキュメントで取得できます。
    > 
    > 
   
    d. click **[次へ]**

4. **[Blackboard Learn - Shibboleth でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_05.png)
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. **[次へ]**をクリックします。

5. アプリケーション用に構成された SSO を入手するには、Blackboard Learn - Shibboleth パートナーに連絡し、次のものを情報として提供してください。
   
    • ダウンロードした **メタデータ**
   
    • **発行者の URL**
   
    • **SAML SSO URL**
   
    • **シングル サインアウト サービス URL**

6. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]

7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. ページの下部にある **[次へ]**」を参照してください。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-an-blackboard-learn---shibboleth-test-user"></a>Blackboard Learn - Shibboleth のテスト ユーザーの作成
このセクションでは、Blackboard Learn - Shibboleth で Britta Simon というユーザーを作成します。 Blackboard Learn - Shibboleth プラットフォームにユーザーを追加する場合は、Blackboard Learn パートナーと協力して作業してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Blackboard Learn - Shibboleth へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Blackboard Learn - Shibboleth に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Blackboard Learn - Shibboleth]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Blackboard Learn - Shibboleth] タイルをクリックすると、自動的に Blackboard Learn - Shibboleth アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO5-->


