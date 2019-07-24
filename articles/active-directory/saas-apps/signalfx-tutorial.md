---
title: チュートリアル:Azure Active Directory と SignalFx の統合 | Microsoft Docs
description: Azure Active Directory と SignalFx の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6f742def0441b5ae18ad9da3a8ac9d280de8f824
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565480"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>チュートリアル:Azure Active Directory と SignalFx の統合

このチュートリアルでは、SignalFx と Azure Active Directory (Azure AD) を統合する方法について説明します。
SignalFx と Azure AD の統合には、次の利点があります。

* SignalFx にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SignalFx に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

SignalFx と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* SignalFx でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SignalFx では、**IDP** によって開始される SSO がサポートされます
* SignalFx では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-signalfx-from-the-gallery"></a>ギャラリーから SignalFx を追加する

Azure AD への SignalFx の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SignalFx を追加する必要があります。

**ギャラリーから SignalFx を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SignalFx**」と入力し、結果パネルで **[SignalFx]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の SignalFx](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SignalFx で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SignalFx 内の関連ユーザー間にリンク関係が確立されている必要があります。

SignalFx で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SignalFx シングル サインオンの構成](#configure-signalfx-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SignalFx のテスト ユーザーの作成](#create-signalfx-test-user)** - SignalFx で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SignalFx で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SignalFx** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[SignalFx のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** テキスト ボックスに、`https://api.signalfx.com/v1/saml/metadata` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://api.signalfx.com/v1/saml/acs/<integration ID>` のパターンを使用して URL を入力します

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 実際の応答 URL に値を置き換えます。実際の値については後で説明します。

5. SignalFx アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、**[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。 

    | Name |  ソース属性|
    | ------------------- | -------------------- |
    | User.FirstName     | User.givenname |
    | User.email          | User.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | User.surname    |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[SignalFx の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-signalfx-single-sign-on"></a>SignalFx でのシングル サインオンの構成

1. SignalFx 企業サイトに管理者としてサインインします。

1. SignalFx で一番上に表示されている **[統合]** をクリックし、統合ページを開きます。

    ![SignalFx 統合](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. **[ログイン サービス]** セクションの **[Azure Active Directory]** タイルをクリックします。

    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. **[NEW INTEGRATION]\(新規統合\)** をクリックし、**[インストール]** タブで次の手順を実行します。

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. **[名前]** テキストボックスに新しい統合名を入力します。たとえば、「**OurOrgName SAML SSO**」にします。

    b. **[統合 ID]** 値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスで、 **応答 URL** の `<integration ID>` の代わりに追加します。

    c. **[ファイルのアップロード]** をクリックし、**[証明書]** テキストボックスで、Azure Portal からダウンロードした **Base64 エンコード証明書**をアップロードします。

    d. **[発行者の URL]** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    e. **[メタデータ URL]** ボックスに、Azure portal からコピーした**ログイン URL** を貼り付けます。

    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SignalFx へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[SignalFx]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SignalFx]** を選択します。

    ![アプリケーションの一覧の [SignalFx] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-signalfx-test-user"></a>SignalFx テスト ユーザーを作成する

このセクションの目的は、SignalFx で Britta Simon というユーザーを作成することです。 SignalFx では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない SignalFx ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

SAML SSO から SignalFx に初めてサインインするとき、[SignalFx サポート チーム](mailto:kmazzola@signalfx.com)からメールが届きます。そのメールに記載されているリンクから認証する必要があります。 これは初めてサインインするときにのみ行われます。その後のログインではメールからの検証は要求されません。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、 [SignalFx サポート チーム](mailto:kmazzola@signalfx.com)にお問い合わせください

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SignalFx] タイルをクリックすると、SSO を設定した SignalFx に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

