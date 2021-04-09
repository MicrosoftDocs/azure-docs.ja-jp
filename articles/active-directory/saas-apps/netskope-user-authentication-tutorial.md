---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Netskope User Authentication の統合 | Microsoft Docs
description: Azure Active Directory と Netskope User Authentication の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 1ae9e39f5a23725b17451d7144c8304194d2a1d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514369"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Netskope User Authentication の統合

このチュートリアルでは、Netskope User Authentication と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Netskope User Authentication を統合すると、次のことが可能になります。

* Netskope User Authentication にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Netskope User Authentication に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Netskope User Authentication でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Netskope User Authentication では、**SP および IDP によって開始される SSO** がサポートされます

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>ギャラリーからの Netskope User Authentication の追加

Azure AD への Netskope User Authentication の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Netskope User Authentication を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Netskope User Authentication**」と入力します。
1. 結果のパネルから **[Netskope User Authentication]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Netskope User Authentication の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Netskope User Authentication に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Netskope User Authentication の関連ユーザーとの間にリンク関係を確立する必要があります。

Netskope User Authentication で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Netskope User Authentication SSO の構成](#configure-netskope-user-authentication-sso)** - アプリケーション側でシングル サインオンを構成します。
    * **[Netskope User Authentication のテスト ユーザーの作成](#create-netskope-user-authentication-test-user)** - Netskope User Authentication で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Netskope User Authentication** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<tenantname>.goskope.com/<customer entered string>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値については、このチュートリアルの後半で説明します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<tenantname>.goskope.com` という形式で URL を入力します。

    > [!NOTE]
    > [サインオン URL] は実際の値ではありません。 実際のサインオン URL で [サインオン URL] の値を更新してください。 サインオン URL 値を取得するには、[Netskope User Authentication クライアント サポート チーム](mailto:support@netskope.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Set up Netskope User Authentication]\(Netskope User Authentication の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Netskope User Authentication へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Netskope User Authentication]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-netskope-user-authentication-sso"></a>Netskope User Authentication SSO を構成する

1. ブラウザーで新しいタブを開き、Netskope User Authentication の会社のサイトに管理者としてサインインします。

1. **[Active Platform]\(アクティブなプラットフォーム\)** タブをクリックします。

    ![[設定] で [アクティブなプラットフォーム] が選択されていることを示すスクリーンショット。](./media/netskope-user-authentication-tutorial/user1.png)

1. **[FORWARD PROXY]\(転送プロキシ\)** までスクロールして **[SAML]** を選択します。

    ![[アクティブなプラットフォーム] で [SAML] が選択されていることを示すスクリーンショット。](./media/netskope-user-authentication-tutorial/config-saml.png)

1. **[SAML Settings]** ページで、次の手順を実行します。

    ![[SAML 設定] を示すスクリーンショット。ここでは、説明されている値を入力できます。](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. **[SAML Entity ID]\(SAML エンティティ ID\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]** ボックスに貼り付けます。

    b. **[SAML ACS URL]** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

1. **[ADD ACCOUNT]\(アカウントの追加\)** をクリックします。

    ![[アカウントの追加] を示すスクリーンショット。[SAML] ウィンドウが選択されています。](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. **[Add SAML Account]\(SAML アカウントの追加\)** ページで、次の手順を実行します。

    ![[SAML アカウントの追加] を示すスクリーンショット。ここでは、説明されている値を入力できます。](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. **[NAME]\(名前\)** ボックスに、Azure AD のような名前を入力します。

    b. **[IDP URL]** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[IDP ENTITY ID]\(IDP エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    d. ダウンロードしたメタデータ ファイルをメモ帳で開き、その内容をクリップボードにコピーし、 **[IDP CERTIFICATE]\(IDP 証明書\)** テキストボックスに貼り付けます。

    e. **[保存]** をクリックします。

### <a name="create-netskope-user-authentication-test-user"></a>Netskope User Authentication テスト ユーザーを作成する

1. ブラウザーで新しいタブを開き、Netskope User Authentication の会社のサイトに管理者としてサインインします。

1. 左側のナビゲーション ペインの **[Settings]\(設定\)** タブをクリックします。

    ![選択した [設定] を示すスクリーンショット。](./media/netskope-user-authentication-tutorial/config-settings.png)

1. **[Active Platform]\(アクティブなプラットフォーム\)** タブをクリックします。

    ![[設定] で [アクティブなプラットフォーム] が選択されていることを示すスクリーンショット。](./media/netskope-user-authentication-tutorial/user1.png)

1. **[Users]** タブをクリックします。

    ![[アクティブなプラットフォーム] で [ユーザー] が選択されていることを示すスクリーンショット。](./media/netskope-user-authentication-tutorial/add-user.png)

1. **[ADD USERS]\(ユーザーの追加\)** をクリックします。

    ![[ユーザー] ダイアログ ボックスを示すスクリーンショット。ここでは、[ユーザーの追加] を選択できます。](./media/netskope-user-authentication-tutorial/user-add.png)

1. 追加するユーザーのメール アドレスを入力し、 **[ADD]\(追加\)** をクリックします。

    ![[ユーザーの追加] を示すスクリーンショット。ここでは、ユーザーの一覧を入力できます。](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Netskope User Authentication] タイルをクリックすると、SSO を設定した Netskope User Authentication に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Netskope User Authentication を試す](https://aad.portal.azure.com/)