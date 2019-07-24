---
title: チュートリアル:Azure Active Directory と Atlassian Cloud の統合 | Microsoft Docs
description: Azure Active Directory と Atlassian Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49d44fa0926afac917ae0ba355d37f13a354f432
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57887936"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>チュートリアル:Azure Active Directory と Atlassian Cloud の統合

このチュートリアルでは、Atlassian Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。
Atlassian Cloud と Azure AD の統合には、次の利点があります。

* Azure AD で、Atlassian Cloud にアクセスできる ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Atlassian Cloud にサインオン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Atlassian Cloud と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Atlassian Cloud でのシングル サインオンが有効なサブスクリプション
* Atlassian Cloud 製品の Security Assertion Markup Language (SAML) シングル サインオンを有効にするには、Atlassian Access を設定する必要があります。 詳細については、「[Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)」を参照してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Atlassian Cloud では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-atlassian-cloud-from-the-gallery"></a>ギャラリーからの Atlassian Cloud の追加

Azure AD への Atlassian Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Atlassian Cloud を追加する必要があります。

**ギャラリーから Atlassian Cloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Atlassian Cloud**」と入力し、結果ウィンドウで **[Atlassian Cloud]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果リスト内の Atlassian Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Atlassian Cloud で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Atlassian Cloud の関連ユーザー間にリンク関係が確立されている必要があります。

Atlassian Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Atlassian Cloud シングル サインオンの構成](#configure-atlassian-cloud-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Atlassian Cloud のテスト ユーザーの作成](#create-atlassian-cloud-test-user)** - Atlassian Cloud で、ユーザーの Azure AD 表現にリンクされた Britta Simon の対応するユーザーを作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Atlassian Cloud との Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Atlassian Cloud** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Atlassian Cloud のドメインと URL] のシングル サインオン情報](common/idp-relay.png)

    a. **[識別子]** ボックスに、`https://auth.atlassian.com/saml/<unique ID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>` のパターンを使用して URL を入力します

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** ボックスに、`https://<instancename>.atlassian.net` のパターンで URL を入力します。

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値は、このチュートリアルで後述する Atlassian Cloud SAML 構成画面から取得できます。

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Atlassian Cloud のドメインと URL] のシングル サインオン情報](common/both-signonurl.png)

    **[サインオン URL]** ボックスに、`https://<instancename>.atlassian.net` という形式で URL を入力します。

    > [!NOTE]
    > 上記のサインオン URL の値は、実際の値ではありません。 実際のサインオン URL で値を更新する必要があります。 この値を取得するには、[クライアント サポート チーム](https://support.atlassian.com/)に問い合わせてください。

6. Atlassian Cloud アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 Atlassian Cloud アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、**[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Atlassian Cloud のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-atlassian-cloud-single-sign-on"></a>Atlassian Cloud のシングル サインオンを構成する

1. ご利用のアプリケーションの SSO を構成するには、管理者の資格情報で Atlassian ポータルにサインインします。

2. シングル サインオンを構成する前に、ドメインを確認する必要があります。 詳細については、[Atlassian の「Domain verification」(ドメインの確認)](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) ドキュメントを参照してください。

3. 左側のウィンドウで、**[SAML single sign-on]\(SAML シングル サインオン\)** を選択します。 まだ Atlassian の Identity Manager に登録していない場合は、登録します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. **[Add SAML configuration]\(SAML 構成の追加\)** ウィンドウで、次の手順を実行します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. **[Identity provider Entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに、Azure Portal でコピーした SAML エンティティ ID を貼り付けます。

    b. **[Identity provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに、Azure Portal でコピーした SAML シングル サインオン サービス URL を貼り付けます。

    c. Azure Portal からダウンロードした証明書を .txt ファイルで開き、*Begin Certificate (証明書の開始)* 行と *End Certificate (証明書の終了)* 行以外の値をコピーして、**[Public X509 certificate]\(公開 X509 証明書\)** ボックスに貼り付けます。

    d. **[Save Configuration]** をクリックします。

5. 確実に正しい URL を設定するには、次の手順を実行して Azure AD 設定を更新します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. SAML ウィンドウで **[SP Identity ID]\(SP 固有 ID\)** の値をコピーし、Azure Portal の **[Atlassian Cloud のドメインと URL]** で、**[識別子]** ボックスに貼り付けます。

    b. SAML ウィンドウで **[SP Assertion Consumer Service URL]\(SP アサーション利用者サービス URL\)** の値をコピーし、Azure Portal の **[Atlassian Cloud のドメインと URL]** で **[応答 URL]** ボックスに貼り付けます。 サインオン URL は、ご利用の Atlassian Cloud のテナント URL です。

    > [!NOTE]
    > 既存のお客様であれば、Azure Portal の **[SP Identity ID]\(SP 固有 ID\)** と **[SP Assertion Consumer Service URL]\(SP アサーション利用者サービス URL\)** の値を更新したあと、**[Yes, update configuration]\(はい、構成を更新します\)** を選択します。 新規のお客様であれば、この手順をスキップしてかまいません。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Atlassian Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Atlassian Cloud]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Atlassian Cloud**」と入力して選択します。

    ![[アプリケーション] リストの [Atlassian Cloud] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-atlassian-cloud-test-user"></a>Atlassian Cloud のテスト ユーザーの作成

Azure AD ユーザーが Atlassian Cloud にサインインできるようにするには、Atlassian Cloud で次の手順を実行して、手動でユーザー アカウントをプロビジョニングします。

1. **[Administration]\(管理\)** ウィンドウで、**[Users]\(ユーザー\)** を選択します。

    ![Atlassian Cloud の [Users]\(ユーザー\) リンク](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Atlassian Cloud でユーザーを作成するには、**[Invite user]\(ユーザーの招待\)** を選択します。

    ![Atlassian Cloud ユーザーの作成](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. **[Email address]\(電子メール アドレス\)** ボックスにユーザーのメール アドレスを入力してから、アプリケーション アクセスを割り当てます。

    ![Atlassian Cloud ユーザーの作成](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. ユーザーに招待メールを送信するには、**[Invite user]\(ユーザーの招待\)** を選択します。 ユーザーに招待メールが送信され、招待が受け入れられると、システム上でそのユーザーがアクティブになります。

> [!NOTE]
> **[Users]\(ユーザー\)** セクションの **[Bulk Create]\(一括作成\)** を選択することで、まとめて複数のユーザーを作成することもできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Atlassian Cloud] タイルをクリックすると、SSO を設定した Atlassian Cloud に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   
