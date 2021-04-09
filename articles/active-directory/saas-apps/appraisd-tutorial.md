---
title: チュートリアル:Azure Active Directory と Appraisd の統合 | Microsoft Docs
description: Azure Active Directory と Appraisd の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.openlocfilehash: 79db0a7bb769d6138bfeeb4e765621a1cffb4d14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457862"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>チュートリアル:Appraisd と Azure Active Directory の統合

このチュートリアルでは、Appraisd と Azure Active Directory (Azure AD) を統合する方法について説明します。 Appraisd と Azure AD を統合すると、次のことができます。

* Appraisd にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Appraisd に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Appraisd サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Appraisd では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="adding-appraisd-from-the-gallery"></a>ギャラリーから Appraisd を追加する

Azure AD への Appraisd の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Appraisd を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Appraisd**」と入力します。
1. 結果のパネルから **[Appraisd]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B. Simon** というテスト ユーザーを使用して、Appraisd に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Appraisd の関連ユーザーとの間にリンク関係を確立する必要があります。

Appraisd に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Appraisd の構成](#configure-appraisd)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user)** - B. Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B. Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Appraisd のテスト ユーザーの作成](#create-appraisd-test-user)** - Appraisd で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Appraisd** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 ユーザーは、[保存] ボタンをクリックして構成を保存し、次の手順を実行する必要があります。

    a. **[追加の URL を設定します]** をクリックします。

    b. **[リレー状態]** テキスト ボックスに、URL `<TENANTCODE>` を入力します

    c. **[SP]** 開始モードでアプリケーションを構成する場合は、 **[サインオン URL]** テキストボックスに `https://app.appraisd.com/saml/<TENANTCODE>` のパターンで URL を入力します。

    > [!NOTE]
    > このチュートリアルで後述する Appraisd SSO の [Configuration]\(構成\) ページで、実際の [Sign-on URL]\(サインオン URL\) と [Relay State]\(リレー状態\) の値を取得します。

1. Appraisd アプリケーションは、特定の形式の SAML アサーションを使用するため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Appraisd アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![このスクリーンショットは、[編集] アイコンが強調表示された状態の [ユーザー属性] ペインを示しています。](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Appraisd]\(Appraisd のセットアップ\)** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Appraisd の構成

1. Appraisd 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup Appraisd]\(Appraisd のセットアップ)** をクリックすると、Appraisd アプリケーションに移動します。 そこから、管理者の資格情報を入力して Appraisd にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Appraisd を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Appraisd 企業サイトにサインインして、次の手順を実行します。

4. ページの右上にある **[Settings]\(設定\)** アイコンをクリックし、 **[Configuration]\(構成\)** に移動します。

    ![このスクリーンショットは、コールアウトされた状態の [Configuration]\(構成\) リンクを示しています。](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. メニューの左側から **[SAML single sign-on]\(SAML シングル サインオン\)** をクリックします。

    ![このスクリーンショットは、[SAML single sign-on]\(SAML シングル サインオン\) オプションが強調表示された構成オプションを示しています。](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. **[SAML 2.0 Single Sign-On configuration]\(SAML 2.0 のシングル サインオンの構成\)** ページで、次の手順を実行します。

    ![このスクリーンショットは、[SAML 2.0 Single Sign-On configuration]\(SAML 2.0 のシングル サインオンの構成\) ページを示しています。ここで、[Default Relay State]\(既定のリレー状態\) と [Service-initiated login U R L]\(サービス開始ログイン U R L\) を編集できます。](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. **[Default Relay State]\(既定のリレー状態\)** 値をコピーし、Azure portal の **[基本的な SAML 構成]** の **[リレー状態]** ボックスに貼り付けます。

    b. **[Service-initiated login URL]\(サービス開始ログイン URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** の **[サインオン URL]** ボックスに貼り付けます。

7. 同じページの **[Identifying users]\(ユーザーの識別\)** まで下にスクロールし、次の手順を実行します。

    ![このスクリーンショットは、[Identifying users]\(ユーザーの識別\) を示しています。ここで、この手順の値を入力できます。](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. **[Identity Provider Single Sign-On URL]\(ID プロバイダー シングル サインオン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付け、 **[Save]\(保存\)** をクリックします。

    b. **[Identity Provider Issuer URL]\(ID プロバイダーの発行者 URL\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付け、 **[Save]\(保存\)** をクリックします。

    c. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付け、 **[Save]\(保存\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B. Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B. Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に Appraisd へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Appraisd]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B. Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-appraisd-test-user"></a>Appraisd テスト ユーザーを作成する

Azure AD ユーザーが Appraisd にサインインできるようにするには、ユーザーを Appraisd にプロビジョニングする必要があります。 Appraisd では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Appraisd にサインインします。

2. ページの右上にある **[Settings]\(設定\)** アイコンをクリックし、 **[Administration centre]\(管理センター\)** に移動します。

    ![このスクリーンショットは、[Settings]\(設定\) オプションを示しています。ここで、[Administration centre]\(管理センター\) を選択できます。](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. ページの上部にあるツール バーで、 **[People]\(人\)** をクリックし、 **[Add a new user]\(新しいユーザーの追加\)** に移動します。

    ![このスクリーンショットは、[People]\(人\) と [Add a new user]\(新しいユーザーの追加\) がコールアウトされた状態の Appraisd ページを示しています。](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. **[Add a new user]\(新しいユーザーの追加\)** ページで、次の手順を実行します。

    ![このスクリーンショットは、[Add a new user]\(新しいユーザーの追加\) ページを示しています。](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. **[First name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    b. **[Last name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    c. **[電子メール]** ボックスに、ユーザーのメール アドレスを入力します (例: `B. Simon@contoso.com`)。

    d. **[ユーザーの追加]** をクリックします。

### <a name="test-sso"></a>SSO のテスト

アクセス パネル上で [Appraisd] タイルを選択すると、SSO を設定した Appraisd に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)