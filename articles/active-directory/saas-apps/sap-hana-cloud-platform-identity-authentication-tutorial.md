---
title: チュートリアル:Azure Active Directory と SAP Cloud Platform Identity Authentication の統合 | Microsoft Docs
description: Azure Active Directory と SAP Cloud Platform Identity Authentication の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eff07f157f6011b9b561334c00b3a4d94989ff5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091854"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>チュートリアル:Azure Active Directory と SAP Cloud Platform Identity Authentication の統合

このチュートリアルでは、SAP Cloud Platform Identity Authentication と Azure Active Directory (Azure AD) を統合する方法について説明します。
SAP Cloud Platform Identity Authentication を Azure AD と統合すると、次の利点があります。

* SAP Cloud Platform Identity Authentication にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SAP Cloud Platform Identity Authentication に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

SAP Cloud Platform Identity Authentication と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAP Cloud Platform Identity Authentication でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAP Cloud Platform Identity Authentication では、**SP** および **IDP** によって開始される SSO がサポートされます

技術的な詳細の説明に入る前に、調べようとしている事柄の概念を理解する必要があります。 SAP Cloud Platform Identity Authentication と Active Directory フェデレーション サービスにより、SAP Cloud Platform Identity Authentication で保護された SAP アプリケーションおよびサービスに対し、(IdP としての) Azure AD によって保護されたアプリケーションまたはサービス全体で SSO を実装できます。

現時点では、SAP Cloud Platform Identity Authentication は、SAP アプリケーションへのプロキシ ID プロバイダーとして機能します。 さらに Azure Active Directory は、このセットアップにおける主要な ID プロバイダーとして機能します。 

次の図にこの関係を示します。

![Azure AD のテスト ユーザーの作成](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

このセットアップにより、SAP Cloud Platform Identity Authentication テナントは、Azure Active Directory で信頼されたアプリケーションとして構成されます。

この方法で保護するすべての SAP アプリケーションおよびサービスは、その後で SAP Cloud Platform Identity Authentication 管理コンソールで構成されます。

つまり、SAP アプリケーションおよびサービスへのアクセスの許可のための承認は、(Azure Active Directory ではなく) SAP Cloud Platform Identity Authentication で行われる必要があります。

Azure Active Directory Marketplace を通じて SAP Cloud Platform Identity Authentication をアプリケーションとして構成すれば、要求/SAML アサーションを個別に構成する必要がありません。

> [!NOTE]
> 現在、その両者で Web SSO のみがテストされています。 アプリ対API または API 対 API の通信に必要なフローは機能するものの、まだテストされていません。 そのテストは後のアクティビティ中に行われる予定です。

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>ギャラリーからの SAP Cloud Platform Identity Authentication の追加

Azure AD への SAP Cloud Platform Identity Authentication の統合を構成するには、SAP Cloud Platform Identity Authentication をギャラリーから一連のマネージド SaaS アプリに追加する必要があります。

**ギャラリーから SAP Cloud Platform Identity Authentication を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SAP Cloud Platform Identity Authentication**」と入力して、結果パネルで **[SAP Cloud Platform Identity Authentication]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの SAP Cloud Platform Identity Authentication](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと [アプリケーション名] 内の関連ユーザー間にリンク関係が確立されている必要があります。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SAP Cloud Platform Identity Authentication シングル サインオンの構成](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SAP Cloud Platform Identity Authentication テスト ユーザーの作成](#create-sap-cloud-platform-identity-authentication-test-user)** - SAP Cloud Platform Identity Authentication で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **SAP Cloud Platform Identity Authentication** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[SAP Cloud Platform Identity Authentication] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`<IAS-tenant-id>.accounts.ondemand.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SAP Cloud Platform Identity Authentication クライアント サポート チーム](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)にお問い合わせください。 識別子の値がわからない場合は、[テナントの SAML 2.0 の構成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)に関する SAP Cloud Platform Identity Authentication のドキュメントをお読みください。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[SAP Cloud Platform Identity Authentication] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`{YOUR BUSINESS APPLICATION URL}` という形式で URL を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 特定のビジネス アプリケーションのサインオン URL を使ってください。 わからないことがある場合は、[SAP Cloud Platform Identity Authentication クライアント サポート チーム](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)にお問い合わせください。

6. SAP Cloud Platform Identity Authentication アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. SAP アプリケーションで **firstName** などの属性が期待されている場合は、 **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションに **firstName** 属性を追加し、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、属性名 firstName を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** リストから、属性値 user.givenname を選択します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

8. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)**  ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションから**メタデータ XML**をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

9. **[SAP Cloud Platform Identity Authentication のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>SAP Cloud Platform Identity Authentication のシングル サインオンの構成

1. アプリケーション用に構成された SSO を入手するために、SAP Cloud Platform Identity Authentication 管理コンソールにアクセスします。 URL は `https://<tenant-id>.accounts.ondemand.com/admin` というパターンです。 次に、「[Integration with Microsoft Azure AD (Microsoft Azure AD との統合)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)」で SAP Cloud Platform Identity Authentication に関するドキュメントを読みます。

2. Azure Portal で **[保存]** ボタンを選択します。

3. 以降は、もう 1 つの SAP アプリケーションに対して SSO を追加して有効にする場合にのみ行います。 「**ギャラリーからの SAP Cloud Platform Identity Authentication の追加**」セクションの手順を繰り返してください。

4. Azure Portal の **SAP Cloud Platform Identity Authentication** アプリケーション統合ページで、 **[リンクされたサインオン]** を選択します。

    ![リンクされたサインオンの構成](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. 構成を保存します。

> [!NOTE]
> 新しいアプリケーションでは、前の SAP アプリケーションのシングル サインオン構成が再利用されます。 SAP Cloud Platform Identity Authentication 管理コンソールで、同じ会社の ID プロバイダーを使用していることを確認してください。

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

このセクションでは、Britta Simon に SAP Cloud Platform Identity Authentication へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[SAP Cloud Platform Identity Authentication]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[SAP Cloud Platform Identity Authentication]** を選択します。

    ![アプリケーションの一覧の [SAP Cloud Platform Identity Authentication] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>SAP Cloud Platform Identity Authentication のテスト ユーザーの作成

SAP Cloud Platform Identity Authentication でユーザーを作成する必要はありません。 Azure AD ユーザー ストアに存在するユーザーは、SSO 機能を使用できます。

SAP Cloud Platform Identity Authentication は、ID フェデレーション オプションをサポートしています。 このオプションにより、アプリケーションは、企業の ID プロバイダーによって認証されたユーザーが SAP Cloud Platform Identity Authentication のユーザー ストアに存在するかどうかを確認できます。

既定では、ID フェデレーション オプションは無効になっています。 ID フェデレーションが有効になっていると、SAP Cloud Platform Identity Authentication にインポートされているユーザーのみがアプリケーションにアクセスできます。

SAP Cloud Platform Identity Authentication との ID フェデレーションを有効または無効にする方法の詳細については、[SAP Cloud Platform Identity Authentication のユーザー ストアとの ID フェデレーションの構成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)に関するページで、SAP Cloud Platform Identity Authentication との ID フェデレーションの有効化に関するセクションを参照してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [SAP Cloud Platform Identity Authentication] タイルをクリックすると、SSO を設定した SAP Cloud Platform Identity Authentication に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
