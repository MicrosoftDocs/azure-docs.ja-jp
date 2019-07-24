---
title: チュートリアル:Azure Active Directory と Proxyclick の統合 | Microsoft Docs
description: Azure Active Directory と Proxyclick の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b797c7d49495aac90abb31a9214bf2928784a866
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262399"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>チュートリアル:Azure Active Directory と Proxyclick の統合

このチュートリアルでは、Proxyclick と Azure Active Directory (Azure AD) を統合する方法について説明します。
Proxyclick と Azure AD の統合には、次の利点があります。

* Proxyclick にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Proxyclick にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Proxyclick と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Proxyclick でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Proxyclick では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-proxyclick-from-the-gallery"></a>ギャラリーからの Proxyclick の追加

Azure AD への Proxyclick の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Proxyclick を追加する必要があります。

**ギャラリーから Proxyclick を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Proxyclick**」と入力し、結果パネルで **Proxyclick** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Proxyclick](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Proxyclick で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Proxyclick 内の関連ユーザー間にリンク関係が確立されている必要があります。

Proxyclick で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Proxyclick シングル サインオンの構成](#configure-proxyclick-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Proxyclick テスト ユーザーの作成](#create-proxyclick-test-user)** - Proxyclick で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Proxyclick で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Proxyclick** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Proxyclick のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://saml.proxyclick.com/init/<companyId>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://saml.proxyclick.com/consume/<companyId>` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Proxyclick のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://saml.proxyclick.com/init/<companyId>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL に値を置き換えます。実際の値については後で説明します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Proxyclick の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-proxyclick-single-sign-on"></a>Proxyclick のシングル サインオンを構成する

1. 別の Web ブラウザー ウィンドウで、Proxyclick 企業サイトに管理者としてサインインします。

2. **[Account & Settings]\(アカウントと設定\)** を選択します。

    ![Proxyclick 構成](./media/proxyclick-tutorial/configure1.png)

3. **[INTEGRATIONS]\(統合\)** まで下へスクロールし、**[SAML]** を選択します。

    ![Proxyclick 構成](./media/proxyclick-tutorial/configure2.png)

4. **[SAML]** セクションで、次の手順に従います。

    ![Proxyclick 構成](./media/proxyclick-tutorial/configure3.png)

    a. **[SAML Consumer URL]\(SAML コンシューマー URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    b. **[SAML SSO Redirect URL]\(SAML SSO リダイレクト URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスと **[識別子]** ボックスに貼り付けます。

    c. **[SAML Request Method]\(SAML 要求メソッド\)** で **[HTTP Redirect]\(HTTP リダイレクト\)** を選択します。

    d. **[Issuer]\(発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    e. **[SAML 2.0 Endpoint URL]\(SAML 2.0 エンドポイント URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    f. Azure portal からダウンロードした証明書ファイルをメモ帳で開き、**[Certificate]\(証明書\)** ボックスに貼り付けます。

    g. **[変更を保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Proxyclick へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Proxyclick]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Proxyclick]** を選択します。

    ![アプリケーションの一覧の Proxyclick のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-proxyclick-test-user"></a>Proxyclick テスト ユーザーの作成

Azure AD ユーザーが Proxyclick にサインインできるようにするには、ユーザーを Proxyclick にプロビジョニングする必要があります。 Proxyclick の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Proxyclick 企業サイトに管理者としてサインインします。

1. 上部のナビゲーション バーから **[Colleagues]\(仕事仲間\)** をクリックします。

    ![従業員の追加](./media/proxyclick-tutorial/user1.png)

1. **[Add Colleague]\(仕事仲間の追加\)** をクリックします

    ![従業員の追加](./media/proxyclick-tutorial/user2.png)

1. **[Add a colleague]\(仕事仲間の追加\)** セクションで、次の手順に従います。

    ![従業員の追加](./media/proxyclick-tutorial/user3.png)

    a. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (brittasimon@contoso.com など) を入力します。

    b. **[名]** ボックスに、ユーザーの名を入力します (この例では Britta)。

    c. **[姓]** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    d. **[ユーザーの追加]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Proxyclick] タイルをクリックすると、SSO を設定した Proxyclick に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

