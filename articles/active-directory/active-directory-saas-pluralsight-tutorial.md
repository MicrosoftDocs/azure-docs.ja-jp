---
title: "チュートリアル: Azure Active Directory と Pluralsight の統合 | Microsoft Docs"
description: "Azure Active Directory と Pluralsight の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: f77f459219a5c9a0e218de924c0c7578647a3594
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>チュートリアル: Azure Active Directory と Pluralsight の統合
このチュートリアルの目的は、Pluralsight と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Pluralsight と Azure AD の統合には、次の利点があります。

* Pluralsight にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Pluralsight にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Pluralsight と Azure AD の統合を構成するには、次のものが必要です。

* Azure サブスクリプション
* Pluralsight での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーから Pluralsight を追加する
2. Azure AD SSO の構成とテスト

## <a name="add-pluralsight-from-the-gallery"></a>ギャラリーからの Pluralsight の追加
Azure AD への Pluralsight の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Pluralsight を追加する必要があります。

**ギャラリーから Pluralsight を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「 **Pluralsight**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)
7. 結果ウィンドウで **[Pluralsight]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Pluralsight で Azure AD の SSO を構成し、テストする方法について説明することです。

Pluralsight で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Pluralsight テスト ユーザーの作成](#creating-a-pluralsight-test-user)** - Pluralsight で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Pluralsight アプリケーションで SSO を構成することです。

Pluralsight アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 

![[シングル サインオンの構成]](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) 

**"一意の ID"** 属性を追加し、EmployeeID などの組織に合った適切な値を設定することもできます。 これは必須の属性ではありませんが、一意のユーザーを識別するために追加できます。 

**Pluralsight で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Pluralsight** アプリケーション統合ページで、上部のメニューの **[属性]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png) 
2. 重複する **SAML トークン属性**を削除するには、次の手順を実行します。 
   
    ![Configure Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/2829.png) 
  * 上の表で赤いボックスに囲まれたユーザー属性ごとに、ポインターを合わせて [削除] をクリックします。 
3. 必要な **SAML トークン属性**を追加するには、以下の表の各行について、次の手順を実行します。
   
   | 属性名 | 属性値 |
   | --- | --- |
   | 名 |User.givenname |
   | 姓 |User.surname |
   | 電子メール |User.mail |
4. **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png)
  1. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
  2. **[属性値]** 一覧から、その行に対して表示される属性値を選択します。
  3. **[完了]** をクリックします。    
  4. **[変更の適用]**をクリックします。
 
   ![[シングル サインオンの構成]](./media/active-directory-saas-pluralsight-tutorial/3232.png) 
    
5. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)  
6. Azure クラシック ポータルの **Pluralsight** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
7. **[ユーザーの Pluralsight へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png) 
8. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) 
  1. `https://<instance name>.pluralsight.com/sso/<comapny name>`
  2. **[次へ]**をクリックします。
9. **[Pluralsight でのシングル サインオンの構成]** ページで、次の手順を実行します。

  ![Configure Single Sign-On](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png)   
  1. **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
10. アプリケーション用に構成された SSO を入手するには、Pluralsight の [プロフェッショナル サービス](mailTo:professionalservices@pluralsight.com) チームに連絡し、ダウンロードしたメタデータ ファイルを提供します。
11. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
12. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

* ユーザーの一覧で **[Britta Simon]**を選択します。

  ![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png)  
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   

### <a name="create-a-pluralsight-test-user"></a>Pluralsight テスト ユーザーの作成
このセクションの目的は、Pluralsight で Britta Simon というユーザーを作成することです。 Pluralsight サポート チームと連携し、Pluralsight アカウントにユーザーを追加してください。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、Pluralsight のサポート チームにお問い合わせください。 
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Pluralsight へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**Pluralsight に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータル上の、ディレクトリ ビューでアプリケーション ビューを開くには、トップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Pluralsight]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Pluralsight] タイルをクリックすると、自動的に Pluralsight アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png

