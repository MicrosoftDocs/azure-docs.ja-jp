---
title: "チュートリアル: Azure Active Directory と Evernote の統合 | Microsoft Docs"
description: "Azure Active Directory と Evernote の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: e4e2ca40ae270e7d3bfe4e828fcd571b5594ffc8
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>チュートリアル: Azure Active Directory と Evernote の統合

このチュートリアルでは、Evernote と Azure Active Directory (Azure AD) を統合する方法について説明します。

Evernote と Azure AD の統合には、次の利点があります。

- Evernote にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Evernote にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Evernote の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Evernote でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Evernote の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-evernote-from-the-gallery"></a>ギャラリーからの Evernote の追加
Azure AD への Evernote の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Evernote を追加する必要があります。

**ギャラリーから Evernote を追加するには、次の手順に従います。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Evernote**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search01.png)

5. 結果ウィンドウで **[Evernote]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search_result01.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Evernote で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Evernote ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Evernote の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Evernote の **[Username]** の値として割り当てます。

Evernote で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
4. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Evernote アプリケーションでシングル サインオンを構成します。

**Evernote で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure 管理ポータルの **Evernote** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. **IDP 開始モード**でアプリケーションを構成する場合は、**[Evernote のドメインと URL]** セクションで手順を実行する必要はありません。

    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_idp.png)
    
4. **SP 開始モード**でアプリケーションを構成する場合は、**[Evernote のドメインと URL]** セクションで次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sp_01.png)
    
    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[サインオン URL]** テキストボックスに、サインオン URL `https://www.evernote.com/Login.action` を入力します。

5. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_cert.png)     

6. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_general_300.png)

7. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_makecertactive.png)

8. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

9. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

10. **[Evernote Configuration (Evernote 構成)]** セクションで、**[Configure Evernote (Evernote を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![シングル サインオンの構成](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configuresignon.png)

11. 別の Web ブラウザー ウィンドウで、Evernote 企業サイトに管理者としてログインします。

12. **[管理コンソール]** に移動します。

    ![管理コンソール](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

13. **[管理コンソール]** から **[セキュリティ]** に移動し、**[シングル サインオン]** を選択します。

    ![SSO の設定](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

14. 次の値を構成します。

    a.  **SSO の有効化** - SSO は既定で有効になっています (SSO の要件を削除する場合は、**[シングル サインオンを無効にする]** をクリックします)

    b. **SAML HTTP 要求 URL** - Azure AD の**[Configure Evernote (Evernote 構成)]** セクションから **SAML シングル サインオン サービス URL** を入力します

    c. **X.509 証明書** - Azure AD からダウンロードした証明書をメモ帳で開き、"BEGIN CERTIFICATE" および "END CERTIFICATE" を含む内容をコピーします

    ![Certificate-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)

    d. **[変更の保存]** をクリックします。 


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。 



### <a name="creating-an-evernote-test-user"></a>Evernote テスト ユーザーの作成

Azure AD ユーザーが Evernote にログインできるようにするには、そのユーザーを Evernote にプロビジョニングする必要があります。  
Evernote の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Evernote の企業サイトに管理者としてログインします。

2. **[管理コンソール]** をクリックします。

    ![管理コンソール](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. **[管理コンソール]** から **[ユーザーの追加]** に移動します。

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **[メール]** テキストボックスの **[チーム メンバーを追加]** にユーザー アカウントのメール アドレスを入力し、**[招待する]** をクリックします。

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. 招待が送信された後、招待を承諾するメールが Azure Active Directory アカウント保有者に届きます。   


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Evernote へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Evernote に割り当てるには、次の手順に従います。**

1. Microsoft Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Evernote]**を選択します。

    ![シングル サインオンの構成](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Evernote のタイルをクリックすると、Evernote アプリケーションに自動的にサインオンします。 組織のアカウントとしてログインしますが、その後に個人用アカウントでログインする必要があります。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

