---
title: "チュートリアル: Azure Active Directory と BetterWorks の統合 | Microsoft Docs"
description: "Azure Active Directory と BetterWorks の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 30dfd46c8133a2a5bb0b32bd5c035260026eec42
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>チュートリアル: Azure Active Directory と BetterWorks の統合
このチュートリアルの目的は、BetterWorks と Azure Active Directory (Azure AD) を統合する方法を説明することです。

BetterWorks と Azure AD の統合には、次の利点があります。

* BetterWorks にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に BetterWorks にシングル サインオン (SSO) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
BetterWorks と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* BetterWorks でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
>  

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

* ギャラリーからの BetterWorks の追加
* Azure AD SSO の構成とテスト

## <a name="add-betterworks-from-the-gallery"></a>ギャラリーからの BetterWorks の追加
Azure AD への BetterWorks の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BetterWorks を追加する必要があります。

**ギャラリーから BetterWorks を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **BetterWorks**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)
7. 結果ウィンドウで **[BetterWorks]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ギャラリーでアプリを選択する](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、BetterWorks で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する BetterWorks ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BetterWorks の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、BetterWorks の **[Username]** の値として割り当てることで確立されます。

BetterWorks で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[BetterWorks テスト ユーザーの作成](#creating-a-betterworks-test-user)** - BetterWorks で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、BetterWorks アプリケーションでシングル サインオンを構成することです。

BetterWorks アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーションの**[属性]**タブから管理できます。 次のスクリーンショットはその例です。 

![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**BetterWorks で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[BetterWorks]** アプリケーション統合ページで、上部のメニューから **[属性]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)
2. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。

    | 属性名 | 属性値 |
    | --- | --- |    
    | saml_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    1. **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)
    2. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    3. **[属性値]** リストから、その行に対して表示される saml トークン ID を入力します。
    4. ページの下部にある **[完了]**

1. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png) 
2. **[ユーザーの BetterWorks へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)
3. **[アプリケーション設定の構成]** ダイアログ ページで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)
   1. **[識別子]** ボックスに、`https://app.betterworks.com/saml2/metadata/` の形式で URL を入力します。
   2. **[応答 URL]** ボックスに、`https://app.betterworks.com/saml2/acs/` の形式で URL を入力します。
   3. **[次へ]**

1. **[アプリケーション設定の構成]** ダイアログ ページで、**SP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)
    1. **[詳細設定を表示します (オプション)]** を選択します。
    2. **[サインオン URL]** ボックスに、`https://app.betterworks.com` のパターンを使用して、ユーザーが BetterWorks アプリケーションへのサインオンに使用する URL を入力します。
    3. **[次へ]**をクリックします。

1. **[BetterWorks シングル サインオンの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)
    1. **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
    2. **[次へ]**をクリックします。
2. お使いのアプリケーション用に構成された SSO を取得するために、BetterWorks のサポート チーム (<mailto:support@betterworks.com>) に問い合わせます。 BetterWorks チーム側で SSO を設定する必要があるため、ダウンロードしたメタデータ ファイルを添付して、 チームと共有してください。
3. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
4. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
   ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)
    1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
    2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
    3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)
   1. **[名]** ボックスに「**Britta**」と入力します。  
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)
    1. **[新しいパスワード]** の値を書き留めます。
    2. **[完了]** をクリックします。   

### <a name="create-a-betterworks-test-user"></a>BetterWorks テスト ユーザーの作成
このセクションでは、BetterWorks で Britta Simon というユーザーを作成します。 

BetterWorks サポート チーム (<mailto:support@betterworks.com>) と連携し、BetterWorks プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に BetterWorks へのアクセスを許可することで、このユーザーが Azure の SSO を使用できるようにすることです。

   ![ユーザーの割り当て][200]

**BetterWorks に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[BetterWorks]**を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [BetterWorks] タイルをクリックすると、自動的に BetterWorks アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png

