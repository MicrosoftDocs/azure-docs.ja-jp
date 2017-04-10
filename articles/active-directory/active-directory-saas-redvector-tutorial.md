---
title: "チュートリアル: Azure Active Directory と RedVector の統合 | Microsoft Docs"
description: "Azure Active Directory と RedVector の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 99042f39-0ab2-475b-8df8-3016d7f875e9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 73ebb6c18dbb39ed812252a73099860f8a5b8205
ms.openlocfilehash: b7dd45ef8cc29ba96469317612c58431a7855db2
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-redvector"></a>チュートリアル: Azure Active Directory と RedVector の統合
このチュートリアルでは、RedVector と Azure Active Directory (Azure AD) を統合する方法について説明します。

RedVector と Azure AD の統合には、次の利点があります。

* RedVector にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に RedVector にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と RedVector の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* **RedVector** でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
>  

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの RedVector の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-redvector-from-the-gallery"></a>ギャラリーからの RedVector の追加
Azure AD への RedVector の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RedVector を追加する必要があります。

**ギャラリーから RedVector を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **RedVector**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_01.png)
7. 結果ウィンドウで **[RedVector]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、RedVector で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する RedVector ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと RedVector の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を RedVector の **[Username (ユーザー名)]** の値として割り当てます。

RedVector で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[RedVector のテスト ユーザーの作成](#creating-a-RedVector-test-user)** - RedVector で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、RedVector アプリケーションで SSO を構成することです。

**RedVector で Azure AD SSO を構成するには、次の手順に従います。**

1. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![Configure Single Sign-On][6]
2. クラシック ポータルの **RedVector** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][7] 
3. **[ユーザーの RedVector へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_06.png)
4. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_07.png)
 1. [サインオン URL] ボックスに、`https://sso2.redvector.com/adfs/<Companyname>` という形式で URL を入力します。 場合によっては、Redvector サポート (<sso@redvector.com>) に連絡して、お使いの環境に適した値を入手する必要があります。
 2. **[次へ]**をクリックします。

5. **[RedVector でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。 さらに、[シングル サインオン サービス URL] の値をコピーします。 構成された SSO を取得するには、この情報を RedVector サポートと共有する必要があります。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_08.png)
6. アプリケーション用に構成された SSO を入手するために、RedVector のサポート チーム (<mailto:sso@redvector.com>) に問い合わせます。 SSO を構成するための適切な手続きについてサポートを受けることができます。 電子メールで以下を提供してください。 
   * ダウンロードした証明書
   * **SAML SSO URL**
6. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。 
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-redvector-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-redvector-test-user"></a>RedVector テスト ユーザーの作成
このセクションでは、RedVector で Britta Simon というユーザーを作成します。 RedVector に Britta Simon を追加する方法がわからない場合は、RedVector サポート チームと協力してテスト ユーザーを追加し、SSO を有効にしてください。 連絡先は <mailto:sso@redvector.com>です。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に RedVector へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][200] 

**RedVector に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[RedVector]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-redvector-tutorial/tutorial_redvector_09.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. [すべてのユーザー] の一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [RedVector] タイルをクリックすると、自動的に RedVector アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-redvector-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-redvector-tutorial/tutorial_general_205.png

