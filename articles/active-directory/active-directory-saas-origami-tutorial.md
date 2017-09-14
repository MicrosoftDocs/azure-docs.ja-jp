---
title: "チュートリアル: Azure Active Directory と Origami の統合 | Microsoft Docs"
description: "Azure Active Directory と Origami の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3420409b72ff032e64ac59365083dd141dfc3c1b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>チュートリアル: Azure Active Directory と Origami の統合

このチュートリアルでは、Origami と Azure Active Directory (Azure AD) を統合する方法について説明します。

Origami と Azure AD の統合には、次の利点があります。

- Origami にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Origami にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Origami の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Origami でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Origami の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-origami-from-the-gallery"></a>ギャラリーからの Origami の追加
Azure AD への Origami の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Origami を追加する必要があります。

**ギャラリーから Origami を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Origami**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-origami-tutorial/tutorial_origami_search.png)

5. 結果パネルで **[Origami]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Origami で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Origami ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Origami の関連ユーザーの間で、リンク関係が確立されている必要があります。

Origami で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Origami で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Origami のテスト ユーザーの作成](#creating-an-origami-test-user)** - Origami で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Origami アプリケーションでシングル サインオンを構成します。

**Origami で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Origami** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_samlbase.png)

3. **[Origami のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_url.png)

    **[サインオン URL]** ボックスに、`https://live.origamirisk.com/origami/account/login?account=<companyname>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値は実際のサインオン URL で更新します。 この値を取得するには、[Origami クライアント サポート チーム](https://wordpress.org/support/theme/origami)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-origami-tutorial/tutorial_origami_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_general_400.png)

6. **[Origami 構成]** セクションで、**[Origami の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_configure.png) 

7. 管理者権限で Origami アカウントにログインします。

8. 上部のメニューで **[管理者]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

9. [Single Sign On Setup] ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_531.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[シングル サインオンを有効にする]** を選択します。

    b. **[Identity Provider's Sign-in Page URL (ID プロバイダー シングル サインイン ページ URL)]** テキストボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[Identity Provider's Sign-out Page URL (ID プロバイダー シングル サインアウト ページ URL)]** テキストボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    d. **[Browse (参照する)]** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。

    e. **[変更を保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-origami-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-origami-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-origami-test-user"></a>Origami テスト ユーザーの作成

このセクションでは、Origami で Britta Simon というユーザーを作成します。 

1. 管理者権限で Origami アカウントにログインします。

2. 上部のメニューで **[管理者]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

3. **[Users and Security]** ダイアログ ボックスで、**[Users]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)

4. **[新しいユーザーの追加]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)

5. [新規ユーザーの追加] ダイアログで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)

    a. **[ユーザー名]** ボックスに、ユーザーの電子メール (**brittasimon@contoso.com** など) を入力します。

    b. **[パスワード]** ボックスに、パスワードを入力します。

    c. **[パスワードの確認]** ボックスに、パスワードを再度入力します。

    d. **[名]** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    e. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[保存]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)

6. **ユーザー ロール**と**クライアント アクセス**をユーザーに割り当てます。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Origami へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Origami に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Origami]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-origami-tutorial/tutorial_origami_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Origami] タイルをクリックすると、自動的に Origami アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-origami-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png


