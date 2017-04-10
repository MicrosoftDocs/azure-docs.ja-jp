---
title: "チュートリアル: Azure Active Directory と Workrite の統合 | Microsoft Docs"
description: "Azure Active Directory と Workrite の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2a5c2956-a011-4d5c-877b-80679b6587b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a410fb385ed2960139987a71236b7f84a32c8099
ms.openlocfilehash: 5e640983825fb521a00b84518eca76f007224269
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workrite"></a>チュートリアル: Azure Active Directory と Workrite の統合
このチュートリアルの目的は、Workrite と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Workrite と Azure AD の統合には、次の利点があります。 

* Workrite にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に Workrite にシングル サインオンできるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Workrite の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Workrite でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Workrite の追加 
2. Azure AD SSO の構成とテスト

## <a name="adding-workrite-from-the-gallery"></a>ギャラリーからの Workrite の追加
Azure AD への Workrite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workrite を追加する必要があります。

**ギャラリーから Workrite を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. [検索] ボックスに、「 **Workrite**」と入力します。
   
    ![アプリケーション][5]
7. 結果ウィンドウで **[Workrite]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![アプリケーション][500]

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Workrite で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Workrite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workrite の関連ユーザーの間で、リンク関係が確立されている必要があります。  

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Workrite の **[Username]** の値として割り当てることで確立されます。

Workrite で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Workrite テスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Workrite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、Workrite アプリケーションで SSO を構成することです。

**Workrite で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Workrite** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Workrite へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][7] 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のシングル サインオン][8] 
  1. **[サインオン URL]** ボックスに、ユーザーが Workrite サイトへのサインオンに使用する URL (例: *https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=1a82b5aa-4dd6-4472-9721-7d0193f59e22*) を入力します。

    >[!NOTE]
    >サインオン URL の値がわからない場合は、Workrite サポート チーム ( [support@workrite.co.uk](mailto:support@workrite.co.uk) ) に問い合わせてください。 
    >   
  2. **[次へ]**をクリックします。
4. **[Workrite でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![Azure AD のシングル サインオン][9] 
 1. [証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。  
 2. Workrite サポート チーム ([support@workrite.co.uk](mailto:support@workrite.co.uk)) に連絡して、ダウンロードした証明書、**発行者の URL** (エンティティ ID)、**シングル サインオン サービス URL**、**シングル サインアウト URL** を提供し、Workrite アプリの SSO のセットアップを依頼します。  
 3. **[次へ]**をクリックします。
5. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。 
   
    ![Azure AD のシングル サインオン][10]
6. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。  

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workrite-tutorial/create_aaduser_09.png)  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workrite-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workrite-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workrite-tutorial/create_aaduser_05.png)  
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。  
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workrite-tutorial/create_aaduser_06.png)  
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workrite-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workrite-tutorial/create_aaduser_08.png)  
 1. **[新しいパスワード]** の値を書き留めます。  
 2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-workrite-test-user"></a>Workrite テスト ユーザーの作成
このセクションの目的は、Workrite で Britta Simon というユーザーを作成することです。

**Workrite で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Workrite 企業サイトに管理者としてサインオンします。
2. ナビゲーション ウィンドウで **[Admin]**をクリックします。
   
    ![ユーザーの割り当て][400]
3. クイック リンクに移動し、 **[Create User]**をクリックします。 
   
    ![ユーザーの割り当て][401]
4. **[Create User]** ダイアログで、次の手順に従います。
   
    ![ユーザーの割り当て][402]
 1. プロビジョニングする有効な Azure AD ユーザーの**電子メール**、**名**、**姓**を入力します。  
 2. **[ロールの選択]** で **[クライアント管理者]** を選択します。  
 3. **[保存]**をクリックします。   

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Workrite へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

    ![Assign User][200] 

**Workrite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Workrite]**を選択します。
   
    ![ユーザーの割り当て][202] 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Workrite] タイルをクリックすると、自動的に Workrite アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_01.png
[500]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_05.png

[6]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_02.png
[8]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_03.png
[9]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_04.png
[10]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_07.png
[203]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_402.png





