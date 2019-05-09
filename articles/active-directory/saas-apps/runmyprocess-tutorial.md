---
title: チュートリアル:Azure Active Directory と RunMyProcess の統合 | Microsoft Docs
description: Azure Active Directory と RunMyProcess の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfef1371b7ac61712c0f70efd48c0e791c4c729d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162681"
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>チュートリアル:Azure Active Directory と RunMyProcess の統合

このチュートリアルでは、RunMyProcess と Azure Active Directory (Azure AD) を統合する方法について説明します。

RunMyProcess と Azure AD の統合には、次の利点があります。

- RunMyProcess にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで RunMyProcess に自動的にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

RunMyProcess と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- RunMyProcess でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの RunMyProcess の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-runmyprocess-from-the-gallery"></a>ギャラリーからの RunMyProcess の追加
Azure AD への RunMyProcess の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RunMyProcess を追加する必要があります。

**ギャラリーから RunMyProcess を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**RunMyProcess**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/runmyprocess-tutorial/tutorial_runmyprocess_search.png)

1. 結果パネルで **[RunMyProcess]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、RunMyProcess で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する RunMyProcess ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと RunMyProcess の関連ユーザーの間でリンク関係が確立されている必要があります。

RunMyProcess で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

RunMyProcess で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[RunMyProcess のテスト ユーザーの作成](#creating-a-runmyprocess-test-user)** - RunMyProcess で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、RunMyProcess アプリケーションでシングル サインオンを構成します。

**RunMyProcess で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **RunMyProcess** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

1. **[RunMyProcess のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    **[サインオン URL]** ボックスに、`https://live.runmyprocess.com/live/<tenant id>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[RunMyProcess クライアント サポート チーム](mailto:support@runmyprocess.com)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_general_400.png)

1. **[RunMyProcess Configuration (RunMyProcess 構成)]** セクションで、**[Configure RunMyProcess (RunMyProcess を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **クイック リファレンス セクション**から、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

1. 別の Web ブラウザーのウィンドウで、管理者として RunMyProcess テナントにサインオンします。

1. 左側のナビゲーション パネルで、**[Account (アカウント)]**、**[Configuration (構成)]** の順にクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. **[Authentication method (認証方法)]** セクションに移動し、次の手順に従います。
   
    ![アプリ側でのシングル サインオンの構成](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. **[Method]** として、**[SSO with Samlv2]** を選択します。 

    b. **[SSO redirect (SSO リダイレクト)]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[Logout redirect (ログアウト リダイレクト)]** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[名前識別子形式]** ボックスで、**名前識別子形式**の値を「**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**」と入力します。

    e. ダウンロードした証明書ファイルのコンテンツをコピーし、**[Certificate (証明書)]** ボックスに貼り付けます。 
 
    f. **[Save (保存)]** アイコンをクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/runmyprocess-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/runmyprocess-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/runmyprocess-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/runmyprocess-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-runmyprocess-test-user"></a>RunMyProcess のテスト ユーザーの作成

Azure AD ユーザーが RunMyProcess にログインできるようにするには、ユーザーを RunMyProcess にプロビジョニングする必要があります。 RunMyProcess の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. RunMyProcess 企業サイトに管理者としてログインします。

1. 左側のナビゲーション パネルで **[Account (アカウント)]**、**[Users (ユーザー)]** の順にクリックし、**[New User (新しいユーザー)]** をクリックします。
   
    ![New User](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "New User")

1. **[ユーザーの設定]** セクションで、次の手順に従います。
   
    ![プロファイル](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profile") 
  
    a. プロビジョニングする有効な Azure AD アカウントの**名前**と**メール**を対応するボックスに入力します。 

    b. **[IDE 言語]**、**[言語]**、**[プロファイル]** を選択します。 

    c. **[アカウント作成の電子メールを自分に送信]** を選択します。 

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Save]** をクリックします。
   
    >[!NOTE]
    >他の RunMyProcess ユーザー アカウントの作成ツールまたは RunMyProcess から提供されている API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に RunMyProcess へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を RunMyProcess に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[RunMyProcess]** を選択します。

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [RunMyProcess] タイルをクリックすると、RunMyProcess アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/runmyprocess-tutorial/tutorial_general_203.png

