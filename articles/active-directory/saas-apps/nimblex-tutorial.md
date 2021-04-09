---
title: 'チュートリアル: Azure Active Directory と Nimblex の統合 | Microsoft Docs'
description: Azure Active Directory と Nimblex の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: jeedes
ms.openlocfilehash: 528411f7ef662e90bce98a52b67caa9123fad6ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522464"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>チュートリアル: Azure Active Directory と Nimblex の統合

このチュートリアルでは、Nimblex と Azure Active Directory (Azure AD) を統合する方法について説明します。
Nimblex と Azure AD の統合には、次の利点があります。

* Nimblex にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Nimblex に自動的にサインイン (シングル サインオン) されるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Nimblex と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Nimblex でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Nimblex では、**SP** によって開始される SSO がサポートされます

* Nimblex では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-nimblex-from-the-gallery"></a>ギャラリーからの Nimblex の追加

Azure AD への Nimblex の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Nimblex を追加する必要があります。

**ギャラリーから Nimblex を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Nimblex**」と入力し、結果パネルで **[Nimblex]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Nimblex](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Nimblex で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Nimblex 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Nimblex で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Nimblex のシングル サインオンの構成](#configure-nimblex-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Nimblex のテスト ユーザーの作成](#create-nimblex-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Nimblex で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Nimblex で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Nimblex** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Nimblex のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<YOUR APPLICATION PATH>/Login.aspx` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<YOUR APPLICATION PATH>/` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<path-to-application>/SamlReply.aspx` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 この値を取得するには、[Nimblex クライアント サポート チーム](mailto:support@ebms.com.au)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** をクリックして、要件に適した特定のオプションの **証明書 (Base64)** をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Nimblex のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-nimblex-single-sign-on"></a>Nimblex のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Nimblex にセキュリティ管理者としてサインインします。

2. ページの右上の **設定** ロゴをクリックします。

    ![スクリーンショットは、設定アイコンを示しています。](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

3. **コントロール パネル** ページの **[セキュリティ]** の下で、 **[シングル サインオン]** をクリックします。

    ![スクリーンショットは、[Security]\(セキュリティ\) メニューの [Single Sign-On]\(シングル サインオン\) が選択されていることを示しています。](./media/nimblex-tutorial/tutorial_nimblex_single.png)

4. **[シングル サインオンの管理]** ページで、インスタンス名を選択して **[編集]** をクリックします。

    ![スクリーンショットは、[Single Sign-On]\(シングル サインオン\) を示しています。ここでは、[Edit]\(編集\) を選択できます。](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

5. **[SSO プロバイダーの編集]** ページで、次の手順を行います。

    ![スクリーンショットは、説明した値を入力できる [Edit SSO Provider]\(SSO プロバイダーの編集\) を示しています。](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. **[説明]** テキストボックスに、インスタンス名を入力します。

    b. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[証明書]** ボックスに貼り付けます。

    c. **[Identity Provider Sso Target Url]\(ID プロバイダーの SSO ターゲット URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「 **brittasimon@yourcompanydomain.extension** 」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Nimblex へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Nimblex]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Nimblex]** を選択します。

    ![アプリケーションの一覧の Nimblex のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-nimblex-test-user"></a>Nimblex のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Nimblex に作成します。 Nimblex では、Just-In-Time ユーザー プロビジョニングがサポートされます。この設定は既定で有効です。 このセクションでは、ユーザー側で必要な操作はありません。 Nimblex にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Nimblex クライアント サポート チーム](mailto:support@ebms.com.au)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Nimblex] タイルをクリックすると、SSO を設定した Nimblex に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)