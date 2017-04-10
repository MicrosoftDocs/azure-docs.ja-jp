---
title: "チュートリアル: Azure Active Directory と Asana の統合 | Microsoft Docs"
description: "Azure Active Directory と Asana の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 257c1032ed8f1347c17be7e51021b9284b73f4a2
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>チュートリアル: Azure Active Directory と Asana の統合
このチュートリアルでは、Asana と Azure Active Directory (Azure AD) を統合する方法について説明します。

Asana と Azure AD の統合には、次の利点があります。

* Asana にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントでに自動的 Asana にシングル サインオン (SSO) できるようにします
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Asana と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* **Asana** でのシングル サインオン (SSO) が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Asana の追加
2. Azure AD SSO の構成とテスト

## <a name="adding-asana-from-the-gallery"></a>ギャラリーからの Asana の追加
Azure AD への Asana の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Asana を追加する必要があります。

**ギャラリーから Asana を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに「 **Asana**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)
7. 結果ウィンドウで **[Asana]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Asana で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Asana ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Asana の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Asana の **[Username (ユーザー名)]** の値として割り当てます。

Asana で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Asana テスト ユーザーの作成](#creating-an-Asana-test-user)** - Asana で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成
このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Asana アプリケーションで SSO を構成することです。

**Asana で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. 上部のメニューで **[クイック スタート]**をクリックします。
   
    ![[シングル サインオンの構成]][6]
2. クラシック ポータルの **Asana** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][7] 
3. **[ユーザーの Asana へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)
4. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。 
   
    ![Configure Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)
  1. [サインオン URL] ボックスに、`https://app.asana.com` という形式で URL を入力します。
  2. **[次へ]**をクリックします。
5. **[Asana でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。 また、SAML SSO URL の値をコピーします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)
6. 証明書を右クリックし、メモ帳またはお好きなテキスト エディターを使って証明書ファイルを開きます。 証明書タイトルの内容を最初から最後までコピーします。 これが、SSO の構成に Asana で使用する X.509 証明書です。
7. 別のブラウザー ウィンドウで、Asana アプリケーションにサインオンします。 Asana で SSO を構成するために、画面の右上隅にあるワークスペース名をクリックして、ワークスペースの設定にアクセスします。 その後、**[\<your workspace name\> Settings (<ワークスペース名> 設定)]** をクリックします。 
   
    ![Configure Single Sign-On](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)
8. **[Organization settings (組織の設定)]** ウィンドウで **[Administration (管理)]** をクリックします。 その後、 **[Members must log in via SAML (メンバーは SAML でログインする必要がある)]** をクリックして SSO 構成を有効にします。 次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  
  1. **[Sign-in page URL (サインイン ページ URL)]** ボックスに、Azure AD からコピーした SAML サインオン URL を貼り付けます。
  2. **[X.509 Certificate (X.509 証明書)]** ボックスに、Azure AD からコピーした X.509 証明書を貼り付けます。
9. [ **Save**] をクリックします。 さらにサポートが必要な場合は、 [SSO の設定に関する Asana ガイド](https://asana.com/guide/help/premium/authentication#gl-saml) をご覧ください。
10. Azure AD の **[Asana でのシングル サインオンの構成]** ページに移動し、SSO の構成確認を選択して、**[次へ]** をクリックします。
   
    ![Azure AD のシングル サインオン][10]
11. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png) 
  1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
  2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
  3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png) 
  1. **[名]** ボックスに「**Britta**」と入力します。   
  2. **[姓]** ボックスに「**Simon**」と入力します。
  3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
  4. **[ロール]** 一覧で **[ユーザー]** を選択します。
  5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png) 
  1. **[新しいパスワード]** の値を書き留めます。 
  2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-an-asana-test-user"></a>Asana テスト ユーザーの作成
このセクションでは、Asana で Britta Simon というユーザーを作成します。

1. **Asana** の左側のパネルで、**[Teams (チーム)]** セクションに移動します。 プラス記号ボタンをクリックします。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 
2. 電子メール アドレス「 britta.simon@contoso.com 」をテキスト ボックスに入力し、 **[Invite (招待)]**を選択します。
3. **[Send Invite (招待の送信)]**をクリックします。 新しいユーザーの電子メール アカウントに電子メールが届きます。 新しいユーザーは、アカウントを作成、確認する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Asana へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][200] 

**Asana に Britta Simon を割り当てるには、次の手順を実行します。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Asana]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. [すべてのユーザー] の一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、Azure AD のシングル サインオンをテストすることです。

Asana のログイン ページに移動します。 電子メール アドレスのボックスに、電子メール アドレスとして「britta.simon@contoso.com」と入力します。 パスワード ボックスは空白のままにして **[Log In (ログイン)]** をクリックします。 Azure AD ログイン ページにリダイレクトされます。 Azure AD 資格情報をすべて入力します。 これで、Asana へのログインが完了しました。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png

