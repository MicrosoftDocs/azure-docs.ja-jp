---
title: チュートリアル:Azure Active Directory と Salesforce の統合 | Microsoft Docs
description: Azure Active Directory と Salesforce の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dffa40d4a34241f54b67fc28a1d4b7ba320347d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092505"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>チュートリアル:Azure Active Directory と Salesforce の統合

このチュートリアルでは、Salesforce と Azure Active Directory (Azure AD) を統合する方法について説明します。
Salesforce と Azure AD の統合には、次の利点があります。

* Salesforce にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Salesforce に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Salesforce の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Salesforce でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Salesforce では、**SP** によって開始される SSO がサポートされます

* Salesforce では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

* Salesforce では、[**自動化された**ユーザー プロビジョニング](salesforce-provisioning-tutorial.md)がサポートされます

## <a name="adding-salesforce-from-the-gallery"></a>ギャラリーからの Salesforce の追加

Azure AD への Salesforce の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Salesforce を追加する必要があります。

**ギャラリーから Salesforce を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Salesforce**」と入力し、結果パネルで **[Salesforce]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Salesforce](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Salesforce で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Salesforce 内の関連ユーザー間にリンク関係が確立されている必要があります。

Salesforce で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Salesforce シングル サインオンの構成](#configure-salesforce-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Salesforce テスト ユーザーの作成](#create-salesforce-test-user)** - Salesforce で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Salesforce で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Salesforce** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Salesforce のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して値を入力します。

    エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`

    開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. **[識別子]** ボックスに、次の形式で値を入力します。

    エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`

    開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)に問い合わせてください。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Salesforce のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-salesforce-single-sign-on"></a>Salesforce のシングル サインオンを構成する

1. ブラウザーで新しいタブを開き、Salesforce の管理者アカウントにサインインします。

2. ページの右上隅の**設定アイコン**の下の **[Setup]\(セットアップ\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/configure1.png)

3. ナビゲーション ウィンドウの **[SETTINGS]\(設定\)** まで下へスクロールし、 **[Identity]\(ID\)** をクリックして、関連セクションを展開します。 次に、 **[シングル サインオンの設定]** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-admin-sso.png)

4. **[シングル サインオンの設定]** ページで、 **[編集]** ボタンをクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Salesforce アカウント用のシングル サインオン設定を有効にできない場合は、必要に応じて、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)にお問い合わせください。

5. **[SAML 有効]** を選択し、 **[保存]** をクリックします。

      ![Configure single sign-on](./media/salesforce-tutorial/sf-enable-saml.png)

6. SAML のシングル サインオン設定を構成するには、 **[New from Metadata File]\(メタデータ ファイルから新規)** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. **[ファイルの選択]** をクリックして、Azure portal からダウンロードしたメタデータ XML ファイルをアップロードし、 **[作成]** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/xmlchoose.png)

8. **[SAML シングル サインオンの設定]** ページでは、フィールドは自動的に入力されます。[保存] をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/salesforcexml.png)

9. Salesforce の左側のナビゲーション ウィンドウで、 **[Company Settings]\(会社の設定\)** をクリックして関連するセクションを展開し、 **[My Domain]\(マイ ドメイン\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-my-domain.png)

10. **[認証の構成]** セクションまで下へスクロールし、 **[編集]** ボタンをクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. **[Authentication Configuration]\(認証の構成\)** セクションで、SAML SSO 構成の **[Authentication Service]\(認証サービス\)** として **[AzureSSO]** をオンにし、 **[Save]\(保存\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 複数の認証サービスを選択した場合、ユーザーが Salesforce 環境へのシングル サインオンを開始すると、サインインに使用する認証サービスを選択するよう要求されます。 このメッセージが表示されないようにするには、**その他すべての認証サービスをオフのままに**しておいてください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon\@yourcompanydomain.extension`」と入力します。 たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Salesforce へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Salesforce]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Salesforce]** を選択します。

    ![アプリケーションの一覧の Salesforce リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-salesforce-test-user"></a>Salesforce テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Salesforce に作成します。 Salesforce では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Salesforce に存在しない場合は、Salesforce にアクセスしようとしたときに新しいユーザーが作成されます。 Salesforce は、自動ユーザー プロビジョニングもまたサポートしています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](salesforce-provisioning-tutorial.md)をご覧ください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Salesforce] タイルをクリックすると、SSO を設定した Salesforce に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [[ユーザー プロビジョニングの構成]](salesforce-provisioning-tutorial.md)
