---
title: "チュートリアル: Azure Active Directory と Citrix ShareFile の統合 | Microsoft Docs"
description: "Azure Active Directory と Citrix ShareFile の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ddf6c69b-4a76-4b42-8619-6f2a22800a23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9dfbf4bcdcd580773a368b90c869bf9c4fefbd77
ms.lasthandoff: 03/25/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>チュートリアル: Azure Active Directory と Citrix ShareFile の統合

このチュートリアルでは、Citrix ShareFile と Azure Active Directory (Azure AD) を統合する方法について説明します。

Citrix ShareFile と Azure AD の統合には、次の利点があります。

- Citrix ShareFile にアクセスできるユーザーを Azure AD で制御できます。
- ユーザーが Azure AD アカウントで自動的に Citrix ShareFile にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Citrix ShareFile の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Citrix ShareFile でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Citrix ShareFile の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-citrix-sharefile-from-the-gallery"></a>ギャラリーからの Citrix ShareFile の追加
Azure AD への Citrix ShareFile の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Citrix ShareFile を追加する必要があります。

**ギャラリーから Citrix ShareFile を追加するには、次の手順を実行します。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Citrix ShareFile**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_001.png)

5. 結果ウィンドウで **[Citrix ShareFile]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Citrix ShareFile で Azure AD シングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Citrix ShareFile ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Citrix ShareFile の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Citrix ShareFile の **[Username]** の値として割り当てます。

Citrix ShareFile で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Citrix ShareFile テスト ユーザーの作成](#creating-a-citrix-sharefile-test-user)** - Citrix ShareFile で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure 管理ポータルで Azure AD シングル サインオンを有効にし、Citrix ShareFile アプリケーションでシングル サインオンを構成します。

**Citrix ShareFile で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure 管理ポータルの **Citrix ShareFile** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_01.png)

3. **[Citrix ShareFile のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_02.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<tenant-name>.sharefile.com/saml/login` のパターンを使用して URL を入力します。    

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 これらの URL がわからない場合は、サンプル パターンを使用してサンプル URL を入力します。実際の URL は、手順 13. の完了後に取得できます。

4. **[SAML 署名証明書]** セクションで、**[Certificate (base64) (証明書 (base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_05.png) 

5. **[Citrix ShareFile Configuration (Citrix ShareFile 構成)]** セクションで、**[Configure Citrix ShareFile (Citrix ShareFile の構成)]** をクリックして **[サインオンの構成]** ウィンドウを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_06.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_07.png)

6. 別の Web ブラウザー ウィンドウで、Citrix ShareFile 企業サイトに管理者としてログインします。

7. 上部のツールバーの **[Admin]**をクリックします。

8. 左側のナビゲーション ウィンドウで、 **[Configure Single Sign-On]**を選択します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_002.png)

9. **[Single Sign-On/ SAML 2.0 Configuration]** ダイアログ ページの **[基本設定]** で、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-sharefile-tutorial/sso_configuration_2.png)

    a. **[Enable SAML]**をクリックします。

    b. **[Your IDP Issuer/ Entity ID]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Entity ID (SAML エンティティ ID)]** の値を入力します。

    c. **[X.509 Certificate]** フィールドの横の **[Change]** をクリックし、ダウンロードした証明書ファイルをアップロードします。 

    d. **[Login URL (ログイン URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    e. **[Logout URL]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[Sign-Out URL (サインアウト URL)]** の値を入力します。

10. Citrix ShareFile 管理ポータルで **[Save]** をクリックします。
    
 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-a-citrix-sharefile-test-user"></a>Citrix ShareFile テスト ユーザーの作成

Azure AD ユーザーが Citrix ShareFile にログインできるようにするには、ユーザーを Citrix ShareFile にプロビジョニングする必要があります。 Citrix ShareFile の場合、プロビジョニングは手動で行います。

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. Citrix ShareFile 企業サイトに管理者としてログインします。

2. 上部のツール バーの **[Manage Users]** をクリックします。 **[Manage Users Home]** に移動し、**[Create Employee]** をクリックします。

    ![New User](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_010.png "New User")

3. **[Basic Information]** セクションで、次の手順を実行します。

    ![New User](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_011.png "New User")

    a. **[Email Address]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。  

    b. **[名]** ボックスに「**Britta**」と入力します。  

    c. **[姓]** ボックスに「**Simon**」と入力します。

4. **[ユーザーの追加]**をクリックします。

    > [!NOTE]
    > AAD アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。 Citrix ShareFile から提供されている他の Citrix ShareFile ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。



### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Citrix ShareFile へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Citrix ShareFile に割り当てるには、次の手順を実行します。**

1. Microsoft Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Citrix ShareFile]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Citrix ShareFile] タイルをクリックすると、自動的に Citrix ShareFile アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png
