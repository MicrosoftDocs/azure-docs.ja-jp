---
title: "チュートリアル: Azure Active Directory と RunMyProcess の統合 | Microsoft Docs"
description: "Azure Active Directory と RunMyProcess の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 8aeac420b3848a270a3a9a325c9db0f1569a33e6
ms.openlocfilehash: 2f6bcdd9ea97494d3d12ea4ffd541b4b95a91c4f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>チュートリアル: Azure Active Directory と RunMyProcess の統合
このチュートリアルの目的は、RunMyProcess と Azure Active Directory (Azure AD) を統合する方法を説明することです。

RunMyProcess と Azure AD の統合には、次の利点があります。

* RunMyProcess にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に RunMyProcess にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
RunMyProcess と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* RunMyProcess での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの RunMyProcess の追加
2. Azure AD SSO の構成とテスト

## <a name="add-runmyprocess-from-the-gallery"></a>ギャラリーからの RunMyProcess の追加
Azure AD への RunMyProcess の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RunMyProcess を追加する必要があります。

**ギャラリーから RunMyProcess を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**RunMyProcess**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. 結果ウィンドウで **[RunMyProcess]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーをベースに、RunMyProcess での Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する RunMyProcess ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと RunMyProcess の関連ユーザーの間でリンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を RunMyProcess の **[Username (ユーザー名)]** の値として割り当てます。

RunMyProcess で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[RunMyProcess のテスト ユーザーの作成](#creating-a-runmyprocess-test-user)** - RunMyProcess で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、RunMyProcess アプリケーションで SSO を構成します。

**RunMyProcess で Azure AD SSO を構成するには、次の手順に従います。**

1. クラシック ポータルの **RunMyProcess** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの RunMyProcess へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
  1. **[サインオン URL]** ボックスに、`https://live.runmyprocess.com/live/<tenant id>` のパターンを使用して URL を入力します。 
  2. **[次へ]**をクリックします。
    >[!NOTE]
    >実際のサインオン URL で値を更新する必要があることに注意してください。 この値を取得するには、RunMyProcess サポート チーム (<mailto:support@runmyprocess.com>) に問い合わせてください。
    >  
4. **[RunMyProcess でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、ファイルをコンピューターに保存します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. 別の Web ブラウザーのウィンドウで、管理者として RunMyProcess テナントにサインオンします。
6. 左側のナビゲーション パネルで、**[Account (アカウント)]**、**[Configuration (構成)]** の順にクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. **[Authentication method (認証方法)]** セクションに移動し、次の手順に従います。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
  1. **[Method]** として、**[SSO with Samlv2]** を選択します。 
  2. **[SSO redirect (SSO リダイレクト)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[SAML SSO URL]** の値を入力します。
  3. **[Logout redirect (ログアウト リダイレクト)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[シングル サインアウト サービス URL]** の値を入力します。
  4. **[Name Id Format (名前 ID フォーマット)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[名前識別子形式]** の値を入力します。
  5. ダウンロードした証明書ファイルのコンテンツをコピーし、**[Certificate (証明書)]** ボックスに貼り付けます。 
  6. **[Save (保存)]** アイコンをクリックします。
8. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
9. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。  
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png)  
 1. **[新しいパスワード]** の値を書き留めます。
 2. **[完了]** をクリックします。   

### <a name="create-a-runmyprocess-test-user"></a>RunMyProcess テスト ユーザーの作成
Azure AD ユーザーが RunMyProcess にログインできるようにするには、ユーザーを RunMyProcess にプロビジョニングする必要があります。 RunMyProcess の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. RunMyProcess 企業サイトに管理者としてログインします。
2. 左側のナビゲーション パネルで **[Account (アカウント)]**、**[Users (ユーザー)]** の順にクリックし、**[New User (新しいユーザー)]** をクリックします。
   
   ![New User](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "New User")
3. **[ユーザーの設定]** セクションで、次の手順に従います。
   
   ![プロファイル](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profile")   
 1. プロビジョニングする有効な AAD アカウントの**名前**と**電子メール**を対応するボックスに入力します。 
 2. **[IDE 言語]**、**[言語]**、**[プロファイル]** を選択します。 
 3. **[アカウント作成の電子メールを自分に送信]**を選択します。 
 4. **[保存]**をクリックします。
   
   >[!NOTE]
   >他の RunMyProcess ユーザー アカウントの作成ツールまたは RunMyProcess から提供されている API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。 
   > 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に RunMyProcess へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**Britta Simon を RunMyProcess に割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[RunMyProcess]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. ツール バーで、**[割り当て]** をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [RunMyProcess] タイルをクリックすると、RunMyProcess アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png

