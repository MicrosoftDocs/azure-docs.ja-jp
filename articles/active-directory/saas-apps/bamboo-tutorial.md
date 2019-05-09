---
title: チュートリアル:Azure Active Directory と SAML SSO for Bamboo by resolution GmbH の統合 | Microsoft Docs
description: Azure Active Directory と SAML SSO for Bamboo by resolution GmbH の間のシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5353cc921d4fc07770737bb70d02361fa0e5f438
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198441"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>チュートリアル:Azure Active Directory と SAML SSO for Bamboo by resolution GmbH の統合

このチュートリアルでは、SAML SSO for Bamboo by resolution GmbH と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAML SSO for Bamboo by resolution GmbH と Azure AD の統合には、次の利点があります。

- SAML SSO for Bamboo by resolution GmbH にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで SAML SSO for Bamboo by resolution GmbH に自動的にサインオン (シングル サインオン) するように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAML SSO for Bamboo by resolution GmbH の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAML SSO for Bamboo by resolution GmbH でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SAML SSO for Bamboo by resolution GmbH の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>ギャラリーからの SAML SSO for Bamboo by resolution GmbH の追加
Azure AD への SAML SSO for Bamboo by resolution GmbH の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAML SSO for Bamboo by resolution GmbH を追加する必要があります。

**ギャラリーから SAML SSO for Bamboo by resolution GmbH を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. [検索] ボックスに「**SAML SSO for Bamboo by resolution GmbH**」と入力し、結果パネルで **[SAML SSO for Bamboo by resolution GmbH]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の SAML SSO for Bamboo by resolution GmbH](./media/bamboo-tutorial/tutorial_bamboo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAML SSO for Bamboo by resolution GmbH で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAML SSO for Bamboo by resolution GmbH ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAML SSO for Bamboo by resolution GmbH の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAML SSO for Bamboo by resolution GmbH で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

SAML SSO for Bamboo by resolution GmbH で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[SAML SSO for Bamboo by resolution GmbH テスト ユーザーの作成](#create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** - SAML SSO for Bamboo by resolution GmbH で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、SAML SSO for Bamboo by resolution GmbH アプリケーションでシングル サインオンを構成します。

**SAML SSO for Bamboo by resolution GmbH で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAML SSO for Bamboo by resolution GmbH** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/bamboo-tutorial/tutorial_bamboo_samlbase.png)

1. **[SAML SSO for Bamboo by resolution GmbH のドメインと URL]** セクションで、IDP 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[SAML SSO for Bamboo by resolution GmbH のドメインと URL] のシングル サインオン情報](./media/bamboo-tutorial/tutorial_bamboo_url.png)

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[SAML SSO for Bamboo by resolution GmbH のドメインと URL] のシングル サインオン情報](./media/bamboo-tutorial/tutorial_bamboo_url1.png)

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SAML SSO for Bamboo by resolution GmbH クライアント サポート チーム](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support)に問い合わせます。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/bamboo-tutorial/tutorial_bamboo_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/bamboo-tutorial/tutorial_general_400.png)

1. SAML SSO for Bamboo by resolution GmbH 企業サイトに管理者としてサインオンします。

1. メイン ツールバーの右側で、**[Settings]\(設定\)** > **[Add-ons]\(アドオン\)** の順にクリックします。

    ![設定](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. [SECURITY]\(セキュリティ\) セクションに移動し、メニュー バーの **[SAML SingleSignOn]** をクリックします。

    ![SAML Single](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. **[SAML SingleSignOn Plugin Configuration]\(SAML SingleSignOn プラグインの構成\)** ページで、**[Add idp]\(IDP の追加\)** をクリックします。 

    ![IDP の追加](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. **[Choose your SAML Identity Provider]\(SAML ID プロバイダーの選択\)** ページで、次の手順を実行します。

    ![ID プロバイダー](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. **[Idp Type]\(IDP の種類\)** として **[AZURE AD]** を選択します。

    b. **[Name]\(名前\)** ボックスに、名前を入力します。

    c. **[Description]\(説明\)** ボックスに、説明を入力します。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[次へ]** をクリックします。

1. **[Identity provider configuration]\(ID プロバイダーの構成\)** ページで、**[Next]\(次へ\)** をクリックします。

    ![ID の構成](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1.  **[Import SAML IdP Metadata]\(SAML IDP メタデータのインポート\)** ページで、**[Load File]\(ファイルの読み込み\)** をクリックして、Azure Portal からダウンロードした**メタデータ XML** ファイルをアップロードします。

    ![IDP メタデータ](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. **[次へ]** をクリックします。

1. **[Save settings]\(設定の保存\)** をクリックします。

    ![保存](./media/bamboo-tutorial/tutorial_bamboo_save.png)
    
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/bamboo-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/bamboo-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/bamboo-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/bamboo-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>SAML SSO for Bamboo by resolution GmbH のテスト ユーザーの作成

このセクションの目的は、SAML SSO for Bamboo by resolution GmbH で Britta Simon というユーザーを作成することです。 SAML SSO for Bamboo by resolution GmbH では、Just-In-Time プロビジョニングがサポートされていますが、ユーザーを手動で作成することもできます。要件に応じて [SAML SSO for Bamboo by resolution GmbH クライアント サポート チーム](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAML SSO for Bamboo by resolution GmbH へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を SAML SSO for Bamboo by resolution GmbH に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SAML SSO for Bamboo by resolution GmbH]** を選択します。

    ![アプリケーションの一覧の SAML SSO for Bamboo by resolution GmbH のリンク](./media/bamboo-tutorial/tutorial_bamboo_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAML SSO for Bamboo by resolution GmbH] タイルをクリックすると、SAML SSO for Bamboo by resolution GmbH アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-tutorial/tutorial_general_203.png

