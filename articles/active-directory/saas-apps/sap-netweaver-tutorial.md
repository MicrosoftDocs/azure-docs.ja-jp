---
title: チュートリアル:Azure Active Directory と SAP NetWeaver の統合 | Microsoft Docs
description: Azure Active Directory と SAP NetWeaver の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01a70bfbb169090fbdf4b5f8e00af6895f69c964
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091917"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>チュートリアル:Azure Active Directory と SAP NetWeaver の統合

このチュートリアルでは、SAP NetWeaver と Azure Active Directory (Azure AD) を統合する方法について説明します。
SAP NetWeaver と Azure AD の統合には、次の利点があります。

* SAP NetWeaver にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで SAP NetWeaver に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAP NetWeaver の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAP NetWeaver でのシングル サインオンが有効なサブスクリプション
* SAP NetWeaver V7.20 以降が必要

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAP NetWeaver では、**SP** によって開始される SSO がサポートされます

## <a name="adding-sap-netweaver-from-the-gallery"></a>ギャラリーからの SAP NetWeaver の追加

Azure AD への SAP NetWeaver の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP NetWeaver を追加する必要があります。

**ギャラリーから SAP NetWeaver を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SAP NetWeaver**」と入力し、結果ウィンドウで **SAP NetWeaver** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の SAP NetWeaver](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SAP NetWeaver で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SAP NetWeaver 内の関連ユーザー間にリンク関係が確立されている必要があります。

SAP NetWeaver で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SAP NetWeaver シングル サインオンの構成](#configure-sap-netweaver-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SAP NetWeaver のテスト ユーザーの作成](#create-sap-netweaver-test-user)** - SAP NetWeaver で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SAP NetWeaver で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. 新しい Web ブラウザー ウィンドウを開き、SAP NetWeaver 企業サイトに管理者としてログインします

2. **http** および **https** サービスがアクティブであり、**SMICM** T-Code で適切なポートが割り当てられていることを確認します。

3. SAP システム (T01) のビジネス クライアントにログオンします。SSO が必要であり、HTTP セキュリティ セッション管理をアクティブ化します。

    a. トランザクション コードの **SICF_SESSIONS** に移動します。 すべての関連プロファイル パラメーターと現在の値が表示されます。 次のように表示されます。
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
    > 組織の要件に合わせて上記のパラメーターを調整します。上記のパラメーターは参考用にのみ示してあります。

    b. 必要に応じて SAP システムのインスタンス/既定プロファイルでパラメーターを調整し、SAP システムを再起動します。

    c. 関連するクライアントをダブルクリックして、HTTP セキュリティ セッションを有効にします。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. 以下の SICF サービスをアクティブ化します。
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. SAP システム [T01/122] のビジネス クライアントでトランザクション コード **SAML2** に移動します。 ブラウザーでユーザー インターフェイスが開きます。 この例では、SAP ビジネス クライアントとして 122 を想定しています。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. ユーザー インターフェイスに入るためのユーザー名とパスワードを指定し、 **[Edit]\(編集\)** をクリックします。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. **プロバイダー名** を T01122 から `http://T01122` に変更し、 **[保存]** をクリックします。

    > [!NOTE]
    > 既定ではプロバイダー名は `<sid><client>` という形式ですが、Azure AD では `<protocol>://<name>` という形式の名前が想定されています。プロバイダー名は `https://<sid><client>` のままにして、Azure AD で複数の SAP NetWeaver ABAP エンジンを構成できるようにすることをお勧めします。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **サービス プロバイダーのメタデータの生成**:- SAML 2.0 ユーザー インターフェイスで **[Local Provider]\(ローカル プロバイダー\)** と **[Trusted Providers]\(信頼できるプロバイダー\)** の設定の構成が完了したら、次のステップでは、サービス プロバイダーのメタデータ ファイルを生成します (これには、すべての設定、認証コンテキスト、および SAP での他の構成が含まれます)。 このファイルを生成したら、それを Azure AD にアップロードする必要があります。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. **[Local Provider]\(ローカル プロバイダー\)** タブに移動します。

    b. **[Metadata]\(メタデータ\)** をクリックします。

    c. 生成された**メタデータ XML ファイル**をコンピューターに保存し、それを Azure portal の **[基本的な SAML 構成]** セクションにアップロードして、 **[識別子]** と **[応答 URL]** の値を自動的に設定します。

8. [Azure portal](https://portal.azure.com/) の **SAP NetWeaver** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

9. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

10. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

11. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[メタデータ ファイルをアップロードする]** をクリックして、前に取得した**サービス プロバイダー メタデータ ファイル**をアップロードします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、次に示すように、**識別子**と**応答 URL** の値が、 **[基本的な SAML 構成]** セクションのテキスト ボックスに自動的に設定されます。

    ![[SAP NetWeaver のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    d. **[サインオン URL]** ボックスに、`https://<your company instance of SAP NetWeaver>` という形式で URL を入力します。

    > [!NOTE]
    > ユーザーのインスタンスに対して構成された応答 URL に誤りがあるというエラーはほとんどレポートされていません。 もしそのようなエラーが発生した場合は、回避策として、次の PowerShell スクリプトを使用してください。ご使用のインスタンスに対して正しい応答 URL を設定することができます。
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal Object ID は最初に自分で設定しておくことができるほか、ここで渡すこともできます。

12. SAP NetWeaver アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

13. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    a. **[編集]** アイコンをクリックして、 **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. **[変換]** の一覧で、**ExtractMailPrefix()** を選択します。

    c. **[パラメーター 1]** の一覧で、**user.userprinicipalname** を選択します。

    d. **[Save]** をクリックします。

14. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

15. **[SAP NetWeaver の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sap-netweaver-single-sign-on"></a>SAP NetWeaver のシングル サインオンの構成

1. SAP システムにログオンし、トランザクション コード SAML2 に移動します。 新しいブラウザー ウィンドウで SAML 構成画面が開きます。

2. 信頼できる ID プロバイダー (Azure AD) のエンド ポイントを構成するには、 **[Trusted Providers]\(信頼できるプロバイダー\)** タブに移動します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. **[Add]\(追加\)** をクリックして、コンテキスト メニューから **[Upload Metadata File]\(メタデータ ファイルのアップロード\)** を選択します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Azure portal からダウンロードしたメタデータ ファイルをアップロードします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. 次の画面で、エイリアス名を入力します。 たとえば「aadsts」と入力し、 **[Next]\(次へ\)** をクリックして続行します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. **[Digest Algorithm]\(ダイジェスト アルゴリズム\)** が **[SHA-256]** であることを確認します。何も変更する必要はありません。 **[Next]\(次へ\)** をクリックします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. **[Single Sign-On Endpoints]\(シングル サインオン エンドポイント\)** では **[HTTP POST]** を使用し、 **[Next]\(次へ\)** をクリックして続行します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. **[Single Logout Endpoints]\(シングル ログアウト エンドポイント\)** では **[HTTPRedirect]** を選択し、 **[Next]\(次へ\)** をクリックして続行します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. **[Artifact Endpoints]\(アーティファクト エンドポイント\)** では、 **[Next]\(次へ\)** をクリックして続行します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. **[Authentication Requirements]\(認証要件\)** では、 **[Finish]\(完了\)** をクリックします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. **[Trusted Providers]\(信頼できるプロバイダー\)**  >  **[Identity Federation]\(ID フェデレーション\)** タブ (画面下部) に移動します。 **[編集]** をクリックします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. **[Identity Federation]\(ID フェデレーション\)** タブ (下のウィンドウ) で **[Add]\(追加\)** をクリックします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. ポップアップ ウィンドウで **[Supported NameID formats]\(サポートされる名前 ID 形式\)** から **[Unspecified]\(未指定\)** を選択し、[OK] をクリックします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. **[user ID Source]\(ユーザー ID ソース\)** と **[user id mapping mode]\(ユーザー ID マッピング モード\)** の値によって、SAP ユーザーと Azure AD 要求の間のリンクが決まることに注意してください。  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>シナリオ: SAP ユーザーから Azure AD ユーザーへのマッピング。

    a. SAP の NameID 詳細スクリーンショット。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Azure AD からの必要な要求の説明のスクリーンショット。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>シナリオ: SU01 で構成済みのメール アドレスに基づいて SAP ユーザー ID を選択する。 このケースでは、SSO を必要とする各ユーザーの su01 でメール ID を構成する必要があります。

    a.  SAP の NameID 詳細スクリーンショット。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Azure AD からの必要な要求の説明のスクリーンショット。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/claimsaad2.png)

15. **[Save]\(保存\)** をクリックし、 **[Enable]\(有効\)** をクリックして ID プロバイダーを有効にします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/configuration1.png)

16. メッセージが表示されたら、 **[OK]** をクリックします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/configuration2.png)

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

このセクションでは、Britta Simon に SAP NetWeaver へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[SAP NetWeaver]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SAP NetWeaver]** を選択します。

    ![アプリケーションの一覧の SAP NetWeaver リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver のテスト ユーザーの作成

このセクションでは、SAP NetWeaver で Britta Simon というユーザーを作成します。 社内の SAP 専門家チームまたは組織の SAP パートナーと協力して、SAP NetWeaver プラットフォームにユーザーを追加してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

1. ID プロバイダー Azure AD がアクティブになったら、次の URL にアクセスして SSO を確認します (ユーザー名とパスワードの入力は求められません)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (または) 下記の URL を使用します

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > sapurl は実際の SAP のホスト名に置き換えます。

2. 上記の URL により、下の画面に移動するはずです。 下のページに移動できる場合、Azure AD の SSO のセットアップは正常に行われています。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/testingsso.png)

3. ユーザー名とパスワードの入力を求められた場合は、次の URL を使用してトレースを有効にして問題を診断してください

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
