---
title: "チュートリアル: Azure Active Directory と Moxtra の統合 | Microsoft Docs"
description: "Azure Active Directory と Moxtra の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: db2f041a44b6771b0a4f734e58d899298ef0847b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>チュートリアル: Azure Active Directory と Moxtra の統合

このチュートリアルでは、Moxtra と Azure Active Directory (Azure AD) を統合する方法について説明します。

Moxtra と Azure AD の統合には、次の利点があります。

- Moxtra にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Moxtra にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Moxtra と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Moxtra でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Moxtra を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-moxtra-from-the-gallery"></a>ギャラリーから Moxtra を追加する
Azure AD への Moxtra の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Moxtra を追加する必要があります。

**ギャラリーから Moxtra を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Moxtra**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. 結果ウィンドウで **[Moxtra]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Moxtra で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Moxtra ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Moxtra の関連ユーザーの間で、リンク関係が確立されている必要があります。

Moxtra で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Moxtra で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Moxtra テスト ユーザーの作成](#creating-a-moxtra-test-user)** - Moxtra で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure AD のシングル サインオンを Azure Portal で有効にし、Moxtra アプリケーションでシングル サインオンを構成します。

**Moxtra で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Moxtra** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. **[Moxtra Domain and URLs]\(Moxtra のドメインと URL\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    **[サインオン URL]** ボックスに、「`https://www.moxtra.com/service/#login`」と入力します。

4. Moxtra アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットは、この構成の例を示しています。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |    
    | firstname | User.givenname |
    | lastname | User.surname |
    | idpid    | < SAML エンティティ ID > 

    > [!Note]
    > 属性 **idpid** の値は、実際のものではありません。 実際の値は、**[Moxtra Configuration]\(Moxtra 構成\)** の **[クイック リファレンス]** セクションから取得できます。
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[OK]**をクリックします。
    
5. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. **[Moxtra Configuration]\(Moxtra 構成\)** セクションで、**[Configure Moxtra]\(Moxtra を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**SAML エンティティ ID と SAML Single シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. 別の Web ブラウザー ウィンドウで、管理者として Moxtra 企業サイトにサインオンします。

9. 左のツール バーで、**[管理コンソール] > [SAML Single Sign-on]\(SAML シングル サインオン\)** の順にクリックし、**[新規]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. **[SAML]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[Name (名前)]** ボックスに、構成の名前を入力します (例: *SAML*)。 
  
    b. **[IdP Entity ID]\(IdP エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 
 
    c. **[ログイン URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。 
 
    d. **[AuthnContextClassRef]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:ac:classes:Password**」と入力します。 
 
    e. **[NameID 形式]** ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**」と入力します。 
 
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure Portal からダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[証明書]** ボックスに貼り付けます。    
 
    g. SAML 電子メール ドメイン テキストボックスに、SAML 電子メール ドメインを入力します。    
  
    >[!NOTE]
    >ドメインを検証するための手順を確認するには、下の**i**をクリックします。

    h. **[Update]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-moxtra-test-user"></a>Moxtra テスト ユーザーの作成

このセクションの目的は、Moxtra で Britta Simon というユーザーを作成することです。

**Moxtra で Britta Simon というユーザーを作成するには、次の手順を実行します。**

1. Moxtra 企業サイトに管理者としてサインオンします。

2. 左のツール バーで、**[Admin Console (管理コンソール)]、[User Management (ユーザー管理)]** の順にクリックし、**[Add User (ユーザーの追加)]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. **[Add User]** ダイアログで、次の手順を実行します。
  
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。
  
    b. **[姓]** ボックスに「**Simon**」と入力します。
  
    c. **[電子メール]** ボックスに、Azure Portal と同じ Britta の電子メール アドレスを入力します。
  
    d. **[Division (事業部)]** ボックスに「**Dev**」と入力します。
  
    e. **[Department (部門)]** ボックスに「**IT**」と入力します。
  
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[管理者]** を選択します。
  
    g. **[追加]**をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Moxtra へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Moxtra に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Moxtra]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Moxtra のタイルをクリックすると、自動的に Moxtra アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png

