---
title: チュートリアル:Azure Active Directory と Kantega SSO for Bitbucket の統合 | Microsoft Docs
description: Azure Active Directory と Kantega SSO for Bitbucket の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7f24c211e057447782c7fab596c9be73106c552
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165300"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>チュートリアル:Azure Active Directory と Kantega SSO for Bitbucket の統合

このチュートリアルでは、Kantega SSO for Bitbucket と Azure Active Directory (Azure AD) を統合する方法について説明します。

Kantega SSO for Bitbucket と Azure AD を統合すると、次の利点が得られます。

- Kantega SSO for Bitbucket にアクセス可能な Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Kantega SSO for Bitbucket にシングル サインオン (Single Sign-On) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Kantega SSO for Bitbucket と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Kantega SSO for Bitbucket でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Kantega SSO for Bitbucket の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>ギャラリーからの Kantega SSO for Bitbucket の追加
Azure AD への Kantega SSO for Bitbucket の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kantega SSO for Bitbucket を追加する必要があります。

**ギャラリーから Kantega SSO for Bitbucket を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「**Kantega SSO for Bitbucket**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

1. 結果ウィンドウで **Kantega SSO for Bitbucket** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーを基に、Kantega SSO for Bitbucket で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Kantega SSO for Bitbucket ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Kantega SSO for Bitbucket の関連ユーザーの間で、リンク関係が確立されている必要があります。

Kantega SSO for Bitbucket で、Azure AD での **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当てることによってリンク関係を確立します。

Kantega SSO for Bitbucket で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Kantega SSO for Bitbucket テスト ユーザーの作成](#creating-a-kantega-sso-for-bitbucket-test-user)** - Kantega SSO for Bitbucket で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Kantega SSO for Bitbucket アプリケーションでシングル サインオンを構成します。

**Kantega SSO for Bitbucket で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Kantega SSO for Bitbucket** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

1. **IDP** 開始モードにして、**[Kantega SSO for Bitbucket のドメインと URL]** セクションで次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` のパターンを使用して URL を入力します。

1. **SP** 開始モードで、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は Bitbucket プラグインの構成中に受け取ります (これについてはこのチュートリアルの後半で説明します)。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザー ウィンドウで、Bitbucket 管理者ポータルに管理者としてログインします。

1. 歯車アイコンをクリックして、**[Find new add-ons]\(新しいアドオンの検出\)** をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. **Kantega SSO for Bitbucket SAML & Kerberos** を検索し、**[Install]\(インストール\)** ボタンをクリックして、新しい SAML プラグインをインストールします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. プラグインのインストールが開始されます。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. インストールが完了したら、 **[閉じる]** をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon33.png)

1.  **Manage**をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon34.png)
    
1. **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。 

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. **[SAML]** セクションに移動します。 **[Add identity provider]\(ID プロバイダーの追加\)** ボックスで **[Azure Active Directory (Azure AD)]** を選択します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. サブスクリプション レベルは **[Basic]** を選択します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. **[App properties]\(アプリのプロパティ\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. **[App ID URI]\(アプリの ID URI\)** の値をコピーして、Azure Portal の **[Kantega SSO for Bitbucket のドメインと URL]** セクションの**識別子、応答 URL、サインオン URL** として使います。

    b. **[次へ]** をクリックします。

1. **[Metadata import]\(メタデータのインポート\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. **[Metadata file on my computer]\(コンピューターにあるメタデータ ファイル\)** を選び、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    b. **[次へ]** をクリックします。

1. **[Name and SSO location]\(名前と SSO の場所\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. **[Identity provider name]\(ID プロバイダー名\)** ボックスに、ID プロバイダーの名前 (例: Azure AD) を追加します。

    b. **[次へ]** をクリックします。

1. 署名証明書を確認し、**[Next]\(次へ\)** をクリックします。   

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. **[Bitbucket user accounts]\(Bitbucket ユーザー アカウント\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. **[Create users in Bitbucket's internal Directory if needed]\(必要に応じて Bitbucket の内部ディレクトリにユーザーを作成する\)** を選択して、ユーザー グループの適切な名前を入力します (グループはコンマで区切られた複数の番号 になる場合があります)。

    b. **[次へ]** をクリックします。

1. **[完了]** をクリックします。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. **[Known domains for Azure AD]\(既知の Azure AD ドメイン\)** セクションで、次の手順を実行します。  

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. ページの左側のパネルにある **[Known domains]\(既知のドメイン\)** を選択します。

    b. **[Known domains]\(既知のドメイン\)** ボックスにドメイン名を入力します。

    c. **[Save]** をクリックします。  

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>Kantega SSO for Bitbucket テスト ユーザーの作成

Azure AD ユーザーが Bitbucket にログインできるようにするには、ユーザーを Bitbucket にプロビジョニングする必要があります。 Kantega SSO for Bitbucket では、手動でプロビジョニングします。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Bitbucket 企業サイトに管理者としてログインします。

1. 設定アイコンをクリックします。

    ![従業員の追加](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. **[Administration]\(管理\)** タブ セクションで、**[Users]\(ユーザー\)** をクリックします。

    ![従業員の追加](./media/kantegassoforbitbucket-tutorial/user2.png)

1. **[Create user]\(ユーザーの作成\)** をクリックします。

    ![従業員の追加](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. **[Create User]\(ユーザーの作成\)** ダイアログ ページで、以下の手順を実行します。

    ![従業員の追加](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーの電子メール (Brittasimon@contoso.com など) を入力します。
    
    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。
    
    c. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。  

    e. **[Confirm Password]\(パスワードの確認\)** ボックスに、ユーザーのパスワードを再入力します。

    f. **[Create user]\(ユーザーの作成\)** をクリックします。   

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Kantega SSO for Bitbucket へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Kantega SSO for Bitbucket に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[Kantega SSO for Bitbucket]** を選択します。

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Kantega SSO for Bitbucket のタイルをクリックすると、自動的に Kantega SSO for Bitbucket アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_203.png

