---
title: "チュートリアル: Azure Active Directory と BambooHR の統合 | Microsoft Docs"
description: "Azure Active Directory と BambooHR の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 06bf91b0e598fd3d8e644378efdb753611ee1ebc
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>チュートリアル: Azure Active Directory と BambooHR の統合

このチュートリアルでは、BambooHR と Azure Active Directory (Azure AD) を統合する方法について説明します。

BambooHR と Azure AD の統合には、次の利点があります。

- Azure AD で誰が BambooHR にアクセスできるかを制御できます
- ユーザーが自分の Azure AD アカウントで BambooHR に自動的にサインオンされる (シングル サインオン) ようにできます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と BambooHR の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- BambooHR でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの BambooHR の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-bamboohr-from-the-gallery"></a>ギャラリーからの BambooHR の追加
BambooHR の Azure AD への統合を構成するには、BambooHR をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから BambooHR を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**BambooHR**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_search.png)

5. 結果ウィンドウで **[BambooHR]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、BambooHR で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する BambooHR のユーザーを認識している必要があります。 つまり、Azure AD ユーザーと BambooHR の関連するユーザーの間のリンク関係が確立されている必要があります。

BambooHR で、Azure AD での **[ユーザー名]** の値を **[ユーザー名]** の値として割り当てることによってリンク関係を確立します。

BambooHR で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[BambooHR テスト ユーザーの作成](#creating-a-bamboohr-test-user)** - Azure AD でのユーザーにリンクされた、BambooHR での Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、BambooHR アプリケーションでシングル サインオンを構成します。

**BambooHR で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **[BambooHR]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. **[BambooHR Domain and URLs] \(BambooHR のドメインと URL)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    **[サインオン URL]** ボックスに、`https://<company>.bamboohr.com` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[BambooHR クライアント サポート チーム](https://www.bamboohr.com/contact.php)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. **[BambooHR Configuration] \(BambooHR 構成)** セクションで、**[Configure BambooHR] \(BambooHR の構成)** をクリックして **[Configure sign-on] \(サインオンの構成)** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

6. 別の Web ブラウザーのウィンドウで、管理者として BambooHR 企業サイトにログインします。

7. このホーム ページで、次の手順を実行します。
   
    ![シングル サインオン](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Single Sign-On")   

    a. **[Apps]**をクリックします。
   
    b. 左側の [Apps] メニューで、 **[Single Sign-On]**をクリックします。
   
    c. **[SAML Single Sign-On]**をクリックします。

8. **[SAML Single Sign-On]** セクションで、次の手順に従います。
   
    ![SAML シングル サインオン](./media/active-directory-saas-bamboo-hr-tutorial/ic796692.png "SAML Single Sign-On")
   
    a. **SAML シングル サインオン サービス URL** 値を **[SSO Login Url] \(SSO ログイン URL)** テキスト ボックスに貼り付けます。
      
    b. Azure Portal からダウンロードされた Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X.509 証明書]** テキスト ボックスに貼り付けます
   
    c. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-bamboohr-test-user"></a>BambooHR テスト ユーザーの作成

Azure AD ユーザーが BambooHR にログインできるようにするには、そのユーザーを BambooHR にプロビジョニングする必要があります。  

BambooHR の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **BambooHR** 企業サイトに管理者としてログインします。

2. 上部のツールバーで **[設定]**をクリックします。
   
    ![設定](./media/active-directory-saas-bamboo-hr-tutorial/ic796694.png "Setting")

3. **[Overview]**をクリックします。

4. 左側のナビゲーション ウィンドウの **[Security] \> [Users]** を選択します。

5. プロビジョニングする有効な AAD アカウントのユーザー名、パスワード、および電子メール アドレスを関連するテキスト ボックスに入力します。

6. [ **Save**] をクリックします。
        
>[!NOTE]
>他の BambooHR ユーザー アカウントの作成ツールまたは BambooHR から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、BambooHR へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を BambooHR に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[BambooHR]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [BambooHR] タイルをクリックすると、BambooHR アプリケーションに自動的にサインオンされます。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png


