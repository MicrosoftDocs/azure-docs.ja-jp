---
title: チュートリアル:Azure Active Directory と Brightidea の統合 | Microsoft Docs
description: Azure Active Directory と Brightidea の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.openlocfilehash: 9967f349011b52a2218681956885c33456ba1d46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672765"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>チュートリアル:Azure Active Directory と Brightidea の統合

このチュートリアルでは、Brightidea と Azure Active Directory (Azure AD) を統合する方法について説明します。
Azure AD と Brightidea の統合には、次の利点があります。

* Brightidea にアクセスできるユーザーを Azure AD 内で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Brightidea に自動的にサインイン (シングル サインオン) することを可能にします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Brightidea と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Brightidea でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。


* Brightidea では、**SP と IDP** によって開始される SSO がサポートされます
* Brightidea では、**Just In Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-brightidea-from-the-gallery"></a>ギャラリーからの Brightidea の追加

Azure AD への Brightidea の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Brightidea を追加する必要があります。

**ギャラリーから Brightidea を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Brightidea**」と入力し、結果ウィンドウから **[Brightidea]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧にある Brightidea](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Brightidea による Azure AD のシングル サインオンを構成してテストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Brightidea 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Brightidea による Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Brightidea シングル サインオンの構成](#configure-brightidea-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Brightidea のテスト ユーザーの作成](#create-brightidea-test-user)** - Azure AD のユーザー表現にリンクさせる Britta Simon に対応するユーザーを Brightidea 内に作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Brightidea によって Azure AD のシングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) 内の **Brightidea** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **サービス プロバイダー メタデータ ファイル** を保持しており、**IDP** によって開始されるモードに構成したい場合は、 **[基本的な SAML 構成]** セクション上で次の手順を実行します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、Brightidea セクションのテキスト ボックスに自動的に設定されます。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    > [!Note]
    > **識別子** と **応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.brightidea.com` という形式で URL を入力します。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Brightidea のセットアップ]** セクション上で、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-brightidea-single-sign-on"></a>Brightidea シングル サインオンを構成する

1. 別の Web ブラウザー ウィンドウで、管理者の資格情報を使って Brightidea にサインインします。

2. Brightidea システム内の SSO 機能にアクセスするために、 **[Enterprise Setup]\(エンタープライズ セットアップ\)**  ->  **[認証] タブ** の順に移動します。サブ タブとして[Auth Selection]\(認証の選択\) と [SAML Profiles]\(SAML プロファイル\) の 2 つが表示されます。

    ![このスクリーンショットは、[認証] タブが選択された状態の Brightidea サイトを示しています。](./media/brightidea-tutorial/configure1.png)

3. **[Auth Selection]\(認証の選択\)** を選択します。 既定では、2 つの標準的な方法として、"Brightidea ログイン" と "登録" だけが表示されます。 SSO の方法が追加されると、一覧に表示されるようになります。

    ![Brightidea の [認証] タブのスクリーンショット。[Auth Selection]\(認証の選択\) が選択されています。](./media/brightidea-tutorial/configure2.png)

4. **[SAML Profiles]\(SAML プロファイル\)** を選択し、次の手順を実行します。

    ![Brightidea の [認証] タブのスクリーンショット。[Download Metadata]\(メタデータのダウンロード\) と [Add New]\(新規追加\) オプションを備えた [SAML Profiles]\(SAML プロファイル\) が選択されています。](./media/brightidea-tutorial/configure3.png)

    a. **[メタデータのダウンロード]** をクリックして、Azure portal 内の **[基本的な SAML 構成]** セクションからアップロードを行います。

    b. **[Identity Provider Setting]\(ID プロバイダー設定\)** 下にある **[新規追加]** ボタンをクリックして、次の手順を実行します。

    ![情報の入力先となる Brightidea の [Identity Provider Setting]\(ID プロバイダー設定\) のスクリーンショット。](./media/brightidea-tutorial/configure4.png)

   * たとえば `Azure Ad SSO` のように、 **[SAML Profile Name]\(SAML プロファイル名\)** に入力します。

   * **[Upload Metadata]\(メタデータのアップロード\)** において、[ファイルの選択] をクリックして、Azure portal からダウンロード済みのメタデータ ファイルをアップロードします。

     > [!NOTE]
     > メタデータ ファイルをアップロードすると、残りのフィールド ( **[シングル サインオン サービス]、[ID プロバイダーの発行者]、[公開キーのアップロード ]** ) は自動的に入力されます。

   * **[電子メール]** ボックスに、`mail` の値を入力します。

   * **[画面の名前]** ボックスに、`givenName` の値を入力します。

   * **[変更を保存]** をクリックします。  

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Brightidea へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal 内で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Brightidea]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧上で **[Brightidea]** を選択します。

    ![アプリケーションの一覧上の [Brightidea] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-brightidea-test-user"></a>Brightidea テスト ユーザーを作成する

このセクションでは、Brightidea 内に Britta Simon というユーザーを作成します。 Brightidea では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Brightidea にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル内の [Brightidea] タイルをクリックすると、SSO を設定した Brightidea に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)