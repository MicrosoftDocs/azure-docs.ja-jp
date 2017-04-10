---
title: "チュートリアル: Azure Active Directory と Image Relay の統合 | Microsoft Docs"
description: "Azure Active Directory と Image Relay の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a14be44b66c5e61350785ddfaf5b96125a196103
ms.openlocfilehash: 94fdae2992e69f4330d325968a9e66b3f6f38ce0


---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>チュートリアル: Azure Active Directory と Image Relay の統合
このチュートリアルの目的は、Image Relay と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
Image Relay と Azure AD の統合には、次の利点があります。

* Image Relay にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に ImageRelay にサインオンする機能 (シングル サインオン) を有効にできます。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Image Relay の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Image Relay でのシングル サインオンが有効なサブスクリプション

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

1. ギャラリーからの Image Relay の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-image-relay-from-the-gallery"></a>ギャラリーからの Image Relay の追加
Azure AD への Image Relay の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Image Relay を追加する必要があります。

**ギャラリーから ImageRelay を追加するには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。 
   
    ![[Active Directory]][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「**Image Relay**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)
7. 結果ウィンドウで **[Image Relay]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、ImageRelay で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD で Image Relay の関連ユーザーを表すユーザー アカウントが必要です。  つまり、Azure AD ユーザーと Image Relay の関連ユーザーの間でリンク関係が確立されている必要があるということです。  
このリンク関係は、Azure AD の **[ユーザー名]** の値を、Image Relay の **[Username]** の値として割り当てることで確立します。

Image Relay で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Image Relay テスト ユーザーの作成](#creating-a-userecho-test-user)** - Image Relay で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Image Relay アプリケーションでシングル サインオンを構成することです。

**Image Relay で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Image Relay** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
     ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Image Relay へのアクセスを設定してください]** ページで、**[Azure AD Single Sign-On] \(Azure AD のシングル サインオン)** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
     ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 
   
    a. **[サインオン URL]** ボックスに、ユーザーが Image Relay アプリケーションへのサインオンに使用する URL を入力します (例: *https://fabrikam.ImageRelay.com/*)。
   
    b. **[次へ]**をクリックします。
4. **[Image Relay でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 
   
    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. **[次へ]**をクリックします。
5. 別のブラウザー ウィンドウで、管理者として Image Relay 企業サイトにサインインします。
6. 上部にあるツール バーで **[Users & Permissions]** ワークロードをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 
7. **[Create New Permission (新しいアクセス許可を作成)]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 
8. **[Single Sign On Settings]** ワークロードで、**[This Group can only sign-in via Single Sign On]** チェックボックスをオンにして **[Save]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 
9. **[Account Settings (アカウントの設定)]**に移動します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 
10. **[Single Sign On Settings (シングル サインオンの設定)]** ワークロードに移動します。
    
     ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)
11. **[SAML Settings (SAML の設定)]** ダイアログで、次の手順を実行します。
    
    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. Azure クラシック ポータルで **[シングル サインオン サービス URL]** の値をコピーし、**[Login URL]** ボックスに貼り付けます。

    b. Azure クラシック ポータルの **[シングル サインアウト サービス URL]** をコピーし、**[Logout URL]** ボックスに貼り付けます。

    c. **[Name Id Format]** として **[urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress]** を選択します。

    d. **[Binding Options for Requests from the Service Provider (Image Relay)]** で **[POST Binding]** を選択します。

    e. **[x.509 Certificate]** の下にある **[Update Certificate]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、[x.509 Certificate (x.509 証明書)] ボックスに貼り付けます。

    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. **[Just-In-Time User Provisioning]** で、**[Enable Just-In-Time User Provisioning]** をオンにします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. シングル サインオンによるサインインのみを許可するアクセス許可グループを選択します (**[SSO Basic]** など)。

    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. **[Save]**をクリックします。

1. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
2. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-an-image-relay-test-user"></a>Image Relay の テスト ユーザーの作成
このセクションの目的は、Image Relay で Britta Simon というユーザーを作成することです。

**Image Relay で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Image Relay 企業サイトに管理者としてログインします。
2. **[Users & Permissions]** に移動して **[Create SSO User]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 
3. プロビジョニングするユーザーの **[Email]**、**[First Name]**、**[Last Name]**、**[Company]** を入力し、シングル サインオンのみでサインインできるアクセス許可グループ ([SSO Basic] など) を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 
4. **[作成]**をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Image Relay へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**Image Relay に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Image Relay]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [Image Relay] タイルをクリックすると、自動的に Image Relay アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


