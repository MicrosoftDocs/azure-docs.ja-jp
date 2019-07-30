---
title: チュートリアル:Azure Active Directory と SAP Fiori の統合 | Microsoft Docs
description: Azure Active Directory と SAP Fiori の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 897685eb967e03cfd30182eec6b237e27386496c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092148"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>チュートリアル:Azure Active Directory と SAP Fiori の統合

このチュートリアルでは、SAP Fiori と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP Fiori と Azure AD の統合により、次の利点が得られます。

* Azure AD を使用して、SAP Fiori にアクセスするユーザーを管理できます。
* ユーザーは、自分の Azure AD アカウントで SAP Fiori に自動的にサインイン (シングル サインオン) できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

SAP Fiori と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* シングル サインオンが有効な SAP Fiori のサブスクリプション。
* SAP Fiori 7.20 以降が必要です。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、SAP Fiori を Azure AD と統合します。

SAP Fiori では、次の機能をサポートしています。

* **SP によって開始されるシングル サインオン**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Azure portal で SAP Fiori を追加する

SAP Fiori を Azure AD と統合するには、マネージド SaaS アプリの一覧に SAP Fiori を追加する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のメニューで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] オプション](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] オプション](common/add-new-app.png)

1. 検索ボックスに「**SAP Fiori**」と入力します。 検索結果で **[SAP Fiori]** を選択し、 **[追加]** を選択します。

    ![結果一覧の SAP Fiori](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SAP Fiori で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと SAP Fiori 内の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Fiori で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

| タスク | 説明 |
| --- | --- |
| **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** | ユーザーがこの機能を使用できるようにします。 |
| **[SAP Fiori シングル サインオンの構成](#configure-sap-fiori-single-sign-on)** | アプリケーションでシングル サインオン設定を構成します。 |
| **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** | Britta Simon という名前のユーザーの Azure AD シングル サインオンをテストします。 |
| **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** | Britta Simon が Azure AD シングル サインオンを使用できるようにします。 |
| **[SAP Fiori テスト ユーザーの作成](#create-an-sap-fiori-test-user)** | SAP Fiori で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。 |
| **[シングル サインオンのテスト](#test-single-sign-on)** | 構成が機能することを確認します。 |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で SAP Fiori による Azure AD シングル サインオンを構成します。

1. 新しい Web ブラウザー ウィンドウを開き、SAP Fiori 企業サイトに管理者としてサインインします。

1. **http** および **https** サービスがアクティブであり、トランザクション コード **SMICM** に適切なポートが割り当てられていることを確認します。

1. シングル サインオンが必要な、SAP システム **T01** の SAP Business Client にサインインします。 次に、HTTP セキュリティ セッション管理を有効にします。

    1. トランザクション コードの **SICF_SESSIONS** に移動します。 すべての関連プロファイル パラメーターとその現在の値が表示されます。 次の例のようになります。

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > 組織の要件に基づいて、パラメーターを調整します。 上のパラメーターは、単に例として挙げたものです。

    1. 必要に応じて SAP システムのインスタンス (既定) プロファイルでパラメーターを調整し、SAP システムを再起動します。

    1. 関連するクライアントをダブルクリックして、HTTP セキュリティ セッションを有効にします。

        ![SAP の [Current Values of Relevant Profile Parameters]\(関連プロファイル パラメーターの現在の値\) ページ](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. 以下の SICF サービスをアクティブ化します。

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. SAP システム **[T01/122]** の Business Client でトランザクション コード **SAML2** に移動します。 新しいブラウザー ウィンドウで構成 UI が開きます。 この例では、SAP システム 122 の Business Client を使用します。

    ![SAP Fiori Business Client のサインイン ページ](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. ユーザー名とパスワードを入力して、 **[Log on]\(ログオン\)** を選択します。

    ![SAP の [SAML 2.0 Configuration of ABAP System T01/122]\(ABAP システム T01/122 の SAML 2.0 構成\) ページ](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. **[Provider Name]\(プロバイダー名\)** ボックスで **T01122** を **http:\//T01122** に置き換え、 **[Save]\(保存\)** を選択します。

    > [!NOTE]
    > 既定では、プロバイダー名は \<sid>\<クライアント>という形式です。 Azure AD で想定されている名前の形式は、\<プロトコル>://\<名前> です。 Azure AD で複数の SAP Fiori ABAP エンジンを構成できるように、プロバイダー名を https\://\<sid>\<クライアント> という形式で保持することをお勧めします。

    ![SAP の [SAML 2.0 Configuration of ABAP System T01/122]\(ABAP システム T01/122 の SAML 2.0 構成\) ページでの更新されたプロバイダー名](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. **[Local Provider]\(ローカル プロバイダー\)** タブ >  **[Metadata]\(メタデータ\)** の順に選択します。

1. **[SAML 2.0 Metadata]\(SAML 2.0 メタデータ\)** ダイアログ ボックスで、生成されたメタデータ XML ファイルをダウンロードし、コンピューターに保存します。

    ![SAP の [SAML 2.0 Metadata]\(SAML 2.0 メタデータ\) ダイアログ ボックスの [Download Metadata]\(メタデータのダウンロード\) リンク](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. [Azure portal](https://portal.azure.com/) の **SAP Fiori** アプリケーション統合ウィンドウで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] オプション](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML]** または **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、 **[編集]** (鉛筆アイコン) を選択して **[基本的な SAML 構成]** ウィンドウを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    1. **[メタデータ ファイルをアップロードする]** を選択します。

        ![[メタデータ ファイルをアップロードします] オプション](common/upload-metadata.png)

   1. メタデータ ファイルを選択するには、フォルダー アイコンを選択し、 **[アップロード]** を選択します。

       ![メタデータ ファイルを選択し、[アップロード] ボタンを選択する](common/browse-upload-metadata.png)

1. メタデータ ファイルが正常にアップロードされると、 **[基本的な SAML 構成]** ウィンドウの **[識別子]** と **[応答 URL]** の値が自動的に入力されます。 **[サインオン URL]** ボックスに、https:\//\<SAP Fiori の会社インスタンス\> という形式で URL を入力します。

    ![[SAP Fiori のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    > [!NOTE]
    > 一部のお客様から、誤って構成された**応答 URL** の値に関連するエラーがレポートされています。 このようなエラーが表示されたら、以下の PowerShell スクリプトを使用して、インスタンスの正しい応答 URL を設定できます。
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > スクリプトを実行する前に `ServicePrincipal` オブジェクト ID を自分で設定することも、ここで渡すこともできます。

1. SAP Fiori アプリケーションは、特定の形式の SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性値を管理するには、 **[SAML でシングル サインオンをセットアップします]** ウィンドウで **[編集]** を選択します。

    ![[ユーザー属性] ウィンドウ](common/edit-attribute.png)

1. **[ユーザー属性と要求]** ウィンドウで、前の画像で示されているように、SAML トークン属性を構成します。 その後、次の手順を完了します。

    1. **[編集]** を選択し、 **[ユーザー要求の管理]** ウィンドウを開きます。

    1. **[変換]** の一覧で、**ExtractMailPrefix()** を選択します。

    1. **[パラメーター 1]** の一覧で、**user.userprinicipalname** を選択します。

    1. **[保存]** を選択します。

       ![[ユーザー要求の管理] ウィンドウ](./media/sapfiori-tutorial/nameidattribute.png)

       ![[ユーザー要求の管理] ウィンドウの [変換] セクション](./media/sapfiori-tutorial/nameidattribute1.png)


1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** の横の **[ダウンロード]** を選択します。 要件に基づいてダウンロード オプションを選択します。 コンピューターに証明書を保存します。

    ![証明書のダウンロード オプション](common/metadataxml.png)

1. **[SAP Fiori のセットアップ]** セクションで、要件に基づいて次の URL をコピーします。

    * ログイン URL
    * Azure AD 識別子
    * ログアウト URL

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>SAP Fiori シングル サインオンの構成

1. SAP システムにサインインし、トランザクション コード **SAML2** に移動します。 新しいブラウザー ウィンドウで SAML 構成ページが開かれます。

1. 信頼されている ID プロバイダー (Azure AD) のエンドポイントを構成するには、 **[Trusted Providers]\(信頼できるプロバイダー\)** タブを選択します。

    ![SAP の [Trusted Providers]\(信頼できるプロバイダー\) タブ](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. **[Add]\(追加\)** を選択し、コンテキスト メニューの **[Upload Metadata File]\(メタデータ ファイルのアップロード\)** を選択します。

    ![SAP の [Add]\(追加\) と [Upload Metadata File]\(メタデータ ファイルのアップロード\) オプション](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Azure portal でダウンロードしたメタデータ ファイルをアップロードします。 **[次へ]** を選択します。

    ![SAP でアップロードするメタデータ ファイルを選択する](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. 次のページの **[Alias]\(エイリアス\)** ボックスに、エイリアス名を入力します。 たとえば、「**aadsts**」と入力します。 **[次へ]** を選択します。

    ![SAP の [Alias]\(エイリアス\) ボックス](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. **[Digest Algorithm]\(ダイジェスト アルゴリズム\)** ボックスの値が **SHA-256** であることを確認してください。 **[次へ]** を選択します。

    ![SAP で [Digest Algorithm]\(ダイジェスト アルゴリズム\) の値を確認する](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. **[Single Sign-On Endpoints]\(シングル サインオン エンドポイント\)** で **[HTTP POST]** を選択し、 **[Next]\(次へ\)** を選択します。

    ![SAP の [Single Sign-On Endpoints]\(シングル サインオン エンドポイント\) オプション](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. **[Single Logout Endpoints]\(シングル ログアウト エンドポイント\)** で **[HTTP Redirect]** を選択し、 **[Next]\(次へ\)** を選択します。

    ![SAP の [Single Logout Endpoints]\(シングル ログアウト エンドポイント\) オプション](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. **[Artifact Endpoints]\(アーティファクト エンドポイント\)** で **[Next]\(次へ\)** を選択して続行します。

    ![SAP の [Artifact Endpoints]\(アーティファクト エンドポイント\) オプション](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. **[Authentication Requirements]\(認証要件\)** で **[Finish]\(完了\)** を選択します。

    ![SAP の [Authentication Requirements]\(認証要件\) オプションと [Finish]\(完了\) オプション](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. **[Trusted Provider]\(信頼できるプロバイダー\)**  >  **[Identity Federation]\(ID フェデレーション\)** (画面下部) の順に選択します。 **[編集]** を選択します。

    ![SAP の [Trusted Provider]\(信頼できるプロバイダー\) タブと [Identity Federation]\(ID フェデレーション\) タブ](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. **[追加]** を選択します。

    ![[Identity Federation]\(ID フェデレーション\) タブの [Add]\(追加\) オプション](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. **[Supported NameID Formats]\(サポートされる名前 ID 形式\)** ダイアログ ボックスで **[Unspecified]\(未指定\)** を選択します。 **[OK]** を選択します。

    ![SAP の [Supported NameID Formats]\(サポートされる名前 ID 形式\) ダイアログ ボックスとオプション](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    **[User ID Source]\(ユーザー ID ソース\)** と **[User ID Mapping Mode]\(ユーザー ID マッピング モード\)** の値によって、SAP ユーザーと Azure AD 要求の間のリンクが決まります。  

    **シナリオ 1**: SAP ユーザーから Azure AD ユーザーへのマッピング

    1. SAP で、 **[Details of NameID Format "Unspecified"]\(NameID 形式 "未指定" の詳細\)** の詳細を書き留めます。

        ![SAP の [Details of NameID Format "Unspecified"]\(NameID 形式 "未指定" の詳細\) ダイアログ ボックス](./media/sapfiori-tutorial/nameiddetails.png)

    1. Azure portal の **[ユーザー属性と要求]** で、Azure AD からの必要な要求を書き留めます。

        ![Azure portal の [ユーザー属性と要求] ダイアログ ボックス](./media/sapfiori-tutorial/claimsaad1.png)

    **シナリオ 2**: SU01 で構成したメール アドレスに基づいて、SAP ユーザー ID を選択します。 このケースでは、SSO を必要とする各ユーザーに対して、SU01 でメール ID を構成する必要があります。

    1.  SAP で、 **[Details of NameID Format "Unspecified"]\(NameID 形式 "未指定" の詳細\)** の詳細を書き留めます。

        ![SAP の [Details of NameID Format "Unspecified"]\(NameID 形式 "未指定" の詳細\) ダイアログ ボックス](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Azure portal の **[ユーザー属性と要求]** で、Azure AD からの必要な要求を書き留めます。

       ![Azure portal の [ユーザー属性と要求] ダイアログ ボックス](./media/sapfiori-tutorial/claimsaad2.png)

1. **[Save]\(保存\)** を選択し、 **[Enable]\(有効\)** を選択して、ID プロバイダーを有効にします。

    ![SAP の [Save]\(保存\) と [Enable]\(有効\) オプション](./media/sapfiori-tutorial/configuration1.png)

1. メッセージが表示されたら、 **[OK]** を選択します。

    ![SAP の [SAML 2.0 Configuration]\(SAML 2.0 の構成\) ダイアログ ボックスの [OK] オプション](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

1. Azure portal 内で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] オプション](common/users.png)

1. **[ 新規ユーザー]** を選択します。

    ![[新しいユーザー] オプション](common/new-user.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに、「**brittasimon\@\<企業のドメイン>.\<extension>** 」と入力します。 たとえば、「**brittasimon\@contoso.com**」と入力します。

    1. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。

    1. **作成** を選択します。

    ![[ユーザー] ウィンドウ](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP Fiori へのアクセス権を付与して、彼女が Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[SAP Fiori]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションの一覧で **[SAP Fiori]** を選択します。

    ![アプリケーションの一覧の SAP Fiori](common/all-applications.png)

1. メニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] オプション](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ウィンドウで、ユーザーの一覧から **Britta Simon** を選択します。 **[選択]** を選択します。

1. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ウィンドウで、ユーザーに関連したロールを一覧から選択します。 **[選択]** を選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-an-sap-fiori-test-user"></a>SAP Fiori テスト ユーザーの作成

このセクションでは、SAP Fiori で Britta Simon というユーザーを作成します。 組織内の SAP 専門家チームまたは組織の SAP パートナーと協力して、SAP Fiori プラットフォームにユーザーを追加してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

1. SAP Fiori で ID プロバイダー Azure AD がアクティブ化されたら、次のいずれかの URL にアクセスしてみて、シングル サインオンをテストします (ユーザー名とパスワードの入力は求められないはずです)。

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > *sapurl* は実際の SAP のホスト名に置き換えます。

1. テスト URL によって、SAP の以下のテスト アプリケーション ページに移動するはずです。 ページが開いたら、Azure AD シングル サインオンが正常に設定されています。

    ![SAP の標準テスト アプリケーション ページ](./media/sapfiori-tutorial/testingsso.png)

1. ユーザー名とパスワードの入力が求められる場合は、トレースを有効にして、問題の診断に役立てます。 トレースには、https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN# という URL を使用します。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
