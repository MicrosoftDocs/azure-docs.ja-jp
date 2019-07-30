---
title: チュートリアル:Azure Active Directory と SAP HANA の統合 | Microsoft Docs
description: Azure Active Directory と SAP HANA の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231b9b6d217a9ad1fe5f4a6478f5e8799257b92b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091620"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>チュートリアル:Azure Active Directory と SAP HANA の統合

このチュートリアルでは、SAP HANA と Azure Active Directory (Azure AD) を統合する方法について説明します。
SAP HANA と Azure AD の統合には、次の利点があります。

* SAP HANA にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して SAP HANA に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

SAP HANA と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン (SSO) が有効な SAP HANA サブスクリプション
- 任意のパブリック IaaS、オンプレミス、Azure VM、または Azure 内の SAP Large Instances で実行している HANA インスタンス
- HANA インスタンスにインストールされた XSA 管理 Web インターフェイスと HANA Studio

> [!NOTE]
> このチュートリアルの手順をテストする場合、SAP HANA の運用環境を使わないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAP HANA でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAP HANA では、**IDP** によって開始される SSO がサポートされます
* SAP HANA では、**Just-In-Time** ユーザー プロビジョニングがサポートされています

## <a name="adding-sap-hana-from-the-gallery"></a>ギャラリーからの SAP HANA の追加

Azure AD への SAP HANA の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP HANA を追加する必要があります。

**ギャラリーから SAP HANA を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SAP HANA**」と入力し、結果ウィンドウで **SAP HANA** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の SAP HANA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SAP HANA で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SAP HANA 内の関連ユーザー間にリンク関係が確立されている必要があります。

SAP HANA で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SAP HANA シングル サインオンの構成](#configure-sap-hana-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SAP HANA テスト ユーザーの作成](#create-sap-hana-test-user)** - SAP HANA で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SAP HANA で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SAP HANA** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    ![[SAP HANA のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** テキスト ボックスに「`HA100`」と入力します。

    b. **[応答 URL]** ボックスに、`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SAP HANA クライアント サポート チーム](https://cloudplatform.sap.com/contact.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. SAP HANA アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性とクレーム]** ダイアログの **[ユーザー属性]** セクションで、次の手順を実行します。
 
    a. **[編集]** アイコンをクリックして、 **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. **[変換]** の一覧で、**ExtractMailPrefix()** を選択します。

    c. **[パラメーター 1]** の一覧で、**user.mail** を選択します。

    d. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>SAP HANA でシングル サインオンを構成する

1. SAP HANA 側でシングル サインオンを構成するには、それぞれの HTTPS エンドポイントにアクセスして **HANA XSA Web Console** にログインします。

    > [!NOTE]
    > 既定の構成では、この URL で要求はサインイン画面にリダイレクトされ、認証済みの SAP HANA データベース ユーザーの資格情報を要求されます。 サインインするユーザーには、SAML 管理タスクを実行するアクセス許可が必要です。

2. XSA Web インターフェイスで、**SAML Identity Provider** に移動します。 ここから画面の下部にある **+** ボタンを選択し、 **[Add Identity Provider Info]\(ID プロバイダー情報の追加\)** ウィンドウを表示します。 その後、次の手順を実行します。

    ![ID プロバイダーの追加](./media/saphana-tutorial/sap1.png)

    a. **[Add Identity Provider Info]\(ID プロバイダーの情報の追加\)** ウィンドウで、Azure Portal からダウンロードしたメタデータ XML の内容を、 **[Metadata]\(メタデータ\)** ボックスに貼り付けます。

    ![ID プロバイダーの設定の追加](./media/saphana-tutorial/sap2.png)

    b. XML ドキュメントの内容が有効な場合、解析プロセスで、 **[General data]\(全般的なデータ\)** 画面領域の **[Subject, Entity ID, and Issuer]\(件名、エンティティ ID、発行者\)** に必要な情報が抽出されます。 **[Base URL and SingleSignOn URL (*)]\(ベース URL と SingleSignOn URL (*)\)** のフィールドなど、 **[Destination]\(接続先\)** 画面領域に URL フィールドに必要な情報も抽出されます。

    ![ID プロバイダーの設定の追加](./media/saphana-tutorial/sap3.png)

    c. **[General Data]\(全般データ\)** 画面領域の **[Name]\(名前\)** ボックスに、新しい SAML SSO ID プロバイダーの名前を入力します。

    > [!NOTE]
    > SAML IDP の名前は必須です。また、一意にする必要があります。 使用する SAP HANA XS アプリケーションの認証方法として SAML を選択すると表示される使用可能な SAML IDP の一覧に、この名前が表示されます。 たとえば、XS Artifact Administration の **[Authentication]\(認証\)** 画面領域で確認できます。

3. **[Save]\(保存\)** を選択して SAML ID プロバイダーの詳細を保存し、既知の SAML IDP の一覧に新しい SAML IDP を追加します。

    ![[保存] ボタン](./media/saphana-tutorial/sap4.png)

4. HANA Studio の **[Configuration]\(構成\)** タブのシステム プロパティで、**saml** を使用して設定を絞り込みます。 次に、 **[assertion_timeout]** を **[10 sec]\(10 秒\)** から **[120 sec]\(120 秒\)** に調整します。

    ![assertion_timeout の設定](./media/saphana-tutorial/sap7.png)

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

このセクションでは、Britta Simon に SAP HANA へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[SAP HANA]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[SAP HANA]** を入力および選択します。

    ![アプリケーションの一覧の SAP HANA のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-sap-hana-test-user"></a>SAP HANA のテスト ユーザーを作成する

SAP HANA への Azure AD ユーザーのサインインを有効にするには、SAP HANA でプロビジョニングする必要があります。
SAP HANA では、**Just-In-Time プロビジョニング**がサポートされています。この設定は、既定で有効になっています。

ユーザーを手動で作成する必要がある場合は、次の手順を実行します。

>[!NOTE]
>ユーザーが使う外部認証を変更することができます。 Kerberos などの外部システムを使用して認証できます。 外部 ID について詳しくは、[ドメイン管理者](https://cloudplatform.sap.com/contact.html)に問い合わせてください。

1. 管理者として [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) を開き、SAML SSO の DB-User を有効にします。

    ![ユーザーの作成](./media/saphana-tutorial/sap5.png)

2. **[SAML]** の左側にある非表示のチェック ボックスをオンにし、[**Configure]\(構成\)** リンクを選択します。

3. **[Add]\(追加\)** を選択して SAML IDP を追加します。  適切な SAML IDP を選択してから **[OK]** を選択します。

4. **[External Identity]\(外部 ID\)** を追加するか (この例では BrittaSimon)、 **[Any]\(任意\)** を選択します。 **[OK]** をクリックします。

   > [!Note]
   > **[Any]\(任意\)** チェック ボックスがオフの場合、HANA のユーザー名は UPN でドメイン サフィックスの前のユーザーの名前と正確に一致している必要があります (つまり、BrittaSimon@contoso.com は HANA では BrittaSimon になります)。

5. テストの場合は、すべての **XS** ロールをユーザーに割り当てます。

    ![ロールの割り当て](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > ユース ケースに適切なアクセス許可のみを付与する必要があります。

6. ユーザーを保存します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SAP HANA] タイルをクリックすると、SSO を設定した SAP HANA に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

