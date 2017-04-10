---
title: "チュートリアル: Azure Active Directory と Mixpanel の統合 | Microsoft Docs"
description: "Azure Active Directory と Mixpanel の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 87b409c73e68a6ed6ea84e92b15bbcf3ed82d5f9
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>チュートリアル: Azure Active Directory と Mixpanel の統合
このチュートリアルの目的は、Mixpanel と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Mixpanel と Azure AD の統合には、次の利点があります。

* Mixpanel にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Mixpanel にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Mixpanel と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Mixpanel でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーから Mixpanel を追加する
2. Azure AD SSO の構成とテスト

## <a name="add-mixpanel-from-the-gallery"></a>ギャラリーからの Mixpanel の追加
Azure AD への Mixpanel の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Mixpanel を追加する必要があります。

**ギャラリーから Mixpanel を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Mixpanel**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_01.png)
7. 結果ウィンドウで **[Mixpanel]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Mixpanel で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Mixpanel ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Mixpanel の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Mixpanel の **[Username]** の値として割り当てます。

Mixpanel で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Mixpanel テスト ユーザーの作成](#creating-a-mixpanel-test-user)** - Mixpanel で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Mixpanel アプリケーションで SSO を構成することです。

**Mixpanel で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Mixpanel** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Mixpanel へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_04.png)
  1. **[サインオン URL]** ボックスに、"**https://mixpanel.com/login/**" のパターンを使用して、ユーザーが Mixpanel アプリケーションへのサインオンに使用する URL を入力します。

    >[!NOTE]
    >[https://mixpanel.com/register/](https://mixpanel.com/register/) で登録してログイン資格情報を設定し、[Mixpanel サポート チーム ](mailto:support@Mixpanel.com) に連絡してテナントの SSO 設定を有効してください。 必要であれば、Mixpanel サポート チームはサインオン URL 値も提供します。
    >

  2. **[次へ]**をクリックします。

1. **[Mixpanel でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_05.png) 
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。 
  2. **[次へ]**をクリックします。
2. 別のブラウザー ウィンドウで、管理者として Mixpanel アプリケーションにサインオンします。
3. ページの下部の左隅にある小さな **歯車** アイコンをクリックします。 
   
    ![Mixpanel シングル サインオン](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_06.png) 
4. **[Access security]** タブをクリックし、**[Change settings]** をクリックします。
   
    ![Mixpanel 設定](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_08.png) 
5. **[Change your certificate]** ダイアログ ページで、[**Choose file]** をクリックしてダウンロードした証明書をアップロードし、**[Next]** をクリックします。
   
    ![Mixpanel 設定](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_09.png) 
6. Azure クラシック ポータルで、**[Mixpanel でのシングル サインオンの構成]** ダイアログ ページの **[シングル サインオン サービス URL]** の値をコピーし、**[Change your authentication URL]** ダイアログ ページの認証 URL テキストボックスに貼り付けて、**[Next]** をクリックします。
   
    ![Mixpanel 設定](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_10.png) 
7. **[Done]**をクリックします。
8. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
9. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

* ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_05.png)  
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。  
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。
 2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-mixpanel-test-user"></a>Mixpanel テスト ユーザーの作成
このセクションの目的は、Mixpanel で Britta Simon というユーザーを作成することです。 

1. Mixpanel 企業サイトに管理者としてサインオンします。
2. ページの下部の左隅にある小さな歯車ボタンをクリックし、 **[設定]** ウィンドウを開きます。
3. **[チーム]** タブをクリックします。
4. **[Invite a team member by email]** ボックスに、Azure での Britta の 電子メール アドレスを入力します。
   
    ![Mixpanel 設定](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_11.png) 
5. **[招待]**をクリックします。 

ユーザーにプロファイルを設定するために電子メールが届きます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Mixpanel へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**Mixpanel に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Mixpanel]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の単一の SSO 構成をテストすることです。

アクセス パネルで Mixpanel のタイルをクリックすると、自動的に Mixpanel アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_205.png

