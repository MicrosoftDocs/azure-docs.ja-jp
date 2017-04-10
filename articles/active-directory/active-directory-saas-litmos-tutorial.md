---
title: "チュートリアル: Azure Active Directory と Litmos の統合 | Microsoft Docs"
description: "Azure Active Directory と Litmos の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: 84cd0c3eb2753a209d0aebda405f0b98a487140d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>チュートリアル: Azure Active Directory と Litmos の統合
このチュートリアルの目的は、Litmos と Azure Active Directory (Azure AD) を統合する方法を説明することです。  

Litmos と Azure AD の統合には、次の利点があります。 

* Litmos にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に Litmos にシングル サインオン (SSO) できるようにします。
* 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。 

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Litmos と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Litmos でのシングル サインオンが有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。  

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Litmos の追加 
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-litmos-from-the-gallery"></a>ギャラリーからの Litmos の追加
Azure AD への Litmos の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Litmos を追加する必要があります。

**ギャラリーから Litmos を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Litmos**」と入力します。
   
    ![アプリケーション][5]
7. 結果ウィンドウで **[Litmos]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Litmos で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Litmos ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Litmos の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Litmos の **[Username]** の値として割り当てることで確立されます。

Litmos で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Litmos のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Litmos で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure AD クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Litmos アプリケーションでシングル サインオンを構成することです。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  

この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

構成の一環として、Litmos アプリケーションの **SAML トークン属性** をカスタマイズする必要があります。  

![Azure AD のシングル サインオン][17] 

**Litmos で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD クラシック ポータルの **Litmos** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Litmos へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][7] 
3. Litmos 企業サイト (例: *https://azureapptest.litmos.com/account/Login*) に、管理者としてサインオンします。
   
    ![Azure AD のシングル サインオン][21] 
4. 左側にあるナビゲーション バーで、 **[Accounts]**をクリックします。
   
    ![Azure AD のシングル サインオン][22] 
5. **[Integrations]** タブをクリックします。
   
    ![Azure AD のシングル サインオン][23] 
6. **[Integrations]** タブで、下へスクロールして **[3rd Party Integrations]** を表示し、**[SAML 2.0]** タブをクリックします。
   
    ![Azure AD のシングル サインオン][24] 
7. **[The SAML endoiint for litmos is:]**の下の値をコピーします。
   
    ![Azure AD のシングル サインオン][26] 
8. Azure クラシック ポータルの **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行します。
   
    ![Azure AD のシングル サインオン][8] 
   
    1. **[識別子]** ボックスに、ユーザーが Litmos アプリケーションへのサインオンに使用する URL (例: *https://azureapptest.litmos.com/account/Login*) を入力します。
   
    2. **[応答 URL]** ボックスに、前の手順で Litmos アプリケーションからコピーした値を貼り付けます。
   
    3. **[次へ]**をクリックします。
9. **[Litmos でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Azure AD のシングル サインオン][2] 
   
    * [証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。
10. **Litmos** アプリケーションで、次の手順を実行します。
    
     ![Azure AD のシングル サインオン][25] 
    
     1. **[Enable SAML]**をクリックします。
    
     2. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
    >[!TIP]
    >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
     >

     3. Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[SAML X.509 Certificate]** ボックスに貼り付けます。
    
     4. **[変更を保存]**をクリックします。
11. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] \(シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。 
    
     ![Azure AD のシングル サインオン][10]
12. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
     ![Azure AD のシングル サインオン][11]
13. 上部のメニューで、 **属性** to open the **SAML Token 属性** ダイアログを開きます。 
    
    ![[シングル サインオンの構成]][12]
14. **[ユーザー属性の追加]** ダイアログで、次の手順を実行します。 
    
    ![[シングル サインオンの構成]][14]
    
    | 属性名 | 属性値 |
    | --- | --- |
    | 電子メール |User.mail |
    | FirstName |User.givenname |
    | Lastname |User.surname |
    
    上の表のデータ行ごとに、次の手順を実行します。
    
 1. **[ユーザー属性の追加]**をクリックします。    
   ![[シングル サインオンの構成]][15]
 2. **[属性名]** ボックスに、その行に対して表示される**属性名**を入力します。
 3. その行に対して表示される**属性値**を選択します。
 4. **[完了]** をクリックして **[ユーザー属性の追加]** ダイアログ ボックスを閉じます。


1. **[変更の適用]**をクリックします。 
   
   ![[シングル サインオンの構成]][16]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  
   
    1. **[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。
   
    2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
   
   1. **[名]** ボックスに「**Britta**」と入力します。  
   
   2. **[姓]** ボックスに「**Simon**」と入力します。
   
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
   
    1. **[新しいパスワード]** の値を書き留めます。
   
    2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-litmos-test-user"></a>Litmos のテスト ユーザーの作成
このセクションの目的は、Litmos で Britta Simon というユーザーを作成することです。  
Litmos アプリケーションでは、ジャストインタイム プロビジョニングがサポートされています。 そのため、アクセス パネルを使用してアプリケーションにアクセスを試みると、必要に応じてユーザー アカウントが自動的に作成されます。

**Litmos で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Litmos 企業サイト (例: *https://azureapptest.litmos.com/account/Login*) に、管理者としてサインオンします。
   
    ![Azure AD のシングル サインオン][21] 
2. 左側にあるナビゲーション バーで、 **[Accounts]**をクリックします。
   
    ![Azure AD のシングル サインオン][22] 
3. **[Integrations]** タブをクリックします。
   
    ![Azure AD のシングル サインオン][23] 
4. **[Integrations]** タブで、下へスクロールして **[3rd Party Integrations]** を表示し、**[SAML 2.0]** タブをクリックします。
   
    ![Azure AD のシングル サインオン][24] 
5. **[Autogenerate Users:]**を選択します。
   
    ![Azure AD のシングル サインオン][27] 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Litmos へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**Litmos に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Litmos]**を選択します。
   
    ![ユーザーの割り当て][202] 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  

アクセス パネルで Litmos のタイルをクリックすると、自動的に Litmos アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png






