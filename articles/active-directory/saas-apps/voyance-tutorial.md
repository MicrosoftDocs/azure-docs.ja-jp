---
title: チュートリアル:Azure Active Directory と Voyance の統合 | Microsoft Docs
description: Azure Active Directory と Voyance の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1771fe1d94eb64cf1e5227953bdc5defa488e85f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208454"
---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>チュートリアル:Azure Active Directory と Voyance の統合

このチュートリアルでは、Voyance と Azure Active Directory (Azure AD) を統合する方法について説明します。

Voyance と Azure AD の統合には、次の利点があります。

- Voyance にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Voyance に自動サインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Voyance の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Voyance でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Voyance の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-voyance-from-the-gallery"></a>ギャラリーからの Voyance の追加
Azure AD への Voyance の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Voyance を追加する必要があります。

**ギャラリーから Voyance を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Voyance**」と入力し、結果ウィンドウで **[Voyance]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Voyance](./media/voyance-tutorial/tutorial_voyance_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Voyance で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Voyance ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Voyance の関連ユーザーの間で、リンク関係が確立されている必要があります。

Voyance で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Voyance で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Voyance のテスト ユーザーの作成](#create-a-voyance-test-user)** - Voyance で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Voyance アプリケーションでシングル サインオンを構成します。

**Voyance で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Voyance** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/voyance-tutorial/tutorial_voyance_samlbase.png)

1. アプリケーションを **IDP** 開始モードで構成する場合は、**[Voyance のドメインと URL]** セクションで次の手順を実行します。

    ![IDP の場合の [Voyance のドメインと URL] のシングル サインオン情報](./media/voyance-tutorial/tutorial_voyance_url1.png)

    a. **[識別子]** ボックスに、`https://<companyname>.nyansa.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<companyname>.nyansa.com/saml/create/` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェック ボックスをオンにして次の手順を実行します。

    ![SP の場合の [Voyance のドメインと URL] のシングル サインオン情報](./media/voyance-tutorial/tutorial_voyance_url2.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.nyansa.com/` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Voyance クライアント サポート チーム](mailto:support@nyansa.com)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/voyance-tutorial/tutorial_voyance_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/voyance-tutorial/tutorial_general_400.png)
    
1. **[Voyance 構成]** セクションで、**[Voyance の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Voyance 構成](./media/voyance-tutorial/tutorial_voyance_configure.png) 

1. 別の Web ブラウザーのウィンドウで、管理者として Voyance テナントにサインオンします。

1. ナビゲーション バーの右上隅に移動し、ドロップダウンの **[Acme University]** をクリックします。
    
    ![アプリ側でのシングル サインオンの構成: Acme University](./media/voyance-tutorial/tutorial_voyance_001.png) 

1. **[管理者設定]** をクリックします。

    ![アプリ側でのシングル サインオンの構成: 管理者設定](./media/voyance-tutorial/tutorial_voyance_002.png)

1. **[ユーザー アクセス]** タブをクリックします。

    ![アプリ側でのシングル サインオンの構成: ユーザー アクセス](./media/voyance-tutorial/tutorial_voyance_003.png)

1. **[SSO is disabled]** (SSO は無効) ボタンをクリックして、SAML 2.0 を使用する IdP として Azure AD を構成します。

    ![アプリ側でのシングル サインオンの構成: SSO は無効](./media/voyance-tutorial/tutorial_voyance_004.png)

1. **SAML V2** セクションに移動して、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成: SAML V2](./media/voyance-tutorial/tutorial_voyance_005.png)
    
    a. **[Enabled]** を選択します。
    
    b. Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[IdP Login URL]\(IdP ログイン URL\)** ボックスに貼り付けます。

    c. ダウンロード済みの Base64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[IdP 証明書]** ボックスに貼り付けます。
    
    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/voyance-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/voyance-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![[追加] ボタン](./media/voyance-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/voyance-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-voyance-test-user"></a>Voyance のテスト ユーザーの作成

このセクションの目的は、Voyance で Britta Simon というユーザーを作成することです。 Voyance では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合、Voyance へのアクセス時に新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[Voyance サポート チーム](maiLto:support@nyansa.com)に問い合わせてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Voyance へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Britta Simon を Voyance に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Voyance]** を選択します。

    ![アプリケーションの一覧の Voyance リンク](./media/voyance-tutorial/tutorial_voyance_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Voyance] タイルをクリックすると、自動的に Voyance アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/voyance-tutorial/tutorial_general_01.png
[2]: ./media/voyance-tutorial/tutorial_general_02.png
[3]: ./media/voyance-tutorial/tutorial_general_03.png
[4]: ./media/voyance-tutorial/tutorial_general_04.png

[100]: ./media/voyance-tutorial/tutorial_general_100.png

[200]: ./media/voyance-tutorial/tutorial_general_200.png
[201]: ./media/voyance-tutorial/tutorial_general_201.png
[202]: ./media/voyance-tutorial/tutorial_general_202.png
[203]: ./media/voyance-tutorial/tutorial_general_203.png

