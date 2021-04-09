---
title: 'チュートリアル: Azure Active Directory と IBM Kenexa Survey Enterprise の統合| Microsoft Docs'
description: Azure Active Directory と IBM Kenexa Survey Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: faea5fdc7fdab11c630f24dbb8527e5bf198765c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459154"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>チュートリアル: Azure Active Directory と IBM Kenexa Survey Enterprise の統合

このチュートリアルでは、IBM Kenexa Survey Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。
IBM Kenexa Survey Enterprise と Azure AD の統合には、次の利点があります。

* IBM Kenexa Survey Enterprise にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで IBM Kenexa Survey Enterprise に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

IBM Kenexa Survey Enterprise と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* IBM Kenexa Survey Enterprise のシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* IBM Kenexa Survey Enterprise では、**IDP** Initiated SSO がサポートされます

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>ギャラリーからの IBM Kenexa Survey Enterprise の追加

Azure AD への IBM Kenexa Survey Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に IBM Kenexa Survey Enterprise を追加する必要があります。

**ギャラリーから IBM Kenexa Survey Enterprise を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**IBM Kenexa Survey Enterprise**」と入力し、結果パネルで **[IBM Kenexa Survey Enterprise]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の IBM Kenexa Survey Enterprise](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、IBM Kenexa Survey Enterprise で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと IBM Kenexa Survey Enterprise 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

IBM Kenexa Survey Enterprise で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[IBM Kenexa Survey Enterprise のシングル サインオンの構成](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[IBM Kenexa Survey Enterprise のテスト ユーザーの作成](#create-ibm-kenexa-survey-enterprise-test-user)** - IBM Kenexa Survey Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

IBM Kenexa Survey Enterprise で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **IBM Kenexa Survey Enterprise** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[IBM Kenexa Survey Enterprise のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://surveys.kenexa.com/<companycode>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://surveys.kenexa.com/<companycode>/tools/sso.asp` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を入手するには、[IBM Kenexa Survey Enterprise クライアント サポート チーム](https://www.ibm.com/support/home/?lnk=fcw)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. IBM Kenexa Survey Enterprise アプリケーションでは、特定の形式の Security Assertions Markup Language (SAML) アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 応答内のユーザー識別子要求の値は、Kenexa システムで構成された SSO ID に一致する必要があります。 組織内の適切なユーザー ID を SSO Internet Datagram Protocol (IDP) としてマッピングするには、[IBM Kenexa Survey Enterprise サポート チーム](https://www.ibm.com/support/home/?lnk=fcw)と連携してください。

    既定では、Azure AD はユーザーの識別子を、ユーザー プリンシパル名 (UPN) の値として設定します。 この値は、以下のスクリーン ショットに示すように **[ユーザー属性]** タブから変更できます。 マッピングを正しく完了した後にのみ統合は機能します。

    ![image](common/edit-attribute.png)

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[IBM Kenexa Survey Enterprise のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>IBM Kenexa Survey Enterprise のシングル サインオンの構成

**IBM Kenexa Survey Enterprise** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を [IBM Kenexa Survey Enterprise サポート チーム](https://www.ibm.com/support/home/?lnk=fcw)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に IBM Kenexa Survey Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[IBM Kenexa Survey Enterprise]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[IBM Kenexa Survey Enterprise]** を選択します。

    ![アプリケーションの一覧の IBM Kenexa Survey Enterprise リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>IBM Kenexa Survey Enterprise のテスト ユーザーの作成

このセクションでは、IBM Kenexa Survey Enterprise で Britta Simon というユーザーを作成します。

IBM Kenexa Survey Enterprise システムでユーザーを作成し、それに SSO ID をマッピングするには、[IBM Kenexa Survey Enterprise サポート チーム](https://www.ibm.com/support/home/?lnk=fcw)と連携してください。 また、この SSO ID 値を Azure AD のユーザー ID の値にマップする必要があります。 **[属性]** タブでこの既定の設定を変更できます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [IBM Kenexa Survey Enterprise] タイルをクリックすると、SSO を設定した IBM Kenexa Survey Enterprise に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)