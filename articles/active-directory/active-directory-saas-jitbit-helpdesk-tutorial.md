---
title: "チュートリアル: Azure Active Directory と Jitbit Helpdesk の統合 | Microsoft Docs"
description: "Azure Active Directory と Jitbit Helpdesk の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 6523ee3179dafd79528093b856b0ec10fafb4f7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>チュートリアル: Azure Active Directory と Jitbit Helpdesk の統合

このチュートリアルでは、Jitbit Helpdesk と Azure Active Directory (Azure AD) を統合する方法について説明します。

Jitbit Helpdesk と Azure AD の統合には、次の利点があります。

- Jitbit Helpdesk にアクセスするユーザーを Azure AD で管理できます
- ユーザーが各自の Azure AD アカウントで Jitbit Helpdesk に自動的にサインオン (シングル サインオン) できるように、設定できます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Jitbit Helpdesk と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Jitbit Helpdesk でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Jitbit Helpdesk の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>ギャラリーからの Jitbit Helpdesk の追加
Azure AD への Jitbit Helpdesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Jitbit Helpdesk を追加する必要があります。

**ギャラリーから Jitbit Helpdesk を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Jitbit Helpdesk**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

5. 結果ウィンドウで **[Jitbit Helpdesk]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Jitbit Helpdesk で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Jitbit Helpdesk ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Jitbit Helpdesk の関連ユーザーの間で、リンク関係が確立されている必要があります。

Jitbit Helpdesk で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

Jitbit Helpdesk で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Jitbit Helpdesk テスト ユーザーの作成](#creating-a-jitbit-helpdesk-test-user)** - Jitbit Helpdesk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Jitbit Helpdesk アプリケーションにシングル サインオンを構成します。

**Jitbit Helpdesk で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Jitbit Helpdesk** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

3. **[Jitbit Helpdesk のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Jitbit Helpdesk クライアント サポート チーム](https://www.jitbit.com/support/)にお問い合わせください。 
    
    b.  **[識別子]** ボックスに、次のように URL を入力します。`https://www.jitbit.com/web-helpdesk/`

    
 


4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_400.png)

6. **[Jitbit Helpdesk Configuration]\(Jitbit Helpdesk の構成\)** セクションで、**[Configure Jitbit Helpdesk]\(Jitbit Helpdesk の構成\)** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

7. 別の Web ブラウザーのウィンドウで、Jitbit Helpdesk の企業サイトに管理者としてログインします。

8. 上部のツールバーで **[管理]**をクリックします。
   
    ![Administration](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "Administration")

9. **[全般設定]**をクリックします。
   
    ![[Users, companies, and permissions]\(ユーザー、企業、アクセス許可\)](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777680.png "[Users, companies, and permissions]\(ユーザー、企業、アクセス許可\)")

10. **[認証設定]** 構成セクションで、次の手順を実行します。
   
    ![Authentication settings](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777683.png "Authentication settings")
    
    a. **[Enable SAML 2.0 single sign on]\(SAML 2.0 シングル サインオンを有効にする\)** を選択して、**OneLogin** でシングル サインオン (SSO) を使用してサインインします。

    b. **[エンドポイント URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X.509 証明書]** ボックスに貼り付けます。

    d. **[変更を保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と名前を入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Jitbit Helpdesk のテスト ユーザーの作成

Azure AD ユーザーが Jitbit Helpdesk にログインできるようにするには、そのユーザーを Jitbit Helpdesk にプロビジョニングする必要があります。  Jitbit Helpdesk の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Jitbit Helpdesk** テナントにログインします。

2. 上部のメニューで **[管理]**をクリックします。
   
    ![Administration](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "Administration")

3. **[ユーザー、会社、およびアクセス許可]**をクリックします。
   
    ![[Users, companies, and permissions]\(ユーザー、企業、アクセス許可\)](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777682.png "[Users, companies, and permissions]\(ユーザー、企業、アクセス許可\)")

4. **[ユーザーの追加]**をクリックします。
   
    ![Add user](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777685.png "Add user")
   
5. [作成] セクションで、プロビジョニングする Azure AD アカウントのデータを次のように入力します。

    ![作成](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777686.png "作成")
   
   a. **[ユーザー名]** ボックスに、Azure Portal でのユーザー名「**BrittaSimon**」を入力します。

   b. **[電子メール]** ボックスに、ユーザーの電子メール (**BrittaSimon@contoso.com** など) を入力します。

   c. **[名]** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

   d. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。
   
   e. **Create** をクリックしてください。

>[!NOTE]
>Jitbit Helpdesk から提供されている他の Jitbit Helpdesk ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Jitbit Helpdesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Jitbit Helpdesk に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Jitbit Helpdesk]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Jitbit Helpdesk] タイルをクリックすると、Jitbit Helpdesk アプリケーションのログイン ページが表示されます。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_203.png

