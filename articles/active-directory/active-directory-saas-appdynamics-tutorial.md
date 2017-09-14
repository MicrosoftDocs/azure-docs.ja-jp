---
title: "チュートリアル: Azure Active Directory と AppDynamics の統合 | Microsoft Docs"
description: "Azure Active Directory と AppDynamics の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 634e68bdb937eba68b27b824dc62fe2677e24ffe
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>チュートリアル: Azure Active Directory と AppDynamics の統合

このチュートリアルでは、AppDynamics と Azure Active Directory (Azure AD) を統合する方法について説明します。

AppDynamics と Azure AD の統合には、次の利点があります。

- AppDynamics にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に AppDynamics にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

AppDynamics と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- AppDynamics でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの AppDynamics の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-appdynamics-from-the-gallery"></a>ギャラリーからの AppDynamics の追加
Azure AD への AppDynamics の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AppDynamics を追加する必要があります。

**ギャラリーから AppDynamics を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. [検索] ボックスに、「**AppDynamics**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_search.png)

5. 結果ウィンドウで **[AppDynamics]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、AppDynamics で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する AppDynamics ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと AppDynamics の関連ユーザーの間で、リンク関係が確立されている必要があります。

AppDynamics で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

AppDynamics で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[AppDynamics のテスト ユーザーの作成](#creating-an-appdynamics-test-user)** - AppDynamics で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、AppDynamics アプリケーションでシングル サインオンを構成します。

**AppDynamics で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **AppDynamics** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_samlbase.png)

3. **[AppDynamics のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.saas.appdynamics.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.saas.appdynamics.com/controller` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[AppDynamics クライアント サポート チーム](https://www.appdynamics.com/support/)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-appdynamics-tutorial/tutorial_general_400.png)

6. **[AppDynamics Configuration (AppDynamics 構成)]** セクションで、**[Configure AppDynamics (AppDynamics を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_configure.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として AppDynamics 企業サイトにログインします。

8. 上部にあるツールバーで **[Settings]**、**[Administration]** の順にクリックします。
   
    ![Administration](./media/active-directory-saas-appdynamics-tutorial/ic790216.png "Administration")

9. **[Authentication Provider]** タブをクリックします。
   
    ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/ic790224.png "Authentication Provider")

10. **[Authentication Provider]** セクションで、次の手順を実行します。
   
    ![SAML の構成](./media/active-directory-saas-appdynamics-tutorial/ic790225.png "SAML の構成")   

    a. **[Authentication Provider]** として、**[SAML]** を選択します。

    b. **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。
       
    d. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[証明書]** テキストボックスに貼り付けます。

    e. [ **Save**] をクリックします。

     ![保存](./media/active-directory-saas-appdynamics-tutorial/ic777673.png "保存")

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-appdynamics-test-user"></a>AppDynamics のテスト ユーザーの作成

Azure AD ユーザーが AppDynamics にログインできるようにするには、そのユーザーを AppDynamics にプロビジョニングする必要があります。 AppDynamics の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. AppDynamics 企業サイトに管理者としてログインします。

2. **[Users]** に移動して、[**+**] をクリックして **[Create User]** ダイアログを開きます。
   
    ![ユーザー](./media/active-directory-saas-appdynamics-tutorial/ic790229.png "Users")

3. **[Create User]** セクションで、次の手順に従います。
   
    ![Create User](./media/active-directory-saas-appdynamics-tutorial/ic790230.png "Create User")
   
    a. 関連するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの **[Username]**、**[Name]**、**[Email]**、**[New Password]**、**[Repeat New Password]** を入力します。

    b. [ **Save**] をクリックします。

    >[!NOTE]
    >他の AppDynamics ユーザー アカウント作成ツールまたは AppDynamics から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、AppDynamics へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**AppDynamics に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[AppDynamics]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで AppDynamics のタイルをクリックすると、AppDynamics アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_203.png


