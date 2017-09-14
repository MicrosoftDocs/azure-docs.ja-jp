---
title: "チュートリアル: Azure Active Directory と Sprinklr の統合 | Microsoft Docs"
description: "Azure Active Directory と Sprinklr の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 6e1622cd55e3b0e8063604ac9dc0cb0673fa9753
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>チュートリアル: Azure Active Directory と Sprinklr の統合

このチュートリアルでは、Sprinklr と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sprinklr と Azure AD の統合には、次の利点があります。

- Sprinklr にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Sprinklr にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Sprinklr の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Sprinklr でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Sprinklr の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sprinklr-from-the-gallery"></a>ギャラリーからの Sprinklr の追加
Azure AD への Sprinklr の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sprinklr を追加する必要があります。

**ギャラリーから Sprinklr を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Sprinklr**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_search.png)

5. 結果ウィンドウで **[Sprinklr]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Sprinklr で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Sprinklr ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Sprinklr の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sprinklr で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Sprinklr で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Sprinklr テスト ユーザーの作成](#creating-a-sprinklr-test-user)** - Sprinklr で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Sprinklr アプリケーションでシングル サインオンを構成します。

**Sprinklr で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Sprinklr** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

3. **[Sprinklr のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.sprinklr.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.sprinklr.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子で値を更新してください。 これらの値を取得するには、[Sprinklr クライアント サポート チーム](https://www.sprinklr.com/contact-us/)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sprinklr-tutorial/tutorial_general_400.png)

6. **[Sprinklr 構成]** セクションで、**[Sprinklr の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

7. 別の Web ブラウザー ウィンドウで、Sprinklr 企業サイトに管理者としてログインします。

8. **[管理] \> [設定]** の順に選択します。
   
    ![Administration](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "Administration")

9. 左側のパネルで、**[Manage Partner] \> [Single Sign on]** の順に選択します。
   
    ![Manage Partner](./media/active-directory-saas-sprinklr-tutorial/ic782908.png "Manage Partner")

10. **[+Add Single Sign Ons]**をクリックします。
   
    ![Single Sign-Ons](./media/active-directory-saas-sprinklr-tutorial/ic782909.png "Single Sign-Ons")

11. **[Single Sign on]** ページで、次の手順に従います。
   
    ![Single Sign-Ons](./media/active-directory-saas-sprinklr-tutorial/ic782910.png "Single Sign-Ons")

    a. **[Name]\(名前\)** ボックスに、構成の名前を入力します (例: *WAADSSOTest*)。

    b. **[Enabled]**を選択します。

    c. **[Use new SSO Certificate]**を選択します。
             
    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    f. Azure Portal からコピーした **SAML エンティティ ID** 値を **[Entity ID]\(エンティティ ID\)** ボックスに貼り付けます。

    g. Azure Portal からコピーした **SAML シングル サインオン サービスの URL** を **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** ボックスに貼り付けます。

    h. Azure Portal からコピーした **サインアウト URL** を **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** ボックスに貼り付けます。
     
    i. **[SAML User ID Type]** として**[Assertion contains User”s sprinklr.com username]** を選択します。

    j. **[SAML User ID Location]** として **[User ID is in the Name Identifier element of the Subject statement]** を選択します。

    k. [ **Save**] をクリックします。
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-sprinklr-test-user"></a>Sprinklr テスト ユーザーの作成

1. Sprinklr 企業サイトに管理者としてログインします。

2. **[管理] \> [設定]** の順に選択します。
   
    ![Administration](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "Administration")

3. 左側のパネルで、**[クライアントの管理] \> [ユーザー]** の順に選択します。
   
    ![設定](./media/active-directory-saas-sprinklr-tutorial/ic782914.png "Settings")

4. **[ユーザーの追加]**をクリックします。
   
    ![設定](./media/active-directory-saas-sprinklr-tutorial/ic782915.png "Settings")

5. **[Edit user]** ダイアログで、次の手順に従います。
   
    ![Edit user](./media/active-directory-saas-sprinklr-tutorial/ic782916.png "Edit user") 

    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Email]**、**[First Name]**、および **[Last Name]** テキスト ボックスに、プロビジョニングする Azure AD のユーザー アカウントの情報を入力します。

    b. **[Password Disabled]**を選択します。

    c. **[Language]\(言語\)** を選択します。

    d. **[User Type]\(ユーザー タイプ\)** を選択します。

    e. **[Update]**をクリックします。
   
     >[!IMPORTANT]
     >**[[Password Disabled]]** を選択する必要があります。 
     
6. **[Role]**に移動して、次の手順に従います。
   
    ![Partner Roles](./media/active-directory-saas-sprinklr-tutorial/ic782917.png "Partner Roles")

    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Global]** ボックスの一覧から、**[ALL\_Permissions]** を選択します。  

    b. **[Update]**をクリックします。

>[!NOTE]
>Sprinklr から提供されている他の Sprinklr ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sprinklr へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Sprinklr に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Sprinklr]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Sprinklr] タイルをクリックすると、自動的に Sprinklr アプリケーションにサインオンします。アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_203.png


