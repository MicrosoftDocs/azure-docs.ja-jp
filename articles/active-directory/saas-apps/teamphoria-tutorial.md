---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Teamphoria の統合 | Microsoft Docs
description: Azure Active Directory と Teamphoria の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: a8cf58f49cba1bcc77c96a0da2061a037bb62bf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92504747"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Teamphoria の統合

このチュートリアルでは、Teamphoria と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Teamphoria を統合すると、次のことができます。

* Teamphoria にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Teamphoria に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Teamphoria でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Teamphoria では、**SP** によって開始される SSO がサポートされます

## <a name="adding-teamphoria-from-the-gallery"></a>ギャラリーからの Teamphoria の追加

Azure AD への Teamphoria の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Teamphoria を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Teamphoria**」と入力します。
1. 結果のパネルから **[Teamphoria]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Teamphoria の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Teamphoria に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Teamphoria の関連ユーザーとの間にリンク関係を確立する必要があります。

Teamphoria に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Teamphoria の SSO の構成](#configure-teamphoria-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Teamphoria のテスト ユーザーの作成](#create-teamphoria-test-user)** - Teamphoria で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Teamphoria** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、`https://<sub-domain>.teamphoria.com/login` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Teamphoria クライアント サポート チーム](https://www.teamphoria.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Teamphoria のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Teamphoria へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Teamphoria]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-teamphoria-sso"></a>Teamphoria の SSO の構成

1. Teamphoria 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Teamphoria のセットアップ]** をクリックすると、Teamphoria アプリケーションに移動します。 そこから、管理者の資格情報を入力して Teamphoria にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Teamphoria を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Teamphoria 企業サイトにサインインして、次の手順を実行します。

4. 左のツールバーの [Configure] タブの **[ADMIN SETTINGS]** オプションに移動し、 **[SINGLE SIGN-ON]** をクリックして SSO 構成ウィンドウを開きます。

    ![[ADMIN SETTINGS]\(管理者設定\) を示すスクリーンショット。ここで、[SINGLE SIGN-ON]\(シングル サインオン\) を選択できます。](./media/teamphoria-tutorial/admin_sso_configure.png)

5. 右上隅の **[ADD NEW IDENTITY PROVIDER (新しい ID プロバイダーの追加)]** をクリックして、SSO の設定を追加するためのフォームを開きます。

    ![[ADD NEW IDENTITY PROVIDER]\(新しい ID プロバイダーの追加\) を選択できる場所を示すスクリーンショット。](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. 以下の説明に従って、フィールドに詳細を入力します。

    ![スクリーンショットは、説明した値を入力できるページを示しています。](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **表示名**:管理者ページでのプラグインの表示名を入力します。

    b. **ボタン名**: SSO を使用してログインするためのログイン ページに表示されるタブの名前。

    c. **証明書**: Azure portal から先ほどダウンロードした証明書をメモ帳で開き、内容をコピーして、こちらのボックス内に貼り付けます。

    d. **エントリ ポイント**: 先ほど Azure portal からコピーした **ログイン URL** を貼り付けます。

    e. オプションを **[ON (オン)]** に切り替え、 **[保存]** をクリックします。

### <a name="create-teamphoria-test-user"></a>Teamphoria テスト ユーザーの作成

Azure AD ユーザーが Teamphoria にサインインできるようにするには、そのユーザーを Teamphoria にプロビジョニングする必要があります。 Teamphoria の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Teamphoria 企業サイトに管理者としてサインインします。

1. 左のツールバーの **[管理]** タブで **管理者** 設定をクリックし、 **[ユーザー]** をクリックして、ユーザーの管理者ページを開きます。

    ![従業員の追加](./media/teamphoria-tutorial/admin_manage_users.png)

1. **[MANUAL INVITE (手動招待)]** オプションをクリックします。

    ![[MANUAL INVITE]\(手動招待\) オプションを示すスクリーンショット。](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. このページで、次の操作を実行します。

    ![[MANUAL USER INVITE]\(ユーザーの手動招待\) ページを示すスクリーンショット。ここで、名前とメール アドレスを入力できます。](./media/teamphoria-tutorial/manual_user_invite.png)

    a. **[EMAIL ADDRESS]\(メール アドレス\)** ボックスに、ユーザー (B.Simon など) の **メール アドレス** を入力します。

    b. **[FIRST NAME]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **B**)。

    c. **[LAST NAME]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    d. **[INVITE 1 USER (1 ユーザーを招待)]** をクリックします。 ユーザーをシステムに作成するには、そのユーザーが招待を受け入れる必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Teamphoria] タイルをクリックすると、SSO を設定した Teamphoria に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Teamphoria を試す](https://aad.portal.azure.com/)