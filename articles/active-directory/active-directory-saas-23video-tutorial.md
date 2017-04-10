---
title: "チュートリアル: Azure Active Directory と 23 Video の統合 | Microsoft Docs"
description: "Azure Active Directory と 23 Video の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5e73dd1d-3995-4a73-b9cf-1b2318d49cb3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a59a0782176f5221bb8b2f590faeee660f4d1101
ms.openlocfilehash: 2ebc4571cb7ff763449f192f5140c79a65d69833
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>チュートリアル: Azure Active Directory と 23 Video の統合
このチュートリアルの目的は、23 Video と Azure Active Directory (Azure AD) を統合する方法を説明することです。

23 Video と Azure AD の統合には、次の利点があります。 

* 23 Video にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に 23 Video にシングル サインオン (SSO) できるようにします

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
23 Video と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* 23 Video でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの 23 Video の追加 
2. Azure AD SSO の構成とテスト

## <a name="adding-23-video-from-the-gallery"></a>ギャラリーからの 23 Video の追加
Azure AD への 23 Video の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に 23 Video を追加する必要があります。

**ギャラリーから 23 Video を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **23 Video**」と入力します。
   
    ![アプリケーション][5]
7. 結果ウィンドウで **[23 Video]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][25]

## <a name="configuring-and-testing-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、23 Video で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する 23 Video ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと 23 Video の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を 23 Video の **[Username (ユーザー)]** の値として割り当てます。

23 Video で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[23 Video テスト ユーザーの作成](#creating-a-23-video-test-user)** - 23 Video で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、23 Video アプリケーションで SSO を構成することです。

**23 Video で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **23 Video** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの 23 Video へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][7] 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のシングル サインオン][8] 
  1. **[応答 URL]** ボックスに、ユーザーが 23 Video サイトへのサインオンに使用する URL を入力します (例: *https://britta-simon.23Video.com/saml/login*)。
   
    >[!NOTE]
    >SAML 2.0 を使用した Active Directory の統合は、23 Video のすべてのユーザーが使用できます。 関連するメタデータが必要な場合は、サポート ( [support@23company.com](mailto:support@23company.com) ) にお問い合わせください。 
    > 
 
  2. **[次へ]**をクリックします。
4. **[23 Video でのシングル サインオンの構成]** ページで、次のステップを実行します。
   
    ![Azure AD のシングル サインオン][9] 
 1. [証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。
 2. 23 Video サポート チーム ([support@23company.com](mailto:support@23company.com)) に連絡して、ダウンロードした証明書、**発行者の URL**、**シングル サインオン サービス URL**、**シングル サインアウト URL** を提供し、23 Video アプリの SSO のセットアップを依頼します。    
 3. **[次へ]**をクリックします。
5. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。 
   
    ![Azure AD のシングル サインオン][10]
6. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)  
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。 
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。 
 2. **[完了]** をクリックします。   

### <a name="create-a-23-video-test-user"></a>23 Video テスト ユーザーの作成
このセクションの目的は、23 Video で Britta Simon というユーザーを作成することです。

**23 Video で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 23 Video 企業サイトに管理者としてサインオンします。
2. **[設定]**に移動します。
3. **[Users (ユーザー)]** セクションで **[Configure (構成)]** をクリックします。
   
    ![ユーザーの割り当て][400]
4. **[Add a new user (新しいユーザーの追加)]**をクリックします。 
   
    ![ユーザーの割り当て][401]
5. **[Invite someone to join this site (別のユーザーをこのサイトに招待)]** セクションで、次の手順を実行します。
   
    ![ユーザーの割り当て][402]
 1. **[E-mail addresses (メール アドレス)]** ボックスに、Azure AD の Britta Simon の電子メール アドレスを入力します。  
 2. **[Add the user (ユーザーの追加)]** をクリックします。   

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に 23 Video へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**23 Video に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[23 Video]**を選択します。
   
    ![ユーザーの割り当て][202] 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [23 Video] タイルをクリックすると、自動的に 23 Video アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png





