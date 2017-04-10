---
title: "チュートリアル: Azure Active Directory と Predictix Assortment Planning の統合 | Microsoft Docs"
description: "Azure Active Directory と Predictix Assortment Planning の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 37e686ff-f8e5-40b1-9d7e-f64b076917b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 8314db293ac3c9cbc2882c9a981819d0c5c02df3
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>チュートリアル: Azure Active Directory と Predictix Assortment Planning の統合
このチュートリアルでは、Predictix Assortment Planning と Azure Active Directory (Azure AD) を統合する方法について説明します。

Predictix Assortment Planning と Azure AD の統合には、次の利点があります。

* Predictix Assortment Planning にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Predictix Assortment Planning にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Predictix Assortment Planning と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Predictix Assortment Planning での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Microsoft Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Predictix Assortment Planning の追加
2. Microsoft Azure AD SSO の構成とテスト

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>ギャラリーからの Predictix Assortment Planning の追加
Azure AD への Predictix Assortment Planning の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Predictix Assortment Planning を追加する必要があります。

**ギャラリーから Predictix Assortment Planning を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Predictix Assortment Planning**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)
7. 結果ウィンドウで **[Predictix Assortment Planning]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)

## <a name="configure-and-test-microsoft-azure-ad-sso"></a>Microsoft Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Predictix Assortment Planning で Microsoft Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Predictix Assortment Planning ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Predictix Assortment Planning の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を Predictix Assortment Planning の **[Username (ユーザー名)]** の値として割り当てます。

Predictix Assortment Planning で Microsoft Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Microsoft Azure AD のシングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Microsoft Azure AD シングル サインオンをテストします。
3. **[Predictix Assortment Planning のテスト ユーザーの作成](#creating-a-predictix-price-reporting-test-user)** - Predictix Assortment Planning で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Microsoft Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-microsoft-azure-ad-sso"></a>Microsoft Azure AD SSO の構成
このセクションでは、クラシック ポータルで Microsoft Azure AD のシングル サインオンを有効にして、Predictix Assortment Planning アプリケーションでシングル サインオンを構成します。

**Predictix Assortment Planning で Microsoft Azure AD SSO を構成するには、次の手順に従います。**

1. クラシック ポータルの **Predictix Assortment Planning** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Predictix Assortment Planning へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png) 
  1. **[サインオン URL]** ボックスに、ユーザーが Predictix Assortment Planning アプリケーションへのサインオンに使用する URL を、**https://\<company name-pricing\>.ap.predictix.com/sso/request** 形式で入力します。
  2. click **[次へ]**
4. **[Predictix Assortment Planning でのシングル サインオンの構成]** ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。  
  2. **[次へ]**をクリックします。
5. アプリケーション用に構成された SSO を入手するために、Predictix Assortment Planning サポート チームに連絡し、次のものを情報として提供します。
   
  * ダウンロードした証明書
  * **エンティティ ID**  
  * **SAML SSO URL**
  * **シングル サインアウト サービス URL**
6. クラシック ポータルで、SSO の構成確認を選択し、**[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。 
  2. **[完了]** をクリックします。   

### <a name="create-an-predictix-assortment-planning-test-user"></a>Predictix Assortment Planning テスト ユーザーの作成
このセクションでは、Predictix Assortment Planning で Britta Simon というユーザーを作成します。 Predictix Assortment Planning サポート チームと連携し、Predictix Assortment Planning プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Predictix Assortment Planning へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Predictix Assortment Planning に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Predictix Assortment Planning]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-sso"></a>SSO のテスト
このセクションでは、アクセス パネルを使用して Microsoft Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Predictix Assortment Planning] タイルをクリックすると、自動的に Predictix Assortment Planning アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png

