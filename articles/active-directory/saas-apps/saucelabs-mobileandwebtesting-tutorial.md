---
title: チュートリアル:Azure Active Directory と Sauce Labs - Mobile and Web Testing の統合 | Microsoft Docs
description: Azure Active Directory と Sauce Labs - Mobile and Web Testing の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99f794c4eb9db78f50f63f14ccfad08c65ddb07
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442058"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>チュートリアル:Azure Active Directory と Sauce Labs - Mobile and Web Testing の統合

このチュートリアルでは、Sauce Labs - Mobile and Web Testing と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sauce Labs - Mobile and Web Testing と Azure AD の統合には、次の利点があります。

- Sauce Labs - Mobile and Web Testing にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが各自の Azure AD アカウントで Sauce Labs - Mobile and Web Testing に自動的にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Sauce Labs - Mobile and Web Testing の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Sauce Labs - Mobile and Web Testing でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Sauce Labs - Mobile and Web Testing を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>ギャラリーから Sauce Labs - Mobile and Web Testing を追加する
Azure AD への Sauce Labs - Mobile and Web Testing の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Sauce Labs - Mobile and Web Testing を追加する必要があります。

**ギャラリーから Sauce Labs - Mobile and Web Testing を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Sauce Labs - Mobile and Web Testing**」と入力し、結果パネルで **[Sauce Labs - Mobile and Web Testing]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Sauce Labs - Mobile and Web Testing](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Sauce Labs - Mobile and Web Testing で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Sauce Labs - Mobile and Web Testing ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Sauce Labs - Mobile and Web Testing の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sauce Labs - Mobile and Web Testing で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Sauce Labs - Mobile and Web Testing テスト ユーザーの作成](#create-a-sauce-labs---mobile-and-web-testing-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Sauce Labs - Mobile and Web Testing で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にし、Sauce Labs - Mobile and Web Testing アプリケーションでシングル サインオンを構成します。

**Sauce Labs - Mobile and Web Testing で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure portal の **Sauce Labs - Mobile and Web Testing** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. アプリは Azure と事前に統合済みであるため、**[Sauce Labs - Mobile and Web Testing のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![[Sauce Labs - Mobile and Web Testing のドメインと URL] のシングル サインオン情報](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. Web ブラウザーの別のウィンドウで、Sauce Labs - Mobile and Web Testing 企業サイトに管理者としてサインインします。

7. **[User]\(ユーザー\) アイコン**をクリックし、**[Team Management]\(チーム管理\)** タブを選択します。

    ![Configure single sign-on](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. テキストボックスに**ドメイン名**を入力します。

    ![Configure single sign-on](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. **[Configure]\(構成\)** タブをクリックします。

    ![Configure single sign-on](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. **[Configure Single Sign On]\(シングル サインオンの構成\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. **[Browse]\(参照\)** をクリックし、Azure AD からダウンロードしたメタデータ ファイルをアップロードします。

    b. **[ALLOW JUST-IN-TIME PROVISIONING]\(Just-In-Time プロビジョニングを許可する\)** チェックボックスをオンにします。

    c. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Sauce Labs - Mobile and Web Testing テスト ユーザーを作成する

このセクションの目的は、Sauce Labs - Mobile and Web Testing で Britta Simon というユーザーを作成することです。 Sauce Labs - Mobile and Web Testing では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Sauce Labs - Mobile and Web Testing ユーザーにアクセスしようとすると、新しいユーザーが作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、 [Sauce Labs - Mobile and Web Testing サポート チーム](mailto:support@saucelabs.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sauce Labs - Mobile and Web Testing へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Britta Simon を Sauce Labs - Mobile and Web Testing に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Sauce Labs - Mobile and Web Testing]** を選択します。

    ![アプリケーションの一覧の [Sauce Labs - Mobile and Web Testing] リンク](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Sauce Labs - Mobile and Web Testing のタイルをクリックすると、自動的に Sauce Labs - Mobile and Web Testing アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png
