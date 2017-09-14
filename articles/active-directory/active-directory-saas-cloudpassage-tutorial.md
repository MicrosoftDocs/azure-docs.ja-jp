---
title: "チュートリアル: Azure Active Directory と CloudPassage の統合 | Microsoft Docs"
description: "Azure Active Directory と CloudPassage の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 094740e20570665e975dec1a591989e411f90c16
ms.contentlocale: ja-jp
ms.lasthandoff: 06/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>チュートリアル: Azure Active Directory と CloudPassage の統合

このチュートリアルでは、CloudPassage と Azure Active Directory (Azure AD) を統合する方法について説明します。

CloudPassage と Azure AD の統合には、次の利点があります。

- CloudPassage にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に CloudPassage にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

CloudPassage と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- CloudPassage でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの CloudPassage の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-cloudpassage-from-the-gallery"></a>ギャラリーからの CloudPassage の追加
Azure AD への CloudPassage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に CloudPassage を追加する必要があります。

**ギャラリーから CloudPassage を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **CloudPassage**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_search.png)

5. 結果ウィンドウで **[CloudPassage]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、CloudPassage で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する CloudPassage ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと CloudPassage の関連ユーザーの間で、リンク関係が確立されている必要があります。

CloudPassage で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

CloudPassage での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[CloudPassage テスト ユーザーの作成](#creating-a-cloudpassage-test-user)** - CloudPassage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、CloudPassage アプリケーションでシングル サインオンを構成します。

**CloudPassage との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **CloudPassage** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

3. **[CloudPassage のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. **[サインオン URL]** ボックスに、`https://portal.cloudpassage.com/saml/init/accountid` のパターンを使用して URL を入力します。

    b. **[応答 URL]** ボックスに、`https://portal.cloudpassage.com/saml/consume/accountid` のパターンを使用して URL を入力します。 この属性の値は、CloudPassage ポータルの **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで **[SSO Setup documentation (SSO セットアップのドキュメント)]** をクリックすることで取得できます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 これらの値を取得するには、[CloudPassage クライアント サポート チーム](https://www.cloudpassage.com/company/contact/)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

5. CloudPassage アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。
   
   ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。

    | 属性名 | 属性値 |
    | --- | --- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]**をクリックします。

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_400.png)
    
8. **[CloudPassage Configuration]\(CloudPassage 構成\)** セクションで、**[Configure CloudPassage]\(CloudPassage の構成\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

9. 別の Web ブラウザーのウィンドウで、管理者として CloudPassage 企業サイトにサインオンします。

10. 上部のメニューの **[Settings (設定)]** をクリックし、**[Site Administration (サイトの管理)]** をクリックします。 
   
    ![[シングル サインオンの構成]][12]

11. **[Authentication Settings (認証設定)]** タブをクリックします。 
   
    ![[シングル サインオンの構成]][13]

12. **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで、次の手順に従います。 
   
    ![[シングル サインオンの構成]][14]

    a. **[Enable Single sign-on(SSO)(SSO Setup Documentation)]\(シングル サインオン (SSO)(SSO セットアップ ドキュメント) を有効にする\)** チェックボックスをオンにします。
    
    b. **SAML エンティティ ID** を **[SAML issuer URL]\(SAML 発行者 URL\)** に貼り付けます。
  
    c. **SAML シングル サインオン サービス URL** を **[SAML endpoint URL]\(SAML エンドポイント URL\)** ボックスに貼り付けます。
  
    d. **サインアウト URL** を **[Logout landing page]\(ログアウト ランディング ページ\)** ボックスに貼り付けます。
  
    e. ダウンロードした証明書をメモ帳で開き、ダウンロードした証明書の内容をクリップボードにコピーして、**[x 509 certificate]\(x 509 証明書\)** ボックスに貼り付けます。
  
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-cloudpassage-test-user"></a>CloudPassage テスト ユーザーの作成

このセクションの目的は、CloudPassage で Britta Simon というユーザーを作成することです。

**CloudPassage で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. **CloudPassage** 企業サイトに管理者としてサインオンします。 

2. 上部にあるツールバーの **[Settings (設定)]** をクリックし、**[Site Administration (サイトの管理)]** をクリックします。 
   
   ![CloudPassage テスト ユーザーの作成][22] 

3. **[Users (ユーザー)]** タブをクリックし、**[Add New User (新しいユーザーの追加)]** をクリックします。 
   
   ![CloudPassage テスト ユーザーの作成][23]

4. **[新しいユーザーの追加]** セクションで、次の手順を実行します。 
   
   ![CloudPassage テスト ユーザーの作成][24]
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「Britta」と入力します。 
  
    b. **[姓]** ボックスに「Simon」と入力します。
  
    c. **[Username]**、**[Email]**、**[Retype Email]** の各ボックスに、Britta の Azure AD でのユーザー名を入力します。
  
    d. **[Access Type]** で **[Enable Halo Portal Access]** を選択します。
  
    e. **[追加]**をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に CloudPassage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を CloudPassage に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[CloudPassage]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [CloudPassage] タイルをクリックすると、自動的に CloudPassage アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_203.png


