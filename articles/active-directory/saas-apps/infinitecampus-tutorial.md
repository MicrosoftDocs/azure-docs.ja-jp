---
title: 'チュートリアル: Azure Active Directory と Infinite Campus の統合 | Microsoft Docs'
description: Azure Active Directory と Infinite Campus の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 08cee87d94b20e99bcc46d42cbac7eb4272561bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460236"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>チュートリアル: Azure Active Directory と Infinite Campus の統合

このチュートリアルでは、Infinite Campus と Azure Active Directory (Azure AD) を統合する方法について説明します。
Infinite Campus と Azure AD の統合には、次の利点があります。

* Infinite Campus にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Infinite Campus に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Infinite Campus と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Infinite Campus シングル サインオンが有効なサブスクリプション
* 構成を実行するには、少なくとも Azure Active Directory 管理者であり、「Student Information System (SIS)」のキャンパス製品セキュリティ ロールを持っている必要があります。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Infinite Campus では、**SP** によって開始される SSO がサポートされます

## <a name="adding-infinite-campus-from-the-gallery"></a>ギャラリーからの Infinite Campus の追加

Azure AD への Infinite Campus の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Infinite Campus を追加する必要があります。

**ギャラリーから Infinite Campus を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Infinite Campus**」と入力し、結果パネルで **Infinite Campus** を選び、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧での Infinite Campus](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Infinite Campus で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Infinite Campus 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Infinite Campus で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Infinite Campus シングル サインオンの構成](#configure-infinite-campus-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Infinite Campus テスト ユーザーの作成](#create-infinite-campus-test-user)** - Infinite Campus で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Infinite Campus で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Infinite Campus** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. [基本的な SAML 構成] セクションで、次の手順を実行します (ドメインはホスティング モデルによって異なりますが、**FULLY-QUALIFIED-DOMAIN** の値は Infinite Campus のインストールと一致する必要があることに注意してください)。

    a. **[サインオン URL]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>` のパターンを使用して URL を入力します。

    ![[Infinite Campus のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Infinite Campus シングルサインオンの構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Infinite Campus にサインインします。

2. メニューの左側にある **[System Administration]\(システム管理\)** をクリックします。

    ![管理](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. **[User Security]\(ユーザー セキュリティ\)**  >  **[SAML Management]\(SAML 管理\)**  >  **[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** に移動します。

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. **[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** ページで、次の手順に従います。

    ![SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. **[Enable SAML Single Sign On]\(SAML シングル サインオンを有効にする\)** チェック ボックスをオンにします。

    b. **[Optional Attribute Name]\(省略可能な属性名\)** を編集して **名前** を含めます

    c. **[Select an option to retrieve Identity Provider (IDP) server data]\(ID プロバイダー (IDP) サーバー データ取得のオプションを選択する\)** セクションで、**[Metadata URL]\(メタデータ URL\)** を選択し、Azure portal からコピーした **アプリのフェデレーション メタデータ URL** をボックスに貼り付けて、**[Sync]\(同期\)** をクリックします。

    d. **[Sync]\(同期\)** をクリックすると、**[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** ページに値が自動的に設定されます。 これらの値は上記のステップ 4 で示される値と一致することを確認できます。

    e. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

> [!NOTE]
> すべての Azure ユーザーに Infinite Campus へのシングル サインオン アクセス権を付与し、Infinite Campus の内部アクセス許可システムによってアクセスを制御する場合は、アプリケーションの **[User Assignment Required]\(ユーザーの割り当てが必要\)** プロパティを No に設定し、以下の手順をスキップできます。

このセクションでは、Britta Simon に Infinite Campus へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Infinite Campus]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Infinite Campus]** を選択します。

    ![アプリケーションの一覧の Infinite Campus のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-infinite-campus-test-user"></a>Infinite Campus テスト ユーザーの作成

Infinite Campus はデモグラフィック中心のアーキテクチャです。 Infinite Campus プラットフォームでのユーザーの追加については、[Infinite Campus サポート チーム](mailto:sales@infinitecampus.com)に問い合わせてください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Infinite Campus] タイルをクリックすると、SSO を設定した Infinite Campus に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)