---
title: "チュートリアル: Azure Active Directory と Igloo Software の統合 | Microsoft Docs"
description: "Azure Active Directory と Igloo Software の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: ab3891e11eb33b4d233e4fc967a40c7df06e4f4e
ms.contentlocale: ja-jp
ms.lasthandoff: 06/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>チュートリアル: Azure Active Directory と Igloo Software の統合

このチュートリアルでは、Igloo Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

Igloo Software と Azure AD の統合には、次の利点があります。

- Igloo Software にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Igloo Software にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Igloo Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン対応の Igloo Software サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Igloo Software の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-igloo-software-from-the-gallery"></a>ギャラリーからの Igloo Software の追加
Azure AD への Igloo Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Igloo Software を追加する必要があります。

**ギャラリーから Igloo Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Igloo Software**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. 結果ウィンドウで **Igloo Software** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Igloo Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Igloo Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Igloo Software の関連ユーザーの間で、リンクの関係が確立されている必要があります。

Igloo Software で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Igloo Software で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Igloo Software のテスト ユーザーの作成](#creating-an-igloo-software-test-user)** - Igloo Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Igloo Software アプリケーションでシングル サインオンを構成します。

**Igloo Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Igloo Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. **[Igloo Software のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. **[サインオン URL]** ボックスに、`https://<company name>.igloocommmunities.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<company name>.igloocommmunities.com/saml.digest` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<company name>.igloocommmunities.com/saml.digest` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Igloo Software クライアント サポート チーム](https://www.igloosoftware.com/services/support)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-igloo-software-tutorial/tutorial_general_400.png)
    
6. **[Igloo Software 構成]** セクションで **[Igloo の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として Igloo Software 企業サイトにログインします。

8. **コントロール パネル**に移動します。
   
     ![コントロール パネル](./media/active-directory-saas-igloo-software-tutorial/ic799949.png "コントロール パネル")

9. **[Membership (メンバーシップ)]** タブで **[Sign In Settings (サインインの設定)]** をクリックします。
   
    ![サインインの設定](./media/active-directory-saas-igloo-software-tutorial/ic783968.png "サインインの設定")

10. SAML の構成のセクションで、 **[SAML 認証の構成]**をクリックします。
   
    ![SAML Configuration](./media/active-directory-saas-igloo-software-tutorial/ic783969.png "SAML Configuration")
   
11. **[全般構成]** セクションで、次の手順を実行します。
   
    ![全般構成](./media/active-directory-saas-igloo-software-tutorial/ic783970.png "全般構成")

    a. **[接続名]** テキストボックスに、構成のカスタム名を入力します。
   
    b. **[IdP Login URL]\(IdP ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    c. **[IdP Logout URL]\(IdP ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。
    
    d. **[POST]\(POST\)** として、**[Logout Response and Request HTTP Type]\(ログアウト応答と要求 HTTP のタイプ\)** を選択します。
   
    e. Azure Portal からダウンロードした **base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[Public Certificate]\(パブリック証明書\)** ボックスに貼り付けます。
    
12. **[応答および認証の構成]**で、次の手順を実行します。
    
    ![応答および認証の構成](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "応答および認証の構成")
  
      a. **[Identity Provider (ID プロバイダー)]** として **[Microsoft ADFS]** を選択します。
      
      b. **[Identifier Type (識別子の種類)]** として **[Email Address (メール アドレス)]** を選択します。 

      c. **[Email Attribute]** ボックスに、「**emailaddress**」と入力します。

      d. **[First Name Attribute (名属性)]** ボックスに、「**givenname**」と入力します。

      e. **[Last Name Attribute (姓属性)]** ボックスに、「**surname**」と入力します。

13. 次の手順を実行して、構成を完成させます。
    
    ![サインインでユーザー作成](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "サインインでユーザー作成") 

     a. **[User creation on Sign in (サインイン時のユーザー作成)]** で **[Create a new user in your site when they sign in (サインイン時に新しいユーザーをサイトに作成する)]** を選択します。

     b. **[Sign in Settings (サインインの設定)]** で、**[Use SAML button on "Sign in" screen ([サインイン] 画面で SAML ボタンを使用する)]** を選択します。

     c. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-igloo-software-test-user"></a>Igloo Software のテスト ユーザーの作成

Igloo Software へのユーザー プロビジョニングを構成するときに必要な操作はありません。  

割り当てられたユーザーがアクセス パネルを使用して Igloo Software にログインしようとすると、そのユーザーが存在するかどうかが Igloo Software によって確認されます。  使用可能なユーザー アカウントがない場合、ユーザー アカウントは Igloo Software によって自動的に作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Igloo Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Igloo Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Igloo Software]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Igloo Software のタイルをクリックすると、自動的に Igloo Software アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_203.png


