---
title: "チュートリアル: Azure Active Directory と Tableau Server の統合 | Microsoft Docs"
description: "Azure Active Directory と Tableau Server の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 6b35609d88fbbf649e15863901d521886db2a4d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>チュートリアル: Azure Active Directory と Tableau Server の統合

このチュートリアルでは、Tableau Server と Azure Active Directory (Azure AD) を統合する方法について説明します。

Tableau Server と Azure AD の統合には、次の利点があります。

- Tableau Server にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが各自の Azure AD アカウントで Tableau Server に自動的にサインオン (シングル サインオン) するように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Tableau Server の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Tableau Server のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Tableau Server を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-tableau-server-from-the-gallery"></a>ギャラリーから Tableau Server を追加する
Azure AD への Tableau Server の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tableau Server を追加する必要があります。

**ギャラリーから Tableau Server を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「 **Tableau Server**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_search.png)

5. 結果ウィンドウで **[Tableau Server]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Tableau Server で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Tableau Server ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Tableau Server の関連ユーザーの間で、リンク関係が確立されている必要があります。

Tableau Server で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Tableau Server で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Tableau Server テスト ユーザーの作成](#creating-a-tableau-server-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Tableau Server で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Tableau Server アプリケーションでシングル サインオンを構成します。

**Tableau Server で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Tableau Server** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. **[Tableau Server のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. **[サインオン URL]** ボックスに、`https://azure.<domain name>.link` のパターンを使用して URL を入力します。
    
    b. **[識別子]** ボックスに、`https://azure.<domain name>.link` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://azure.<domain name>.link/wg/saml/SSO/index.html` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > 上記の値は、実際の値ではありません。 後で、[Tableau Server Configiuration]\(Tableau Server の構成\) ページから入手した実際の URL と識別子で値を更新します。 

4. Tableau Server アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/3.png)
    
5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ---------------| --------------- |    
    | username | *user.displayname* |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。


6. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![シングル サインオンの構成](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. アプリケーションに合わせて SSO を構成するには、管理者として Tableau Server テナントにサインオンする必要があります。
   
   a. Tableau Server の構成で、**[SAML]** タブをクリックします。
  
    ![Configure Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. **[Use SAML for single sign-on]** チェックボックスをオンにします。
   
   c. [Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。http://tableau_server など。 http://localhost の使用は推奨されません。 末尾にスラッシュが付いている URL (http://tableau_server/ など) はサポートされていません。 **[Tableau Server return URL]\(Tableau Server の戻り先 URL\)** をコピーし、Azure AD の **[Tableau Server のドメインと URL]** セクションにある **[サインオン URL]** ボックスに貼り付けます。
   
   d. [SAML entity ID]: IdP に対して Tableau Server のインストールを一意に識別するエンティティ ID。 必要に応じてこの欄にも Tableau Server URL を入力できますが、使用する Tableau Server URL にする必要はありません。 **[SAML entity ID]\(SAML エンティティ ID\)** をコピーし、Azure AD の **[Tableau Server のドメインと URL]** セクションにある **[識別子]** ボックスに貼り付けます。
     
   e. **[Export Metadata File]\(メタデータ ファイルのエクスポート\)** をクリックし、テキスト エディター アプリケーションで開きます。 Http Post で Index 0 の [Assertion Consumer Service URL] を探し、URL をコピーします。 Azure AD の **[Tableau Server のドメインと URL]** セクションにある **[応答 URL]** ボックスに貼り付けます。
   
   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure Portal からダウンロードしたフェデレーション メタデータ ファイルを検索し、**[SAML Idp metadata file]\(SAML Idp メタデータ ファイル\)** でアップロードします。
   
   g. [Tableau Server Configiuration]\(Tableau Server の構成\) ページの **[OK]** ボタンをクリックします。
   
    >[!NOTE] 
    >顧客は任意の証明書を Tableau Server の SAML SSO 構成でアップロードする必要があります。SSO フローではその証明書は無視されます。
    >Tableau Server で SAML を構成する方法について不明な点がある場合は、[SAML の構成](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm)に関する記事を参照してください。
    >
<CE>

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-tableau-server-test-user"></a>Tableau Server テスト ユーザーの作成

このセクションの目的は、Tableau Server で Britta Simon というユーザーを作成することです。 Tableau Server 内のすべてのユーザーをプロビジョニングする必要があります。 

また、ユーザーのユーザー名は、Azure AD のカスタム属性 **username** で構成した値と一致する必要があります。 正しい対応付けがあれば、統合で「 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)」が機能します。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、組織の Tableau Server 管理者に問い合わせてください。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Tableau Server へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Tableau Server に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Tableau Server]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Tableau Server] タイルをクリックすると、Tableau Server アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)に関する記事をご覧ください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png

