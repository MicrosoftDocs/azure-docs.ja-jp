---
title: "チュートリアル: Azure Active Directory と Mozy Enterprise の統合 | Microsoft Docs"
description: "Azure Active Directory と Mozy Enterprise の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: ac73aadcb8205f24f9d2dbce5af76f53bbcb9753
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>チュートリアル: Azure Active Directory と Mozy Enterprise の統合

このチュートリアルでは、Mozy Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。

Mozy Enterprise と Azure AD の統合には、次の利点があります。

- Mozy Enterprise にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Mozy Enterprise にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Mozy Enterprise と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Mozy Enterprise でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Mozy Enterprise の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-mozy-enterprise-from-the-gallery"></a>ギャラリーからの Mozy Enterprise の追加
Azure AD への Mozy Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Mozy Enterprise を追加する必要があります。

**ギャラリーから Mozy Enterprise を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Mozy Enterprise**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. 結果ウィンドウで **[Mozy Enterprise]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Mozy Enterprise で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Mozy Enterprise ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Mozy Enterprise の関連ユーザーの間で、リンク関係が確立されている必要があります。

Mozy Enterprise で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Mozy Enterprise で Azure AD のシングル サインオンを構成し、テストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Mozy Enterprise のテスト ユーザーの作成](#creating-a-mozy-enterprise-test-user)** - Mozy Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Mozy Enterprise アプリケーションでシングル サインオンを構成します。

**Mozy Enterprise で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Mozy Enterprise** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. **[Mozy Enterprise Domain and URLs]\(Mozy Enterprise のドメインと URL\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    **[サインオン URL]** ボックスに、`https://<tenantname>.Mozyenterprise.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Mozy Enterprise クライアント サポート チーム](http://support.mozy.com/)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_400.png)

6. **[Mozy Enterprise Configuration]\(Mozy Enterprise 構成\)** セクションで、**[Configure Mozy Enterprise]\(Mozy Enterprise を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. 別の Web ブラウザーのウィンドウで、Mozy Enterprise の企業サイトに管理者としてログインします。

8. **[構成]** セクションで、**[認証ポリシー]** をクリックします。
   
   ![認証ポリシー](./media/active-directory-saas-mozy-enterprise-tutorial/ic777314.png "認証ポリシー")

9. [ **認証ポリシー** ] セクションで、次の手順に従います。
   
   ![認証ポリシー](./media/active-directory-saas-mozy-enterprise-tutorial/ic777315.png "認証ポリシー")
   
   a. **[プロバイダー]** に **[ディレクトリ サービス]** を選択します。
   
   b. **[Use LDAP Push (LDAP プッシュを使用)]**を選択します。
   
   c. **[SAML 認証]** タブをクリックします。
   
   d. Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[認証 URL]** ボックスに貼り付けます。
   
   e. Azure Portal からコピーした **SAML エンティティ ID** を **[SAML エンドポイント]** ボックスに貼り付けます。
   
   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 ダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[SAML 証明書]** ボックスに貼り付けます。
   
   g. **[Enable SSO for Admins to log in with their network credentials 管理者のネットワーク資格情報を使用した SSO を許可する]**を選択します。
   
   h. **[変更を保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Mozy Enterprise テスト ユーザーの作成

Azure AD ユーザーが Mozy Enterprise にログインできるようにするには、ユーザーを Mozy Enterprise にプロビジョニングする必要があります。 Mozy Enterprise の場合、プロビジョニングは手動で行います。

>[!NOTE]
>Mozy Enterprise から提供されている他の Mozy Enterprise ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. お使いの **Mozy Enterprise** テナントにログインします。

2. **[ユーザー]** をクリックして、**[新規ユーザーの追加]** をクリックします。
   
   ![ユーザー](./media/active-directory-saas-mozy-enterprise-tutorial/ic777317.png "Users")
   
   >[!NOTE]
   >**[新規ユーザーの追加]** オプションは、**[認証ポリシー]** で **[Mozy]** が選択されている場合にのみ表示されます。 SAML 認証が構成されている場合、ユーザーはシングル サインオンでの初回ログイン時に自動的に追加されます。
    
3. 新しいユーザーのダイアログで、次の手順に従います。
   
   ![ユーザーの追加](./media/active-directory-saas-mozy-enterprise-tutorial/ic777318.png "ユーザーの追加")
   
   a. **[Choose a Group (グループを選択)]** の一覧で、グループを選択します。
   
   b. **[What type of user (ユーザーのタイプ)]** の一覧で、タイプを選択します。
   
   c. **[User Name (ユーザー名)]** ボックスに、Azure AD ユーザーの名前を入力します。
   
   d. **[Email (メール)]** ボックスに、Azure AD ユーザーのメール アドレスを入力します。
   
   e. **[Send user instruction email (ユーザーに指示メールを送信する)]**を選択します。
   
   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Add User(s) (ユーザーの追加)]**をクリックします。

     >[!NOTE]
     > ユーザーの作成後、Azure AD ユーザーに、アカウントがアクティブになる前にアカウントを確認するためのリンクが記載されたメールが送信されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Mozy Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Mozy Enterprise に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Mozy Enterprise]\(Mozy Enterprise\)** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Mozy Enterprise] タイルをクリックすると、Mozy Enterprise アプリケーションのログイン ページが表示されます。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_203.png


