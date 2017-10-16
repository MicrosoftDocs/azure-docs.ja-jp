---
title: "チュートリアル: Azure Active Directory と AirWatch の統合 | Microsoft Docs"
description: "Azure Active Directory と AirWatch の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 1996ec97e7c0d94c5606ca43bb5956548f1f3712
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>チュートリアル: Azure Active Directory と AirWatch の統合

このチュートリアルでは、AirWatch と Azure Active Directory (Azure AD) を統合する方法について説明します。

AirWatch と Azure AD の統合には、次の利点があります。

- AirWatch にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に AirWatch にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

AirWatch と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- AirWatch でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから AirWatch を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-airwatch-from-the-gallery"></a>ギャラリーから AirWatch を追加する
Azure AD への AirWatch の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AirWatch を追加する必要があります。

**ギャラリーから AirWatch を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. [検索] ボックスに、「**AirWatch**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. 結果ウィンドウで **AirWatch** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、AirWatch で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する AirWatch ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと AirWatch の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、AirWatch の **[ユーザー名]** の値として割り当てることで確立されます。

AirWatch で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[AirWatch テスト ユーザーの作成](#creating-a-airwatch-test-user)** - AirWatch で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、AirWatch アプリケーションでシングル サインオンを構成します。

**AirWatch で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **AirWatch** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. **[AirWatch Domain and URLs]\(AirWatch のドメインと URL\)** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、値として「`AirWatch`」と入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[AirWatch Client サポート チーム](http://www.air-watch.com/company/contact-us/)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. **[AirWatch Configuration]\(AirWatch 構成\)** セクションで、**[Configure AirWatch]\(AirWatch を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. **[保存]** ボタンをクリックします。

    ![シングル サインオンを構成する](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. 別の Web ブラウザーのウィンドウで、管理者として AirWatch 企業サイトにログインします。

8. 左側のナビゲーション ウィンドウで、**[Accounts]**、**[Administrators]** の順にクリックします。
   
   ![管理者](./media/active-directory-saas-airwatch-tutorial/ic791920.png "Administrators")

9. **[Settings]** メニューを展開し、**[Directory Services]** をクリックします。
   
   ![設定](./media/active-directory-saas-airwatch-tutorial/ic791921.png "Settings")

10. **[ユーザー]** タブをクリックし、**[ベース DN]** テキストボックスにドメイン名を入力してから **[保存]** をクリックします。
   
   ![ユーザー](./media/active-directory-saas-airwatch-tutorial/ic791922.png "User")

11. [ **Server** ] タブをクリックします。
   
   ![サーバー](./media/active-directory-saas-airwatch-tutorial/ic791923.png "Server")

12. 次の手順に従います。
    
    ![アップロード](./media/active-directory-saas-airwatch-tutorial/ic791924.png "Upload")   
    
    a. **[Directory Type]** として **[None]** を選択します。

    b. [ **Use SAML For Authentication**] を選択します。

    c. ダウンロードした証明書をアップロードするには、[ **Upload**] をクリックします。

13. [ **Request** ] セクションで、次の手順に従います。
    
    ![要求](./media/active-directory-saas-airwatch-tutorial/ic791925.png "Request")  

    a. **[Request Binding Type]** として **[POST]** を選択します。

    b. Azure Portal の **[Airwatch でのシングル サインオンの構成]** ダイアログ ページで、**SAML シングル サインオン サービス URL** の値をコピーし、**[ID プロバイダー シングル サインオン URL]** テキスト ボックスに貼り付けます。

    c. **[NameID Format]** として **[Email Address]** を選択します。

    d. [ **Save**] をクリックします。

14. [ **User** ] タブをもう一度クリックします。
    
    ![ユーザー](./media/active-directory-saas-airwatch-tutorial/ic791926.png "User")

15. [ **Attribute** ] セクションで、次の手順に従います。
    
    ![属性](./media/active-directory-saas-airwatch-tutorial/ic791927.png "Attribute")

    a. **[Object Identifier]** ボックスに、**http://schemas.microsoft.com/identity/claims/objectidentifier** と入力します。

    b. **[Username]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** と入力します。

    c. **[Display Name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** と入力します。

    d. **[First Name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** と入力します。

    e. **[Last Name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** と入力します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Email]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** と入力します。

    g. [ **Save**] をクリックします。

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-airwatch-test-user"></a>AirWatch テスト ユーザーを作成する

Azure AD ユーザーが AirWatch にログインできるようにするには、そのユーザーを AirWatch にプロビジョニングする必要があります。

* AirWatch の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **AirWatch** 企業サイトに管理者としてログインします。
2. 左側のナビゲーション ウィンドウで、**[Accounts]**、**[Users]** の順にクリックします。
   
   ![ユーザー](./media/active-directory-saas-airwatch-tutorial/ic791929.png "Users")
3. **[Users]** メニューで、**[List View]**、**[Add] \> [Add User]** の順にクリックします。
   
   ![ユーザーの追加](./media/active-directory-saas-airwatch-tutorial/ic791930.png "Add User")
4. [ **Add / Edit User** ] ダイアログで、次の手順を実行します。

   ![ユーザーの追加](./media/active-directory-saas-airwatch-tutorial/ic791931.png "Add User")   
   1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの **[Username]**、**[Password]**、**[Confirm Password]**、**[First Name]**、**[Last Name]**、**[Email Address]** を入力します。
   2. **[Save]**をクリックします。

>[!NOTE]
>他の AirWatch ユーザー アカウントの作成ツールまたは AirWatch から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に AirWatch へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**AirWatch に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[AirWatch]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png

