---
title: チュートリアル:Azure Active Directory と Replicon の統合 | Microsoft Docs
description: Azure Active Directory と Replicon の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ade40287bd38580a1e3f6377e54017bfe92bf452
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863408"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>チュートリアル:Azure Active Directory と Replicon の統合

このチュートリアルでは、Replicon と Azure Active Directory (Azure AD) を統合する方法について説明します。

Replicon と Azure AD の統合には、次の利点があります。

- Replicon にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Replicon にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Replicon と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Replicon でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Replicon の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-replicon-from-the-gallery"></a>ギャラリーからの Replicon の追加
Azure AD への Replicon の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Replicon を追加する必要があります。

**ギャラリーから Replicon を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Replicon**」と入力し、結果パネルで **[Replicon]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Replicon](./media/replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Replicon で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Replicon ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Replicon の関連ユーザーの間で、リンク関係が確立されている必要があります。

Replicon で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

Replicon で Azure AD のシングル サインオンを構成してテストするには、次のビルド ブロックを完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Replicon のテスト ユーザーの作成](#create-a-replicon-test-user)** - Replicon で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Replicon アプリケーションでシングル サインオンを構成します。

**Replicon で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Replicon** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/replicon-tutorial/tutorial_replicon_samlbase.png)

3. **[Replicon のドメインと URL]** セクションで、次の手順を実行します。

    ![[Replicon のドメインと URL] のシングル サインオン情報](./media/replicon-tutorial/tutorial_replicon_url.png)

    a. **[サインオン URL]** ボックスに、`https://na2.replicon.com/<companyname>/saml2/sp-sso/post` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://global.replicon.com/<companyname>` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://global.replicon.com/!/saml2/<companyname>/sso/post` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Replicon クライアント サポート チーム](https://www.replicon.com/customerzone/contact-support)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/replicon-tutorial/tutorial_replicon_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/replicon-tutorial/tutorial_general_400.png)

6. 別の Web ブラウザー ウィンドウで、 Replicon 企業サイトに管理者としてログインします。

7. SAML 2.0 を構成するには、次の手順に従います。

    ![Enable SAML authentication](./media/replicon-tutorial/ic777805.png "Enable SAML authentication")

    a. **[EnableSAML Authentication2]** ダイアログを表示するには、URL の企業キーの後に次のパスを追加します。`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * 完全な URL のスキーマを次に示します。`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. **+** をクリックして、**[v20Configuration]** セクションを展開します。

   c. **+** をクリックして、**[metaDataConfiguration]** セクションを展開します。

   d. **[Choose File]** をクリックして、ID プロバイダー メタデータ XML ファイルを選択し、**[送信]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/replicon-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/replicon-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/replicon-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/replicon-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-replicon-test-user"></a>Replicon テスト ユーザーの作成

このセクションの目的は、Replicon で Britta Simon というユーザーを作成することです。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. Web ブラウザー ウィンドウで、 Replicon 企業サイトに管理者としてログインします。

2. **[Administration] \> [Users]** に移動します。

    ![ユーザー](./media/replicon-tutorial/ic777806.png "Users")

3. **[+Add User]** をクリックします。

    ![ユーザーの追加](./media/replicon-tutorial/ic777807.png "Add User")

4. **[User Profile]** セクションで、次の手順に従います。

    ![User profile](./media/replicon-tutorial/ic777808.png "User profile")

    a. **[ログイン名]** ボックスに、プロビジョニングする Azure AD ユーザーの Azure AD メール アドレス (例: **BrittaSimon\@contoso.com**) を入力します。

    b. **[Authentication Type]** として **[SSO]** を選択します。

    c. **[Department]** テキストボックスに、ユーザーの所属を入力します。

    d. **[Employee Type]** として **[Administrator]** を選択します。

    e. **[Save User Profile]** をクリックします。

>[!NOTE]
>Replicon から提供されている他の Replicon ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Replicon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Britta Simon を Replicon に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Replicon]** を選択します。

    ![アプリケーションの一覧の Replicon のリンク](./media/replicon-tutorial/tutorial_replicon_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Replicon のタイルをクリックすると、自動的に Replicon アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/replicon-tutorial/tutorial_general_01.png
[2]: ./media/replicon-tutorial/tutorial_general_02.png
[3]: ./media/replicon-tutorial/tutorial_general_03.png
[4]: ./media/replicon-tutorial/tutorial_general_04.png

[100]: ./media/replicon-tutorial/tutorial_general_100.png

[200]: ./media/replicon-tutorial/tutorial_general_200.png
[201]: ./media/replicon-tutorial/tutorial_general_201.png
[202]: ./media/replicon-tutorial/tutorial_general_202.png
[203]: ./media/replicon-tutorial/tutorial_general_203.png
