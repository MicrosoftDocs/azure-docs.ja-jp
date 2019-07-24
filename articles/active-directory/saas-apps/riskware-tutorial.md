---
title: チュートリアル:Azure Active Directory と Riskware の統合 | Microsoft Docs
description: Azure Active Directory と Riskware の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3de8422efdbb7802e0532503e2733518e8050f6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842534"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>チュートリアル:Azure Active Directory と Riskware の統合

このチュートリアルでは、Riskware と Azure Active Directory (Azure AD) を統合する方法について説明します。

Riskware と Azure AD の統合には、次の利点があります。

- Riskware にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Riskware にサインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Riskware と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Riskware でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Riskware の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-riskware-from-the-gallery"></a>ギャラリーからの Riskware の追加
Azure AD への Riskware の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Riskware を追加する必要があります。

**ギャラリーから Riskware を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Riskware**」と入力し、結果パネルで **[Riskware]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Riskware](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Riskware で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Riskware ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Riskware の関連ユーザーの間で、リンク関係が確立されている必要があります。

Riskware で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Riskware テスト ユーザーの作成](#create-a-riskware-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Riskware で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Riskware アプリケーションでシングル サインオンを構成します。

**Riskware で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Riskware** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

1. **[Riskware ドメインと URL]** セクションで、次の手順を実行します。

    ![[Riskware ドメインと URL] のシングル サインオン情報](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    | 環境| URL パターン|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. **[識別子 (エンティティ ID)]** ボックスに、URL を入力します。
    
    | 環境| URL パターン|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Riskware クライアント サポート チーム](mailto:support@pansoftware.com.au)にお問い合わせください。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/riskware-tutorial/tutorial_general_400.png)

1. **[Riskware 構成]** セクションで、**[Riskware の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Riskware 構成](./media/riskware-tutorial/tutorial_riskware_configure.png)

1. 別の Web ブラウザー ウィンドウで、Riskware 企業サイトに管理者としてサインインします。

1. 右上の **[Maintenance]\(メンテナンス\)** をクリックしてメンテナンス ページを開きます。

    ![Riskware 構成、メンテナンス](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. メンテナンス ページで、**[Authentication]\(認証\)** をクリックします。

    ![Riskware 構成、認証](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. **[Authentication Configuration]\(認証の構成\)** ページで、次の手順に従います。

    ![Riskware 構成、authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. 認証の **[Type]\(種類\)** として **[SAML]** を選択します。

    b. **[Code]\(コード\)** ボックスにコードを入力します (例: AZURE_UAT)。

    c. **[Description]\(説明\)** ボックスに説明を入力します (例:SSO 用 AZURE 構成)。

    d. **[Single Sign On Page]\(シングル サインオン ページ\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. **[Sign out Page]\(サインアウト ページ\)** ボックスに、Azure portal からコピーした**サインアウト URL** の値を貼り付けます。

    f. **[Post Form Field]\(Post フォーム フィールド\)** ボックスに、SAML を含む Post 応答にあるフィールド名を入力します (例: SAMLResponse)

    g. **[XML Identity Tag Name]\(XML ID タグ名\)** ボックスに、SAML 応答内の一意の識別子を含む属性を入力します (例: NameID)。

    h. Azure portal からダウンロードした **メタデータ Xml**  をメモ帳で開き、メタデータ ファイルから証明書をコピーして **[Certificate]\(証明書\)** ボックスに貼り付けます

    i. **[Consumer URL]\(コンシューマー URL\)** ボックスに、サポート チームから入手した**応答 URL** の値を貼り付けます。

    j. **[Issuer]\(発行者\)** ボックスに、サポート チームから入手した**識別子**の値を貼り付けます。

    > [!Note]
    > これらの値を取得するには、[Riskware クライアント サポート チーム](mailto:support@pansoftware.com.au)にお問い合わせください

    k. **[Use POST]\(POST を使用する\)** チェックボックスをオンにします。

    l. **[Use SAML Request]\(SAML 要求を使用する\)** チェックボックスをオンにします。

    m. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/riskware-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/riskware-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/riskware-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/riskware-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-riskware-test-user"></a>Riskware のテスト ユーザーの作成

Azure AD ユーザーが Riskware にサインインできるようにするには、ユーザーを Riskware にプロビジョニングする必要があります。 Riskware では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Riskware にサインインします。

1. 右上の **[Maintenance]\(メンテナンス\)** をクリックしてメンテナンス ページを開きます。 

    ![Riskware 構成、メンテナンス](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. メンテナンス ページで、**[People]\(ユーザー\)** をクリックします。

    ![Riskware 構成、ユーザー](./media/riskware-tutorial/tutorial_riskware_people.png)

1. **[Details]\(詳細\)** タブを選択し、次の手順を実行します。

    ![Riskware 構成、詳細](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. **[Person Type]\(ユーザーの種類\)** で、ユーザーの種類を選択します (例: [Employee]\(従業員\))。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名前を入力します (例: **Britta**)。

    c. **[Surname]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

1. **[セキュリティ]** タブで、次の手順に従います。

    ![Riskware 構成、セキュリティ](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. **[Authentication]\(認証\)** セクションで、既に設定済みの**認証**モードを選択します (例:SSO 用 AZURE 構成)。

    b. **[Logon Details]\(ログオンの詳細\)** セクションの **[User ID]\(ユーザー ID\)** ボックスに、ユーザーのメール アドレス (例: **brittasimon\@contoso.com**) を入力します。

    c. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

1. **[Organization]\(組織\)** タブで、次の手順に従います。

    ![Riskware 構成、組織](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. **Level1** 組織としてオプションを選択します。

    b. **[Person's Primary Workplace]\(ユーザーのプライマリ ワークプレース\)** セクションで、**[Location]\(場所\)** ボックスに場所を入力します。

    c. **[Employee]\(従業員\)** セクションで、**[Employee Status]\(従業員のステータス\)** を選択します (例: [Casual]\(カジュアル\))。

1. **[Save]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Riskware へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Riskware に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Riskware]** を選択します。

    ![アプリケーションの一覧の Riskware のリンク](./media/riskware-tutorial/tutorial_riskware_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Riskware のタイルをクリックすると、自動的に Riskware アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

