---
title: "チュートリアル: Azure Active Directory と Hosted Graphite の統合 | Microsoft Docs"
description: "Azure Active Directory と Hosted Graphite の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cc0992476d89b86b5848e9ae0bacb36b20f510fa
ms.openlocfilehash: be29ac00d9f6e9dcdfd2ca3df5c2c29a293a7ee6
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>チュートリアル: Azure Active Directory と Hosted Graphite の統合
このチュートリアルの目的は、Hosted Graphite と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Hosted Graphite と Azure AD の統合には、次の利点があります。

* Hosted Graphite にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで自動的に Hosted Graphite にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Hosted Graphite の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Hosted Graphite での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Hosted Graphite の追加
2. Azure AD SSO の構成とテスト

## <a name="add-hosted-graphite-from-the-gallery"></a>ギャラリーからの Hosted Graphite の追加
Azure AD への Hosted Graphite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Hosted Graphite を追加する必要があります。

**ギャラリーから Hosted Graphite を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「 **Hosted Graphite**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_01.png)
7. 結果ウィンドウで **[Hosted Graphite]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ギャラリーでアプリを選択する](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Hosted Graphite で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Hosted Graphite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Hosted Graphite の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Hosted Graphite の **[Username (ユーザー名)]** の値として割り当てます。

Hosted Graphite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Hosted Graphite テスト ユーザーの作成](#creating-a-hosted-graphite-test-user)** - Hosted Graphite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO の構成
このセクションでは、クラシック ポータルで Azure AD の SSO を有効にして、Hosted Graphite アプリケーションで SSO を構成します。

**Hosted Graphite で Azure AD SSO を構成するには、次の手順に従います。**

1. クラシック ポータルの **Hosted Graphite** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Hosted Graphite へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_03.png)
3. **[アプリケーション設定の構成]** ダイアログ ページで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_04.png)
  1. **[識別子]** ボックスに、`https://www.hostedgraphite.com/metadata/<user id>` の形式で URL を入力します。
  2. **[応答 URL]** ボックスに、`https://www.hostedgraphite.com/complete/saml/<user id>` のパターンを使用して URL を入力します。
  3. **[次へ]**をクリックします。
4. **[アプリケーション設定の構成]** ダイアログ ページで、**SP 開始モード**でアプリケーションを構成する場合は、**[詳細設定を表示します (オプション)]** をクリックし、**サインオン URL** を入力して、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
  1. **[サインオン URL]** ボックスに、`https://www.hostedgraphite.com/login/saml/<user id>/` のパターンを使用して URL を入力します。
  2. **[次へ]**をクリックします。
   
   >[!NOTE]
   >これは実際の値ではないので注意してください。 実際のサインオン URL、識別子、および応答 URL で値を更新する必要があります。 これらの値を取得するには、アプリケーション側で [Access (アクセス)]、[SAML setup (SAML のセットアップ)] と移動するか、Hosted Graphite に問い合わせます。
   >  
5. **[Hosted Graphite でのシングル サインオンの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
 ![[シングル サインオンの構成]](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_05.png)   
 1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
 2. **[次へ]**をクリックします。
6. Hosted Graphite テナントに管理者としてサインオンします。
7. サイド バーの **SAML のセットアップ ページ**に移動します (**[Access (アクセス)]、[SAML Setup (SAML のセットアップ)]** の順に移動)。
   
 ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)
8. これらの URI が手順 3 の構成と一致することを確認します。
   
 ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)
9. Hosted Graphite の **[Entity or Issuer ID (エンティティまたは発行者の ID)]** と **[SSO Login URL (SSO ログイン URL)]** に、Azure AD の **[発行者の URL]** と **[SAML SSO URL]** をコピーします。
   
 ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   
 ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_003.png)
10. **[Default User Role (既定のユーザー ロール)]** として **[Read-only (読み取り専用)]** を選択します。
    
 ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)
11. ダウンロードした証明書ファイルのコンテンツをコピーし、 **[X.509 証明書]** テキストボックスに貼り付けます。
    
 ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)
12. **[保存]** ボタンをクリックします。
13. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
    
 ![Azure AD のシングル サインオン][10]
14. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
 ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_05.png)
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。  
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_06.png) 
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_08.png) 
 1. **[新しいパスワード]** の値を書き留めます。  
 2. **[完了]** をクリックします。   
 
### <a name="create-a-hosted-graphite-test-user"></a>Hosted Graphite テスト ユーザーの作成
このセクションの目的は、Hosted Graphite で Britta Simon というユーザーを作成することです。 Hosted Graphite では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない Hosted Graphite ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、Hosted Graphite のサポート チーム (<mailto:help@hostedgraphite.com>) にお問い合わせください。 
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Hosted Graphite へのアクセスを許可し、このユーザーが Azure の SSO を使用できるようにすることです。

![ユーザーの割り当て][200]

**Hosted Graphite に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[Hosted Graphite]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_50.png)
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Hosted Graphite] タイルをクリックすると、自動的に Hosted Graphite アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_205.png

