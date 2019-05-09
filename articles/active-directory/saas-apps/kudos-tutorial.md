---
title: チュートリアル:Azure Active Directory と Kudos の統合 | Microsoft Docs
description: Azure Active Directory と Kudos の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b1fa6284979a8c0bd0698f85b119117dd27a05
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172274"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>チュートリアル:Azure Active Directory と Kudos の統合

このチュートリアルでは、Kudos と Azure Active Directory (Azure AD) を統合する方法について説明します。

Kudos と Azure AD の統合には、次の利点があります。

- Kudos にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Kudos にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Kudos と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Kudos でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Kudos の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-kudos-from-the-gallery"></a>ギャラリーからの Kudos の追加
Azure AD への Kudos の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kudos を追加する必要があります。

**ギャラリーから Kudos を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Kudos**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/kudos-tutorial/tutorial_kudos_search.png)

1. 結果ウィンドウで **Kudos** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/kudos-tutorial/tutorial_kudos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Kudos で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Kudos ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Kudos の関連ユーザーの間で、リンク関係が確立されている必要があります。

Kudos で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Kudos で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Kudos テスト ユーザーの作成](#creating-a-kudos-test-user)** - Kudos で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Kudos アプリケーションでシングル サインオンを構成します。

**Kudos で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Kudos** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/kudos-tutorial/tutorial_kudos_samlbase.png)

1. **[Kudos のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/kudos-tutorial/tutorial_kudos_url.png)

    **[サインオン URL]** ボックスに、`https://<company>.kudosnow.com` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Kudos クライアント サポート チーム](http://success.kudosnow.com/home)にお問い合わせください。 
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/kudos-tutorial/tutorial_kudos_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/kudos-tutorial/tutorial_general_400.png)

1. **[Kudos 構成]** セクションで、**[Kudos の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/kudos-tutorial/tutorial_kudos_configure.png) 

1. 別の Web ブラウザーのウィンドウで、Kudos の企業サイトに管理者としてログインします。

1. 上部のメニューで **[Settings]** をクリックします。
   
    ![設定](./media/kudos-tutorial/ic787806.png "Settings")

1. **[Integrations] \> [SSO]** をクリックします。

1. **[SSO]** セクションで、次の手順に従います。
   
    ![SSO](./media/kudos-tutorial/ic787807.png "SSO")
   
    a. **[Sign on URL]\(サインオン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。 

    b. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 証明書]** テキストボックスに貼り付けます。
   
    c. **[Logout To URL]\(ログアウト先 URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。
   
    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Your Kudos URL (Kudos の URL)]** テキストボックスに、企業名を入力します。
   
    e. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/kudos-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/kudos-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/kudos-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/kudos-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-kudos-test-user"></a>Kudos テスト ユーザーの作成

Azure AD ユーザーが Kudos にログインできるようにするには、そのユーザーを Kudos にプロビジョニングする必要があります。 

Kudos の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Kudos** の企業サイトに管理者としてログインします。

1. 上部のメニューで **[Settings]** をクリックします。
   
   ![設定](./media/kudos-tutorial/ic787806.png "Settings")

1. **[ユーザー管理者]** をクリックします。

1. **[Users]\(ユーザー\)** タブをクリックして、**[Add a User]\(ユーザーの追加\)** をクリックします。
   
   ![User Admin](./media/kudos-tutorial/ic787809.png "User Admin")

1. **[ユーザーの追加]** セクションで、次の手順に従います。
   
    ![Add a User](./media/kudos-tutorial/ic787810.png "Add a User")
   
    a. プロビジョニングする有効な Azure Active Directory アカウントの **[名]**、**[姓]**、**[メール]**、その他の詳細を該当するボックスに入力します。
   
    b. **[Create User]** をクリックします。

>[!NOTE]
>Kudos から提供されている他の Canvas ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Kudos へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Kudos に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Kudos]** を選択します。

    ![Configure single sign-on](./media/kudos-tutorial/tutorial_kudos_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Kudos のタイルをクリックすると、自動的に Kudos アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kudos-tutorial/tutorial_general_01.png
[2]: ./media/kudos-tutorial/tutorial_general_02.png
[3]: ./media/kudos-tutorial/tutorial_general_03.png
[4]: ./media/kudos-tutorial/tutorial_general_04.png

[100]: ./media/kudos-tutorial/tutorial_general_100.png

[200]: ./media/kudos-tutorial/tutorial_general_200.png
[201]: ./media/kudos-tutorial/tutorial_general_201.png
[202]: ./media/kudos-tutorial/tutorial_general_202.png
[203]: ./media/kudos-tutorial/tutorial_general_203.png

