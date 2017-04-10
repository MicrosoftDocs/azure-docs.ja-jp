---
title: "チュートリアル: Azure Active Directory と Tableau Online の統合 | Microsoft Docs"
description: "Azure Active Directory と Tableau Online の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: de8292e6d3642776fc684ded731f48845de846d4
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>チュートリアル: Azure Active Directory と Tableau Online の統合
このチュートリアルでは、Tableau Online と Azure Active Directory (Azure AD) を統合する方法について説明します。

Tableau Online と Azure AD の統合には、次の利点があります。

* Tableau Online にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Tableau Online にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Tableau Online の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* **Tableau Online** での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Tableau Online の追加
2. Azure AD SSO の構成とテスト

## <a name="adding-tableau-online-from-the-gallery"></a>ギャラリーからの Tableau Online の追加
Azure AD への Tableau Online の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tableau Online を追加する必要があります。

**ギャラリーから Tableau Online を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「 **Tableau Online**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_01.png)
7. 結果ウィンドウで **[Tableau Online]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Tableau Online で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Tableau Online ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Tableau Online の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Tableau Online の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

Tableau Online で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Tableau Online のテスト ユーザーの作成](#creating-a-Tableau-Online-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Tableau Online で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Tableau Online アプリケーションで SSO を構成することです。

**Tableau Online で Azure AD SSO を構成するには、次の手順に従います。**

1. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]][6]
2. クラシック ポータルの **Tableau Online** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][7] 
3. **[ユーザーの Tableau Online へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_06.png)
4. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_07.png)
  1. [サインオン URL] ボックスに、`https://sso.online.tableau.com` という形式で URL を入力します。
  2. **[次へ]**をクリックします。
5. **[Tableau Online でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_08.png)
6. シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]
8. 別のブラウザー ウィンドウで、Tableau Online アプリケーションにサインオンします。 **[設定]**、**[認証]** の順にクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
9. **[Authentication Types (認証の種類)]** セクションで、 **[Single sign-on with SAML (SAML によるシングル サインオン)]** チェック ボックスをオンにし、SAML を有効にします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)
10. 下へスクロールして、 **[Import metadata file into Tableau Online (Tableau Online にメタデータ ファイルをインポートする)]** セクションを表示します。  [Browse (参照)] をクリックし、Azure AD からダウンロードしたメタデータ ファイルをインポートします。 次に、 **[Apply (適用)]**をクリックします。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)
11. **[Match assertions (アサーションの一致)]** セクションで、電子メール アドレス、姓、名の対応する ID プロバイダーのアサーション名を挿入します。 Azure AD から情報を取得するには、以下の手順に従います。 
  1. Azure AD に戻ります。 Azure クラシック ポータルで **Tableau Online** アプリケーション統合ページに移動します。
  2. 上部のメニューの **[属性]** をクリックします。 
  3. 値 userprincipalname、givenname、surname の名前をコピーします。
   
     ![Azure AD のシングル サインオン](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
  4. Tableau Online アプリケーションに切り替えて、**[Tableau Online Attributes (Tableau Online 属性)]** セクションを次のように設定します。
     * Email (電子メール): **mail** または **userprincipalname**
     * First name (名): **givenname**
     * Last name (姓): **surname**
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-tableau-online-test-user"></a>Tableau Online テスト ユーザーの作成
このセクションでは、Tableau Online で Britta Simon というユーザーを作成します。

1. **Tableau Online** で、**[Settings (設定)]** をクリックし、**[Authentication (認証)]** セクションをクリックします。 下へスクロールして、 **[Select Users (ユーザーの選択)]** セクションを表示します。 **[Add Users (ユーザーの追加)]** をクリックし、**電子メール アドレスを入力**します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. **[Add users for single sign-on (SSO) authentication (シングル サインオン (SSO) 認証用にユーザーを追加する)]**を選択します。 **[Enter Email Addresses (電子メール アドレスの入力)]** ボックスに「britta.simon@contoso.com」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. **[作成]**をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Tableau Online へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Tableau Online に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Tableau Online]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. [すべてのユーザー] の一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Tableau Online] タイルをクリックすると、Tableau Online アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_205.png

