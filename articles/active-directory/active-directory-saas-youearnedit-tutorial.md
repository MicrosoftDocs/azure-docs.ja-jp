---
title: "チュートリアル: Azure Active Directory と YouEarnedIt の統合 | Microsoft Docs"
description: "Azure Active Directory と YouEarnedIt の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3011d44d-dfcf-4061-888f-cff90fbc8150
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: a1e6f6738a3e6426a5ec5e0dab6f479a0a74b0ad
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>チュートリアル: Azure Active Directory と YouEarnedIt の統合
このチュートリアルでは、YouEarnedIt と Azure Active Directory (Azure AD) を統合する方法について説明します。

YouEarnedIt と Azure AD の統合には、次の利点があります。

* YouEarnedIt にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に YouEarnedIt にサインオン (シングル サインオン (SSO)) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
YouEarnedIt と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* YouEarnedIt でのシングル サインオンが有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーから YouEarnedIt の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-youearnedit-from-the-gallery"></a>ギャラリーからの YouEarnedIt の追加
Azure AD への YouEarnedIt の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に YouEarnedIt を追加する必要があります。

**ギャラリーから YouEarnedIt を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **YouEarnedIt**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_01.png)
7. 結果ウィンドウで **[YouEarnedIt]** を選び、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、YouEarnedIt で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する YouEarnedIt ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと YouEarnedIt の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を YouEarnedIt の **[Username (ユーザー名)]** の値として割り当てます。

YouEarnedIt で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[YouEarnedIt のテスト ユーザーの作成](#creating-a-predictix-price-reporting-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを YouEarnedIt で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にし、YouEarnedIt アプリケーションでシングル サインオンを構成します。

**YouEarnedIt で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **YouEarnedIt** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの YouEarnedIt へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_04.png) 
  1. **[サインオン URL]** ボックスに、次のパターンを使用して、ユーザーが YouEarnedIt アプリケーションへのサインオンに使用する URL を入力します。  
    * サンドボックス環境: `https://<company name>.sandbox.youearnedit.com/users/sign_in`
    * 運用環境: `https://<company name>.youearnedit.com/users/sign_in`  
   2. click **[次へ]**
4. **[YouEarnedIt でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_05.png)
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。  
  2. **[次へ]**をクリックします。
5. アプリケーション用に構成された SSO を入手するには、YouEarnedIt サポート チームに連絡し、次のものを情報として提供してください。  
    • ダウンロードした**証明書**
    • **SAML SSO URL**
6. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_05.png)  
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。  
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_06.png)    
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-an-youearnedit-test-user"></a>YouEarnedIt テスト ユーザーの作成
このセクションでは、YouEarnedIt で Britta Simon というユーザーを作成します。 YouEarnedIt サポート チームと連携し、YouEarnedIt プラットフォームにユーザーを追加してください。

>[!NOTE]
>YouEarnedIt は、ID プロバイダーが NameID 属性の EmailAddress または UserName を提供することを求めています。 対応する UserName または EmailAddress がデータベース内に見つからないか、正確に一致しない場合は、認証に失敗します。 その場合は、SSO 統合前に、YouEarnedIt システムにアカウントをインポートする必要があります (通常、API または CSV インポートを使用します)。
>  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に YouEarnedIt へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**YouEarnedIt に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[YouEarnedIt]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [YouEarnedIt] タイルをクリックすると、自動的に YouEarnedIt アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_205.png

