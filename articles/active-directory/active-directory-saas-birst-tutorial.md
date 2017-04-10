---
title: "チュートリアル: Azure Active Directory と Birst Agile Business Analytics の統合 | Microsoft Docs"
description: "Azure Active Directory と Birst Agile Business Analytics の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c9aa91bb61a097dbe70415e7d118a781ea3c607f
ms.openlocfilehash: 55187dda51da35966148956f8fa8fbc1dce6f0b0
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>チュートリアル: Azure Active Directory と Birst Agile Business Analytics の統合
このチュートリアルの目的は、Birst Agile Business Analytics と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Birst Agile Business Analytics と Azure AD の統合には、次の利点があります。

* Birst Agile Business Analytics にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Birst Agile Business Analytics にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Birst Agile Business Analytics の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Birst Agile Business Analytics での SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーから Birst Agile Business Analytics を追加する
2. Azure AD SSO の構成とテスト

## <a name="add-birst-agile-business-analytics-from-the-gallery"></a>ギャラリーからの Birst Agile Business Analytics の追加
Azure AD への Birst Agile Business Analytics の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Birst Agile Business Analytics を追加する必要があります。

**ギャラリーから Birst Agile Business Analytics を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Birst Agile Business Analytics**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/tutorial_birst_01.png)
7. 結果ウィンドウで **[Birst Agile Business Analytics]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/tutorial_birst_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Birst Agile Business Analytics で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Birst Agile Business Analytics ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Birst Agile Business Analytics の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Birst Agile Business Analytics の **[Username]** の値として割り当てます。

Birst Agile Business Analytics で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Birst Agile Business Analytics テスト ユーザーの作成](#creating-a-birst-agile-business-analytics-test-user)** - Birst Agile Business Analytics で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Birst Agile Business Analytics アプリケーションで SSO を構成することです。

**Birst Agile Business Analytics で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Birst Agile Business Analytics** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Birst Agile Business Analytics へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-birst-tutorial/tutorial_birst_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-birst-tutorial/tutorial_birst_04.png) 
  1. [サインオン URL] ボックスに、ユーザーが Birst Agile Business Analytics アプリケーションへのサインオンに使用する URL を、https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID という形式で入力します。
   この URL は、Birst アカウントが存在するデータセンターに依存します。 米国のデータセンターの場合は "https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID" を使用し、ヨーロッパのデータセンターの場合は "https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID" を使用します。
  2. **[次へ]**をクリックします。
4. **[Birst Agile Business Analytics でのシングル サインオンの構成]** ページで、次の手順に従います。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-birst-tutorial/tutorial_birst_05.png)   
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
5. お使いのアプリケーション用に構成された SSO を取得するには、ダウンロードした証明書ファイルを電子メールに添付して、Birst Agile Business Analytics サポート チーム ([info@birst.com](emailTo:info@birst.com)) に問い合わせます。 SAML SSO URL、サインアウト URL、発行元 URL もお知らせください。サポート チームが SSO 統合で設定します。

 >[!NOTE]
 >Birst チームが **app2101** などの適切なサーバーに SSO を設定できるように、この統合には SHA256 アルゴリズムが必要であることを伝えてください (SHA1 はサポートされません)。
 >  

6. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-birst-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   

### <a name="create-a-birst-agile-business-analytics-test-user"></a>Birst Agile Business Analytics テスト ユーザーの作成
このセクションの目的は、Birst Agile Business Analytics で Britta Simon というユーザーを作成することです。 Birst Agile Business Analytics サポート チームと連携し、Birst アカウントにユーザーを追加してください。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、Birst Agile Business Analytics のサポート チームにお問い合わせください。
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Birst Agile Business Analytics へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**Birst Agile Business Analytics に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で、 **[Birst Agile Business Analytics]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-birst-tutorial/tutorial_birst_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで Birst Agile Business Analytics タイルをクリックすると、Birst Agile Business Analytics アプリケーションに自動的にサインオンするはずです。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-birst-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-birst-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-birst-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-birst-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-birst-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-birst-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-birst-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-birst-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-birst-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-birst-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-birst-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-birst-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-birst-tutorial/tutorial_general_205.png

