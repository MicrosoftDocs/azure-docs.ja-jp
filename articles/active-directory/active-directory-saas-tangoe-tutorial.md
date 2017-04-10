---
title: "チュートリアル: Azure Active Directory と Tangoe Command Premium Mobile の統合 | Microsoft Docs"
description: "Azure Active Directory と Tangoe Command Premium Mobile の間でシングル サインオンを構成する方法について確認します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: bb140097831453d46f6bfef1c9fbe569eefb3020
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>チュートリアル: Azure Active Directory と Tangoe Command Premium Mobile の統合
このチュートリアルでは、Tangoe Command Premium Mobile と Azure Active Directory (Azure AD) を統合する方法について説明します。

Tangoe Command Premium Mobile と Azure AD の統合には、次の利点があります。

* Tangoe Command Premium Mobile にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが各自の Azure AD アカウントで Tangoe Command Premium Mobile に自動的にサインオン (シングル サインオン) するように設定できます。
* Azure クラシック ポータルでアカウントを一元的に管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Tangoe Command Premium Mobile の統合を構成するには、次のものが必要です。

* Azure サブスクリプション
* Tangoe Command Premium Mobile でのシングル サインオン (SSO) が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Tangoe Command Premium Mobile の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-tangoe-command-premium-mobile-from-the-gallery"></a>ギャラリーからの Tangoe Command Premium Mobile の追加
Azure AD への Tangoe Command Premium Mobile の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tangoe Command Premium Mobile を追加する必要があります。

**ギャラリーから Tangoe Command Premium Mobile を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Tangoe Command Premium Mobile**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_01.png)
7. 結果ウィンドウで **[Tangoe Command Premium Mobile]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Tangoe Command Premium Mobile で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Tangoe Command Premium Mobile ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Tangoe Command Premium Mobile の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を、Tangoe Command Premium Mobile の **[Username]** の値として割り当てます。

Tangoe Command Premium Mobile で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Tangoe Command Premium Mobile テスト ユーザーの作成](#creating-an-tangoe-test-user)** - Tangoe Command Premium Mobile で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Tangoe Command Premium Mobile アプリケーションでシングル サインオンを構成します。

**Tangoe Command Premium Mobile で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Tangoe Command Premium Mobile** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして **[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure Single Sign-On][6]
2. **[ユーザーの Tangoe Command Premium Mobile へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_04.png) 

   1. **[サインオン URL]** ボックスに、ユーザーが Tangoe Command Premium Mobile アプリケーションへのサインオンに使用する URL を **"https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=\<tenant issuer\>&Target=\<target page URL\>"** の形式で入力します。

   2. **[応答 URL]** ボックスに、**"https://sso.tangoe.com/sp/ACS.saml2"** の形式で URL を入力します。

    > [!NOTE]  
    > URL の正しい値がわからない場合、上記の値をプレース ホルダーとして使用し、Tangoe のカスタマー サポート担当者に連絡して正しい値を入手してください。
    >

4. **[Tangoe Command Premium Mobile でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_05.png) 
   
   1. **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。
   2. **[次へ]**をクリックします。

5. アプリケーション用に構成された SSO を入手するには、Tangoe のカスタマー サポート担当者に連絡し、次のものを情報として提供してください。

   - ダウンロードしたメタデータ ファイル
   - **発行者の URL**
   - **SAML SSO URL**
   - **シングル サインアウト サービス URL**

6. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
7. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

* ユーザーの一覧で **[Britta Simon]**を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/create_aaduser_06.png) 
   
  1. **[名]** ボックスに「**Britta**」と入力します。  
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/create_aaduser_08.png) 
   
  1. **[新しいパスワード]** の値を書き留めます。
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-an-tangoe-command-premium-mobile-test-user"></a>Tangoe Command Premium Mobile テスト ユーザーの作成
このセクションでは、Tangoe Command Premium Mobile で Britta Simon というユーザーを作成します。 Tangoe Command Premium Mobile アプリケーションでは、シングル サインオンを行う前に、すべてのユーザーをアプリケーションにプロビジョニングする必要があります。 Tangoe のカスタマー サポート担当者と連携し、すべてのユーザーをアプリケーションにプロビジョニングしてください。 

> [!NOTE]
> ユーザーを手動で作成する必要がある場合、またはユーザーのバッチを作成する必要がある場合は、Tangoe Command Premium Mobile のサポート チームにお問い合わせください。
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Tangoe Command Premium Mobile へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Tangoe Command Premium Mobile に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

 ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Tangoe Command Premium Mobile]**を選択します。

 ![[シングル サインオンの構成]](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_50.png) 
 
3. 上部のメニューで **[ユーザー]**をクリックします。

 ![ユーザーの割り当て][203] 

4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。

![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Tangoe Command Premium Mobile] タイルをクリックすると、自動的に Tangoe Command Premium Mobile アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_205.png

