---
title: "チュートリアル: Azure Active Directory と Expensify の統合 | Microsoft Docs"
description: "Azure Active Directory と Expensify の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e45576fd92706881121469ccd82150b3d48059cd
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>チュートリアル: Azure Active Directory と Expensify の統合

このチュートリアルでは、Expensify と Azure Active Directory (Azure AD) を統合する方法について説明します。

Expensify と Azure AD の統合には、次の利点があります。

- Expensify にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Expensify にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Expensify と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Expensify でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Expensify の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-expensify-from-the-gallery"></a>ギャラリーからの Expensify の追加
Azure AD への Expensify の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Expensify を追加する必要があります。

**ギャラリーから Expensify を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Expensify**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_search.png)

5. 結果ウィンドウで **Expensify** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Expensify で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Expensify ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Expensify の関連ユーザーの間でリンク関係が確立されている必要があります。

Expensify で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Expensify で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Expensify テスト ユーザーの作成](#creating-an-expensify-test-user)** - Expensify で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Expensify アプリケーションでシングル サインオンを構成します。

**Expensify で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Expensify** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_samlbase.png)

3. **[Expensify のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_url.png)

    a. **[サインオン URL]** ボックスに、`https://www.expensify.com/authentication/saml/login` のパターンを使用して URL を入力します。

    b. **[Identifier URL]\(ID URL\)** ボックスに、 `https://www.<companyname>.expensify.com/` のパターンで URL を入力します。 

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と ID URL でこれらの値を更新してください。 これらの値を取得するには、[Expensify クライアント サポート チーム](mailto:help@expensify.com)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-expensify-tutorial/tutorial_general_400.png)

6. Expensify で SSO を有効にするには、まず、アプリケーションで **Domain Control** を有効にする必要があります。 Domain Control は、[こちら](http://help.expensify.com/domain-control)で説明されている手順を使ってアプリケーションで有効にできます。 さらにサポートが必要な場合は、[Expensify クライアント サポート チーム](mailto:help@expensify.com)に問い合わせてください。 Domain Control を有効にしたら、以下の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_51.png)
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 Expensify アプリケーションにサインオンします。
    
    b. 上部のツールバーの **[Admin]**をクリックします。
    
    c. 左側のパネルで **[ドメイン]** をクリックします。
    
    d. 確認済みドメイン名をクリックします。
    
    e. 左側のパネルで **[SAML]** をクリックし、**[Enabled (有効)]** を選択します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure AD からダウンロードしたフェデレーション メタデータをメモ帳で開き、その内容をコピーして、**[Identity Provider Metadata]\(ID プロバイダー メタデータ\)** ボックスに貼り付けます。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-expensify-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-expensify-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-expensify-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-expensify-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-expensify-test-user"></a>Expensify テスト ユーザーの作成

このセクションでは、Expensify で Britta Simon というユーザーを作成します。 [Expensify クライアント サポート チーム](mailto:help@expensify.com)と協力して、Expensify プラットフォームにユーザーを追加します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Expensify へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Expensify に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Expensify]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。  

アクセス パネルで [Expensify] タイルをクリックすると、自動的に Expensify アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_203.png


