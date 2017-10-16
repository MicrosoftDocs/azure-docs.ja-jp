---
title: "チュートリアル: Azure Active Directory と Wdesk の統合 | Microsoft Docs"
description: "Azure Active Directory と Wdesk の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 37660b80cfb01d6a3105aea5ce248f1e03c46695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>チュートリアル: Azure Active Directory と Wdesk の統合

このチュートリアルでは、Wdesk と Azure Active Directory (Azure AD) を統合する方法について説明します。

Wdesk と Azure AD の統合には、次の利点があります。

- Wdesk にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Wdesk にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、 「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Wdesk と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Wdesk でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Wdesk の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-wdesk-from-the-gallery"></a>ギャラリーからの Wdesk の追加
Azure AD への Wdesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Wdesk を追加する必要があります。

**ギャラリーから Wdesk を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Wdesk**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. 結果ウィンドウで **Wdesk** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Wdesk で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Wdesk ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Wdesk の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Wdesk の **[Username]\(ユーザー名\)** の値として割り当てます。

Wdesk で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Wdesk テスト ユーザーの作成](#creating-a-wdesk-test-user)** - Wdesk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Wdesk アプリケーションでシングル サインオンを構成します。

**Wdesk で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Wdesk** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. **[Wdesk のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>` のパターンを使用して URL を入力します。

4. **[詳細な URL 設定の表示]** をクリックします。 **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は、SSO を構成するときに WDesk ポータルから得られます。 
  
5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Wdesk にログインします。

8. 左下の **[Admin]\(管理者\)** をクリックし、**[Account Admin]\(アカウント管理者\)** を選びます。
 
     ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. Wdesk 管理ツールで、**[Security]\(セキュリティ\)** に移動した後、**[SAML]** > **[SAML Settings]\(SAML の設定\)** の順に移動します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. **[General Settings]\(一般設定\)** で、**[Enable SAML Single Sign On]\(SAML のシングル サインオンを有効にする\)** をオンにします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. **[Service Provider Details]\(サービス プロバイダーの詳細\)** で、以下の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. **[Login URL]\(ログイン URL\)** をコピーし、Azure Portal の **[サインオン URL]** ボックスに貼り付けます。
   
      b. **[Metadata Url]\(メタデータ URL\)** をコピーし、Azure Portal の **[識別子]** ボックスに貼り付けます。
       
      c. **[Consumer url]\(コンシューマー URL\)** をコピーし、Azure Portal の **[応答 URL]** ボックスに貼り付けます。
   
      d. Azure Portal の **[保存]** をクリックして、変更を保存します。      

12. **[Configure IdP Settings]\(IdP の設定の構成\)** をクリックして、**[Edit IdP Settings]\(IdP の設定の編集\)** ダイアログを開きます。 **[Choose File]\(ファイルの選択\)** をクリックし、Azure Portal から保存した **Metadata.xml** ファイルを選択して、アップロードします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. **[変更を保存]**をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-wdesk-test-user"></a>Wdesk テスト ユーザーの作成

Azure AD ユーザーが Wdesk にログインできるようにするには、ユーザーを Wdesk にプロビジョニングする必要があります。 Wdesk では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Wdesk にログインします。
2. **[Admin]\(管理者\)** > **[Account Admin]\(アカウント管理者\)** の順に移動します。

     ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. **[People]\(ユーザー\)** の **[Members]\(メンバー\)** をクリックします。

4. **[Add Member]\(メンバーの追加\)** をクリックして、**[Add Member]\(メンバーの追加\)** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. **[User]\(ユーザー\)** ボックスにユーザー名を入力し (例: **brittasimon@contoso.com**)、**[Continue]\(続行\)** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  次のように、詳細を入力します。
  
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. **[E-mail]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: **brittasimon@contoso.com**)。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

7. **[Save Member]\(メンバーの保存\)** ボタンをクリックします。  

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Wdesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Wdesk に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Wdesk]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Wdesk のタイルをクリックすると、自動的に Wdesk アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png

