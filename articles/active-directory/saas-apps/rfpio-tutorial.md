---
title: 'チュートリアル: Azure Active Directory と RFPIO の統合 | Microsoft Docs'
description: Azure Active Directory と RFPIO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: c4e838afa867a7fb1e7fa8f582bc8879c24056a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92506090"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>チュートリアル: Azure Active Directory と RFPIO の統合

このチュートリアルでは、RFPIO と Azure Active Directory (Azure AD) を統合する方法について説明します。
RFPIO と Azure AD の統合には、次の利点があります。

* RFPIO にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して RFPIO に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

RFPIO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* RFPIO でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* RFPIO では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-rfpio-from-the-gallery"></a>ギャラリーからの RFPIO の追加

Azure AD への RFPIO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RFPIO を追加する必要があります。

**ギャラリーから RFPIO を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**RFPIO**」と入力し、結果パネルで **[RFPIO]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の RFPIO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、RFPIO で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと RFPIO 内の関連ユーザー間にリンク関係が確立されている必要があります。

RFPIO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[RFPIO シングル サインオンの構成](#configure-rfpio-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[RFPIO のテスト ユーザーの作成](#create-rfpio-test-user)** - RFPIO で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

RFPIO で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **RFPIO** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[基本的な SAML 構成] を示すスクリーンショット。ここに ID を入力することができます。](common/idp-identifier.png)

    a. **[識別子]** ボックスに、`https://www.rfpio.com` の形式で URL を入力します。

    b. **[追加の URL を設定します]** をクリックします。

    c. **[リレー状態]** テキストボックスに文字列値を入力します。 この値を取得するには、[RFPIO サポート チーム](https://www.rfpio.com/contact/)に問い合わせてください。

    ![[追加の U R L を設定します] 画面のスクリーンショット。](common/idp-preintegrated-relay.png)

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![image](common/both-preintegrated-signon.png)

    **[サインオン URL]** ボックスに、`https://www.app.rfpio.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 この値を取得するには、[RFPIO クライアント サポート チーム](https://www.rfpio.com/contact/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[RFPIO のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-rfpio-single-sign-on"></a>RFPIO シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、**RFPIO** Web サイトに管理者としてサインインします。

1. 左下隅にあるドロップダウンをクリックします。

    ![ペイン下部の下矢印を示すスクリーンショット。](./media/rfpio-tutorial/app1.png)

1. **[組織設定]** をクリックします。 

    ![[組織設定] が選択された画面のスクリーンショット。](./media/rfpio-tutorial/app2.png)

1. **[FEATURES & INTEGRATION (機能と統合)]** をクリックします。

    ![[Settings]\(設定\) の [Features and Integration]\(機能と統合\) が選択されている画面のスクリーンショット。](./media/rfpio-tutorial/app4.png)

1. **[SAML SSO 構成]** で **[編集]** をクリックします。

    ![[SAML S S O 構成] と [編集] ボタンが強調表示されている画面のスクリーンショット。](./media/rfpio-tutorial/app3.png)

1. このセクションでは、次のアクション実行します。

    ![SAML が有効になった SAML S S O 構成を示す画面の SAML S S O 構成。](./media/rfpio-tutorial/app5.png)
    
    a. **ダウンロードしたメタデータ XML** の内容をコピーし、 **[ID 構成]** フィールドに貼り付けます。

    > [!NOTE]
    >ダウンロードした **フェデレーション メタデータ XML** の内容をコピーするには、**Notepad++** または適切な **XML エディター** を使用します。

    b. **[検証]** をクリックします。

    c. **[検証]** をクリックした後、 **[SAML(Enabled)(SAML (有効))]** をフリップしてオンにします。

    d. **[送信]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に RFPIO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[RFPIO]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[RFPIO]** を選択します。

    ![アプリケーションの一覧の RFPIO のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-rfpio-test-user"></a>RFPIO のテスト ユーザーの作成

1. RFPIO 企業サイトに管理者としてサインインします。

1. 左下隅にあるドロップダウンをクリックします。

    ![ペイン下部の下矢印を示すスクリーンショット。](./media/rfpio-tutorial/app1.png)

1. **[組織設定]** をクリックします。 

    ![[組織設定] が選択された画面のスクリーンショット。](./media/rfpio-tutorial/app2.png)

1. **[チーム メンバー]** をクリックします。

    ![[設定] の [チーム メンバー] が選択されている画面のスクリーンショット。](./media/rfpio-tutorial/app6.png)

1. **[メンバーの追加]** をクリックします。

    ![[メンバーの追加] ボタンを示す画面のスクリーンショット。](./media/rfpio-tutorial/app7.png)

1. **[新しいメンバーの追加]** セクションで、 次の操作を実行します。

    ![[新しいメンバーの追加] を示すスクリーンショット。ここで、説明されている値を入力できます。](./media/rfpio-tutorial/app8.png)

    a. **[Enter one email per line(1 行につき 1 つの電子メール アドレスを入力する)]** フィールドに **電子メール アドレス** を入力します。

    b. 要件に応じて **[ロール]** を選択してください。

    c. **[メンバーの追加]** をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [RFPIO] タイルをクリックすると、SSO を設定した RFPIO に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)