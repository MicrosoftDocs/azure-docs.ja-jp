---
title: "チュートリアル: Azure Active Directory と BenSelect の統合 | Microsoft Docs"
description: "Azure Active Directory と BenSelect の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffa17478-3ea1-4356-a289-545b5b9a4494
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4682c79f1328af58326f709d94614429c5fd9f7f
ms.openlocfilehash: e9eb8f444597000afae3de7a4cdfc60301bc3cea
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-benselect"></a>チュートリアル: Azure Active Directory と BenSelect の統合
このチュートリアルでは、BenSelect と Azure Active Directory (Azure AD) を統合する方法について説明します。

BenSelect と Azure AD の統合には、次の利点があります。

* BenSelect にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に BenSelect にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
BenSelect と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* BenSelect でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
>  

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

* ギャラリーからの BenSelect の追加
* Azure AD SSO の構成とテスト

## <a name="adding-benselect-from-the-gallery"></a>ギャラリーからの BenSelect の追加
Azure AD への BenSelect の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BenSelect を追加する必要があります。

**ギャラリーから BenSelect を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **BenSelect**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_01.png)
7. 結果ウィンドウで **[BenSelect]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ギャラリーでアプリを選択する](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、BenSelect で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する BenSelect ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BenSelect の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を BenSelect の **[Username]** の値として割り当てます。

BenSelect で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[BenSelect のテスト ユーザーの作成](#creating-a-benselect-test-user)** - BenSelect で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、BenSelect アプリケーションで SSO を構成することです。

BenSelect アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーションの**[属性]**タブから管理できます。 次のスクリーンショットはその例です。 

![Configure Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_06.png)

**BenSelect で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **BenSelect** アプリケーション統合ページで、上部のメニューの **[属性]** をクリックします。
   
     ![Configure Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_07.png)
2. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。
   
   | 属性名 | 属性値 |
   | --- | --- |
   | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier |extractmailprefix([userprincipalname]) |
  1. **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_08.png)
  2. **[属性名]** ボックスに、その行に対して表示される属性名を入力します 
  3. **[属性値]** 一覧から「ExtractMailPrefix()」と入力します。
  4. **[メール]** 一覧から「User.userprincipalname」と入力します。
  5. ページの下部にある **[完了]**」を参照してください。
3. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_09.png)
4. **[ユーザーの BenSelect へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_03.png) 
5. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_04.png) 
   1. **[応答 URL]** ボックスに、`https://www.benselect.com/enroll/login.aspx?Path={<tenant name>}` のパターンを使用して URL を入力します。
   2. **[次へ]**をクリックします。
6. **[BenSelect でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_05.png)
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
7. アプリケーション用に構成された SSO を入手するには、 [support@selerix.com](mailto:support@selerix.com) を使用して BenSelect サポート チームに連絡して、次の情報を提供してください。
   * ダウンロードした証明書
   * SAML SSO URL
   * サインアウト URL 
   * 発行者 
     
   >[!NOTE]
   >適切なサーバー (app2101 など) で SSO を設定するために、この統合には SHA256 アルゴリズム (SHA1 はサポート対象外) が必要であるという点を伝える必要があります。 
   > 
8. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
9. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-benselect-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-benselect-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-benselect-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-benselect-tutorial/create_aaduser_05.png)    
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-benselect-tutorial/create_aaduser_06.png)   
  1. **[名]** ボックスに「**Britta**」と入力します。 
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-benselect-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-benselect-tutorial/create_aaduser_08.png) 
   1. **[新しいパスワード]** の値を書き留めます。
   2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-an-benselect-test-user"></a>BenSelect テスト ユーザーの作成
このセクションの目的は、BenSelect で Britta Simon というユーザーを作成することです。 BenSelect サポート チームと連携し、BenSelect アカウントにユーザーを追加してください。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、BenSelect サポート チーム (<mailto:support@selerix.com>) にお問い合わせください。
>  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に BenSelect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**BenSelect に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[BenSelect]**を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで [BenSelect] タイルをクリックすると、自動的に BenSelect アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_205.png

