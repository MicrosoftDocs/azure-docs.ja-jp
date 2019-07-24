---
title: チュートリアル:Azure Active Directory と Workstars の統合 | Microsoft Docs
description: Azure Active Directory と Workstars の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 51a4a4e4-ff60-4971-b3f8-a0367b70d220
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a9c1d13c1d51eeadae3ef9dad71e64618d1a5fe
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444703"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>チュートリアル:Azure Active Directory と Workstars の統合

このチュートリアルでは、Workstars を Azure Active Directory (Azure AD) と統合する方法について説明します。

Workstars と Azure AD の統合には、次の利点があります。

- Workstars にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Workstars に自動サインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Workstars と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workstars でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Workstars の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-workstars-from-the-gallery"></a>ギャラリーからの Workstars の追加
Azure AD への Workstars の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workstars を追加する必要があります。

**ギャラリーから Workstars を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Workstars**」と入力し、結果ウィンドウで **[Workstars]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Workstars](./media/workstars-tutorial/tutorial_workstars_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Workstars で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Workstars ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workstars の関連ユーザーの間で、リンク関係が確立されている必要があります。

Workstars で、**[Username]\(ユーザー名\)** の値として Azure AD の **[ユーザー名]** の値を割り当ててリンク関係を確立します。

Workstars で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Workstars テスト ユーザーの作成](#create-a-workstars-test-user)** - Workstars で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Workstars アプリケーションでシングル サインオンを構成します。

**Worstars で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Workstars** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/workstars-tutorial/tutorial_workstars_samlbase.png)

1. **[Workstars のドメインと URL]** セクションで、次の手順に従います。

    ![[Workstars のドメインと URL] のシングル サインオン情報](./media/workstars-tutorial/tutorial_workstars_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://workstars.com`

    b. **[応答 URL]** ボックスに、`https://<subdomain>.workstars.com/saml/login_check` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Workstars サポート チーム](https://support.workstars.com)にお問い合わせください。
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/workstars-tutorial/tutorial_workstars_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/workstars-tutorial/tutorial_general_400.png)

1. **[Workstars 構成]** セクションで、**[Workstars の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Workstars 構成](./media/workstars-tutorial/tutorial_workstars_configure.png) 

1. 別の Web ブラウザー ウィンドウで、管理者として Workstars 企業サイトにサインオンします。

1. メイン ツール バーで、**[Settings]\(設定\)** をクリックします。

    ![Workstars の [Settings]\(設定\)](./media/workstars-tutorial/tutorial_workstars_sett.png)

1. **[Sign On]\(サインオン\)** > **[Settings]\(設定\)** の順に移動します。

    ![Workstars の [Sign On]\(サインオン\)](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Workstars の [Settings]\(設定\)](./media/workstars-tutorial/tutorial_workstars_settings.png)

1. **[Single Sign On (SAML) - Settings]\(シングル サインオン (SAML) - 設定\)** ページで、次の手順を実行します。
    
    ![Workstars の SAML](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. **[Identity Provider Name]\(ID プロバイダー名\)** ボックスに「**Office 365**」と入力します。

    b. **[Identity Provider Entity ID]\(ID プロバイダー エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c. ダウンロードした証明書をメモ帳で開き内容をコピーして、**[x509 Certificate]\(x509 証明書\)** ボックスに貼り付けます。 

    d. **[SAML SSO URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
    
    e. **[Remote Logout URL]\(リモート ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。 

    f. **[Name ID]\(名前 ID\)** で **[Email (Default)]\(電子メール (デフォルト)\)** を選択します。

    g. **[Confirm]\(確認\)** をクリックします。
    
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/workstars-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/workstars-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/workstars-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/workstars-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-workstars-test-user"></a>Workstars テスト ユーザーの作成

このセクションでは、Workstars で Britta Simon というユーザーを作成します。 [Workstars サポート チーム](https://support.workstars.com)と連携して、Workstars プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Workstars へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Workstars に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Workstars]** を選択します。

    ![アプリケーションの一覧の Workstars のリンク](./media/workstars-tutorial/tutorial_workstars_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Workstars] タイルをクリックすると、自動的に Workstars アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workstars-tutorial/tutorial_general_01.png
[2]: ./media/workstars-tutorial/tutorial_general_02.png
[3]: ./media/workstars-tutorial/tutorial_general_03.png
[4]: ./media/workstars-tutorial/tutorial_general_04.png

[100]: ./media/workstars-tutorial/tutorial_general_100.png

[200]: ./media/workstars-tutorial/tutorial_general_200.png
[201]: ./media/workstars-tutorial/tutorial_general_201.png
[202]: ./media/workstars-tutorial/tutorial_general_202.png
[203]: ./media/workstars-tutorial/tutorial_general_203.png

