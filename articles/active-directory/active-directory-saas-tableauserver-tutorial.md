---
title: "チュートリアル: Azure Active Directory と Tableau Server の統合 | Microsoft Docs"
description: "Azure Active Directory と Tableau Server の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0849c77c66ac8617e217a69696b5c404be3b5eb4
ms.openlocfilehash: 3aefb8188880fac5c1fcbe237a1e133584089e6a
ms.lasthandoff: 02/03/2017

---

# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>チュートリアル: Azure Active Directory と Tableau Server の統合
このチュートリアルの目的は、Tableau Server と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Tableau Server と Azure AD の統合には、次の利点があります。

* Tableau Server にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが各自の Azure AD アカウントで Tableau Server に自動的にサインオン (シングル サインオン) するように、設定が可能です。
* Azure クラシック ポータルでアカウントを一元的に管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Tableau Server の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Tableau Server のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーから Tableau Server を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-tableau-server-from-the-gallery"></a>ギャラリーから Tableau Server を追加する
Azure AD への Tableau Server の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tableau Server を追加する必要があります。

**ギャラリーから Tableau Server を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「 **Tableau Server**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)
7. 結果ウィンドウで **[Tableau Server]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ギャラリーでアプリを選択する](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーをベースに、Tableau Server での Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Tableau Server ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Tableau Server の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Tableau Server の **[Username]** の値として割り当てることで確立されます。

Tableau Server で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Tableau Server テスト ユーザーの作成](#creating-a-tableauserver-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Tableau Server で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Tableau Server アプリケーションでシングル サインオンを構成することです。

Tableau Server アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 次のスクリーンショットはその例です。 

![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png) 

**Tableau Server で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[Tableau Server]** アプリケーション統合ページで、上部のメニューから **[属性]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png) 
2. **[Saml トークン属性]** ダイアログで、次の手順を実行します。

   1. **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png) 
   2. **[属性名]** テキストボックスに「**username**」と入力します。
   3. **[属性値]** リストから **[user.displayname]** を選択します。
   4. ページの下部にある **[完了]**」を参照してください。    

3. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)  
4. **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
5. **[ユーザーの Tableau Server へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png) 
6. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png) 

   1. **[サインイン URL]** テキストボックスに Tableau Server の URL を入力します。 
   2. **[識別子]** ボックスで URL をコピーします。
   3. **[次へ]**をクリックします。
h
7. **[Tableau Server シングル サインオン パラメーターの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png) 

   1. **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   2. **[次へ]**をクリックします。

8. アプリケーションに合わせて SSO を構成するには、管理者として Tableau Server テナントにサインオンする必要があります。
   
   1. Tableau Server の構成で、**[SAML]** タブをクリックします。
  
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
   2. **[Use SAML for single sign-on]** チェックボックスをオンにします。
   3. Azure クラシック ポータルからダウンロードしたフェデレーション メタデータ ファイルを検索し、**[SAML Idp metadata file]** でアップロードします。
   4. [Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。http://tableau_server など。 http://localhost の使用は推奨されません。 末尾にスラッシュが付いている URL (http://tableau_server/ など) はサポートされていません。 **[Tableau Server return URL]** をコピーし、手順 3 のように Azure AD の **[サインオン URL]** テキストボックスに貼り付けます。
   5. [SAML entity ID]: IdP に対して Tableau Server のインストールを一意に識別するエンティティ ID。 必要に応じてこの欄にも Tableau Server URL を入力できますが、使用する Tableau Server URL にする必要はありません。 **[SAML entity ID]** をコピーし、手順 3 のように Azure AD の **[識別子]** テキストボックスに貼り付けます。
   6. **[Export Metadata File]** をクリックし、テキスト エディター アプリケーションで開きます。 Http Post で Index 0 の [Assertion Consumer Service URL] を探し、URL をコピーします。 手順 3 のように、その URL を Azure AD の **[応答 URL]** に貼り付けます。 
   7. Tableau Server の [Configiuration] \(構成) ページの **[OK]** ボタンをクリックします。
   
    >[!NOTE] 
    >Tableau Server で SAML を構成する方法について不明な点がある場合は、[SAML の構成](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm)に関する記事を参照してください。
    >

9. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。 
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

* ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png) 
   
   1. **[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。
   2. **[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。
   3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png) 
   
   1. **[名]** ボックスに「**Britta**」と入力します。  
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png) 
   
   1. **[新しいパスワード]** の値を書き留めます。
   2. **[完了]** をクリックします。   

### <a name="creating-a-tableau-server-test-user"></a>Tableau Server テスト ユーザーの作成
このセクションの目的は、Tableau Server で Britta Simon というユーザーを作成することです。 Tableau Server 内のすべてのユーザーをプロビジョニングする必要があります。 また、ユーザーのユーザー名は、Azure AD のカスタム属性 **username**で構成した値と同じにする必要があります。 正しい対応付けがあれば、統合で「 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)」が機能します。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、組織の Tableau Server 管理者に問い合わせてください。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Tableau Server へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200] 

**Tableau Server に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Tableau Server]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。

![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Tableau Server] タイルをクリックすると、Tableau Server アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png

