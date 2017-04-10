---
title: "チュートリアル: Azure Active Directory と GitHub の統合 | Microsoft Docs"
description: "Azure Active Directory と GitHub の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 8b0417d131bc25225e777285b9e4bfbaa97aad8f
ms.lasthandoff: 03/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>チュートリアル: Azure Active Directory と GitHub の統合

このチュートリアルでは、GitHub と Azure Active Directory (Azure AD) を統合する方法について説明します。

GitHub と Azure AD の統合には、次の利点があります。

- GitHub にアクセスする Azure AD ユーザーを制御できる
- ユーザーが自分の Azure AD アカウントで自動的に GitHub にサインオン (シングル サインオン) できるようになる
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

GitHub と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- GitHub でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの GitHub の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-github-from-the-gallery"></a>ギャラリーからの GitHub の追加
Azure AD への GitHub の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に GitHub を追加する必要があります。

**ギャラリーから GitHub を追加するには、次の手順に従います。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**GitHub.com**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. 結果ウィンドウで **[GitHub]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、GitHub で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する GitHub ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと GitHub の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を GitHub の **[Username]** の値として割り当てます。

GitHub で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[GitHub のテスト ユーザーの作成](#creating-a-GitHub-test-user)** - GitHub で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、GitHub アプリケーションにシングル サインオンを構成します。

**GitHub で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **GitHub** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. **[GitHub のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. **[サインオン URL]** テキストボックスに、「`https://github.com/orgs/<entity-id>/sso`」と入力します。

    b. **[識別子]** ボックスに、`https://github.com/orgs/<entity-id>` の形式で URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、GitHub 管理者セクションに移動します。 

4. **[ユーザー属性]** セクションで、**[ユーザー識別子]** として [user.mail] を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
     
5. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

     ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. **[GitHub Configuration (GitHub 構成)]** セクションで、**[Configure GitHub (GitHub を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. 別の Web ブラウザー ウィンドウで、GitHub 組織サイトに管理者としてログインします。

12. **[設定]** に移動し、**[セキュリティ]** をクリックします。

    ![[設定]](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. **[Enable SAML authentication (SAML 認証を有効にする)]** チェック ボックスをオンにして、シングル サインオンの構成フィールドを表示します。 次に、シングル サインオン URL 値を使用して、Azure AD 構成でシングル サインオン URL を更新します。

    ![[設定]](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. 次のフィールドを構成します。

    a. **サインオン URL**: Azure AD の**[Configure GitHub (GitHub 構成)]** セクションから **SAML シングル サインオン サービス URL** を入力します

      b. **発行者**: Azure AD の**[Configure GitHub (GitHub 構成)]** セクションから **SAML エンティティ ID** を入力します

       c. **公開証明書**: Azure AD からダウンロードした証明書をメモ帳で開き、"BEGIN CERTIFICATE" および "END CERTIFICATE" を含む内容をコピーします

    ![[設定]](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. **[Test SAML configuration (SAML 構成のテスト)]** をクリックして、SSO の際に検証の失敗やエラーがないことを確認します。

    ![[設定]](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. **[保存]**

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 


### <a name="creating-a-github-test-user"></a>GitHub テスト ユーザーの作成

Azure AD ユーザーが GitHub にログインできるようにするには、そのユーザーを GitHub にプロビジョニングする必要があります。  
GitHub の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. GitHub 企業サイトに管理者としてログインします。

2. **[ユーザー]**をクリックします。

    ![ユーザー](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "People")

3. **[メンバーの招待]**をクリックします。

    ![ユーザーの招待](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "ユーザーの招待")

4. **[メンバーの招待]** ダイアログ ページで、次の手順を実行します。

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    ![ユーザーの招待](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "ユーザーの招待")
    
    b. **[Send Invitation (招待状の送信)]** をクリックします。

    ![ユーザーの招待](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "ユーザーの招待")

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に GitHub へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**GitHub に Britta Simon を割り当てるには、次の手順を実行します。**

1. Microsoft Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[GitHub.com]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで GitHub のタイルをクリックすると、GitHub アプリケーションに自動的にサインオンします。 組織のアカウントとしてログインしますが、その後に個人用アカウントでログインする必要があります。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png

