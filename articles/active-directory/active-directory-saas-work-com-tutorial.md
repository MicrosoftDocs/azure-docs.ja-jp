---
title: "チュートリアル: Azure Active Directory と Work.com の統合 | Microsoft Docs"
description: "Azure Active Directory と Work.com の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 7cfec8e9ac12d43095483696a15c0580776d3114
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>チュートリアル: Azure Active Directory と Work.com の統合

このチュートリアルでは、Work.com と Azure Active Directory (Azure AD) を統合する方法について説明します。

Work.com と Azure AD の統合には、次の利点があります。

- Work.com にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Work.com に自動サインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Work.com と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Work.com でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Work.com の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-workcom-from-the-gallery"></a>ギャラリーからの Work.com の追加
Azure AD への Work.com の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Work.com を追加する必要があります。

**ギャラリーから Work.com を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Work.com**」と入力し、結果ウィンドウで **[Work.com]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![ギャラリーから追加する](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Work.com で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Work.com ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Work.com の関連ユーザーの間で、リンク関係が確立されている必要があります。

Work.com で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Work.com で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Work.com テスト ユーザーの作成](#create-a-workcom-test-user)** - Work.com で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Work.com アプリケーションでシングル サインオンを構成します。

>[!NOTE]
>シングル サインオンを構成する場合でも、カスタムの Work.com ドメイン名をセットアップする必要があります。 少なくともドメイン名を定義し、ドメイン名をテストしてから、組織全体にデプロイする必要があります。

**Work.com で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Work.com** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![SAML ベースのサインオン](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. **[Work.com のドメインと URL]** セクションで、次の手順を実行します。

    ![[Work.com のドメインと URL] セクション](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    **[サインオン URL]** ボックスに、`http://<companyname>.my.salesforce.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[Work.com クライアント サポート チーム](https://help.salesforce.com/articleView?id=000159855&type=3)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[SAML 署名証明書] セクション](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[保存] ボタン](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. **[Work.com 構成]** セクションで、**[Work.com の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[Work.com 構成] セクション](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. 管理者として Work.com テナントにログインします。

8. **[セットアップ]**に移動します。
   
    ![Setup](./media/active-directory-saas-work-com-tutorial/ic794108.png "Setup")

9. 左側のナビゲーション ウィンドウの **[管理]** セクションで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。 
   
    ![My Domain](./media/active-directory-saas-work-com-tutorial/ic767825.png "My Domain")

10. ドメインが正しく設定されていることを確認するには、“**ステップ 4 ユーザーへのデプロイ**” で “**マイ ドメインの設定**” を確認します。
   
    ![ユーザーにデプロイされたドメイン](./media/active-directory-saas-work-com-tutorial/ic784377.png "ユーザーにデプロイされたドメイン")

11. Work.com テナントにログインします。

12. **[セットアップ]**に移動します。
    
    ![Setup](./media/active-directory-saas-work-com-tutorial/ic794108.png "Setup")

13. **[セキュリティ制御]** メニューを展開表示し、**[シングル サインオンの設定]** をクリックします。
    
    ![Single Sign-On Settings](./media/active-directory-saas-work-com-tutorial/ic794113.png "Single Sign-On Settings")

14. **[シングル サインオンの設定]** ダイアログ ページで、次の手順を実行します。
    
    ![SAML 有効](./media/active-directory-saas-work-com-tutorial/ic781026.png "SAML Enabled")
    
    a. **[SAML Enabled]**を選択します。
    
    b. **[新規]**をクリックします。

15. **[SAML シングル サインオンの設定]** セクションで、次の手順に従います。
    
    ![SAML Single Sign-On Setting](./media/active-directory-saas-work-com-tutorial/ic794114.png "SAML Single Sign-On Setting")
    
    a. **[名前]** テキスト ボックスに、構成の名前を入力します。  
       
    > [!NOTE]
    > **[名前]** の値を指定すると、**[API 名]** ボックスが自動的に入力されます。
    
    b. **[Issuer]\(発行者\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
    
    c. **[Browse]\(参照\)** をクリックして、Azure Portal からダウンロードした証明書をアップロードします。
    
    d. **[Entity Id]\(エンティティ ID\)** ボックスに、「`https://salesforce-work.com`」と入力します。
    
    e. **[SAML ID の種類]** として、**[アサーションにはユーザー オブジェクトからのフェデレーション ID が含まれます]** を選択します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[SAML ID の場所]** として、**[ID を Subject ステートメントの NameIdentifier 要素にする]** チェックボックスをオンにします。
    
    g. **[Identity Provider Login URL]\(ID プロバイダー ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    h. **[Identity Provider Logout URL]\(ID プロバイダー ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。
    
    i. **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP POST]** を選択します。
    
    j. [ **Save**] をクリックします。

16. Work.com クラシック ポータルで、左側のナビゲーション ウィンドウの **[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。 
    
    ![My Domain](./media/active-directory-saas-work-com-tutorial/ic794115.png "My Domain")

17. **[マイ ドメイン]** ページの **[Login Page Branding (ログイン ページのブランド)]** をクリックして、**[編集]** をクリックします。
    
    ![Login Page Branding](./media/active-directory-saas-work-com-tutorial/ic767826.png "Login Page Branding")

14. **[Login Page Branding (ログイン ページのブランド)]** ページの **[認証サービス]** セクションに、**[SAML SSO 設定]** の名前が表示されます。 それを選んで、 **[保存]**をクリックします。
    
    ![Login Page Branding](./media/active-directory-saas-work-com-tutorial/ic784366.png "Login Page Branding")

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] -> [すべてのユーザー]](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Add](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ページ](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="create-a-workcom-test-user"></a>Work.com テスト ユーザーの作成
Azure Active Directory ユーザーがサインインできるように、Azure Active Directory ユーザーを Work.com に対してプロビジョニングする必要があります。Work.com の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Work.com 企業サイトに管理者としてサインオンします。

2. **[セットアップ]**に移動します。
   
    ![Setup](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
3. **[ユーザーの管理]\>[ユーザー]** に移動します。
   
    ![Manage Users](./media/active-directory-saas-work-com-tutorial/IC784369.png "Manage Users")

4. **[新しいユーザー]**をクリックします。
   
    ![All Users](./media/active-directory-saas-work-com-tutorial/IC794117.png "All Users")

5. [User Edit]\(ユーザーの編集\) セクションで、次の手順に従って、プロビジョニングする有効な Azure AD アカウントの属性を関連するボックスに入力します。
   
    ![User Edit](./media/active-directory-saas-work-com-tutorial/ic794118.png "User Edit")
   
    a. **[First Name]\(名\)** ボックスに、ユーザーの**名** (**Britta**) を入力します。
    
    b. **[Last Name]\(姓\)** ボックスに、ユーザーの**姓** (**Simon**) を入力します。
    
    c. **[Alias]\(エイリアス\)** ボックスに、ユーザーの**名前** (**BrittaS**) を入力します。
    
    d. **[Email]\(メール\)** ボックスに、ユーザーの**メール アドレス** (**Brittasimon@contoso.com**) を入力します。
    
    e. **[User Name]\(ユーザー名\)** ボックスに、ユーザーのユーザー名 (**Brittasimon@contoso.com** など) を入力します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Nick Name]\(ニック ネーム\)** ボックスに、ユーザーの**ニック ネーム** (**Simon**) を入力します。
    
    g. **[Role]\(役割\)**、**[User License]\(ユーザー ライセンス\)**、**[Profile]\(プロファイル\)** を選択します。
    
    h. [ **Save**] をクリックします。  
      
    > [!NOTE]
    > アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Work.com へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Work.com に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Work.com]** を選択します。

    ![アプリケーションの一覧の Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Work.com] タイルをクリックすると、自動的に Work.com アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png

