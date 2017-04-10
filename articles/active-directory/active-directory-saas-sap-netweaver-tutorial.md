---
title: "チュートリアル: Azure Active Directory と SAP NetWeaver の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP NetWeaver の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: c7205f2a743bf2d835e9a1a5e6aad2542a7782ad


---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>チュートリアル: Azure Active Directory と SAP NetWeaver の統合
このチュートリアルでは、SAP NetWeaver と Azure Active Directory を統合する方法について説明します。

SAP NetWeaver と Azure AD の統合には、次の利点があります。

* SAP NetWeaver にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで SAP NetWeaver に自動的にサインオン (シングル サインオン) する機能を有効にすることができます。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と SAP NetWeaver の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* SAP NetWeaver でのシングル サインオンが有効なサブスクリプション

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

1. ギャラリーからの SAP NetWeaver の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sap-netweaver-from-the-gallery"></a>ギャラリーからの SAP NetWeaver の追加
Azure AD への SAP NetWeaver の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP NetWeaver を追加する必要があります。

**ギャラリーから SAP NetWeaver を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「 **SAP NetWeaver**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_01.png)
7. 結果ウィンドウで **[SAP NetWeaver]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP NetWeaver で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP NetWeaver ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP NetWeaver の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の**ユーザー名**の値を、SAP NetWeaver の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

SAP NetWeaver で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAP NetWeaver のテスト ユーザーの作成](#creating-a-sap-netweaver-test-user)** - SAP NetWeaver で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、SAP NetWeaver アプリケーションでシングル サインオンを構成します。

**SAP NetWeaver で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **SAP NetWeaver** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの SAP NetWeaver へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_04.png) 
   
    a. **[サインオン URL]** ボックスに、**https://\<your company instance of SAP NetWeaver\>** という形式で、ユーザーが SAP NetWeaver アプリケーションへのサインオンに使用する URL を入力します。
   
    b. **[識別子]** ボックスに、**https://\<your company instance of SAP NetWeaver\>** の形式で URL を入力します。
   
    c. **[応答 URL]** ボックスに、**https://\<your company instance of SAP NetWeaver\>/sap/saml2/sp/acs/100** の形式で URL を入力します。
   
    > [!NOTE]
    > これらの値はすべて、SAP NetWeaver パートナーによって提供されるフェデレーション メタデータ ドキュメントで取得できます。
    > 
    > 
   
    d. click **[次へ]**

4. **[SAP NetWeaver でのシングル サインオンの構成]** ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_05.png)
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. **[次へ]**をクリックします。
5. アプリケーション用に構成された SSO を入手するには、SAP NetWeaver パートナーに連絡し、次のものを情報として提供してください。
   
    • ダウンロードした **メタデータ**
   
    • **エンティティ ID**
   
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
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_05.png) 
   
    a. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. ページの下部にある **[次へ]**」を参照してください。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_06.png) 
   
    a. **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-an-sap-netweaver-test-user"></a>SAP NetWeaver テスト ユーザーの作成
このセクションでは、SAP NetWeaver で Britta Simon というユーザーを作成します。 SAP NetWeaver パートナーと協力して、SAP NetWeaver プラットフォームにユーザーを追加してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に SAP NetWeaver へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を SAP NetWeaver に割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[SAP NetWeaver]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAP NetWeaver] タイルをクリックすると、SAP NetWeaver アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


