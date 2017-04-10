---
title: "チュートリアル: Azure Active Directory と Samanage の統合 | Microsoft Docs"
description: "Azure Active Directory で Samanage を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eacd69207e21a5a7c3ef71902bc6c20c37f2003c
ms.openlocfilehash: 0936f7e0b5e1967b629b31cac3c817d04a75287f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>チュートリアル: Azure Active Directory と Samanage の統合
このチュートリアルの目的は、Samanage と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Samanage と Azure AD の統合には、次の利点があります。

* Samanage にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Samanage にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Samanage と Azure AD の統合を構成するには、次のものが必要です。

* 有効な Azure サブスクリプション
* Samanage のテナント

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Samanage の追加
2. Azure AD SSO の構成とテスト

## <a name="adding-samanage-from-the-gallery"></a>ギャラリーからの Samanage の追加
Azure AD への Samanage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Samanage を追加する必要があります。

**ギャラリーから Samanage を追加するには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Add an application from gallerry")
6. **検索ボックス**に、「**Samanage**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Application Gallery")
7. 結果ウィンドウで **[Samanage]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Samanage で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Samanage ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Samanage の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Samanage の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

Samanage で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Samanage のテスト ユーザーの作成](#creating-a-Samanage-test-user)** - Samanage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO の構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Samanage アプリケーションで SSO を構成します。

**Samanage で Azure AD SSO を構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Samanage** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Configure single sign-on")
2. **[ユーザーの Samanage へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Microsoft Azure AD Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Microsoft Azure AD Single Sign-On")
3. [アプリケーション設定の構成] ダイアログ ページで、次の手順に従います。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Configure App URL") 
 1. **[サインオン URL]** ボックスに、`https://<Company Name>.samanage.com/saml_login/<Company Name>` のパターンを使用して URL を入力します。 
 2. **[次へ]**をクリックします。
 
   >[!NOTE]
   >これは実際の値ではないので注意してください。 この値を実際のシングルサインオン URL に置き換える必要があります。 これらの値を取得するには、手順 8. の c を参照して詳細を確認するか、Samanage にお問い合わせください。
   > 
 
4. **[Samanage でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Configure Single Sign-On")
5. 別の Web ブラウザーのウィンドウで、Samanage 企業サイトに管理者としてログインします。
6. **[Dashboard]** をクリックして、左のナビゲーション ウィンドウで **[Setup]** を選択します。
   
   ![Dashboard](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")
7. **[シングル サインオン]**をクリックします。
   
   ![シングル サインオン](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")
8. **[Login using SAML (SAML でログイン)]** セクションで、次の手順を実行します。
   
   ![SAML でログイン](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
 1. **[Enable Single Sign-On with SAML (SAML でのシングル サインオンを有効にする)]** をクリックします。  
 2. **[Identity Provider URL (ID プロバイダー URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[プロバイダー ID の識別]** の値を入力します。    
 3. **[Login URL (ログイン URL)]** の値が手順 3. の **[サインオン URL]** の値と一致していることを確認します。
 4. **[Logout URL (ログアウト URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[リモート ログアウト URL]** の値を入力します。
 5. **[SAML Issuer (SAML 発行者)]** ボックスに、ID プロバイダーに設定されたアプリ ID URI を入力します。
 6. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[Paste your Identity Provider x.509 Certificate below (ID プロバイダー x.509 証明書を貼り付けてください)]** ボックスに貼り付けます。
 7. **[Create users if they do not exist in Samanage (Samanage に存在しない場合にユーザーを作成する)]** をクリックします。
 8. **[Update]**をクリックします。
9. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Configure Single Sign-On")
10. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。
    
    ![シングル サインオンの構成](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Configure Single Sign-On")

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。 
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png) 
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)  
 1. **[新しいパスワード]** の値を書き留めます。 
 2. **[完了]** をクリックします。   

### <a name="create-a-samanage-test-user"></a>Samanage テスト ユーザーの作成
Azure AD ユーザーが Samanage にログインできるようにするには、そのユーザーを Samanage にプロビジョニングする必要があります。Samanage の場合、プロビジョニングは手動で行う必要があります。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Samanage 企業サイトに管理者としてログインします。
2. **[Dashboard (ダッシュボード)]** をクリックし、左のナビゲーション ウィンドウで **[Setup (セットアップ)]** を選択します。
   
   ![Setup](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Setup")
3. **[ユーザー]** タブをクリックします。
   
   ![ユーザー](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Users")
4. **[新しいユーザー]**をクリックします。
   
   ![New User](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "New User")
5. プロビジョニングする Azure AD アカウントの **[Name (名前)]** と **[Email Address (電子メール アドレス)]** を入力し、**[Create user (ユーザーの作成)]** をクリックします。
   
   ![ユーザーの作成](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Creat User")
   
   >[!NOTE]
   >AAD アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。 他の Samanage ユーザー アカウントの作成ツールまたは Samanage から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
   >  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Samanage へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Assign users")

**Britta Simon を Samanage に割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Assign users")
2. アプリケーションの一覧で **[Samanage]**を選択します。
   
    ![Configure single sign-on](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Assign users")
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Assign users")

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Samanage] タイルをクリックすると、自動的に Samanage アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


