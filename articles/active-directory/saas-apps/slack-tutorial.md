---
title: チュートリアル:Azure Active Directory と Slack の統合 | Microsoft Docs
description: Azure Active Directory とSlack の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90950f2725c821729bf38cfdfbfd1782e5ffc09c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090403"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>チュートリアル:Azure Active Directory と Slack の統合

このチュートリアルでは、Slack と Azure Active Directory (Azure AD) を統合する方法について説明します。
Slack と Azure AD の統合には、次の利点があります。

* Slack にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Slack に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Slack と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Slack でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Slack では、**SP** によって開始される SSO がサポートされます
* Slack では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* Slack では、[**自動化された**ユーザー プロビジョニング](slack-provisioning-tutorial.md)がサポートされます

## <a name="adding-slack-from-the-gallery"></a>ギャラリーからの Slack の追加

Azure AD への Slack の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Slack を追加する必要があります。

**ギャラリーから Slack を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Slack**」と入力し、結果パネルで **[Slack]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Slack](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Slack で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Slack 内の関連ユーザー間にリンク関係が確立されている必要があります。

Slack で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Slack シングル サインオンの構成](#configure-slack-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Slack のテスト ユーザーの作成](#create-slack-test-user)** - Slack で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Slack で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Slack** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Slack のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.slack.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、`https://slack.com` という URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL で値を更新する必要があります。 値を取得するには、[Slack クライアント サポート チーム](https://slack.com/help/contact)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. Slack アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

    > [!NOTE] 
    > 割り当てられた**メール アドレス**が Office 365 ライセンスのものではないユーザーがいる場合、**User.Email** 要求は SAML トークンに表示されません。 その場合は、**user.userprincipalname** を **User.Email** 属性の値として使って、代わりに**一意識別子**としてマップすることをお勧めします。

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name | ソース属性 |
    | --- | --- |
    | first_name | User.givenname |
    | last_name | User.surname |
    | User.Email | User.mail |
    | User.Username | user.userprincipalname |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Slack の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-slack-single-sign-on"></a>Slack のシングル サインオンを構成する

1. 別の Web ブラウザー ウィンドウで、Slack 企業サイトに管理者としてログインします。

2. **[Microsoft Azure AD]** 、 **[Team Settings]** の順に選択します。

     ![アプリ側でのシングル サインオンの構成](./media/slack-tutorial/tutorial_slack_001.png)

3. **[Team Settings]** セクションで、 **[Authentication]** タブをクリックし、 **[Change Settings]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/slack-tutorial/tutorial_slack_002.png)

4. **[SAML Authentication Settings]** ダイアログで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/slack-tutorial/tutorial_slack_003.png)

    a.  **[SAML 2.0 Endpoint (HTTP)]\(SAML 2.0 エンドポイント (HTTP)\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b.  **[Identity Provider Issuer]\(ID プロバイダー発行者\)** テキスト ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c.  ダウンロードした証明書をメモ帳で開き、その内容をクリップボードにコピーし、 **[Public Certificate]** ボックスに貼り付けます。

    d. 必要に応じて、Slack チームに上記の 3 つの設定を構成します。 設定の詳細については、**Slack の SSO 構成ガイド**をこちらから参照してください。 `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  **[Save Configuration]** をクリックします。

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

このセクションでは、Slack へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Slack]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Slack]** を選択します。

    ![アプリケーションの一覧の [Slack] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-slack-test-user"></a>Slack のテスト ユーザーの作成

このセクションの目的は、Slack で Britta Simon というユーザーを作成することです。 Slack では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Slack ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 Slack はまた、 自動ユーザー プロビジョニングをサポートしています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](slack-provisioning-tutorial.md)をご覧ください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Slack のサポート チーム](https://slack.com/help/contact)にお問い合わせください。

> [!NOTE]
> Azure AD Connect はオンプレミスの Active Directory の ID を Azure AD と同期できる同期ツールであり、これらの同期済みユーザーもアプリケーションを他のクラウド ユーザーと同じように使用できます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Slack] タイルをクリックすると、SSO を設定した Slack に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [[ユーザー プロビジョニングの構成]](slack-provisioning-tutorial.md)
