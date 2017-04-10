---
title: "チュートリアル: Azure Active Directory と Recognize の統合 | Microsoft Docs"
description: "Azure Active Directory と Recognize の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a2ce8c3a6aad76ca491ca17beab8555ad77a1142
ms.openlocfilehash: 3bfb340639674406a0a983106b56ca4a993aaf14
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>チュートリアル: Azure Active Directory と Recognize の統合
このチュートリアルの目的は、Recognize と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Recognize と Azure AD の統合には、次の利点があります。

* Recognize にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Recognize にシングル サインオンできるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と Recognize の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Recognize でのシングル サインオン (SSO) が有効なサブスクリプション

 >[!NOTE]
 >このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
 > 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD の SSO をテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Recognize の追加
2. Azure AD SSO の構成とテスト

## <a name="adding-recognize-from-the-gallery"></a>ギャラリーからの Recognize の追加
Azure AD への Recognize の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Recognize を追加する必要があります。

**ギャラリーから Recognize を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**Recognize**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)
7. 結果ウィンドウで **[Recognize]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ギャラリーでアプリを選択する](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Recognize で Azure AD の SSO を構成し、テストする方法について説明することです。

SSO を機能させるには、Azure AD ユーザーに対応する Recognize ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Recognize の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Recognize の **[Username (ユーザー名)]** の値として割り当てます。

Recognize で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Recognize テスト ユーザーの作成](#creating-a-recognize-test-user)** - Recognize で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションでは、クラシック ポータルで Azure AD の SSO を有効にして、Recognize アプリケーションで SSO を構成します。

**Recognize で Azure AD SSO を構成するには、次の手順に従います。**

1. クラシック ポータルの **Recognize** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Recognize へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)
  1. **[サインオン URL]** ボックスに、`https://recognizeapp.com/<your-domain>/saml/sso` のパターンを使用して URL を入力します。 
  2. **[識別子]** ボックスに、`https://recognizeapp.com/<your-domain>/saml/metadata` のパターンを使用して URL を入力します。
  3. **[次へ]**をクリックします。
  
    >[!NOTE]
    >これらの URL がわからない場合は、サンプル URL をサンプル パターンで入力します。 これらの値を取得するには、手順 9 で詳細を参照するか、Recognize サポート チーム<mailto:support@recognizeapp.com>にお問い合わせください。
    >

4. **[Recognize でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)
5. 別の Web ブラウザーのウィンドウで、管理者として Recognize テナントにサインオンします。
6. 右上にある **[Menu (メニュー)]** をクリックします。 **[Company Admin (会社の管理者)]** に移動します。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)
7. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]**をクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)
8. **[SSO Settings (SSO 設定)]** セクションで、次の手順に従います。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
 1. **[Enable SSO (SSO を有効にする)]** を**[ON]** にします。
 2. **[IDP Entity ID (IDP エンティティ ID)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[発行者の URL]** の値を入力します。 
 3. **[Sso target url (SSO ターゲット URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[シングル サインオン サービス URL]** の値を入力します。 
 4. **[Slo target url (SLO ターゲット URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[シングル サインアウト サービス URL]** の値を入力します。 
 5. ダウンロードした証明書をメモ帳で開き、その内容をクリップボードにコピーし、**[Certificate (証明書)]** テキストボックスに貼り付けます。  
 6. **[Save settings (設定の保存)]** ボタンをクリックします。 
9. **[SSO Settings (SSO 設定)]** セクションに、**Service Provider Metadata url** の URL をコピーします。
   
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)
10. 空白のブラウザーで**メタデータ URL リンク**を開き、メタデータ ドキュメントをダウンロードします。 **[Configure App Settings (アプリの設定の構成)]** ダイアログの **[Identifier (識別子)]** として、Recognize によって提供された EntityDescriptor 値を使用します。
    
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)
11. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
    
    ![Azure AD のシングル サインオン][10]
12. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)
 1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。  
 2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。 
 3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png) 
 1. **[名]** ボックスに「**Britta**」と入力します。   
 2. **[姓]** ボックスに「**Simon**」と入力します。 
 3. **[表示名]** ボックスに「**Britta Simon**」と入力します。 
 4. **[ロール]** 一覧で **[ユーザー]** を選択します。 
 5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png) 
 1. **[新しいパスワード]** の値を書き留めます。 
 2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-recognize-test-user"></a>Recognize テスト ユーザーの作成
Azure AD ユーザーが Recognize にログインできるようにするには、そのユーザーを Recognize にプロビジョニングする必要があります。 Recognize の場合、プロビジョニングは手動で行います。

このアプリは SCIM プロビジョニングをサポートしていませんが、ユーザーをプロビジョニングする代替ユーザー同期があります。 

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Recognize 企業サイトに管理者としてログインします。
2. 右上にある **[Menu (メニュー)]** をクリックします。 **[Company Admin (会社の管理者)]** に移動します。
3. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]**をクリックします。
4. **[User Sync (ユーザー同期)]** セクションで、次の手順に従います。
   
   ![New User](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "New User")   
 1. **[Sync Enabled (同期の有効化)]** で **[ON]** を選択します。 
 2. **[Choose sync provider (同期プロバイダーの選択)]** で、**[Microsoft / Office 365]** を選択します。 
 3. **[Run User Sync (ユーザー同期の実行)]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に Recognize へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][200]

**Recognize に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201]
2. アプリケーションの一覧で **[Recognize]** を選択します。
   
    ![Configure Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Recognize] タイルをクリックすると、自動的に Recognize アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png

