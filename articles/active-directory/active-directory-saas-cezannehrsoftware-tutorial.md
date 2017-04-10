---
title: "チュートリアル: Azure Active Directory と Cezanne HR Software の統合 | Microsoft Docs"
description: "Azure Active Directory と Cezanne HR Software の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9585494ebff68891d374a29e8e3e4b7756914bcc
ms.openlocfilehash: d8a654340df56002e503f2f61e910facb51696c5
ms.lasthandoff: 02/06/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>チュートリアル: Azure Active Directory と Cezanne HR Software の統合

このチュートリアルでは、Cezanne HR Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

Cezanne HR Software と Azure AD の統合には、次の利点があります。

- Cezanne HR Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Cezanne HR Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Cezanne HR Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Cezanne HR Software でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Cezanne HR Software の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>ギャラリーからの Cezanne HR Software の追加
Azure AD への Cezanne HR Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cezanne HR Software を追加する必要があります。

**ギャラリーから Cezanne HR Software を追加するには、次の手順に従います。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Cezanne HR Software**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_00001.png)

5. 結果ウィンドウで **[Cezanne HR Software]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Cezanne HR Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Cezanne HR Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Cezanne HR Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を Cezanne HR Software の **[Username (ユーザー名)]** の値として割り当てます。

Cezanne HR Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Cezanne HR Software のテスト ユーザーの作成](#creating-a-cezanne-hr-software-test-user)** - Cezanne HR Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Cezanne HR Software アプリケーションでシングル サインオンを構成します。

**Cezanne HR Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Cezanne HR Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure Single Sign-On][4]

2. **[シングル サインオン]** ダイアログ ページで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

3. **[Cezanne HR Software のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_02.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>` のパターンを使用して URL を入力します。
    
    b. **[識別子]** ボックスに、「`https://w3.cezanneondemand.com/CezanneOnDemand/`」と入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 ここでは、識別子に一意の URL を使用することをお勧めします。 これらの値を入手するには、[Cezanne HR Software サポート チーム](mailto:info@cezannehr.com)にお問い合わせください。

4. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)     

5. **[新しい証明書の作成]**ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_300.png)

6. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

7. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

8. **[SAML 署名証明書]** セクションで、**[Certificate (base64) (証明書 (base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png) 

9. **[Cezanne HR Software 構成]** セクションで **[Cezanne HR Software の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_06.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_07.png)

10. 別の Web ブラウザーのウィンドウで、管理者として Cezanne HR Software テナントにサインオンします。

11. 左側のナビゲーション ウィンドウで、 **[System Setup (システム設定)]**をクリックします。 **[Security Settings (セキュリティの設定)]**に移動します。 次に、 **[Single Sign-On Configuration (シングル サインオンの構成)]**に移動します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

12. **[Allow users to log in using the following Single Sign-On (SSO) Service (ユーザーに次のシングル サインオン (SSO) サービスを使用したログインを許可する)]** パネルで **[SAML 2.0]** チェック ボックスをオンにして、**[Advanced Configuration (詳細設定)]** オプションを選択します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

13. **[Add New (新規追加)]** ボタンをクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

14. **[SAML 2.0 IDENTITY PROVIDERS (SAML 2.0 ID プロバイダー)]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[Display Name (表示名)]** に ID プロバイダー名を入力します。

    b. **[Entity Identifier (エンティティ識別子)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Entity ID (SAML エンティティ ID)]** の値を入力します。

    c. **[SAML Binding (SAML バインディング)]** を "POST" に変更します。

    d. **[Security Token Service Endpoint (セキュリティ トークン サービス エンドポイント)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    e. **[User ID Attribute Name (ユーザー ID 属性名)]** ボックスに、「http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name」と入力します。

    f. **アップロード** アイコンをクリックして、Azure AD からダウンロードした証明書をアップロードします。

    g. **[OK]** ボタンをクリックします。 

15. **[保存]** ボタンをクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)



### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-a-cezanne-hr-software-test-user"></a>Cezanne HR Software のテスト ユーザーの作成

Azure AD ユーザーが Cezanne HR Software にログインできるようにするには、ユーザーを Cezanne HR Software にプロビジョニングする必要があります。 Cezanne HR Software の場合、プロビジョニングは手動で行います。

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  Cezanne HR Software 企業サイトに管理者としてログインします。

2.  左側のナビゲーション ウィンドウで、 **[System Setup (システム設定)]**をクリックします。 **[ユーザーの管理]**に移動します。 **[Add New User (新しいユーザーの追加)]**に移動します。

    ![New User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "New User")

3.  **[Person Details (個人の詳細)]** セクションで、次の手順を実行します。

    ![New User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "New User")

    a. **[Internal User (内部ユーザー)]** をオフに設定します。

    b. **[名]** ボックスに「**Britta**」と入力します。  

    c. **[姓]** ボックスに「**Simon**」と入力します。

    d. **[E-mail (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

4.  **[Account Information (アカウント情報)]** セクションで、次の手順を実行します。

    ![New User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "New User")

    a. **[Username (ユーザー名)]** ボックスに、Britta Simon の電子メール アドレスを入力します。

    b. **[Password (パスワード)]** ボックスに、Britta Simon アカウントのパスワードを入力します。

    c. **[Security Role (セキュリティ ロール)]** で **[HR Professional (人事担当者)]** を選択します。

    d. **[OK]**をクリックします。

5. **[Single Sign-On (シングル サインオン)]** タブに移動し、**[SAML 2.0 Identifiers (SAML 2.0 識別子)]** 領域で **[Add New (新規追加)]** を選択します。

    ![ユーザー](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "User")

6. **[Identity Provider (ID プロバイダー)]** でお使いの ID プロバイダーを選択し、**[User Identifier (ユーザー識別子)]** ボックスに Britta Simon アカウントの電子メール アドレスを入力します。

    ![ユーザー](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "User")
    
7. **[保存]** ボタンをクリックします。

    ![ユーザー](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "User")



### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Cezanne HR Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Cezanne HR Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Cezanne HR Software]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** をクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cezanne HR Software] タイルをクリックすると、自動的に Cezanne HR Software アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
