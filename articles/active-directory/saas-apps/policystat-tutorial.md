---
title: チュートリアル:Azure Active Directory と PolicyStat の統合 | Microsoft Docs
description: Azure Active Directory と PolicyStat の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 4d6274f3e1356a8ff8a997e830f0ebee43f50cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92893105"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>チュートリアル:Azure Active Directory と PolicyStat の統合

このチュートリアルでは、PolicyStat と Azure Active Directory (Azure AD) を統合する方法について説明します。
PolicyStat と Azure AD の統合には、次の利点があります。

* PolicyStat にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に PolicyStat にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

PolicyStat と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* PolicyStat でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* PolicyStat では、**SP** によって開始される SSO がサポートされます

* PolicyStat では、**Just In Time** ユーザー プロビジョニングがサポートされています。

## <a name="adding-policystat-from-the-gallery"></a>ギャラリーから PolicyStat を追加する

Azure AD への PolicyStat の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PolicyStat を追加する必要があります。

**ギャラリーから PolicyStat を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**PolicyStat**」と入力し、結果ウィンドウで **PolicyStat** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の PolicyStat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、PolicyStat で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと PolicyStat 内の関連ユーザー間にリンク関係が確立されている必要があります。

PolicyStat で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[PolicyStat シングル サインオンの構成](#configure-policystat-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
4. **[PolicyStat テスト ユーザーの作成](#create-policystat-test-user)** - PolicyStat で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

PolicyStat で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **PolicyStat** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[PolicyStat のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.policystat.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[PolicyStat クライアント サポート チーム](http://www.policystat.com/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

5. PolicyStat アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![[編集] アイコンが選択されている [ユーザー属性] ダイアログを示すスクリーンショット。](common/edit-attribute.png)

6. その他に、PolicyStat アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | 名前 | ソース属性 |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。
    
    ![[新しい要求の追加] および [保存] アクションが強調表示されている [ユーザーの要求] セクションを示すスクリーンショット。](common/new-save-attribute.png)

    ![[ユーザー要求の管理] ダイアログを示すスクリーンショット。[名前]、[変換]、および [パラメーター] のテキスト ボックスが強調表示され、[保存] ボタンが選択されています。](./media/policystat-tutorial/attribute01.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[変換]** を選択します。

    e. **[変換]** の一覧から、その行に対して表示される値を入力します。
    
    f. **[パラメーター 1]** の一覧から、その行に対して表示される値を入力します。

    g. **[保存]** をクリックします。

7. **[PolicyStat の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-policystat-single-sign-on"></a>PolicyStat のシングル サインオンを構成する

1. 別の Web ブラウザーのウィンドウで、PolicyStat 企業サイトに管理者としてログインします。

2. **[Admin]** タブをクリックし、左側のナビゲーション ウィンドウの **[Single Sign-On Configuration]** をクリックします。
   
    ![[Administrator Menu]\(管理者メニュー\)](./media/policystat-tutorial/ic808633.png "管理者メニュー")

3. **[Your IDP Metadata]** をクリックし、 **[Your IDP Metadata]** セクションで次の手順に従います。
   
    ![[Your IDP Metadata]\(IDP メタデータ) アクションが選択されていることを示すスクリーンショット。](./media/policystat-tutorial/ic808636.png "[Single Sign-On Configuration]")
   
    a. ダウンロードしたメタデータ ファイルの内容をコピーし、 **[Your Identity Provider Metadata]\(ID プロバイダーのメタデータ\)** テキスト ボックスに貼り付けます。

    b. **[変更を保存]** をクリックします。

4. **[Configure Attributes]** をクリックし、 **[Configure Attributes]** セクションで次の手順に従います。
   
    a. **[Username Attribute]** ボックスに、「**uid**」と入力します。

    b. **[First Name Attribute]\(名前 (名) 属性\)** テキスト ボックスに、Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** の名前 (名) 属性の要求名を入力します。

    c. **[Last Name Attribute]\(名前 (姓) 属性\)** テキスト ボックスに、Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** の名前 (姓) 属性の要求名を入力します。

    d. **[Email Attribute]\(電子メール属性\)** テキスト ボックスに、Azure **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** の電子メール属性の要求名を入力します。

    e. **[変更を保存]** をクリックします。

5. **[Setup]** セクションで、 **[Enable Single Sign-on Integration]** を選択します。
   
    ![[Single Sign-On Configuration]\(シングル サインオン構成\)](./media/policystat-tutorial/ic808634.png "[Single Sign-On Configuration]")


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、独自のアカウントに PolicyStat へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[PolicyStat]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[PolicyStat]** を選択します。

    ![アプリケーションの一覧の PolicyStat のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] リストで自分のアカウントを選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-policystat-test-user"></a>PolicyStat テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを PolicyStat に作成します。 PolicyStat では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 PolicyStat にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!NOTE]
>PolicyStat から提供されている他の PolicyStat ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [PolicyStat] タイルをクリックすると、SSO を設定した PolicyStat に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)