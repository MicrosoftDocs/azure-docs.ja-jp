---
title: 'チュートリアル: Azure Active Directory と Vidyard の統合 | Microsoft Docs'
description: Azure Active Directory と Vidyard の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 4176c92d48b67b9f9207f22ebd8939b5ec1437ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636750"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>チュートリアル: Azure Active Directory と Vidyard の統合

このチュートリアルでは、Vidyard と Azure Active Directory (Azure AD) を統合する方法について説明します。
Vidyard と Azure AD の統合には、次の利点があります。

* Vidyard にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Vidyard に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Vidyard と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Vidyard でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Vidyard では、**SP** と **IDP** によって開始される SSO がサポートされます

* Vidyard では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-vidyard-from-the-gallery"></a>ギャラリーからの Vidyard の追加

Azure AD への Vidyard の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Vidyard を追加する必要があります。

**ギャラリーから Vidyard を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Vidyard**」と入力し、結果パネルで **Vidyard** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Vidyard](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Vidyard で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Vidyard 内の関連ユーザー間にリンク関係が確立されている必要があります。

Vidyard で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Vidyard シングル サインオンの構成](#configure-vidyard-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Vidyard のテスト ユーザーの作成](#create-vidyard-test-user)** - Vidyard で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Vidyard で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Vidyard** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://secure.vidyard.com/sso/saml/<unique id>/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://secure.vidyard.com/sso/saml/<unique id>/consume` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://secure.vidyard.com/sso/saml/<unique id>/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL に値を置き換えます。実際の値については後で説明します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Vidyard のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-vidyard-single-sign-on"></a>Vidyard シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Vidyard Software 企業サイトにサインインします。

2. Vidyard ダッシュボードから、 **[Group]\(グループ\)**  >  **[Security]\(セキュリティ\)** を選択します

    ![Vidyard Software サイトで [Group]\(グループ\) から [Security]\(セキュリティ\) が選択されていることを示すスクリーンショット。](./media/vidyard-tutorial/configure1.png)

3. **[New Profile]\(新しいプロファイル\)** タブをクリックします。

    ![[New Profile]\(新しいプロファイル\) ボタンを示すスクリーンショット。](./media/vidyard-tutorial/configure2.png)

4. **[SAML Configuration]\(SAML の構成\)** セクションで、次の手順に従います。

    ![[SAML Configuration]\(SAML の構成\) を示すスクリーンショット。ここで、説明されている値を入力できます。](./media/vidyard-tutorial/configure3.png)

    a. **[Profile Name]\(プロファイル名\)** ボックスに一般的なプロファイル名を入力してください。

    b. **[SSO User Login Page]\(SSO ユーザー ログイン ページ\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションにある **[サインオン URL]** ボックスに貼り付けます。

    c. **[ACS URL]** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    d. **[Issuer/Metadata URL]\(発行者/メタデータ URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]** ボックスに貼り付けます。

    e. Azure portal からダウンロードした証明書ファイルをメモ帳で開き、 **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    f. **[SAML Endpoint URL]\(SAML エンドポイント URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    g. **[Confirm]\(確認\)** をクリックします。

5. [Single Sign On]\(シングル サインオン\) タブで、既存のプロファイルの横にある **[Assign]\(割り当て\)** を選択します

    ![Azure A D S S O プロファイルの [Assign]\(割り当て\) ボタンを示すスクリーンショット。](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > SSO プロファイルを作成したら、ユーザーが Azure 経由でアクセスする必要のある任意のグループに SSO プロファイルを割り当てます。 ユーザーが割り当てられたグループ内に存在しない場合、Vidyard は自動的にユーザー アカウントを作成し、そのロールをリアルタイムで割り当てます。

6. **[Groups Available to Assign]\(割り当て可能なグループ\)** に表示されている、対象の組織グループを選択します。

    ![[Assign SAML Configuration to Organizations]\(組織への SAML 構成の割り当て\) セクションを示すスクリーンショット。ここで、対象のグループを選択できます。](./media/vidyard-tutorial/configure5.png)

7. **[Groups Currently Assigned]\(現在割り当てられているグループ\)** に、割り当てられているグループが表示されます。 組織に従ってグループのロールを選択し、 **[Confirm]\(確認\)** をクリックします。

    ![[Assign SAML Configuration to Organizations]\(組織への SAML 構成の割り当て\) セクションを示すスクリーンショット。ここで、ロールを選択できます。](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > 詳しくは、[こちらの文書](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard)を参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Vidyard へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Vidyard]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Vidyard]** を選択します。

    ![アプリケーションの一覧の Vidyard のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-vidyard-test-user"></a>Vidyard のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Vidyard に作成します。 Vidyard では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Vidyard にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Vidyard サポート チーム](mailto:support@vidyard.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Vidyard] タイルをクリックすると、SSO を設定した Vidyard に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)