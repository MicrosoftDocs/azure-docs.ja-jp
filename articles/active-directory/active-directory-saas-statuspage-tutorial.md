---
title: "チュートリアル: Azure Active Directory と StatusPage の統合 | Microsoft Docs"
description: "Azure Active Directory と StatusPage の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 3b901bcb08a194f72c3dc75e33f2b94fffea370e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>チュートリアル: Azure Active Directory と StatusPage の統合
このチュートリアルの目的は、StatusPage と Azure Active Directory (Azure AD) を統合する方法を説明することです。

StatusPage と Azure AD の統合には、次の利点があります。 

* StatusPage にアクセスする Azure AD ユーザーを制御できます。 
* ユーザーが自分の Azure AD アカウントで自動的に StatusPage にサインオン (シングル サインオン (SSO)) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
StatusPage と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* StatusPage でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。 

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

* ギャラリーからの StatusPage の追加 
* Azure AD SSO の構成とテスト

## <a name="add-statuspage-from-the-gallery"></a>ギャラリーからの StatusPage の追加
Azure AD への StatusPage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に StatusPage を追加する必要があります。

**ギャラリーから StatusPage を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **StatusPage**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)
7. 結果ウィンドウで **[StatusPage]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、StatusPage で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する StatusPage ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと StatusPage の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を StatusPage の **[Username]** の値として割り当てます。

StatusPage で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[StatusPage のテスト ユーザーの作成](#creating-a-statuspage-test-user)** - StatusPage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD の SSO を有効にすることと、StatusPage アプリケーションでシングル サインオンを構成することです。 

**StatusPage で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **StatusPage** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの StatusPage へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) 
   
   >[!NOTE]
   >シングル サインオンを構成するために必要なメタデータは、StatusPage サポート チーム ( [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)) に連絡して入手してください。 
   > 
  1. メタデータから発行者の値をコピーし、**[識別子]** ボックスに貼り付けます。
  2. メタデータから応答 URL をコピーし、**[応答 URL]** ボックスに貼り付けます。
  3. **[次へ]**をクリックします。

4. **[StatusPage でのシングル サインオンの構成]** ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png)   
  1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
  2. **[次へ]**をクリックします。
5. 別の Web ブラウザー ウィンドウで、管理者として StatusPage 企業サイトにサインオンします。
6. メイン ツール バーで、 **[Manage Account]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 
7. **[Single Sign-on]** タブをクリックします。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 
8. [SSO Setup] ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png)  
  1. Azure クラシック ポータルで、**[StatusPage でのシングル サインオンの構成]** ダイアログ ページの **[シングル サインオン サービス URL]** の値をコピーし、**[SSO Target URL]** ボックスに貼り付けます。 
  2. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[Certificate]** ボックスに貼り付けます。 
  3. **[保存]**をクリックします。
9. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。 
   
    ![Azure AD のシングル サインオン][10]
10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png)  
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。  
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。 
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png)  
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。  
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-statuspage-test-user"></a>StatusPage のテスト ユーザーの作成
このセクションの目的は、StatusPage で Britta Simon というユーザーを作成することです。

StatusPage では、ジャストインタイム プロビジョニングがサポートされています。 この機能は、「 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)」で既に有効にしています。

**StatusPage で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. StatusPage 企業サイトに管理者としてサインオンします。
2. 上部のメニューで **[Manage Account]**をクリックします。
3. [Team Members] タブをクリックします。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 
4. **[Add Team Member]**をクリックします。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 
5. プロビジョニングする有効なユーザーの**電子メール アドレス**、**名**、**姓**を、対応するテキスト ボックスに入力します。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 
6. **[Role]** で **[Client Administrator]** を選択します。
7. **[Create Account]**をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に StatusPage へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200] 

**Britta Simon を StatusPage に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[StatusPage]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [StatusPage] タイルをクリックすると、自動的に StatusPage アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png







