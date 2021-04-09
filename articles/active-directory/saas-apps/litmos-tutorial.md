---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Litmos の統合 | Microsoft Docs'
description: Azure Active Directory と Litmos の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: cfdcef2b9adf4e7ce500a9a89a45678a60afffc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458453"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Litmos の統合

このチュートリアルでは、Litmos と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Litmos を統合すると、次のことができます。

* Litmos にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Litmos に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Litmos でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Litmos では、**IDP** によって開始される SSO がサポートされます
* Litmos では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-litmos-from-the-gallery"></a>ギャラリーからの Litmos の追加

Azure AD への Litmos の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Litmos を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Litmos**」と入力します。
1. 結果のパネルから **[Litmos]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Litmos の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Litmos に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Litmos の関連ユーザーとの間にリンク関係を確立する必要があります。

Litmos で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Litmos の SSO の構成](#configure-litmos-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Litmos のテスト ユーザーの作成](#create-litmos-test-user)** - Litmos で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Litmos** アプリケーション統合ページで、**[管理]** セクションを見つけて、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<companyname>.litmos.com/account/Login` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<companyname>.litmos.com/integration/samllogin` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は実際の識別子と応答 URL で更新してください。これらの値の取得方法については、チュートリアルの後半の説明を参照するか、[Litmos クライアント サポート チーム](https://www.litmos.com/contact-us)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Litmos のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Litmos へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Litmos]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-litmos-sso"></a>Litmos の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として Litmos 企業サイトにサインオンします。

2. 左側にあるナビゲーション バーで、 **[Accounts]** をクリックします。

    ![アプリ側の [Accounts]\(アカウント\) セクション][22]

3. **[Integrations]** タブをクリックします。

    ![[Integration]\(統合\) タブ][23]

4. **[Integrations]** タブで、下へスクロールして **[3rd Party Integrations]** を表示し、**[SAML 2.0]** タブをクリックします。

    ![[SAML 2.0] セクション][24]

5. **[The SAML endpoint for litmos is:]\(Litmos の SAML エンドポイント:\)** の値をコピーし、Azure Portal の **[Litmos のドメインと URL]** セクションの **[応答 URL]** テキストボックスに貼り付けます。

    ![SAML エンドポイント][26]

6. **Litmos** アプリケーションで、次の手順を実行します。

    ![Litmos アプリケーション][25]

    a. **[Enable SAML]** をクリックします。

    b. Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[SAML X.509 Certificate]** ボックスに貼り付けます。

    c. **[変更を保存]** をクリックします。

### <a name="create-litmos-test-user"></a>Litmos のテスト ユーザーの作成

このセクションの目的は、Litmos で Britta Simon というユーザーを作成することです。 Litmos アプリケーションでは、ジャストインタイム プロビジョニングがサポートされています。 そのため、アクセス パネルを使用してアプリケーションにアクセスを試みると、必要に応じてユーザー アカウントが自動的に作成されます。

**Litmos で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 別の Web ブラウザーのウィンドウで、管理者として Litmos 企業サイトにサインオンします。

2. 左側にあるナビゲーション バーで、 **[Accounts]** をクリックします。

    ![アプリ側の [Accounts]\(アカウント\) セクション][22]

3. **[Integrations]** タブをクリックします。

    ![[Integrations]\(統合\) タブ][23]

4. **[Integrations]** タブで、下へスクロールして **[3rd Party Integrations]** を表示し、**[SAML 2.0]** タブをクリックします。

    ![SAML 2.0][24]

5. **[Autogenerate Users]\(ユーザーを自動生成する\)** をオンにします
  
    ![[Autogenerate Users]\(ユーザーを自動生成する\)][27]

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Litmos] タイルをクリックすると、SSO を設定した Litmos に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Litmos を試す](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png