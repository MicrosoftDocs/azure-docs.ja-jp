---
title: "チュートリアル: Azure Active Directory と eTouches の統合 | Microsoft Docs"
description: "Azure Active Directory と eTouches の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: e5706f1c33e5fb9305090c6c4444cf0adb5737c2
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>チュートリアル: Azure Active Directory と eTouches の統合
このチュートリアルでは、eTouches と Azure Active Directory (Azure AD) を統合する方法について説明します。

eTouches と Azure AD の統合には、次の利点があります。

* eTouches にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に eTouches にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
eTouches と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* eTouches での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの eTouches の追加
2. Azure AD SSO の構成とテスト

## <a name="adding-etouches-from-the-gallery"></a>ギャラリーからの eTouches の追加
Azure AD への eTouches の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に eTouches を追加する必要があります。

**ギャラリーから eTouches を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **eTouches**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_01.png)
7. 結果ウィンドウで **[eTouches]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、eTouches で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する eTouches ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと eTouches の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を eTouches の **[Username (ユーザー名)]** の値として割り当てます。

eTouches で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[eTouches のテスト ユーザーの作成](#creating-a-predictix-price-reporting-test-user)** - eTouches で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、eTouches アプリケーションでシングル サインオンを構成します。

eTouches アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーションの **[属性]** タブから管理できます。 次のスクリーンショットはその例です。 

![[シングル サインオンの構成]](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_07.png) 

**eTocuhes で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **eTouches** アプリケーション統合ページで、上部のメニューから **[属性]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-etouches-tutorial/tutorial_general_80.png) 
2. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。
   
   | 属性名 | 属性値 |
   | --- | --- |
   | 電子メール |User.mail |
   
 1. **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-etouches-tutorial/tutorial_general_81.png) 
  2. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
  3. **[属性値]** 一覧から、その行に対して表示される属性値を選択します。
  4. ページの下部にある **[完了]**」を参照してください。    
3. クラシック ポータルの **eTouches** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
4. **[ユーザーの eTouches へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_03.png) 
5. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_04.png)   
  1. **[サインオン URL]** ボックスに、ユーザーが eTouches アプリケーションへのサインオンに使用する URL を **https://www.eiseverywhere.com/saml/accounts/?sso&accountid=\<accountid\>** の形式で入力します。
  2. **[次へ]**をクリックします。
6. **[eTouches でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_05.png)  
  1. **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
7. お使いのアプリケーション用に構成された SSO を取得するには、eTouches アプリケーションで次の手順を実行します。  
  1. 管理者権限を使用して **eTouches** アプリケーションにログインします。 
  2. **[SAML 構成]** に移動します。
  3. **[General Settings (全般設定)]** セクションで、Azure AD のフェデレーション メタデータの内容をテキスト ボックスに貼り付けます。
  4. **[Save & Stay (保存のみ)]** ボタンをクリックします。
  5. [SAML Metadata (SAML メタデータ)] セクションの **[Update Metadata (メタデータの更新)]** をクリックします。 
  6. ページが開き、SSO が実行されます。 SSO が実行されたら、ユーザー名を設定できます。
  7. **[Username (ユーザー名)]** フィールドで、次の画像に示すように **emailaddress** を選択します。 
  8. **[SSO URL / ACS]** の値をコピーし、Azure AD アプリケーションの構成ウィザードの [サインオン URL] ボックスに貼り付けます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png)
8. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
9. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  

    ![Azure AD のシングル サインオン][11]


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_06.png)  
  1. **[名]** ボックスに「**Britta**」と入力します。   
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
  ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
  ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-etouches-tutorial/create_aaduser_08.png)   
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   

### <a name="create-an-etouches-test-user"></a>eTouches テスト ユーザーの作成
このセクションでは、eTouches で Britta Simon というユーザーを作成します。 eTouches サポート チームと連携し、eTouches プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に eTouches へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**eTouches に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[eTouches]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで eTouches のタイルをクリックすると、自動的に eTouches アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_205.png

