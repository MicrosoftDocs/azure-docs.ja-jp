---
title: "チュートリアル: Azure Active Directory と Jobscience の統合 | Microsoft Docs"
description: "Azure Active Directory と Jobscience の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 66bec35a8f17482433dbf02827b90620d1cff378
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>チュートリアル: Azure Active Directory と Jobscience の統合

このチュートリアルでは、Jobscience と Azure Active Directory (Azure AD) を統合する方法について説明します。

Jobscience と Azure AD の統合には、次の利点があります。

- Jobscience にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Jobscience にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Jobscience の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Jobscience でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Jobscience の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-jobscience-from-the-gallery"></a>ギャラリーからの Jobscience の追加
Azure AD への Jobscience の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Jobscience を追加する必要があります。

**ギャラリーから Jobscience を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Jobscience**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_search.png)

5. 結果ウィンドウで **Jobscience** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Jobscience で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Jobscience ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Jobscience の関連ユーザーの間で、リンク関係が確立されている必要があります。

Jobscience で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Jobscience で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Jobscience テスト ユーザーの作成](#creating-a-jobscience-test-user)** - Jobscience で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Jobscience アプリケーションでシングル サインオンを構成します。

**Jobscience で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Jobscience** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_samlbase.png)

3. **[Jobscience のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_url.png)

    **[サインオン URL]** ボックスに、`http://<company name>.my.salesforce.com` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値は、[Jobscience クライアント サポート チーム](https://www.jobscience.com/support)から、または後で作成する SSO プロファイルから取得します。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jobscience-tutorial/tutorial_general_400.png)

6. **[Jobscience 構成]** セクションで、**[Jobscience の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_configure.png) 

7. Jobscience の企業サイトに管理者としてログインします。

8. **[セットアップ]**に移動します。
   
   ![Setup](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")

9. 左側のナビゲーション ウィンドウの **[管理]** セクションで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。 
   
   ![My Domain](./media/active-directory-saas-jobscience-tutorial/ic767825.png "My Domain")

10. ドメインが正しく設定されていることを確認するには、“**ステップ 4 ユーザーへのデプロイ**” で “**マイ ドメインの設定**” を確認します。

    ![ユーザーにデプロイされたドメイン](./media/active-directory-saas-jobscience-tutorial/ic784377.png "ユーザーにデプロイされたドメイン")

11. Jobscience の企業サイトで、**[セキュリティ コントロール]** をクリックしてから、**[シングル サインオンの設定]** をクリックします。
    
    ![Security Controls](./media/active-directory-saas-jobscience-tutorial/ic784364.png "Security Controls")

12. **[シングル サインオンの設定]** セクションで、次の手順に従います。
    
    ![Single Sign-On Settings](./media/active-directory-saas-jobscience-tutorial/ic781026.png "Single Sign-On Settings")
    
    a. **[SAML Enabled]**を選択します。

    b. **[新規]**をクリックします。

13. **[SAML シングル サインオン設定の編集]** ページで、次の手順に従います。
    
    ![SAML Single Sign-On Setting](./media/active-directory-saas-jobscience-tutorial/ic784365.png "SAML Single Sign-On Setting")
    
    a. **[名前]** テキスト ボックスに、構成の名前を入力します。

    b. **[Issuer]\(発行者\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c. **[Entity Id]\(エンティティ ID\)** ボックスに、「`https://salesforce-jobscience.com`」と入力します。

    d. **[参照]** をクリックして、Azure AD の証明書をアップロードします。

    e. **[SAML ID の種類]** として、**[アサーションにはユーザー オブジェクトからのフェデレーション ID が含まれます]** を選択します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[SAML ID の場所]** として、**[ID を Subject ステートメントの NameIdentifier 要素にする]** チェックボックスをオンにします。

    g. **[Identity Provider Login URL]\(ID プロバイダー ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    h. **[Identity Provider Logout URL]\(ID プロバイダー ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

    i. [ **Save**] をクリックします。

14. 左側のナビゲーション ウィンドウの **[管理]** セクションで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。 
    
    ![My Domain](./media/active-directory-saas-jobscience-tutorial/ic767825.png "My Domain")

15. **[マイ ドメイン]** ページの **[Login Page Branding (ログイン ページのブランド)]** をクリックして、**[編集]** をクリックします。
    
    ![Login Page Branding](./media/active-directory-saas-jobscience-tutorial/ic767826.png "Login Page Branding")

16. **[Login Page Branding (ログイン ページのブランド)]** ページの **[認証サービス]** セクションに、**[SAML SSO 設定]** の名前が表示されます。 それを選んで、 **[保存]**をクリックします。
    
    ![Login Page Branding](./media/active-directory-saas-jobscience-tutorial/ic784366.png "Login Page Branding")

17. SP によって開始されるシングル サインオンのログイン URL を取得するには、**[Security Controls (セキュリティ コントロール)**] メニュー セクションの **[シングル サインオンの設定]** をクリックします。

    ![Security Controls](./media/active-directory-saas-jobscience-tutorial/ic784368.png "Security Controls")
    
    前の手順で作成した SSO プロファイルをクリックします。 このページに、会社のシングル サインオン URL が表示されます (例: [https://companyname.my.salesforce.com?so=companyid](https://companyname.my.salesforce.com?so=companyid))。    

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jobscience-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jobscience-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jobscience-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-jobscience-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-jobscience-test-user"></a>Jobscience テスト ユーザーの作成

Azure AD ユーザーが Jobscience にログインできるようにするには、そのユーザーを Jobscience にプロビジョニングする必要があります。 Jobscience の場合、プロビジョニングは手動で行います。

>[!NOTE]
>他の Jobscience ユーザー アカウント作成ツールや Jobscience から提供されている API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。
>  
        
**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Jobscience** の企業サイトに管理者としてログインします。

2. [セットアップ] に移動します。
   
   ![Setup](./media/active-directory-saas-jobscience-tutorial/ic784358.png "Setup")
3. **[ユーザーの管理]\>[ユーザー]** に移動します。
   
   ![ユーザー](./media/active-directory-saas-jobscience-tutorial/ic784369.png "Users")
4. **[新しいユーザー]**をクリックします。
   
   ![All Users](./media/active-directory-saas-jobscience-tutorial/ic784370.png "All Users")
5. **[ユーザーの編集]** ダイアログで、次の手順に従います。
   
   ![User Edit](./media/active-directory-saas-jobscience-tutorial/ic784371.png "User Edit")
   
   a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では Britta)。
   
   b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では Simon)。
   
   c. **[Alias]\(別名\)** ボックスに、ユーザーの別名を入力します (この例では brittas)。

   d. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

   e. **[User Name]\(ユーザー名\)** ボックスに、ユーザーのユーザー名 (Brittasimon@contoso.com など) を入力します。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Nick Name]\(ニック ネーム\)** ボックスに、ユーザーのニック ネーム (Simon など) を入力します。

   g. [ **Save**] をクリックします。

    
> [!NOTE]
> Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Jobscience へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Jobscience に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Jobscience]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Jobscience] タイルをクリックすると、自動的に Jobscience アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_203.png

