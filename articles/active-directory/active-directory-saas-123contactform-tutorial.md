---
title: "チュートリアル: Azure Active Directory と 123ContactForm の統合 | Microsoft Docs"
description: "Azure Active Directory と 123ContactForm の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 3a99f0841c3e0d973168991f5dbee40e54c1d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>チュートリアル: Azure Active Directory と 123ContactForm の統合

このチュートリアルでは、123ContactForm と Azure Active Directory (Azure AD) を統合する方法について説明します。

123ContactForm と Azure AD の統合には、次の利点があります。

- 123ContactForm にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に 123ContactForm にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と 123ContactForm の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 123ContactForm でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの 123ContactForm の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-123contactform-from-the-gallery"></a>ギャラリーからの 123ContactForm の追加
Azure AD への 123ContactForm の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に 123ContactForm を追加する必要があります。

**ギャラリーから 123ContactForm を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**123ContactForm**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_search.png)

5. 結果パネルで、**[123ContactForm]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、123ContactForm で Azure AD のシングル サインオンを構成してテストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する 123ContactForm ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと 123ContactForm の関連ユーザーの間で、リンク関係が確立されている必要があります。

123ContactForm で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

123ContactForm で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[123ContactForm テスト ユーザーの作成](#creating-a-123contactform-test-user)** - 123ContactForm で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、123ContactForm アプリケーションでシングル サインオンを構成します。

**123ContactForm で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **123ContactForm** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. **[123ContactForm のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-123contactform-tutorial/url1.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs` のパターンを使用して URL を入力します。

4. **SP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-123contactform-tutorial/url2.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[サインオン URL]** ボックスに、URL として「`https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`」と入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値は、このチュートリアルの後半で説明する実際の URL と識別子に更新する必要があります。
    
5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-123contactform-tutorial/tutorial_general_400.png)

7. **123ContactForm** 側からシングル サインオンを構成するために、[https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) に移動して次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-123contactform-tutorial/submit.png) 

    a. **[Email]\(電子メール\)** ボックスに、ユーザーの電子メールを入力します。例:  **BrittaSimon@Contoso.com**。

    b. **[Upload]\(アップロード\)** をクリックし、Azure Portal からダウンロードしたメタデータ XML ファイルを参照します。

    c. **[SUBMIT FORM]\(フォームの送信\)** をクリックします。

8. **[Microsoft Azure AD - Single sign-on - Configure App Settings]\(Microsoft Azure AD - シングル サインオン - アプリケーション設定の構成\)** で、次の手順を実行します。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-123contactform-tutorial/url3.png)

    a. アプリケーションを **IDP 開始モード**で構成する場合は、インスタンスの **[IDENTIFIER]\(識別子\)** をコピーし、Azure ポータルの **[123ContactForm のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。
    
    b. アプリケーションを **IDP 開始モード**で構成する場合は、インスタンスの **[REPLY URL]\(応答 URL\)** をコピーし、Azure ポータルの **[123ContactForm のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。

    c. アプリケーションを **SP 開始モード**で構成する場合は、インスタンスの **[SIGN ON URL]\(サインオン URL\)** をコピーし、Azure ポータルの **[123ContactForm のドメインと URL]** セクションの **[サインオン URL]** ボックスに貼り付けます。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-123contactform-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-123contactform-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-123contactform-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-123contactform-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-123contactform-test-user"></a>123ContactForm テスト ユーザーの作成

アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に 123ContactForm へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**123ContactForm に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[123ContactForm]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [123ContactForm] タイルをクリックすると、自動的に 123ContactForm アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_203.png

