---
title: "チュートリアル: Azure Active Directory と Bonusly の統合 | Microsoft Docs"
description: "Azure Active Directory と Bonusly の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 42875d53-0769-4520-a6af-7308b5240d6b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: af00ed428dda846a06466df1e30ee7e8a7e5758c
ms.openlocfilehash: c160829519a14a55e76a2ecedcb286663dc89d65
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>チュートリアル: Azure Active Directory と Bonusly の統合

このチュートリアルでは、Bonusly と Azure Active Directory (Azure AD) を統合する方法について説明します。

Bonusly と Azure AD の統合には、次の利点があります。

- Bonusly にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Bonusly に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Bonusly と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Bonusly でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Bonusly の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-bonusly-from-the-gallery"></a>ギャラリーからの Bonusly の追加
Azure AD への Bonusly の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Bonusly を追加する必要があります。

**ギャラリーから Bonusly を追加するには、次の手順を実行します。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Bonusly**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_001.png)

5. 結果ウィンドウで **[Bonusly]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Bonusly で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Bonusly ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Bonusly の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Bonusly の **[Username]** の値として割り当てることで確立されます。

Bonusly で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Bonusly のテスト ユーザーの作成](#creating-a-bonusly-test-user)** - Bonusly で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にし、Bonusly アプリケーションでシングル サインオンを構成します。

**Bonusly で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Microsoft Azure 管理ポータルの **Bonusly** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_01.png)

3. **[Bonusly のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_02.png)

    a. **[識別子]** ボックスに、値として「`bonusly`」と入力します。
    
    b. **[応答 URL]** ボックスに、`https://bonus.ly/saml/<APP-ID>/consume` のパターンを使用して URL を入力します。
    
4. **SP 開始モード**でアプリケーションを構成する場合は、**[Bonusly のドメインと URL]** セクションで次の手順を実行します。
    
    ![Configure Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_03.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[サインオン URL]** ボックスに、`https://bonus.ly/saml/<your_subdomain>/consume` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL、識別子、および応答 URL で値を更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、[Bonusly サポート チーム](mailto:sales@easyterritory.com)に連絡してください。

5. **[SAML 署名証明書]** セクションで、**[新しい証明書の作成]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_04.png)     

6. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bonusly-tutorial/tutorial_general_300.png)

7. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_05.png)

8. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bonusly-tutorial/tutorial_general_400.png)

9. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_06.png) 

10. **[Bonusly 構成]** セクションで、**[Bonusly の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_07.png) 

    ![Configure Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_08.png)

11. 別の Web ブラウザー ウィンドウで、Bonusly 企業サイトに管理者としてログインします。

12. 上部のツール バーの **[Settings]** をクリックし、**[Integrations and apps]** を選択します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_002.png)

13. **[Single Sign-On]** の **[SAML]** を選択します。

14. **[SAML]** ダイアログ ページで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_003.png)

    a. **[IdP SSO target URL]** (IdP SSO ターゲット URL) ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL]** (SAML シングル サインオン サービス URL) の値を入力します。

    b. **[IdP Login URL]** (IdP ログイン URL) ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-On Service URL]** (SAML シングル サインオン サービス URL) の値を入力します。

    c. **[IdP Issuer]** (IdP 発行者) ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Entity ID]** (SAML エンティティ ID) の値を入力します。

    d. ダウンロードした証明書から拇印の値をコピーして、**[Cert Fingerprint]** (証明書の指紋) ボックスに貼り付けます。

    e. **[保存]**をクリックします。

    > [!NOTE] 
    > 詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](https://www.youtube.com/watch?v=YKQF266SAxI&feature=youtu.be)」をご覧ください。



### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bonusly-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bonusly-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bonusly-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-bonusly-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-a-bonusly-test-user"></a>Bonusly テスト ユーザーの作成

Azure AD ユーザーが Bonusly にログインできるようにするには、ユーザーを Bonusly にプロビジョニングする必要があります。
Bonusly の場合、プロビジョニングは手動で行います。

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. Bonusly 企業サイトに管理者としてログインします。

2. **[設定]**をクリックします。

    ![New User](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_010.png "New User")

3. **[Users and bonuses]** タブをクリックします。

    ![New User](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_011.png "New User")

4. **[Manage Users]**をクリックします。

    ![New User](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_012.png "New User")

5. **[ユーザーの追加]**をクリックします。

    ![New User](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_013.png "New User")

6. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![New User](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_014.png "New User")

    a. **[名]** ボックスに「**Britta**」と入力します。  

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    d. **[保存]**をクリックします。

    > [!NOTE] 
    > アカウントがアクティブになる前に、AAD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。 Bonus.ly から提供されている他の Bonus.ly ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。



### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Bonusly へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Britta Simon を Bonusly に割り当てるには、次の手順を実行します。**

1. Microsoft Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Bonusly]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Bonusly] タイルをクリックすると、Bonusly アプリケーションに自動的にサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_203.png
