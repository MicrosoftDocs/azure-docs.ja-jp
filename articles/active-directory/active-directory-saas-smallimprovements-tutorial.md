---
title: "チュートリアル: Azure Active Directory と Small Improvements の統合 | Microsoft Docs"
description: "Azure Active Directory と Small Improvements の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 3599d44a3349efbc62dde97a7862a0656d1eb226
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>チュートリアル: Azure Active Directory と Small Improvements の統合
このチュートリアルの目的は、Small Improvements と Azure Active Directory (Azure AD) を統合する方法を説明することです。  

Small Improvements と Azure AD の統合には、次の利点があります。

* Small Improvements にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Small Improvements にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Small Improvements の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Small Improvements での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。  

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーから Small Improvements を追加する
2. Azure AD SSO の構成とテスト

## <a name="adding-small-improvements-from-the-gallery"></a>ギャラリーから Small Improvements を追加する
Azure AD への Small Improvements の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Small Improvements を追加する必要があります。

**ギャラリーから Small Improvements を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Small Improvements**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_01.png)
7. 結果ウィンドウで **[Small Improvements]** を選び、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Small Improvements で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Small Improvements ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Small Improvements の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Small Improvements の **[Username]** の値として割り当てることで確立されます。

Small Improvements で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Small Improvements テスト ユーザーの作成](#creating-a-small-improvements-test-user)** - Small Improvements で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Small Improvements アプリケーションで SSO を構成することです。

**Small Improvements で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Small Improvements** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Small Improvements へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_04.png) 
   
     >[!NOTE]
     >アカウントのドメイン名を構成するには、Small Improvements サポート チーム ( [Support@small-improvements.com](mailto:support@small-improvements.com) ) に連絡してください。 これは、SSO が動作するために必要です。
     >  

  1. **[サインオン URL]** ボックスに、ユーザーが Small Improvements アプリケーションへのサインオンに使用する URL を入力します。  
  2. **[次へ]**をクリックします。
4. **[Small Improvements でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_05.png) 
 1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。  
 2. **[次へ]**をクリックします。
2. 別の Web ブラウザー ウィンドウで、管理者として Small Improvements 企業サイトにサインオンします。
3. メイン ダッシュボード ページで、左側の **[管理]** ボタンをクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) 
4. **[統合]** セクションで、**[SAML SSO]** ボタンをクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png) 
5. [SSO Setup] ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png)  
 1. Azure クラシック ポータルで、**[Small Improvements でのシングル サインオンの構成]** ダイアログ ページの **[SAML SSO URL]** の値をコピーし、**[HTTP エンドポイント]** テキスト ボックスに貼り付けます。  
 2. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[x509 証明書]** ボックスに貼り付けます。 
 3. SSO およびログイン フォーム認証オプションをユーザーが使用できるようにするには、**[ログイン/パスワードによるアクセスも有効にする]** オプションをオンにします。  
 4. **[SAML プロンプト]** ボックスに、SSO ログイン ボタンの名前として適切な値を入力します。  
 5. **[保存]**をクリックします。
6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_05.png) 
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。  
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_08.png) 
 1. **[新しいパスワード]** の値を書き留めます。 
 2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-small-improvements-test-user"></a>Small Improvements テスト ユーザーの作成
このセクションの目的は、Small Improvements で Britta Simon というユーザーを作成することです。

この機能は、「 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)」で既に有効にしています。

**Small Improvements で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 管理者として Small Improvements 企業サイトにサインオンします。
2. ホーム ページの左側のメニューに移動し、 **[管理]**をクリックします。
3. [ユーザー管理] セクションで、 **[ユーザー ディレクトリ]** ボタンをクリックします。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) 
4. **[チーム メンバーの追加]**をクリックします。
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) 
5. 対応するテキスト ボックスに、プロビジョニングする有効なユーザーの **[名]**、**[姓]**、**[電子メール アドレス]** を入力します。 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png) 
6. **[通知電子メールを送信する]** ボックスに、個人のメッセージを入力することもできます。 通知を送信しない場合は、このチェック ボックスをオフにします。
7. **[ユーザーの作成]**をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Small Improvements へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

    ![Assign User][200] 

**Small Improvements に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で、 **[Small Improvements]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで [Small Improvements] タイルをクリックすると、自動的に Small Improvements アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png

