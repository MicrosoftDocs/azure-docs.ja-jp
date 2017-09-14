---
title: "チュートリアル: Azure Active Directory と Kantega SSO for JIRA の統合 | Microsoft Docs"
description: "Azure Active Directory と Kantega SSO for JIRA の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 06a1d301818f025270137f7eaa9f40e5e4503112
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>チュートリアル: Azure Active Directory と Kantega SSO for JIRA の統合

このチュートリアルでは、Kantega SSO for JIRA と Azure Active Directory (Azure AD) を統合する方法について説明します。

Kantega SSO for JIRA と Azure AD を統合すると、次の利点が得られます。

- Kantega SSO for JIRA にアクセス可能な Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Kantega SSO for JIRA にシングル サインオン (Single Sign-On) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Kantega SSO for JIRA と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Kantega SSO for JIRA でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Kantega SSO for JIRA の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>ギャラリーからの Kantega SSO for JIRA の追加
Azure AD への Kantega SSO for JIRA の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kantega SSO for JIRA を追加する必要があります。

**ギャラリーから Kantega SSO for JIRA を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Kantega SSO for JIRA**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_search.png)

5. 結果ウィンドウで **[Kantega SSO for JIRA]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーを基に、Kantega SSO for JIRA で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Kantega SSO for JIRA ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Kantega SSO for JIRA の関連ユーザーの間で、リンク関係が確立されている必要があります。

Kantega SSO for JIRA で、Azure AD での **[ユーザー名]** の値を **[Username]** の値として割り当てることによってリンク関係を確立します。

Kantega SSO for JIRA で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Kantega SSO for JIRA テスト ユーザーの作成](#creating-a-kantega-sso-for-jira-test-user)** - Kantega SSO for JIRA で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Kantega SSO for JIRA アプリケーションでシングル サインオンを構成します。

**Kantega SSO for JIRA で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Kantega SSO for JIRA** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_samlbase.png)

3. **IDP** 開始モードで、**[Kantega SSO for JIRA Domain and URLs]\(Kantega SSO for JIRA のドメインと URL\)** セクションで次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_url1.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` のパターンを使用して URL を入力します。

4. **SP** 開始モードで、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_url2.png)

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は JIRA プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_400.png)
    
7. 別の Web ブラウザー ウィンドウで、オンプレミス サーバーの JIRA に管理者としてログインします。

8. 歯車をポイントし、**[Add-ons]\(アドオン\)** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon1.png)

9. [Add-ons]\(アドオン\) タブ セクションで、**[Find new add-ons]\(新しいアドオンの検索\)** をクリックします。 **[Kantega SSO for JIRA (SAML & Kerberos)]** を検索し、**[インストール]** をクリックして、新しい SAML プラグインをインストールします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon2.png)

10. プラグインのインストールが開始されます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon3.png)

11. インストールが完了したら、 **[閉じる]**をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon33.png)

12. **[管理]**をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon34.png)
    
13. 新しいプラグインが **[統合]** の下に一覧表示されます。 **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon35.png)

14. **[SAML]** セクションに移動します。 **[ID プロバイダーの追加]** ボックスで **[Azure Active Directory (Azure AD)]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon4.png)

15. サブスクリプション レベルは **[Basic]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon5.png)     

16. **[アプリケーションのプロパティ]** セクションで、次の手順を実行します。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon6.png)

    a. **[アプリケーション ID/URI]** の値をコピーして、Azure Portal の **[Kantega SSO for JIRA Domain and URLs]\(Kantega SSO for JIRA のドメインと URL\)** セクションで**識別子、応答 URL、サインオン URL** として使用します。

    b. **[次へ]** をクリックします。

17. **[Metadata import]\(メタデータのインポート\)** セクションで、次の手順を実行します。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon7.png)

    a. **ローカル コンピューターにあるメタデータ ファイル**を選択して、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    b. **[次へ]** をクリックします。

18. **[Name and SSO location]\(名前と SSO の場所\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon8.png)
    
    a. **[ID プロバイダー名]** ボックス (例: Azure AD) に、ID プロバイダーの名前を追加します。

    b. **[次へ]** をクリックします。

19. 署名証明書を確認し、**[次へ]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon9.png)

20. **[JIRA user accounts]\(JIRA ユーザー アカウント\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon10.png)

    a. **[Create users in JIRA's internal Directory if needed]\(必要に応じて JIRA の内部ディレクトリにユーザーを作成する\)** を選択して、ユーザー グループの適切な名前を入力します (グループはコンマで区切られた複数の番号になる場合があります)。

    b. **[次へ]** をクリックします。

21. **[完了]**をクリックします。   

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon11.png)

22. **[Known domains for Azure AD]\(既知の Azure AD ドメイン\)** セクションで、次の手順を実行します。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/addon12.png)

    a. ページの左側のパネルにある **[Known domains]\(既知のドメイン\)** を選択します。

    b. **[Known domains]\(既知のドメイン\)** ボックスにドメイン名を入力します。

    c. [ **Save**] をクリックします。 

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある「**[Create]**」を参照してください。
 
### <a name="creating-a-kantega-sso-for-jira-test-user"></a>Kantega SSO for JIRA テスト ユーザーの作成

Azure AD ユーザーが JIRA にログインできるようにするには、そのユーザーを JIRA にプロビジョニングする必要があります。 Kantega SSO for JIRA では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として、オンプレミス サーバーの JIRA にログインします。

2. 歯車をポイントし、**[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](./media/active-directory-saas-kantegassoforjira-tutorial/user1.png) 

3. **[ユーザー管理]** タブ セクションで、**[ユーザーの作成]** をクリックします。

    ![従業員の追加](./media/active-directory-saas-kantegassoforjira-tutorial/user2.png) 

4. **[Create new user]\(新しいユーザーの作成\)** ダイアログ ページで、以下の手順を実行します。

    ![従業員の追加](./media/active-directory-saas-kantegassoforjira-tutorial/user3.png) 

    a. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    e. **[Create user]\(ユーザーの作成\)** をクリックします。   

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Kantega SSO for JIRA へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Kantega SSO for JIRA に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Kantega SSO for JIRA]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Kantega SSO for JIRA のタイルをクリックすると、自動的に Kantega SSO for JIRA アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_203.png


