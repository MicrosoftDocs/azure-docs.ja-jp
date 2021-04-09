---
title: チュートリアル:Azure Active Directory と Workspot Control の統合 | Microsoft Docs
description: Azure Active Directory と Workspot Control に対してシングル サインオンを構成する方法について学習します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 65e424c6275f9990a5d5a42a54bc4131300a45bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895037"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>チュートリアル:Azure Active Directory と Workspot Control の統合

このチュートリアルでは、Workspot Control と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Workspot Control を統合すると、次のことができます。

* Azure AD を使用して、Workspot Control にアクセスできるユーザーを制御します。
* ユーザーが Azure AD アカウントを使って Workspot Control に自動的にサインイン (シングル サインオン (SSO)) できるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Workspot Control の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

* Workspot Control のシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

> [!Note]
> Workspot Control では、SP-initiated SSO と IDP-initiated SSO がサポートされます。


## <a name="adding-workspot-control-from-the-gallery"></a>ギャラリーからの Workspot Control の追加

Azure AD への Workspot Control の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workspot Control を追加する必要があります。

**ギャラリーから Workspot Control を追加するには、次の手順のようにします。**

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

3. ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Workspot Control**」と入力し、結果パネルから **Workspot Control** を選択して、 **[追加]** を選択します。

     ![[ギャラリーから追加する] ウィンドウ](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon というテスト ユーザーについて Workspot Control で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Workspot Control 内の関連ユーザーとの間にリンク関係を確立する必要があります。

Workspot Control で Azure AD のシングル サインオンを構成してテストするには、次のタスクを完了する必要があります。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Workspot Control シングル サインオンの構成](#configure-workspot-control-single-sign-on) - アプリケーション側でシングル サインオン設定を構成します。
3. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon について Azure AD のシングル サインオンをテストします。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [Workspot Control テスト ユーザーの作成](#create-a-workspot-control-test-user) - Workspot Control で Britta Simon に対応するユーザーを設定し、Azure AD の Britta Simon にリンクさせます。
6. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Workspot Control で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Workspot Control** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択ウィンドウ](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** (鉛筆) アイコンを選択して、 **[基本的な SAML 構成]** にアクセスします。

    ![[基本的な SAML 構成] で強調表示された編集アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを IDP-initiated モードで構成する場合は、次の手順のようにします。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    1. **[識別子]** テキスト ボックスに、次のパターンで URL を入力します。<br/>
    ***https://<<i></i>インスタンス名>-saml.workspot.com/saml/metadata***

    1. **[応答 URL]** テキスト ボックスに、次のパターンで URL を入力します。<br/>
    ***https://<<i></i>インスタンス名>-saml.workspot.com/saml/assertion***

5. アプリケーションを SP-initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** テキスト ボックスに、次のパターンで URL を入力します。<br/>
    ***https://<<i></i>インスタンス名>-saml.workspot.com/***

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を置き換えます。 これらの値を取得するには、[Workspot Control クライアント サポート チーム](mailto:support@workspot.com)に問い合わせてください。 または、Azure portal の **[基本的な SAML 構成]** セクションのパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** を選択し、要件に従って使用可能なオプションから **証明書 (Base64)** をダウンロードします。 それを自分のコンピューターに保存します。

    ![証明書 (Base64) ダウンロード リンク](common/certificatebase64.png)

7. **[Workspot Control の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    - **ログイン URL**

    - **Azure AD 識別子**

    - **ログアウト URL**

### <a name="configure-workspot-control-single-sign-on"></a>Workspot Control のシングル サインオンの構成

1. 異なる Web ブラウザー ウィンドウで、セキュリティ管理者として Workspot Control にサインインします。

2. ページの上部にあるツール バーで **[Setup]\(セットアップ\)** を選択し、次に **[SAML]** を選択します。

    ![セットアップ オプション](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. **[Security Assertion Markup Language Configuration]\(Security Assertion Markup Language の構成\)** ウィンドウで、次の手順のようにします。
 
    ![[Security Assertion Markup Language Configuration]\(Security Assertion Markup Language の構成\) ウィンドウ](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** を貼り付けます。

    1. **[Signon Service URL]\(サインオン サービス URL\)** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。

    1. **[Logout Service URL]\(ログアウト サービス URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** を貼り付けます。

    1. **[Update File]\(ファイルの更新\)** を選択して、Azure portal からダウンロードした base 64 でエンコードされた証明書を X.509 証明書にアップロードします。

    1. **[保存]** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal でテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで **[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. ウィンドウの上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. ユーザーのプロパティで、次の手順のようにします。

    ![ユーザーのプロパティ ウィンドウ](common/user-properties.png)

    1. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** フィールドに、**brittasimon@* yourcompanydomain.extension*** と入力します。 たとえば、「 **BrittaSimon@contoso.<i></i>com**」と入力します。

    1. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。

    1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Workspot Control へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で、**[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]**、**[Workspot Control]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Workspot Control]** を選択します。

    ![アプリケーションの一覧の Workspot Control リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** ボタンを選択します。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ウィンドウで、**[ユーザー]** の一覧から **Britta Simon** を選択します。 **[選択]** をクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ウィンドウで、一覧からユーザーに適したロールを選択します。 次に、下部にある **[選択]** をクリックします。

7. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-a-workspot-control-test-user"></a>Workspot Control のテスト ユーザーを作成する

Azure AD ユーザーが Workspot Control にサインインできるようにするには、ユーザーを Workspot Control にプロビジョニングする必要があります。 プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順のようにします。**

1. セキュリティ管理者として Workspot Control にサインインします。

2. ページの上部にあるツール バーで **[Users]\(ユーザー\)** を選択し、次に **[Add User]\(ユーザーの追加\)** を選択します。

    ![[Users]\(ユーザー\) オプション](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. **[Add a New User]\(新しいユーザーの追加\)** ウィンドウで、次の手順のようにします。

    ![[Add a New User]\(新しいユーザーの追加\) ウィンドウ](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    1. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    1. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: **Brittasimon@contoso.<i></i>com**)。

    1. **[Role]\(ロール\)** ドロップダウン リストから適切なユーザー ロールを選択します。

    1. **[Group]\(グループ\)** ドロップダウン リストから適切なユーザー グループを選択します。

    1. **[Add User]\(ユーザーの追加\)** を選択します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、"*アクセス パネル*" を使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで **[Workspot Control]** タイルをクリックすると、SSO を設定した Workspot Control に自動的にサインインします。 詳細については、「[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)