---
title: チュートリアル:Azure Active Directory と LiquidFiles の統合 | Microsoft Docs
description: Azure Active Directory と LiquidFiles の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: cb517134-0b34-4a74-b40c-5a3223ca81b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20a3144f2a8727420803034426106a29a7924727
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167305"
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>チュートリアル:Azure Active Directory と LiquidFiles の統合

このチュートリアルでは、LiquidFiles と Azure Active Directory (Azure AD) を統合する方法について説明します。

LiquidFiles と Azure AD の統合には、次の利点があります。

- LiquidFiles にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に LiquidFiles にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

LiquidFiles と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LiquidFiles でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LiquidFiles の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-liquidfiles-from-the-gallery"></a>ギャラリーからの LiquidFiles の追加
Azure AD への LiquidFiles の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LiquidFiles を追加する必要があります。

**ギャラリーから LiquidFiles を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**LiquidFiles**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/liquidfiles-tutorial/tutorial_liquidfiles_search.png)

1. 結果ウィンドウで **LiquidFiles** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/liquidfiles-tutorial/tutorial_liquidfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LiquidFiles で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LiquidFiles ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LiquidFiles の関連ユーザーの間で、リンク関係が確立されている必要があります。

LiquidFiles で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

LiquidFiles で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[LiquidFiles テスト ユーザーの作成](#creating-a-liquidfiles-test-user)** - LiquidFiles で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、LiquidFiles アプリケーションでシングル サインオンを構成します。

**LiquidFiles で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **LiquidFiles** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/liquidfiles-tutorial/tutorial_liquidfiles_samlbase.png)

1. **[LiquidFiles のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/liquidfiles-tutorial/tutorial_liquidfiles_url.png)

    a. **[サインオン URL]** ボックスに、`https://<YOUR_SERVER_URL>/saml/init` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<YOUR_SERVER_URL>` の形式で URL を入力します。

    c. b. **[応答 URL]** ボックスに、`https://<YOUR_SERVER_URL>/saml/consume` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[LiquidFiles クライアント サポート チーム](https://www.liquidfiles.com/support.html)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、証明書の **[拇印]** の値をコピーします。

    ![Configure single sign-on](./media/liquidfiles-tutorial/tutorial_liquidfiles_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/liquidfiles-tutorial/tutorial_general_400.png)

1. **[LiquidFiles 構成]** セクションで、**[LiquidFiles の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **クイック リファレンス セクション**から、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/liquidfiles-tutorial/tutorial_liquidfiles_configure.png)
 
1. LiquidFiles 企業サイトに管理者としてサインオンします。

1. メニューから **[Admin]\(管理\) > [Configuration]\(構成\)** をクリックし、**[Single Sign-On]\(シングル サインオン\)** をクリックします。

1. **[Single Sign-On Configuration]\(シングル サインオンの構成\)** ページで、次の手順を実行します。

    ![Configure single sign-on](./media/liquidfiles-tutorial/tutorial_single_01.png)

    a. **[Single Sign On Method]\(シングル サインオンの方法\)** として、**[SAML 2]** を選びます。

    b. **[IDP Login URL]\(IDP ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[IDP Logout URL]\(IDP ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[IDP Cert Fingerprint]\(IDP 証明書の拇印)** ボックスに、Azure Portal からコピーした**拇印**値を貼り付けます。

    e. [Name Identifier Format]\(名前識別子形式\) ボックスに、値「**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**」を入力します。

    f. [Authn Context]\(認証コンテキスト\) ボックスに、値「**urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport**」を入力します。

    g. **[Save]** をクリックします。  

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/liquidfiles-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/liquidfiles-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/liquidfiles-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/liquidfiles-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-liquidfiles-test-user"></a>LiquidFiles テスト ユーザーの作成

このセクションの目的は、LiquidFiles で Britta Simon というユーザーを作成することです。 LiquidFiles アプリケーションにログインする前に、LiquidFiles サーバー管理者と協力して自分自身をユーザーとして追加します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LiquidFiles へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**LiquidFiles に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[LiquidFiles]** を選択します。

    ![Configure single sign-on](./media/liquidfiles-tutorial/tutorial_liquidfiles_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで LiquidFiles のタイルをクリックすると、自動的に LiquidFiles アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/liquidfiles-tutorial/tutorial_general_01.png
[2]: ./media/liquidfiles-tutorial/tutorial_general_02.png
[3]: ./media/liquidfiles-tutorial/tutorial_general_03.png
[4]: ./media/liquidfiles-tutorial/tutorial_general_04.png

[100]: ./media/liquidfiles-tutorial/tutorial_general_100.png

[200]: ./media/liquidfiles-tutorial/tutorial_general_200.png
[201]: ./media/liquidfiles-tutorial/tutorial_general_201.png
[202]: ./media/liquidfiles-tutorial/tutorial_general_202.png
[203]: ./media/liquidfiles-tutorial/tutorial_general_203.png

