---
title: "チュートリアル: Azure Active Directory と SD Elements の統合 | Microsoft Docs"
description: "Azure Active Directory と SD Elements の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 624eff0a0da8f548877e4a4346b21df89cd37b67
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>チュートリアル: Azure Active Directory と SD Elements の統合

このチュートリアルでは、SD Elements と Azure Active Directory (Azure AD) を統合する方法について説明します。

SD Elements と Azure AD の統合には、次の利点があります。

- SD Elements にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SD Elements にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SD Elements と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SD Elements でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから SD Elements を追加します。
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sd-elements-from-the-gallery"></a>ギャラリーから SD Elements を追加します。
Azure AD への SD Elements の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SD Elements を追加する必要があります。

**ギャラリーから SD Elements を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **SD Elements**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_search.png)

5. 結果ウィンドウで **[SD Elements]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SD Elements で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SD Elements ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SD Elements の関連ユーザーの間で、リンク関係が確立されている必要があります。

SD Elements で、Azure AD の**ユーザー名 (user name)** の値を**ユーザー名 (Username)** の値として割り当ててリンク関係を確立します。

SD Elements で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SD Elements テスト ユーザーの作成](#creating-a-sd-elements-test-user)** - SD Elements で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SD Elements アプリケーションでシングル サインオンを構成します。

**SD Elements で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SD Elements** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_samlbase.png)

3. **[SD Elements のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_url.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://<tenantname>.sdelements.com/sso/saml2/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<tenantname>.sdelements.com/sso/saml2/acs/` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SD Elements サポート チーム](mailto:support@sdelements.com)に問い合わせてください。

4. SD Elements アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーションの **[ユーザー属性]** タブから管理できます。 次のスクリーンショットはその例です。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_attribute.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。 

    | 属性名 | 属性値 |
    | --- | --- |
    | 電子メール |User.mail |
    | firstname |User.givenname |
    | lastname |User.surname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_officespace_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[OK]**をクリックします。
 
6. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_general_400.png)

8. **[SD Elements 構成]** セクションで、**[SD Elements の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**SAML エンティティ ID と SAML Single シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_configure.png)

9. シングル サインオンを有効にするには、 [SD Elements サポート チーム](mailto:support@sdelements.com) に連絡し、ダウンロードした証明書ファイルを提示します。 

10. 別のブラウザー ウィンドウで、管理者として SD Elements テナントにサインオンします。

11. 上部のメニューで **[System]\(システム\)** をクリックし、**[Single Sign-on]\(シングル サインオン\)** をクリックします。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) 

12. **[Single Sign-On Settings]** ダイアログで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. **SSO 型** として **SAML** を選びます。
   
    b. **[Identity Provider Entity ID]\(ID プロバイダー エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 
   
    c. **[Identity Provider Single Sign-On Service]\(ID プロバイダー シングル サインオン サービス\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。 
   
    d. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-sd-elements-test-user"></a>SD Elements テスト ユーザーの作成

このセクションの目的は、SD Elements で Britta Simon というユーザーを作成することです。 SD Elements の場合、SD Elements ユーザーは手動で作成します。

**SD Elements で Britta Simon を作成するには、次の手順に従います。**

1. Web ブラウザー ウィンドウで、管理者として SD Elements 企業サイトにサインオンします。

2. 上部のメニューで、**[ユーザー管理]**、**[ユーザー]** の順にクリックします。
   
    ![SD Elements テスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) 

3. **[新しいユーザーの追加]**をクリックします。
   
    ![SD Elements テスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png)
 
4. **[新規ユーザーの追加]** ダイアログで、次の手順を実行します。
   
    ![SD Elements テスト ユーザーの作成](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
    a. **[電子メール]** テキスト ボックスに、ユーザーの電子メール (**brittasimon@contoso.com** など) を入力します。
   
    b. **[名]** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。
   
    c. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。
   
    d. **[ロール]** として **[ユーザー]** を選びます。 
   
    e. **[Create User]**をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SD Elements へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**SD Elements に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SD Elements]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。
  
アクセス パネルで [SD Elements] タイルをクリックすると、自動的に SD Elements アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png


