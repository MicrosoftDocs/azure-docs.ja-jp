---
title: チュートリアル:Azure Active Directory と Secret Server (On-Premises) の統合 | Microsoft Docs
description: Azure Active Directory と Secret Server (On-Premises) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9167a5ed72e6fec2ca03cc97d1d41dd6cd4aaba6
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885844"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>チュートリアル:Azure Active Directory と Secret Server (On-Premises) の統合

このチュートリアルでは、Secret Server (On-Premises) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Secret Server (On-Premises) と Azure AD の統合には、次の利点があります。

- Secret Server (On-Premises) にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが各自の Azure AD アカウントで Secret Server (On-Premises) に自動的にサインオン (シングル サインオン) するように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Secret Server (On-Premises) の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Secret Server (On-Premises) のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Secret Server (On-Premises) を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>ギャラリーから Secret Server (On-Premises) を追加する
Azure AD への Secret Server (On-Premises) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Secret Server (On-Premises) を追加する必要があります。

**ギャラリーから Secret Server (On-Premises) を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Secret Server (On-Premises)**」と入力し、結果ウィンドウで **Secret Server (On-Premises)** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Secret Server (On-Premises) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Secret Server (On-Premises) ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Secret Server (On-Premises) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Secret Server (On-Premises) で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Secret Server (On-Premises) テスト ユーザーの作成](#create-a-secret-server-on-premises-test-user)** - Secret Server (On-Premises) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Secret Server (On-Premises) アプリケーションでシングル サインオンを構成します。

**Secret Server (On-Premises) で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Secret Server (On-Premises)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

1. **[Secret Server (On-Premises) のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Secret Server (On-Premises) のドメインと URL] のシングル サインオン情報](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. **[識別子]** ボックスに、次の例のようにユーザーが選択した値を入力します。 `https://secretserveronpremises.azure`

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。 `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > 上記のエンティティ ID は単なる一例です。Azure AD で Secret Server インスタンスを識別する一意の値を自由に選択することができます。 このエンティティ ID を [Secret Server (On-Premises) クライアント サポート チーム](https://thycotic.force.com/support/s/)に送り、サポート チーム側で構成してもらう必要があります。 詳細については、[こちらの記事](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server)を参照してください。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Secret Server (On-Premises) のドメインと URL] のシングル サインオン情報](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    **[サインオン URL]** ボックスに、次の形式で URL を入力します。 `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Secret Server (On-Premises) クライアント サポート チーム](https://thycotic.force.com/support/s/)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

1. **[証明書署名の設定詳細を表示する]** をオンにし、**[署名オプション]** で **[SAML 応答とアサーションへの署名]** を選択します。

    ![署名オプション](./media/secretserver-on-premises-tutorial/signing.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
1. **[Secret Server (On-Premises) の構成]** セクションで **[Secret Server (On-Premises) の構成]** をクリックし、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Secret Server (On-Premises) の構成](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

1. **Secret Server (On-Premises)** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)、サインアウト URL、SAML シングル サインオン サービス URL**、および **SAML エンティティ ID**を [Secret Server (On-Premises) サポート チーム](https://thycotic.force.com/support/s/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Secret Server (On-Premises) テスト ユーザーを作成する

このセクションでは、Secret Server (On-Premises) で Britta Simon というユーザーを作成します。  [Secret Server (On-Premises) サポート チーム](https://thycotic.force.com/support/s/) と連携しながら、Secret Server (On-Premises) プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Secret Server (On-Premises) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Secret Server (On-Premises) に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[Secret Server (On-Premises)]** を選択します。

    ![アプリケーションの一覧の [Secret Server (On-Premises)] リンク](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Secret Server (On-Premises)] タイルをクリックすると、Secret Server (On-Premises) アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

