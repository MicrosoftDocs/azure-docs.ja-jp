---
title: "チュートリアル: Azure Active Directory と Syncplicity の統合 | Microsoft Docs"
description: "Azure Active Directory と Syncplicity の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 1321fa71bcd625d6ea754432bfb402d3919e38f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>チュートリアル: Azure Active Directory と Syncplicity の統合

このチュートリアルでは、Syncplicity と Azure Active Directory (Azure AD) を統合する方法について説明します。

Syncplicity と Azure AD の統合には、次の利点があります。

- Syncplicity にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Syncplicity にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Syncplicity の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Syncplicity でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Syncplicity の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-syncplicity-from-the-gallery"></a>ギャラリーからの Syncplicity の追加
Azure AD への Syncplicity の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Syncplicity を追加する必要があります。

**ギャラリーから Syncplicity を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Syncplicity**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_search.png)

5. 結果ウィンドウで **[Syncplicity]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Syncplicity で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Syncplicity ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Syncplicity の関連ユーザーの間で、リンク関係が確立されている必要があります。

Syncplicity で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Syncplicity で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Syncplicity テスト ユーザーの作成](#creating-a-syncplicity-test-user)** - Syncplicity で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Syncplicity アプリケーションでシングル サインオンを構成します。

**Syncplicity で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Syncplicity** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. **[Syncplicity のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.syncplicity.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.syncplicity.com/sp` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Syncplicity クライアント サポート チーム](https://www.syncplicity.com/contact-us)に問い合わせてください。 
 

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-syncplicity-tutorial/tutorial_general_400.png)

6. **[Syncplicity 構成]** セクションで、**[Syncplicity の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. **Syncplicity** テナントにサインインします。

8. 上部のメニューで **[admin]** をクリックし、**[settings]** を選択し、**[Custom domain and single sign-on]** をクリックします。
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769545.png "Syncplicity")

9. **[Single Sign-On (SSO)]** ダイアログ ページで、次の手順に従います。
   
    ![Single Sign-On \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Custom Domain]** テキスト ボックスに、ドメインの名前を入力します。
  
    b. **[Single Sign-On Status]** として **[Enabled]** を選択します。

    c. **[Entity Id]\(エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    d. **[Sign-in page URL]\(サインイン ページ URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    e. **[Logout page URL]\(ログアウト ページ URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** を貼り付けます。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Identity Provider Certificate]\(ID プロバイダー証明書\)** で **[Choose file]\(ファイルの選択\)** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。 

    g. **[SAVE CHANGES]\(変更の保存\)** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-syncplicity-test-user"></a>Syncplicity テスト ユーザーの作成
AAD ユーザーがサインインできるようにするには、ユーザーを Syncplicity アプリケーションにプロビジョニングする必要があります。 このセクションでは、Syncplicity で AAD ユーザー アカウントを作成する方法について説明します。

**ユーザー アカウントを Syncplicity にプロビジョニングするには、次の手順に従います。**

1. **Syncplicity** テナント (例: `https://company.Syncplicity.com`) にログインします。

2. **[admin]\(管理\)** をクリックし、**[user accounts]\(ユーザー アカウント\)** を選択します。

3. **[ADD A USER]\(ユーザーの追加\)** をクリックします。
   
    ![Manage Users](./media/active-directory-saas-syncplicity-tutorial/ic769764.png "Manage Users")

4. プロビジョニングする AAD アカウントの**電子メール アドレス**を入力し、**[Role]\(ロール\)** として **[User]\(ユーザー\)** を選択し、**[NEXT]\(次へ\)** をクリックします。
   
    ![Account Information](./media/active-directory-saas-syncplicity-tutorial/ic769765.png "Account Information")
   
    >[!NOTE]
    >AAD アカウントの所有者にアカウントの確認およびアクティブ化用のリンクを含む電子メールが送信されます。 
    > 

5. 新しいユーザーがそのメンバーになる社内のグループを選択し、**[NEXT]\(次へ\)** をクリックします。
   
    ![Group Membership](./media/active-directory-saas-syncplicity-tutorial/ic769772.png "Group Membership")
   
    >[!NOTE]
    >表示されるグループがない場合は、**[NEXT]\(次へ\)** をクリックします。 
    > 

6. ユーザーのコンピューターで Syncplicity の制御下に置くフォルダーを選択し、**[NEXT]\(次へ\)** をクリックします。
   
    ![Syncplicity Folders](./media/active-directory-saas-syncplicity-tutorial/ic769773.png "Syncplicity Folders")

>[!NOTE]
>Syncplicity から提供されている他の Syncplicity ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Syncplicity へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Syncplicity に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Syncplicity]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Syncplicity] タイルをクリックすると、Syncplicity アプリケーションに自動的にサインオンします。
## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_203.png

