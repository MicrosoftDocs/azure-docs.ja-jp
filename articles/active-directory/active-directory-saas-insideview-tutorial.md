---
title: "チュートリアル: Azure Active Directory と InsideView の統合 | Microsoft Docs"
description: "Azure Active Directory と InsideView の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f2b0a1d4bc44f8d0cd57c61e2d78950cb6a99854
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>チュートリアル: Azure Active Directory と InsideView の統合

このチュートリアルでは、InsideView と Azure Active Directory (Azure AD) を統合する方法について説明します。

InsideView と Azure AD の統合には、次の利点があります。

- InsideView にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に InsideView にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

InsideView と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- InsideViewでのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの InsideView の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-insideview-from-the-gallery"></a>ギャラリーからの InsideView の追加
Azure AD への InsideView の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に InsideView を追加する必要があります。

**ギャラリーから InsideView を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**InsideView**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-insideview-tutorial/tutorial_insideview_search.png)

5. 結果ウィンドウで **[InsideView]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-insideview-tutorial/tutorial_insideview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、InsideView で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する InsideView ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと InsideView の関連ユーザーの間で、リンク関係が確立されている必要があります。

InsideView で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

InsideView で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[InsideView テスト ユーザーの作成](#creating-a-insideview-test-user)** - InsideView で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、InsideView アプリケーションでシングル サインオンを構成します。

**InsideView で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **InsideView** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-insideview-tutorial/tutorial_insideview_samlbase.png)

3. **[InsideView Domain and URLs]\(InsideView のドメインと URL\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-insideview-tutorial/tutorial_insideview_url.png)
    
    **[応答 URL]** ボックスに、`https://my.insideview.com/iv/<STS Name>/login.iv` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[InsideView サポート チーム](mailto:support@insideview.com)に連絡してください。
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Raw) (証明書 (Raw))]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-insideview-tutorial/tutorial_insideview_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-insideview-tutorial/tutorial_general_400.png)

6. **[InsideView Configuration]\(InsideView 構成\)** セクションで、**[Configure InsideView]\(InsideView を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-insideview-tutorial/tutorial_insideview_configure.png) 

7. 別の Web ブラウザー ウィンドウで、InsideView 企業サイトに管理者としてログインします。

8. 上部のツールバーで、**[管理者]**、**[シングル サインオン設定]** をクリックし、**[SAML の追加]** をクリックします。
   
   ![SAML Single Sign On Settings](./media/active-directory-saas-insideview-tutorial/ic794135.png "SAML Single Sign On Settings")

9. **[新しい SAML の追加]** セクションで、次の手順を実行します。

    ![Add a New SAML](./media/active-directory-saas-insideview-tutorial/ic794136.png "Add a New SAML")
   
    a. **[STS 名]** テキストボックスに、構成の名前を入力します。

    b. **[SamlP/WS-Fed Unsolicited EndPoint]\(SamlP/WS-Fed 未承諾エンドポイント\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
    
    c. Azure Portal からダウンロードした base-64 でエンコードされた証明書を開き、その内容をクリップボードにコピーして **[STS 証明書]** ボックスに貼り付けます。

    d. **[Crm User Id Mapping]\(Crm ユーザー ID マッピング\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」を入力します。
        
    e. **[Crm Email Mapping]\(Crm 電子メール マッピング)\** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」を入力します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Crm First Name Mapping]/(Crm 名マッピング/)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」を入力します。
    
    g. **[Crm lastName Mapping]\(Crm 姓マッピング\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」を入力します。  

    h. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 
 
### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-insideview-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-insideview-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-insideview-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-insideview-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-insideview-test-user"></a>InsideView テスト ユーザーの作成

Azure AD ユーザーが InsideView にログインできるようにするには、そのユーザーを InsideView にプロビジョニングする必要があります。 InsideView の場合、プロビジョニングは手動で行います。

InsideView で作成したユーザーまたは連絡先を入手するには、[InsideView サポート チーム](mailto:support@insideview.com)にお問い合わせください。

>[!NOTE]
>InsideView から提供されている他の InsideView ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に InsideView へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を InsideView に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[InsideView]\(InsideView\)** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-insideview-tutorial/tutorial_insideview_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで InsideView のタイルをクリックすると、自動的に InsideView アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-insideview-tutorial/tutorial_general_203.png


