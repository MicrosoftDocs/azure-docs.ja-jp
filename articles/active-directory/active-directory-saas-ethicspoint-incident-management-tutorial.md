---
title: "チュートリアル: Azure Active Directory と EthicsPoint Incident Management (EPIM) の統合 | Microsoft Docs"
description: "Azure Active Directory と EthicsPoint Incident Management (EPIM) の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8cb31a4c-9309-469b-93ac-daf0d3c7a3e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 33a5e085722fd9accf70793a580b5c8a5dc124c4
ms.openlocfilehash: 83cfbd6a4297577dc5284982fcc5d0c1318fbf5c


---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>チュートリアル: Azure Active Directory と EthicsPoint Incident Management (EPIM) の統合

このチュートリアルでは、EthicsPoint Incident Management (EPIM) と Azure Active Directory (Azure AD) を統合する方法について説明します。

EthicsPoint Incident Management (EPIM) と Azure AD の統合には、次の利点があります。

- EthicsPoint Incident Management (EPIM) にアクセスするユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に EthicsPoint Incident Management (EPIM) にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

EthicsPoint Incident Management (EPIM) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- EthicsPoint Incident Management (EPIM) でのシングル サインオンが有効なサブスクリプション


>[!NOTE] 
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの EthicsPoint Incident Management (EPIM) の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>ギャラリーからの EthicsPoint Incident Management (EPIM) の追加
Azure AD への EthicsPoint Incident Management (EPIM) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に EthicsPoint Incident Management (EPIM) を追加する必要があります。

**ギャラリーから EthicsPoint Incident Management (EPIM) を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに、「**EthicsPoint Incident Management**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_01.png)

7. 結果ウィンドウで **[EthicsPoint Incident Management]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、EthicsPoint Incident Management (EPIM) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する EthicsPoint Incident Management (EPIM) ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと EthicsPoint Incident Management (EPIM) の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を EthicsPoint Incident Management (EPIM) の **[Username]** の値として割り当てます。

EthicsPoint Incident Management (EPIM) で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[EthicsPoint Incident Management (EPIM) のテスト ユーザーの作成](#creating-a-ethicspoint-incident-management-test-user)** - EthicsPoint Incident Management (EPIM) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、EthicsPoint Incident Management (EPIM) アプリケーションでシングル サインオンを構成します。


**EthicsPoint Incident Management (EPIM) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. クラシック ポータルの **EthicsPoint Incident Management (EPIM)** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
     
    ![Configure Single Sign-On][6] 

2. **[ユーザーの EthicsPoint Incident Management (EPIM) へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_03.png) 

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_04.png) 

    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[サインオン URL]** ボックスに、`https://<companyname>.navexglobal.com` または `https://<companyname>.ethicspointvp.com` のパターンを使用して、ユーザーが EthicsPoint Incident Management (EPIM) アプリケーションへのサインオンに使用する URL を入力します。
    
    b. **[応答 URL]** ボックスに、`https://<servername>.navexglobal.com/adfs/ls/` のパターンを使用して URL を入力します。

    c. **[次へ]**
 
4. **[EthicsPoint Incident Management (EPIM) でのシングル サインオンの構成]** ページで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_05.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b. **[次へ]**をクリックします。


5. アプリケーション用に構成された SSO を入手するには、EthicsPoint Incident Management (EPIM) サポート チームに連絡して、ダウンロードした**メタデータ** ファイルを提供してください。

6. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]**をクリックします。
    
    ![Azure AD のシングル サインオン][10]

7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
 
    ![Azure AD のシングル サインオン][11]


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。


![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_05.png) 

    a. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。

    c. ページの下部にある **[次へ]**」を参照してください。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_06.png) 

    a. **[名]** ボックスに「**Britta**」と入力します。  

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。

    d. **[ロール]** 一覧で **[ユーザー]** を選択します。

    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-ethicspoint-incident-management-tutorial/create_aaduser_08.png) 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。

    b. ページの下部にある **[完了]**」を参照してください。   



### <a name="creating-an-ethicspoint-incident-management-epim-test-user"></a>EthicsPoint Incident Management (EPIM) のテスト ユーザーの作成

このセクションでは、EthicsPoint Incident Management (EPIM) で Britta Simon というユーザーを作成します。 EthicsPoint Incident Management (EPIM) サポート チームと連携して、EthicsPoint Incident Management (EPIM) プラットフォームでユーザーを追加してください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に EthicsPoint Incident Management (EPIM) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を EthicsPoint Incident Management (EPIM) に割り当てるには、次の手順を実行します。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[EthicsPoint Incident Management (EPIM)]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_ethicspointincidentmanagement_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。

    ![ユーザーの割り当て][203]

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。

    ![ユーザーの割り当て][205]


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [EthicsPoint Incident Management (EPIM)] タイルをクリックすると、自動的に EthicsPoint Incident Management (EPIM) アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ethicspoint-incident-management-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


