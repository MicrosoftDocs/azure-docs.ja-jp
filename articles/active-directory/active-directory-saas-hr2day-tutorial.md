---
title: "チュートリアル: Azure Active Directory と HR2day by Merces の統合 | Microsoft Docs"
description: "Azure Active Directory と HR2day by Merces の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf5588885de9c280eb70712dbf800efe509ee912
ms.openlocfilehash: fe040c6d799c8b1a19d8329b6e4aebefcbae6190


---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>チュートリアル: Azure Active Directory と HR2day by Merces の統合
このチュートリアルの目的は、HR2day by Merces と Azure Active Directory (Azure AD) を統合する方法を説明することです。  
HR2day by Merces と Azure AD の統合には、次の利点があります。

* HR2day by Merces にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に HR2day by Merces にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
HR2day by Merces と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* HR2day by Merces でのシングル サインオンが有効なサブスクリプション

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

1. ギャラリーからの HR2day by Merces の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>ギャラリーからの HR2day by Merces の追加
Azure AD への HR2day by Merces の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HR2day by Merces を追加する必要があります。

**ギャラリーから HR2day by Merces を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. [検索] ボックスに、「 **HR2day by Merces**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)

7. 結果ウィンドウで **[HR2day by Merces]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、HR2day by Merces で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する HR2day by Merces ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと HR2day by Merces の関連ユーザーの間で、リンク関係が確立されている必要があります。  
このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、HR2day by Merces の **[Username]** の値として割り当てます。

HR2day by Merces で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
3. **[HR2day by Merces のテスト ユーザーの作成](#creating-a-hr2day-by-merces-test-user)** - HR2day by Merces で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで HR2day by Merces に対する認証を行うことができるようにする方法を説明します。

HR2day by Merces アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 

![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) 

SAML アサーションを構成する前に、HR2day サポート チーム ( [servicedesk@merces.nl](mailto:servicedesk@merces.nl) ) に連絡し、テナントの一意識別子属性の値を請求する必要があります。 次のセクションの手順を完了するには、この値が必要です。

**HR2day by Merces で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **HR2day by Merces** アプリケーション統合ページの一番上のメニューで **[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) 

2. 必要な属性のマッピングを追加するには、次の手順を実行します。 
   
    ![Configure Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) 

    a. **[ユーザー属性の追加]**をクリックします。

    b. **[属性名]** テキストボックスに、「**ATTR_LOGINCLAIM**」と入力します。

    c. **[属性値]** の一覧で、**[Join()]** を選択します。 

    d. **[String1]** の一覧で、**[User.mail]** を選択します。 

    e. **[String2]** テキストボックスに、HR2day チームから提供された**一意識別子**を入力します。 

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[区切り記号]** テキストボックスに「**@**」を入力します。

    g. ページの下部にある **[完了]**」を参照してください。


1. **[変更の適用]**をクリックします。

2. 上部のメニューで **[クイック スタート]** to open the **[クイック スタート]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) 

3. **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 

4. **[ユーザーの HR2day by Merces へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) 

5. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) 

    a. ユーザーが HR2day by Merces アプリケーションへのサインオンに使用する URL を [サインオン URL] ボックスに **"https://\<テナント名\>.force.com/\<インスタンス名\>"** の形式で入力します。

    b. **[次へ]**をクリックします。

1. **[HR2day by Merces でのシングル サインオンの構成]** ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) 
   
    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    b. **[次へ]**をクリックします。

2. お使いのアプリケーション用に構成された SSO を取得するために、ダウンロードした証明書ファイルを電子メールに添付して、HR2day by Merces のサポート チーム ([servicedesk@merces.nl](emailTo:servicedesk@merces.nl)) に問い合わせます。 SAML SSO URL、サインアウト URL、発行元 URL もお知らせください。サポート チームが SSO 統合で設定します。

    > [!NOTE]
    > Merces チームに、この統合ではエンティティ ID を **https://hr2day.force.com/INSTANCENAME** というパターンで設定する必要があることをお伝えください。
    > 
    > 

1. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
2. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-hr2day-by-merces-test-user"></a>HR2day by Merces のテスト ユーザーの作成
このセクションの目的は、HR2day by Merces で Britta Simon というユーザーを作成することです。 HR2day by Merces サポート チームと連携し、HR2day アカウントにユーザーを追加してください。 

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、HR2day by Merces のサポート チームにお問い合わせください。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に HR2day by Merces へのアクセスを許可することによって、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**HR2day by Merces に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[HR2day by Merces]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで [HR2day by Merces] タイルをクリックすると、自動的に HR2day by Merces アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO2-->


