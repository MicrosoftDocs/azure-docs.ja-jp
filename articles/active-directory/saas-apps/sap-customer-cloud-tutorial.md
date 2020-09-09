---
title: チュートリアル:SAP Cloud for Customer と Azure Active Directory のシングル サインオン (SSO) 統合 | Microsoft Docs
description: Azure Active Directory と SAP Cloud for Customer の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.openlocfilehash: f9fd458ea19fa0dad2f630f94a67d5e1db96cee3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543314"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>チュートリアル:SAP Cloud for Customer と Azure Active Directory のシングル サインオン (SSO) 統合

このチュートリアルでは、SAP Cloud for Customer と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Cloud for Customer と Azure AD を統合すると、次のことができます。

* SAP Cloud for Customer にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して SAP Cloud for Customer に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SAP Cloud for Customer でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SAP Cloud for Customer では、**SP** によって開始される SSO がサポートされます

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>ギャラリーから SAP Cloud for Customer を追加する

Azure AD への SAP Cloud for Customer の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Cloud for Customer を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SAP Cloud for Customer**」と入力します。
1. 結果のパネルから **[SAP Cloud for Customer]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>SAP Cloud for Customer の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP Cloud for Customer に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SAP Cloud for Customer の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Cloud for Customer に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SAP Cloud for Customer SSO の構成](#configure-sap-cloud-for-customer-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAP Cloud for Customer テスト ユーザーの作成](#create-sap-cloud-for-customer-test-user)** - SAP Cloud for Customer で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SAP Cloud for Customer** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<server name>.crm.ondemand.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[SAP Cloud for Customer クライアント サポート チーム](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. SAP Cloud for Customer アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

1. **[ユーザー属性とクレーム]** ダイアログの **[ユーザー属性]** セクションで、次の手順を実行します。

    a. **[編集]** アイコンをクリックして、 **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. **ソース**として **[変換]** を選択します。

    c. **[変換]** の一覧で、**ExtractMailPrefix()** を選択します。

    d. **[パラメーター 1]** 一覧から、実装で使用するユーザー属性を選択します。
    たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、[user.extensionattribute2] を選択します。

    e. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[SAP Cloud for Customer のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に SAP Cloud for Customer へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAP Cloud for Customer]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sap-cloud-for-customer-sso"></a>SAP Cloud for Customer SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、SAP Cloud for Customer 企業サイトに管理者としてサインインします。

2. メニューの左側で **[Identity Providers]\(ID プロバイダー\)**  >  **[Corporate Identity Providers]\(企業 ID プロバイダー\)**  >  **[Add]\(追加\)** の順にクリックし、ポップアップで ID プロバイダー名 (**Azure AD** など) を追加します。 **[Save]\(保存\)** をクリックしてから、 **[SAML 2.0 Configuration]\(SAML 2.0 構成\)** をクリックします。

    ![SAP 構成](./media/sap-customer-cloud-tutorial/configure01.png)

3. **[SAML 2.0 Configuration]\(SAML 2.0 の構成\)** セクションで、次の手順を実行します。

    ![SAP 構成](./media/sap-customer-cloud-tutorial/configure02.png)

    a. **[Browse]\(参照\)** をクリックし、Azure portal からダウンロードしたフェデレーション メタデータ XML ファイルをアップロードします。

    b. XML ファイルが正常にアップロードされると、以下の値が自動的に設定されるので、 **[Save]\(保存\)** をクリックします。

### <a name="create-sap-cloud-for-customer-test-user"></a>SAP Cloud for Customer のテスト ユーザーの作成

Azure AD ユーザーが SAP Cloud for Customer にサインインできるようにするには、そのユーザーを SAP Cloud for Customer にプロビジョニングする必要があります。 SAP Cloud for Customer では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として SAP Cloud for Customer にサインインします。

2. メニューの左側で **[Users & Authorizations]\(ユーザーと承認\)**  > **[User Management]\(ユーザー管理\)**  >  **[Add User]\(ユーザーの追加\)** の順にクリックします。

    ![SAP 構成](./media/sap-customer-cloud-tutorial/configure03.png)

3. **[Add New User]\(新しいユーザーの追加\)** セクションで、次の手順を実行します。

    ![SAP 構成](./media/sap-customer-cloud-tutorial/configure04.png)

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **B**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    c. **[E-Mail]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: `B.Simon@contoso.com`)。

    d. **[Login Name]\(ログイン名\)** ボックスに、ユーザーの名前を入力します (例: **B.Simon**)。

    e. 要件に従って **[User Type]\(ユーザーの種類\)** を選択します。

    f. 要件に従って **[Account Activation]\(アカウントのアクティブ化\)** を選択します。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SAP Cloud for Customer] タイルをクリックすると、SSO を設定した SAP Cloud for Customer に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で SAP Cloud for Customer を試す](https://aad.portal.azure.com/)

