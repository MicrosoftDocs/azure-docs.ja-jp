---
title: "チュートリアル: Azure Active Directory と SkyDesk Email の統合 | Microsoft Docs"
description: "Azure Active Directory と SkyDesk Email の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: fcea07a412de7b35931ff95b01fbe1276302f1ea
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>チュートリアル: Azure Active Directory と SkyDesk Email の統合
このチュートリアルの目的は、SkyDesk Email と Azure Active Directory (Azure AD) を統合する方法を説明することです。

SkyDesk Email と Azure AD の統合には、次の利点があります。

* SkyDesk Email にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に SkyDesk Email にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Active Directory クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と SkyDesk Email の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* SkyDesk Email でのシングル サインオン (SSO) が有効なサブスクリプション

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

1. ギャラリーからの SkyDesk Email の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-skydesk-email-from-the-gallery"></a>ギャラリーからの SkyDesk Email の追加
Azure AD への SkyDesk Email の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SkyDesk Email を追加する必要があります。

**ギャラリーから SkyDesk Email を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**SkyDesk Email**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)
7. 結果ウィンドウで **[SkyDesk Email]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、SkyDesk Email で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SkyDesk Email ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SkyDesk Email の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、SkyDesk Email の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

SkyDesk Email で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SkyDesk Email テスト ユーザーの作成](#creating-a-Skydesk-Email-test-user)** - SkyDesk Email で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、SkyDesk Email アプリケーションでシングル サインオンを構成することです。

**Email で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **SkyDesk Email** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6] 
2. **[ユーザーの SkyDesk Email へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png) 

   1. [サインオン URL] ボックスに、次のパターンを使用して、ユーザーが SkyDesk Email アプリケーションへのサインオンに使用する URL を入力します: **"https://mail.skydesk.jp/portal/\<company name\>"**。
   2. **[次へ]**をクリックします。

4. **[SkyDesk Email でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png) 
   
    a. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
    b. **[次へ]**をクリックします。
5. **SkyDesk Email** で SSO を有効にするには、次の手順に従います。
   1. 管理者として SkyDesk Email アカウントにサインオンします。
   2. 上部のメニューで、[設定] をクリックし、[組織] を選択します。 
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)  
   3. 左側のパネルで [ドメイン] をクリックします。
    
    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)
   4. [ドメインの追加] をクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)
   5. 自分のドメイン名を入力し、ドメインを確認します。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)
   6. 左側のパネルで **[SAML Authentication (SAML 認証)]** をクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)
6. **[SAML 認証]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
   > [!NOTE]
   > SAML ベースの認証を使用するには、**ドメインが検証済み**または**ポータル URL** が設定済みである必要があります。 一意の名前でポータル URL を設定できます。
   > 
   > 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)

   1. Azure AD クラシック ポータルで、**[SAML SSO URL]** 値をコピーし、**[ログイン URL]** ボックスに貼り付けます。
   2. Azure AD クラシック ポータルで、**[シングル サインアウト サービス URL]** 値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。
   3. **[パスワード変更 URL]** は省略可能なので、空白のままにします。
   4. **[Get Key From File (ファイルからキーを取得)]** をクリックし、ダウンロードした SkyDesk Email 証明書を選択して、**[Open (開く)]** をクリックして証明書をアップロードします。
   5. **[アルゴリズム]** として **[RSA]** を選択します。
   6. **[OK]** をクリックして変更を保存します。

7. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
8. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png) 
   
   1. **[名]** ボックスに「**Britta**」と入力します。 
   2. **[姓]** ボックスに「**Simon**」と入力します。  
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png) 
   
   1. **[新しいパスワード]** の値を書き留めます。
   2. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-a-skydesk-email-test-user"></a>SkyDesk Email テスト ユーザーの作成
このセクションでは、SkyDesk Email で Britta Simon というユーザーを作成します。

1. SkyDesk Email 内の左側のパネルで **[User Access (ユーザー アクセス)]** をクリックし、自分のユーザー名を入力します。 

![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>ユーザーを一括で作成する必要がある場合は、SkyDesk Email のサポート チームにお問い合わせください。
>


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に SkyDesk Email へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**SkyDesk Email に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[SkyDesk Email]** を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [SkyDesk Email] タイルをクリックすると、自動的に SkyDesk Email アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png

