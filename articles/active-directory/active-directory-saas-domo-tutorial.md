---
title: "チュートリアル: Azure Active Directory と Domo の統合 | Microsoft Docs"
description: "Azure Active Directory と Domo の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 058626e4-73b3-4dc2-86ca-b060d002d70a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 68901f611b743e7178634aa72686a2466c617fea
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-domo"></a>チュートリアル: Azure Active Directory と Domo の統合
このチュートリアルの目的は、Domo と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Domo と Azure AD の統合には、次の利点があります。

* Domo にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Domo にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Domo と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Domo でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

* ギャラリーからの Domo の追加
* Azure AD SSO の構成とテスト

## <a name="adding-domo-from-the-gallery"></a>ギャラリーからの Domo の追加
Azure AD への Domo の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Domo を追加する必要があります。

**ギャラリーから Domo を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Domo**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/tutorial_domo_01.png)
7. 結果ウィンドウで **[Domo]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/tutorial_domo_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Domo で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Domo ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Domo の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Domo の **[Username]** の値として割り当てます。

Domo で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Domo テスト ユーザーの作成](#creating-a-domo-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Domo で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Domo アプリケーションでシングル サインオンを構成することです。

Domo アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーションの **[属性]** タブから管理できます。 次のスクリーンショットはその例です。 

![[シングル サインオンの構成]](./media/active-directory-saas-domo-tutorial/tutorial_domo_06.png) 

**Domo で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Domo** アプリケーション統合ページで、上部のメニューの **[属性]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-domo-tutorial/tutorial_general_80.png) 
2. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。
   
   | 属性名 | 属性値 |
   | --- | --- |
   | name |user.displayname |
   | 電子メール |User.mail |
  1. **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-domo-tutorial/tutorial_general_81.png) 
  2. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
  3. **[属性値]** 一覧から、その行に対して表示される属性値を選択します。
  4. ページの下部にある **[完了]**」を参照してください。    
3. Azure クラシック ポータルの **Domo** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
4. **[ユーザーの Domo へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_03.png) 
5. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_04.png) 
  1. **[サインオン URL]** ボックスに、`https://<company name>.domo.com` のパターンを使用して、ユーザーが Domo アプリケーションへのサインオンに使用する URL を入力します。
  2. **[次へ]**をクリックします。
1. **[Domo でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-domo-tutorial/tutorial_domo_05.png)
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
2. お使いのアプリケーション用に構成された SSO を取得するには、Domo サポート チーム ([support@domo.com](mailto: support@domo.com)) に問い合わせます。その際、ダウンロードした証明書を添付し、**発行者の URL**、**SAML SSO URL**、**サインアウト URL** を提示してください。
3. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
4. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。 
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/create_aaduser_06.png) 
  2. **[名]** ボックスに「**Britta**」と入力します。  
  3. **[姓]** ボックスに「**Simon**」と入力します。
  4. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  5. **[ロール]** 一覧で **[ユーザー]** を選択します。
  6. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-domo-tutorial/create_aaduser_08.png)
  1. **[新しいパスワード]** の値を書き留めます。 
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-domo-test-user"></a>Domo テスト ユーザーの作成
このセクションの目的は、Domo で Britta Simon というユーザーを作成することです。 Domo では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない Domo ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、Domo のサポート チームにお問い合わせください。 
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Domo へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**Domo に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルのディレクトリ ビューで、トップ メニューにある **[アプリケーション]** をクリックしてアプリケーション ビューを開きます
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Domo]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-domo-tutorial/tutorial_domo_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで [Domo] タイルをクリックすると、自動的に Domo アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-domo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-domo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-domo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-domo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-domo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-domo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-domo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-domo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-domo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-domo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-domo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-domo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-domo-tutorial/tutorial_general_205.png

