---
title: チュートリアル:Azure Active Directory と Rollbar の統合 | Microsoft Docs
description: Azure Active Directory と Rollbar の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ebedddf5e62e41bd0ef2d4c426df448a81a1e2d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837220"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>チュートリアル:Azure Active Directory と Rollbar の統合

このチュートリアルでは、Rollbar と Azure Active Directory (Azure AD) を統合する方法について説明します。

Rollbar と Azure AD の統合には、次の利点があります。

- Rollbar にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Rollbar にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Rollbar と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Rollbar でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Rollbar の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-rollbar-from-the-gallery"></a>ギャラリーからの Rollbar の追加
Azure AD への Rollbar の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Rollbar を追加する必要があります。

**ギャラリーから Rollbar を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Rollbar**」と入力し、結果ウィンドウで **[Rollbar]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Rollbar](./media/rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Rollbar で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Rollbar ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Rollbar の関連ユーザーの間で、リンク関係が確立されている必要があります。

Rollbar で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Rollbar で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Rollbar のテスト ユーザーの作成](#create-a-rollbar-test-user)** - Rollbar で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Rollbar アプリケーションでシングル サインオンを構成します。

**Rollbar で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Rollbar** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/rollbar-tutorial/tutorial_rollbar_samlbase.png)

1. **[Rollbar のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順に従います。

    ![[Rollbar のドメインと URL] のシングル サインオン情報](./media/rollbar-tutorial/tutorial_rollbar_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://saml.rollbar.com`

    b. **[応答 URL]** ボックスに、`https://rollbar.com/<accountname>/saml/sso/azure/` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Rollbar のドメインと URL] のシングル サインオン情報](./media/rollbar-tutorial/tutorial_rollbar_url1.png)

    **[サインオン URL]** ボックスに、`https://rollbar.com/<accountname>/saml/login/azure/` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Rollbar クライアント サポート チーム](mailto:support@rollbar.com)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/rollbar-tutorial/tutorial_rollbar_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/rollbar-tutorial/tutorial_general_400.png)
    
1. 別の Web ブラウザー ウィンドウで、Rollbar 企業サイトに管理者としてログインします。

1. 右上の **[Profile Settings]\(プロファイル設定\)** をクリックし、**[Account Name settings]\(アカウント名の設定\)** をクリックします。
    
    ![構成](./media/rollbar-tutorial/general.png)

1. [SECURITY]\(セキュリティ\) の **[Identity Provider]\(ID プロバイダー\)** をクリックします。

    ![構成](./media/rollbar-tutorial/configure1.png)

1. **[SAML Identity Provider]\(SAML ID プロバイダー\)** セクションで、次の手順に従います。
    
    ![構成](./media/rollbar-tutorial/configure2.png)

    a. **[SAML Identity Provider]\(SAML ID プロバイダー\)** ドロップダウンから **[AZURE]** を選択します。

    b. メタデータ ファイルをメモ帳で開き、その内容をクリップボードにコピーし、**[SAML Metadata]\(SAML メタデータ\)** テキストボックスに貼りつけます。

    c. **[Save]** をクリックします。

1. 保存ボタンをクリックすると、次のような画面が表示されます。
    
    ![構成](./media/rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > 次の手順を行うには、最初に、Azure で自分自身を Rollbar アプリにユーザーとして追加する必要があります。
    a. Azure による認証をすべてのユーザーに要求する場合は、**[log in via your identity provider]\(ID プロバイダーを使用してログインする\)** をクリックして Azure で再認証します。  

    b.  画面に戻った後、**[Require login via SAML Identity Provider]\(SAML ID プロバイダーによるログインを要求する\)** チェック ボックスをオンにします。

    b. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/rollbar-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/rollbar-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/rollbar-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/rollbar-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-rollbar-test-user"></a>Rollbar テスト ユーザーの作成

Azure AD ユーザーが Rollbar にログインできるようにするには、ユーザーを Rollbar にプロビジョニングする必要があります。 Rollbar の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Rollbar 企業サイトに管理者としてログインします。

1. 右上の **[Profile Settings]\(プロファイル設定\)** をクリックし、**[Account Name settings]\(アカウント名の設定\)** をクリックします。

    ![User](./media/rollbar-tutorial/general.png)

1. **[ユーザー]** をクリックします。
    
    ![従業員の追加](./media/rollbar-tutorial/user1.png)

1. **[Invite Team Members]\(チーム メンバーの招待\)** をクリックします。

    ![[ユーザーの招待]](./media/rollbar-tutorial/user2.png)

1. ボックスにユーザー名 (例: **brittasimon\@contoso.com**) を入力し、**[Add/Invite]\(追加/招待\)** をクリックします。

    ![[ユーザーの招待]](./media/rollbar-tutorial/user3.png)

1. ユーザーが招待状を受け取り、承認すると、システムにそのユーザーが作成されます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Rollbar へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Rollbar に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Rollbar]** を選択します。

    ![アプリケーションの一覧の [Rollbar] リンク](./media/rollbar-tutorial/tutorial_rollbar_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Rollbar のタイルをクリックすると、Rollbar アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rollbar-tutorial/tutorial_general_01.png
[2]: ./media/rollbar-tutorial/tutorial_general_02.png
[3]: ./media/rollbar-tutorial/tutorial_general_03.png
[4]: ./media/rollbar-tutorial/tutorial_general_04.png

[100]: ./media/rollbar-tutorial/tutorial_general_100.png

[200]: ./media/rollbar-tutorial/tutorial_general_200.png
[201]: ./media/rollbar-tutorial/tutorial_general_201.png
[202]: ./media/rollbar-tutorial/tutorial_general_202.png
[203]: ./media/rollbar-tutorial/tutorial_general_203.png

