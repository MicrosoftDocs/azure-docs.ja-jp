---
title: "チュートリアル: Azure Active Directory と CloudPassage の統合 | Microsoft Docs"
description: "Azure Active Directory と CloudPassage の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a4a1c3763fa13afcb7a93269a210b7dc4270e60
ms.openlocfilehash: 17b80f63ae27c6e42b81d13e221ac5acda970fdd
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>チュートリアル: Azure Active Directory と CloudPassage の統合
このチュートリアルの目的は、CloudPassage と Azure Active Directory (Azure AD) を統合する方法を説明することです。  

CloudPassage と Azure AD の統合には、次の利点があります。 

* CloudPassage にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に CloudPassage にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。 

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
CloudPassage と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* CloudPassage での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)を無料で入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。  

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの CloudPassage の追加 
2. Azure AD SSO の構成とテスト

## <a name="add-cloudpassage-from-the-gallery"></a>ギャラリーからの CloudPassage の追加
Azure AD への CloudPassage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に CloudPassage を追加する必要があります。

**ギャラリーから CloudPassage を追加するには、次の手順に従います。**
1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **CloudPassage**」と入力します。
   
    ![アプリケーション][5]
7. 結果ウィンドウで **[CloudPassage]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][6]

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、CloudPassage で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する CloudPassage ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと CloudPassage の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を CloudPassage の **[Username]** の値として割り当てます。

CloudPassage で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[CloudPassage テスト ユーザーの作成](#creating-a-halogen-software-test-user)** - CloudPassage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure AD クラシック ポータルで Azure AD の SSO を有効にすることと、CloudPassage アプリケーションで SSO を構成することです。  

CloudPassage アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 

次のスクリーンショットはその例です。

![[シングル サインオンの構成]][21]

**CloudPassage との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure AD クラシック ポータルの **CloudPassage** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][7]
2. **[ユーザーの CloudPassage へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On][8]
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。 
   
    ![[アプリケーション設定の構成]][9]   
  1. **[サインオン URL]** ボックスに、ユーザーが CloudPassage アプリケーションへのサインオンに使用する URL (例: *https://portal.cloudpassage.com/saml/init/accountid*) を入力します。  
  2. **[応答 URL]** ボックスに、AssertionConsumerService URL (例: *https://portal.cloudpassage.com/saml/consume/accountid*) を入力します。 この属性の値は、CloudPassage ポータルの **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで **[SSO Setup documentation (SSO セットアップのドキュメント)]** をクリックすることで取得できます。
  
    ![Configure Single Sign-On][10]   
  3. **[次へ]**をクリックします。
4. **[CloudPassage でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターのローカルに保存します。 
   
    ![Configure Single Sign-On][11]
5. 別の Web ブラウザーのウィンドウで、管理者として CloudPassage 企業サイトにサインオンします。
6. 上部のメニューの **[Settings (設定)]** をクリックし、**[Site Administration (サイトの管理)]** をクリックします。 
   
    ![[シングル サインオンの構成]][12]
7. **[Authentication Settings (認証設定)]** タブをクリックします。 
   
    ![[シングル サインオンの構成]][13]
8. **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで、次の手順に従います。 
   
    ![[シングル サインオンの構成]][14]
  1. Azure クラシック ポータルで、**[CloudPassage でのシングル サインオンの構成]** ダイアログ ページの **[発行者の URL]** の値をコピーし、**[SAML issuer URL (SAML 発行者の URL)]** ボックスに貼り付けます。
  2. Azure クラシック ポータルの **[CloudPassage でのシングル サインオンの構成]** ダイアログ ページで **[サービス プロバイダー (SP) によって開始されたエンドポイント]** の値をコピーし、**[SAML endpoint URL (SAML エンドポイントの URL)]** テキスト ボックスに貼り付けます。
  3. Azure クラシック ポータルで、**[CloudPassage でのシングル サインオンの構成]** ダイアログ ページの **[ログアウト URL]** の値をコピーし、**[Logout landing page (ログアウト ランディング ページ)]** ボックスに貼り付けます。
  4. ダウンロードした証明書から **base-64** でエンコードされたファイルを作成します。 

    >[AZURE.TIP] 詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
  5. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X.509 Certificate]** ボックスに貼り付けます。
  6. **[保存]**をクリックします。


1. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] (シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。 
   
    ![Configure Single Sign-On][15]
2. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。 
   
   ![[シングル サインオンの構成]][16]
3. 上部のメニューの **[属性]** をクリックして、**[SAML トークン属性]** ダイアログを開きます。 
   
   ![Configure Single Sign-On][17]
4. 必要なユーザー属性を追加するために、次の表の各行に対して、次の手順を実行します。 
   
   | 属性名 | 属性値 |
   | --- | --- |
   | firstname |User.givenname |
   | lastname |User.surname |
   | email |User.mail |
  1. **[ユーザー属性の追加]**をクリックします。 

    ![[シングル サインオンの構成]][18]
  2. **[属性名]** ボックスにその行に表示される属性名を入力し、**[属性値]** でその行に表示される属性値を選択します。 

    ![[シングル サインオンの構成]][19]
  3. **[完了]** をクリックします。
1. 下部にあるツール バーの **[変更の適用]**をクリックします。 
   
   ![[シングル サインオンの構成]][20]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. [次へ] をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   

### <a name="create-a-cloudpassage-test-user"></a>CloudPassage テスト ユーザーの作成
このセクションの目的は、CloudPassage で Britta Simon というユーザーを作成することです。

**CloudPassage で Britta Simon というユーザーを作成するには、次の手順に従います。**
1. **CloudPassage** 企業サイトに管理者としてサインオンします。 
2. 上部にあるツールバーの **[Settings (設定)]** をクリックし、**[Site Administration (サイトの管理)]** をクリックします。 
   
   ![CloudPassage テスト ユーザーの作成][22] 
3. **[Users (ユーザー)]** タブをクリックし、**[Add New User (新しいユーザーの追加)]** をクリックします。 
   
   ![CloudPassage テスト ユーザーの作成][23]
4. **[新しいユーザーの追加]** セクションで、次の手順を実行します。 
   
   ![CloudPassage テスト ユーザーの作成][24]
  1. **[名]** ボックスに「Britta」と入力します。 
  2. **[姓]** ボックスに「Simon」と入力します。
  3. **[Username]**、**[Email]**、**[Retype Email]** の各ボックスに、Britta の Azure AD でのユーザー名を入力します。
  4. **[Access Type]** で **[Enable Halo Portal Access]** を選択します。
  5. **[追加]**をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に CloudPassage へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][30]

**Britta Simon を CloudPassage に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][26]
2. アプリケーションの一覧で **[CloudPassage]**を選択します。
   
    ![ユーザーの割り当て][27]
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][25]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][29]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [CloudPassage] タイルをクリックすると、自動的に CloudPassage アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png






















