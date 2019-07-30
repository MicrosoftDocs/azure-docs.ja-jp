---
title: チュートリアル:Azure Active Directory と Adaptive Insights の統合 | Microsoft Docs
description: Azure Active Directory と Adaptive Insights の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: c217663c5752907e0b3d6372d4522f6aba982b3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107401"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>チュートリアル:Azure Active Directory と Adaptive Insights の統合

このチュートリアルでは、Adaptive Insights と Azure Active Directory (Azure AD) を統合する方法について説明します。
Adaptive Insights と Azure AD の統合には、次の利点があります。

* Adaptive Insights にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Adaptive Insights に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Adaptive Insights と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Adaptive Insights でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Adaptive Insights では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-adaptive-insights-from-the-gallery"></a>ギャラリーからの Adaptive Insights の追加

Azure AD への Adaptive Insights の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Adaptive Insights を追加する必要があります。

**ギャラリーから Adaptive Insights を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Adaptive Insights**」と入力し、結果パネルで **Adaptive Insights** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Adaptive Insights](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Adaptive Insights で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Adaptive Insights 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Adaptive Insights で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Adaptive Insights のシングル サインオンの構成](#configure-adaptive-insights-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Adaptive Insights のテスト ユーザーの作成](#create-adaptive-insights-test-user)** - Adaptive Insights で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Adaptive Insights で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Adaptive Insights** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[Adaptive Insights のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://login.adaptiveinsights.com:443/samlsso/<unique-id>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://login.adaptiveinsights.com:443/samlsso/<unique-id>` のパターンを使用して URL を入力します

    > [!NOTE]
    > [識別子 (エンティティ ID)] と [応答 URL] の値は、Adaptive Insights の **[SAML SSO 設定]** ページから取得できます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Set up Adaptive Insights]\(Adaptive Insights の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-adaptive-insights-single-sign-on"></a>Adaptive Insights のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Adaptive Insights 企業サイトに管理者としてサインインします。

2. **[Admin]** に移動します。

    ![管理](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. **[ユーザーとロール]** セクションで、 **[SAML SSO 設定の管理]** をクリックします。

    ![SAML SSO 設定の管理](./media/adaptivesuite-tutorial/ic805645.png "Manage SAML SSO Settings")

4. **[SAML SSO 設定]** ページで、次の手順を実行します。

    ![SAML SSO 設定](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO Settings")

    a. **[ID プロバイダー名]** テキスト ボックスに、構成の名前を入力します。

    b. Azure portal からコピーした **Azure AD 識別子**の値を **[Identity provider entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに貼り付けます。

    c. Azure portal からコピーした**ログイン URL** の値を **[Identity provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに貼り付けます。

    d. Azure portal からコピーした**ログアウト URL** の値を **[Custom logout URL]\(カスタム ログアウト URL\)** ボックスに貼り付けます。

    e. ダウンロードした証明書をアップロードするには、 **[ファイルの選択]** をクリックします。

    f. 次のように選択します。

     * **[SAML ユーザー ID]** では、 **[ユーザーの Adaptive Insights ユーザー名]** を選択します。

     * **[SAML ユーザー ID の場所]** では、 **[サブジェクトの NameID 内のユーザー ID ]** を選択します。

     * **[SAML NameID 形式]** では、 **[電子メール アドレス]** を選択します。

     * **[SAML を有効にする]** では、 **[SAML SSO を許可して、Adaptive Insights に直接ログインする]** を選択します。

    g. **Adaptive Insights の SSO URL** をコピーし、Azure portal の **[Adaptive Insights のドメインと URL]** セクションの **[識別子 (エンティティ ID)]** ボックスと **[応答 URL]** ボックスに貼り付けます。

    h. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adaptive Insights へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Adaptive Insights]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Adaptive Insights]** を選択します。

    ![アプリケーションの一覧の Adaptive Insights のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-adaptive-insights-test-user"></a>Adaptive Insights のテスト ユーザーの作成

Azure AD ユーザーが Adaptive Insights にサインインできるようにするには、ユーザーを Adaptive Insights にプロビジョニングする必要があります。 Adaptive Insights の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Adaptive Insights** 企業サイトに管理者としてサインインします。

2. **[Admin]** に移動します。

   ![管理者](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. **[ユーザーとロール]** セクションで **[ユーザーの追加]** をクリックします。

   ![ユーザーの追加](./media/adaptivesuite-tutorial/IC805648.png "Add User")

4. **[新しいユーザー]** セクションで、次の手順に従います。

   ![送信](./media/adaptivesuite-tutorial/IC805649.png "Submit")

   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの **[名前]** 、 **[ログイン]** 、 **[電子メール]** 、 **[パスワード]** を入力します。

   b. **[ロール]** を選択します。

   c. **[送信]** をクリックします。

> [!NOTE]
> 他の Adaptive Insights ユーザー アカウント作成ツールまたは Adaptive Insights から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Adaptive Insights] タイルをクリックすると、SSO を設定した Adaptive Insights に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)