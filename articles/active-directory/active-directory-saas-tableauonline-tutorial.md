---
title: "チュートリアル: Azure Active Directory と Tableau Online の統合 | Microsoft Docs"
description: "Azure Active Directory と Tableau Online の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 443fab1198a91a4d5749e6421f7b8603fc75a81e
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>チュートリアル: Azure Active Directory と Tableau Online の統合

このチュートリアルでは、Tableau Online と Azure Active Directory (Azure AD) を統合する方法について説明します。

Tableau Online と Azure AD の統合には、次の利点があります。

- Tableau Online にアクセスするユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Tableau Online にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Tableau Online の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Tableau Online でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Tableau Online の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-tableau-online-from-the-gallery"></a>ギャラリーからの Tableau Online の追加
Azure AD への Tableau Online の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tableau Online を追加する必要があります。

**ギャラリーから Tableau Online を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「 **Tableau Online**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. 結果ウィンドウで **[Tableau Online]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Tableau Online で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Tableau Online ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Tableau Online の関連ユーザーの間で、リンク関係が確立されている必要があります。

Tableau Online で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Tableau Online で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Tableau Online のテスト ユーザーの作成](#creating-a-tableau-online-test-user)** - Tableau Online で Britta Simon に対応するユーザーを作成し、Azure AD のBritta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Tableau Online アプリケーションでシングル サインオンを構成します。

**Tableau Online で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Tableau Online** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. **[Tableau Online Domain and URLs (Tableau Online のドメインと URL)]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. **[サインオン URL]** ボックスに、URL: `https://sso.online.tableau.com` を入力します。

    b. **[識別子]** ボックスに、URL: `https://sso.online.tableau.com/public/sp/<instancename>` を入力します。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. 別のブラウザー ウィンドウで、Tableau Online アプリケーションにサインオンします。 **[設定]**、**[認証]** の順にクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. **[認証の種類]** セクションで、SAML を有効にするには、 **[Single sign-on with SAML (SAML によるシングル サインオン)]** チェック ボックスをオンにします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. 下へスクロールして、 **[Import metadata file into Tableau Online (Tableau Online にメタデータ ファイルをインポートする)]** セクションを表示します。  [Browse (参照)] をクリックし、Azure AD からダウンロードしたメタデータ ファイルをインポートします。 次に、 **[Apply (適用)]**をクリックします。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. **[Match assertions (アサーションの一致)]** セクションで、**メール アドレス**、**名**、**姓**に対応する ID プロバイダーのアサーション名を挿入します。 Azure AD から情報を取得するには、以下の手順に従います。 
  
    a. Azure Portal で **Tableau Online** アプリケーション統合ページに移動します。
    
    b. 属性セクションで、**[その他のすべてのユーザー属性を表示および編集する]** チェックボックスをオンにします。 
    
   ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    c. 以下の手順で、属性 givenname、email、surname の名前空間の値をコピーします。

   ![Azure AD のシングル サインオン](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. **user.givenname** 値をクリックします 
    
    e. **[名前空間]** ボックスから値をコピーします。

   ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. email、surname の名前空間の値をコピーするには、上記の手順に従います。

    g. Tableau Online アプリケーションに切り替えて、**[Tableau Online Attributes (Tableau Online 属性)]** セクションを次のように設定します。
     * Email (電子メール): **mail** または **userprincipalname**
     * First name (名): **givenname**
     * Last name (姓): **surname**
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-tableau-online-test-user"></a>Tableau Online のテスト ユーザーの作成

このセクションでは、Tableau Online で Britta Simon というユーザーを作成します。

1. **Tableau Online** で、**[設定]**、**[認証]** セクションの順にクリックします。 下へスクロールして、 **[Select Users (ユーザーの選択)]** セクションを表示します。 **[ユーザーの追加]** をクリックし、**メール アドレスを入力**します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. **[Add users for single sign-on (SSO) authentication (シングル サインオン (SSO) 認証用にユーザーを追加する)]**を選択します。 **[Enter Email Addresses (電子メール アドレスの入力)]** ボックスに「britta.simon@contoso.com」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. **[作成]**をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Tableau Online へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Tableau Online に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Tableau Online]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Tableau Online] タイルをクリックすると、Tableau Online アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png


