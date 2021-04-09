---
title: 'チュートリアル: Azure Active Directory と Fidelity NetBenefits の統合 | Microsoft Docs'
description: Azure Active Directory と Fidelity NetBenefits の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: ac41bf89a3c2997fe0c179ef1a64787b5fb8d617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453588"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>チュートリアル: Azure Active Directory と Fidelity NetBenefits の統合

このチュートリアルでは、Fidelity NetBenefits と Azure Active Directory (Azure AD) を統合する方法について説明します。
Fidelity NetBenefits と Azure AD の統合には、次の利点があります。

* Fidelity NetBenefits にアクセスするユーザーを Azure AD 上でコントロールできます。
* ユーザーが自分の Azure AD アカウントで Fidelity NetBenefits に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Fidelity NetBenefits と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Fidelity NetBenefits でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Fidelity NetBenefits では、**IDP** Initiated SSO がサポートされます

* Fidelity NetBenefits では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>ギャラリーからの Fidelity NetBenefits の追加

Azure AD への Fidelity NetBenefits の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Fidelity NetBenefits を追加する必要があります。

**ギャラリーから Fidelity NetBenefits を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Fidelity NetBenefits**」と入力し、結果ウィンドウで **[Fidelity NetBenefits]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Fidelity NetBenefits](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Fidelity NetBenefits で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Fidelity NetBenefits 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Fidelity NetBenefits で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Fidelity NetBenefits のシングル サインオンの構成](#configure-fidelity-netbenefits-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Fidelity NetBenefits のテスト ユーザーの作成](#create-fidelity-netbenefits-test-user)** - Fidelity NetBenefits で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Fidelity NetBenefits で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Fidelity NetBenefits** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[Fidelity NetBenefits のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    テスト環境: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    運用環境: `urn:sp:fidelity:geninbndnbparts20`

    b. **[応答 URL]** テキストボックスに、実装時に Fidelity から提供された URL を入力するか、担当の Fidelity Client Service Manager に問い合わせてください。

5. Fidelity NetBenefits アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Fidelity NetBenefits アプリケーションでは、**nameidentifier** が **employeeid** にマップされるか、または実際の組織の **nameidentifier** に該当する他の要求にマップされると想定されているため、**[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits では、静的および動的なフェデレーションがサポートされます。 静的の場合は、SAML ベースのジャスト イン タイムのユーザー プロビジョニングが使用されません。一方、動的の場合は、ジャスト イン タイムのユーザー プロビジョニングがサポートされます。 JIT ベースのプロビジョニングを使用する場合、お客様は、ユーザーの生年月日など、要求をいくつか Azure AD で追加する必要があります。こうした詳細は、**Fidelity Client Service Manager** から提供されます。インスタンスに対して、この動的なフェデレーションを有効にする必要があります。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Fidelity NetBenefits のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Fidelity NetBenefits のシングル サインオンの構成

**Fidelity NetBenefits** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Fidelity NetBenefits サポート チーム](mailto:SSOMaintenance@fmr.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Fidelity NetBenefits へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Fidelity NetBenefits]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Fidelity NetBenefits]** を選択します。

    ![アプリケーションの一覧の Fidelity NetBenefits のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-fidelity-netbenefits-test-user"></a>Fidelity NetBenefits のテスト ユーザーの作成

このセクションでは、Fidelity NetBenefits で Britta Simon というユーザーを作成します。 静的なフェデレーションを作成している場合、Fidelity NetBenefits プラットフォームでユーザーを作成するには、担当の **Fidelity Client Service Manager** と一緒に作業してください。 こうしたユーザーは、シングル サインオンを使用する前に作成し、アクティブにする必要があります。

動的なフェデレーションについては、ジャスト イン タイムプロビジョニングを使用してユーザーが作成されます。 JIT ベースのプロビジョニングを使用する場合、お客様は、ユーザーの生年月日など、要求をいくつか Azure AD で追加する必要があります。こうした詳細は、**Fidelity Client Service Manager** から提供されます。インスタンスに対して、この動的なフェデレーションを有効にする必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Fidelity NetBenefits] タイルをクリックすると、SSO を設定した Fidelity NetBenefits に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)