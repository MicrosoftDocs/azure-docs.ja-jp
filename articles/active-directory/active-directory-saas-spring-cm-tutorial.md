---
title: "チュートリアル: Azure Active Directory と SpringCM の統合 | Microsoft Docs"
description: "Azure Active Directory と SpringCM の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: edfd06a06c730597fee4569ca1ce29092b45244a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>チュートリアル: Azure Active Directory と SpringCM の統合

このチュートリアルでは、SpringCM と Azure Active Directory (Azure AD) を統合する方法について説明します。

SpringCM と Azure AD の統合には、次の利点があります。

- SpringCM にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に SpringCM にサインオン (シングル サインオン) できるように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SpringCM と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SpringCM でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SpringCM の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-springcm-from-the-gallery"></a>ギャラリーからの SpringCM の追加
Azure AD への SpringCM の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SpringCM を追加する必要があります。

**ギャラリーから SpringCM を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**SpringCM**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_search.png)

5. 結果ウィンドウで **SpringCM** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SpringCM で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SpringCM ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SpringCM の関連ユーザーの間で、リンク関係が確立されている必要があります。

SpringCM で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

SpringCM で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SpringCM テスト ユーザーの作成](#creating-a-springcm-test-user)** - SpringCM で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SpringCM アプリケーションでシングル サインオンを構成します。

**SpringCM で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SpringCM** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_samlbase.png)

3. **[SpringCM のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_url.png)

    **[サインオン URL]** ボックスに、`https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[SpringCM クライアント サポート チーム](https://knowledge.springcm.com/support)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate(Raw)] \(証明書 (Raw))** をクリックし、証明書ファイルをコンピューターに保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-spring-cm-tutorial/tutorial_general_400.png)

6. **[SpringCM 構成]** セクションで、**[SpringCM の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**SAML エンティティ ID と SAML Single シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_configure.png)   

7. 別の Web ブラウザーのウィンドウで、管理者として **SpringCM** 企業サイトにサインオンします。

8. 上部にあるメニューの **[GO TO]** をクリックし、**[Preferences]** をクリックします。次に、**[Account Preferences]** セクションで、**[SAML SSO]** をクリックします。
   
    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/ic797051.png "SAML SSO")

9. [Identity Provider Configuration] セクションで、次の手順に従います。
   
    ![Identity Provider Configuration](./media/active-directory-saas-spring-cm-tutorial/ic797052.png "Identity Provider Configuration")
    
    a. ダウンロードした Azure Active Directory 証明書をアップロードするために、**[Select Issuer Certificate]** または **[Change Issuer Certificate]** をクリックします。
    
    b. Azure Portal からコピーした **SAML エンティティ ID** の値を、**[Issuer]\(発行者\)** ボックスに貼り付けます。
    
    c. Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を、**[Service Provider (SP) Initiated Endpoint]\(サービス プロバイダー (SP) 開始エンドポイント\)** ボックスに貼り付けます。
            
    d. **[SAML Enabled]\(SAML の有効化\)** で **[Enable]\(有効\)** を選びます。

    e. [ **Save**] をクリックします。
 
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="creating-a-springcm-test-user"></a>SpringCM テスト ユーザーの作成

Azure Active Directory ユーザーが SpringCM にログインできるようにするには、そのユーザーを SpringCM にプロビジョニングする必要があります。 SpringCM の場合、プロビジョニングは手動で行います。

>[!NOTE]
>詳細については、[Create and Edit a SpringCM User](http://knowledge.springcm.com/create-and-edit-a-springcm-user) (SpringCM ユーザーの作成と編集に関するページ) をご覧ください。 

**ユーザー アカウントを SpringCM にプロビジョニングするには、次の手順に従います。**

1. **SpringCM** 企業サイトに管理者としてログインします。

2. **[GOTO]\(移動\)** をクリックし、**[ADDRESS BOOK]\(アドレス帳\)** をクリックします。
   
    ![Create User](./media/active-directory-saas-spring-cm-tutorial/ic797054.png "Create User")

3. **[Create User]**をクリックします。

4. **[User Role]**を選択します。

5. **[Send Activation Email]**を選択します。

6. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの姓名と電子メール アドレスを入力します。

7. ユーザーを **[Security group]**に追加します。

8. [ **Save**] をクリックします。

  >[!NOTE]
  >SpringCM から提供されている他の SpringCM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SpringCM へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**SpringCM に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SpringCM]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。
 
アクセス パネルで SpringCM のタイルをクリックすると、自動的に SpringCM アプリケーションにサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_203.png


