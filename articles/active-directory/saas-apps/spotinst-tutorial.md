---
title: チュートリアル:Azure Active Directory と Spotinst の統合 | Microsoft Docs
description: Azure Active Directory と Spotinst の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 817eecce6c2c1be273b47494e8ecc14180394796
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705334"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>チュートリアル:Azure Active Directory と Spotinst の統合

このチュートリアルでは、Spotinst と Azure Active Directory (Azure AD) を統合する方法について説明します。
Spotinst と Azure AD の統合には、次の利点があります。

* Spotinst にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Spotinst に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Spotinst と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Spotinst のシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Spotinst では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-spotinst-from-the-gallery"></a>ギャラリーからの Spotinst の追加

Azure AD への Spotinst の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Spotinst を追加する必要があります。

**ギャラリーから Spotinst を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Spotinst**」と入力し、結果パネルで **Spotinst** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Spotinst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Spotinst で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Spotinst 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Spotinst で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Spotinst シングル サインオンの構成](#configure-spotinst-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Spotinst のテスト ユーザーの作成](#create-spotinst-test-user)** - Spotinst で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Spotinst で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Spotinst** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Spotinst のドメインと URL] のシングル サインオン情報](common/idp-preintegrated-relay.png)

    a. **[追加の URL を設定します]** をクリックします。

    b. **[リレー状態]** ボックスに、値 `<ID>` を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Spotinst のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに、URL として「`https://console.spotinst.com/auth/saml`」と入力します。

    > [!NOTE]
    > リレー状態の値は実際のものではありません。 実際のリレー状態にリレー状態値を置き換えます。実際の値については後で説明します。

6. Spotinst アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。 

    | Name | ソース属性|
    | -----| --------------- |
    | Email | User.mail |
    | FirstName | User.givenname |
    | LastName | User.surname |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

9. **[Set up Spotinst]\(Spotinst の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-spotinst-single-sign-on"></a>Spotinst シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Spotinst にサインインします。

2. 画面の右上にある**ユーザー アイコン**をクリックして、 **[設定]** をクリックします。

    ![Spotinst の設定](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. 上部の **[SECURITY]\(セキュリティ\)** タブをクリックし、 **[Identity Providers]\(ID プロバイダー\)** を選択して、以下の手順を実行します。

    ![Spotinst のセキュリティ](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. インスタンスの **[Relay State]\(リレー状態\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[リレー状態]** テキスト ボックスに貼り付けます。

    b. **[BROWSE]\(参照\)** をクリックし、Azure portal からダウンロードしたメタデータ xml ファイルをアップロードします。

    c. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Spotinst へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Spotinst]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Spotinst]** を選択します。

    ![アプリケーションの一覧の Spotinst のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-spotinst-test-user"></a>Spotinst のテスト ユーザーの作成

このセクションの目的は、Spotinst で Britta Simon というユーザーを作成することです。

1. **SP** 開始モードでアプリケーションを構成してある場合は、次の手順を実行します。

   a. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Spotinst にサインインします。

   b. 画面の右上にある**ユーザー アイコン**をクリックして、 **[設定]** をクリックします。

    ![Spotinst の設定](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. **[Users]\(ユーザー\)** をクリックして、 **[ADD USER]\(ユーザーの追加\)** を選択します。

    ![Spotinst の設定](./media/spotinst-tutorial/adduser1.png)

    d. ユーザーの追加セクションで、次の手順を実行します。

    ![Spotinst の設定](./media/spotinst-tutorial/adduser2.png)

    * **[Full Name]\(フルネーム\)** ボックスに、ユーザーの氏名 (**BrittaSimon** など) を入力します。

    * **[Email]\(電子メール\)** テキスト ボックスに、ユーザーのメール アドレスを入力します (`brittasimon\@contoso.com` など)。

    * **[Organization Role, Account Role, and Accounts]\(組織ロール、アカウント ロール、アカウント\)** で組織に固有の詳細を選択します。

2. **IDP** 開始モードでアプリケーションを構成してある場合は、このセクションにアクション項目はありません。 Spotinst では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 存在しない Spotinst ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Spotinst] タイルをクリックすると、SSO を設定した Spotinst に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

