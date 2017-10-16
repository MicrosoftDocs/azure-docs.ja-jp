---
title: "チュートリアル: Azure Active Directory と SAML SSO for Confluence by resolution GmbH の統合 | Microsoft Docs"
description: "Azure Active Directory と SAML SSO for Confluence by resolution GmbH の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 9a36d686ba39b5168860a20e8c4db357888df6a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>チュートリアル: Azure Active Directory と SAML SSO for Confluence by resolution GmbH の統合

このチュートリアルでは、SAML SSO for Confluence by resolution GmbH と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAML SSO for Confluence by resolution GmbH と Azure AD の統合には、次の利点があります。

- Azure AD で、SAML SSO for Confluence by resolution GmbH にアクセスするユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで SAML SSO for Confluence by resolution GmbH に自動的にサインオン (シングル サインオン) するように設定できます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAML SSO for Confluence by resolution GmbH の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAML SSO for Confluence by resolution GmbH でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SAML SSO for Confluence by resolution GmbH の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>ギャラリーからの SAML SSO for Confluence by resolution GmbH の追加

Azure AD への SAML SSO for Confluence by resolution GmbH の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAML SSO for Confluence by resolution GmbH を追加する必要があります。

**ギャラリーから SAML SSO for Confluence by resolution GmbH を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**SAML SSO for Confluence by resolution GmbH**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_search.png)

5. 結果ウィンドウで **[SAML SSO for Confluence by resolution GmbH]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAML SSO for Confluence by resolution GmbH で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAML SSO for Confluence by resolution GmbH ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAML SSO for Confluence by resolution GmbH の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAML SSO for Confluence by resolution GmbH で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

SAML SSO for Confluence by resolution GmbH で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAML SSO for Confluence by resolution GmbH テスト ユーザーの作成](#creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user)** - SAML SSO for Confluence by resolution GmbH で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、SAML SSO for Confluence by resolution GmbH アプリケーションでシングル サインオンを構成します。

**SAML SSO for Confluence by resolution GmbH で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAML SSO for Confluence by resolution GmbH** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_samlbase.png)

3. **[SAML SSO for Confluence by resolution GmbH のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_1.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します。

4. **[詳細な URL 設定の表示]** をクリックします。 **SP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_2.png)

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SAML SSO for Confluence by resolution GmbH クライアント サポート チーム](https://www.resolution.de/go/support)に問い合わせます。 

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_400.png)  
    
7. 別の Web ブラウザー ウィンドウで、**SAML SSO for Confluence by resolution GmbH 管理者ポータル**に管理者としてログインします。

8. 歯車をポイントし、**[Add-ons]\(アドオン\)** をクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon1.png)

9. [Administrator Access]\(管理者アクセス\) のページにリダイレクトされます。 パスワードを入力し、**[Confirm]\(確認\)** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon2.png)

10. **[ATLASSIAN MARKETPLACE]** タブで、**[Find new add-ons]\(新しいアドオンの検索\)** をクリックします。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon.png)

11. **[SAML Single Sign On (SSO) for Confluence]\(Confluence の SAML シングル サインオン \(SSO\)\)** を検索し、**[Install]\(インストール\)** ボタンをクリックして、新しい SAML プラグインをインストールします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon7.png)

12. プラグインのインストールが開始されます。 **[閉じる]**をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon8.png)

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon9.png)

13. **[管理]**をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon10.png)
    
14. **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon11.png)

15. この新しいプラグインは、**[USERS & SECURITY]\(ユーザーとセキュリティ\)** タブにも表示されます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon3.png)
    
16. **[SAML SingleSignOn Plugin Configuration]\(SAML SingleSignOn プラグインの構成\)** ページで **[Add additional Identity Provider]\(その他の ID プロバイダーの追加\)** ボタンをクリックし、ID プロバイダーの設定を構成します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon4.png)

17. このページで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon5.png)
 
    a. ID プロバイダーの**名前** (たとえば Azure AD) を追加します。
    
    b. ID プロバイダーの**説明** (たとえば Azure AD) を追加します。

    c. **[XML]** をクリックし、Azure Portal からダウンロードした**メタデータ** ファイルを選択します。

    d. **[Load]\(読み込み\)** ボタンをクリックします。

    e. IdP メタデータが読み取られ、スクリーン ショットで強調表示されているフィールドが設定されます。 
18. **[Save settings]\(設定の保存\)** ボタンをクリックして、設定を保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/addon6.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>SAML SSO for Confluence by resolution GmbH のテスト ユーザーの作成

Azure AD ユーザーが SAML SSO for Confluence by resolution GmbH にログインできるようにするには、ユーザーを SAML SSO for Confluence by resolution GmbH にプロビジョニングする必要があります。  
SAML SSO for Confluence by resolution GmbH の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. SAML SSO for Confluence by resolution GmbH 企業サイトに管理者としてログインします。

2. 歯車をポイントし、**[User management]\(ユーザー管理\)** をクリックします。

    ![従業員の追加](./media/active-directory-saas-samlssoconfluence-tutorial/user1.png) 

3. [Users]\(ユーザー\) セクションで、**[Add users]\(ユーザーの追加\)** タブをクリックします。**[Add a User]\(ユーザーの追加\)** ダイアログ ページで、次の手順に従います。

    ![従業員の追加](./media/active-directory-saas-samlssoconfluence-tutorial/user2.png) 

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーのメール (Britta Simon など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、Britta Simon のパスワードを入力します。

    e. **[Confirm Password]\(パスワードの確認\)** をクリックし、パスワードを再入力します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[追加]** ボタンをクリックします。    

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAML SSO for Confluence by resolution GmbH へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を SAML SSO for Confluence by resolution GmbH に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SAML SSO for Confluence by resolution GmbH]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAML SSO for Confluence by resolution GmbH] タイルをクリックすると、SAML SSO for Confluence by resolution GmbH アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_203.png

