---
title: チュートリアル:チュートリアル:SAP NetWeaver と Azure Active Directory のシングル サインオン (SSO) 統合 | Microsoft Docs
description: Azure Active Directory と SAP NetWeaver の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d79c953f22bfe587f740bc29050796834309186a
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103358"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>チュートリアル:SAP NetWeaver と Azure Active Directory のシングル サインオン (SSO) 統合

このチュートリアルでは、SAP NetWeaver と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SAP NetWeaver を統合すると、次のことができます。

* SAP NetWeaver にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して SAP NetWeaver に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SAP NetWeaver でのシングル サインオン (SSO) が有効なサブスクリプション。
* SAP NetWeaver V7.20 以降が必要

## <a name="scenario-description"></a>シナリオの説明

SAP NetWeaver では、**SAML** (**SP Initiated SSO**) と **OAuth** の両方がサポートされます。 このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

> [!NOTE]
> 自分の組織の要件に応じて SAML または OAuth でアプリケーションを構成してください。 

## <a name="adding-sap-netweaver-from-the-gallery"></a>ギャラリーからの SAP NetWeaver の追加

Azure AD への SAP NetWeaver の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP NetWeaver を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SAP NetWeaver**」と入力します。
1. 結果パネルから **[SAP NetWeaver]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>SAP NetWeaver の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP NetWeaver に対する Azure AD SSO を構成してテストします。 SSO が機能するには、Azure AD ユーザーと SAP NetWeaver の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAP NetWeaver で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SAML を使用した SAP NetWeaver の構成](#configure-sap-netweaver-using-saml)** - アプリケーション側で SSO 設定を構成します。
    1. **[SAP NetWeaver のテスト ユーザーの作成](#create-sap-netweaver-test-user)** - SAP NetWeaver で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。
1. **[SAP NetWeaver の OAuth 向け構成](#configure-sap-netweaver-for-oauth)** - アプリケーション側で OAuth 設定を構成します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SAP NetWeaver で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. 新しい Web ブラウザー ウィンドウを開き、SAP NetWeaver 企業サイトに管理者としてサインインします。

1. **http** および **https** サービスがアクティブであり、**SMICM** T-Code で適切なポートが割り当てられていることを確認します。

1. SAP システム (T01) のビジネス クライアントにサインオンします。SSO が必要であり、HTTP セキュリティ セッション管理をアクティブ化します。

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

    b. 必要に応じて SAP システムのインスタンスまたは既定プロファイルでパラメーターを調整し、SAP システムを再起動します。

    c. 関連するクライアントをダブルクリックして、HTTP セキュリティ セッションを有効にします。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. 以下の SICF サービスをアクティブ化します。
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. SAP システム [T01/122] のビジネス クライアントでトランザクション コード **SAML2** に移動します。 ブラウザーでユーザー インターフェイスが開きます。 この例では、SAP ビジネス クライアントとして 122 を想定しています。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. ユーザー インターフェイスに入るためのユーザー名とパスワードを指定し、 **[Edit]\(編集\)** をクリックします。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. **プロバイダー名** を T01122 から `http://T01122` に変更し、 **[保存]** をクリックします。

    > [!NOTE]
    > 既定ではプロバイダー名は `<sid><client>` という形式ですが、Azure AD では `<protocol>://<name>` という形式の名前が想定されています。プロバイダー名は `https://<sid><client>` のままにして、Azure AD で複数の SAP NetWeaver ABAP エンジンを構成できるようにすることをお勧めします。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **サービス プロバイダーのメタデータの生成**:- SAML 2.0 ユーザー インターフェイスで **[Local Provider]\(ローカル プロバイダー\)** と **[Trusted Providers]\(信頼できるプロバイダー\)** の設定の構成が完了したら、次のステップでは、サービス プロバイダーのメタデータ ファイルを生成します (これには、すべての設定、認証コンテキスト、および SAP での他の構成が含まれます)。 このファイルを生成したら、それを Azure AD にアップロードする必要があります。

    ![証明書のダウンロードのリンク](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. **[Local Provider]\(ローカル プロバイダー\)** タブに移動します。

    b. **[Metadata]\(メタデータ\)** をクリックします。

    c. 生成された**メタデータ XML ファイル**をコンピューターに保存し、それを Azure portal の **[基本的な SAML 構成]** セクションにアップロードして、 **[識別子]** と **[応答 URL]** の値を自動的に設定します。

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SAP NetWeaver** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[メタデータ ファイルをアップロードする]** をクリックして、前に取得した**サービス プロバイダー メタデータ ファイル**をアップロードします。

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    c. メタデータ ファイルが正常にアップロードされると、次に示すように、**識別子**と**応答 URL** の値が、 **[基本的な SAML 構成]** セクションのテキスト ボックスに自動的に設定されます。

    d. **[サインオン URL]** ボックスに、`https://<your company instance of SAP NetWeaver>` という形式で URL を入力します。

    > [!NOTE]
    > ユーザーのインスタンスに対して構成された応答 URL に誤りがあるというエラーはほとんどレポートされていません。 もしそのようなエラーが発生した場合は、回避策として、次の PowerShell スクリプトを使用してください。ご使用のインスタンスに対して正しい応答 URL を設定することができます。
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal Object ID は最初に自分で設定しておくことができるほか、ここで渡すこともできます。

1. SAP NetWeaver アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

1. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    a. **[編集]** アイコンをクリックして、 **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. **[変換]** の一覧で、**ExtractMailPrefix()** を選択します。

    c. **[パラメーター 1]** の一覧で、**user.userprinicipalname** を選択します。

    d. **[Save]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[SAP NetWeaver の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、SAP NetWeaver へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAP NetWeaver]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sap-netweaver-using-saml"></a>SAML を使用した SAP NetWeaver の構成

1. SAP システムにサインインし、トランザクション コード SAML2 に移動します。 新しいブラウザー ウィンドウで SAML 構成画面が開きます。

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

14. **[user ID Source]\(ユーザー ID ソース\)** と **[user ID mapping mode]\(ユーザー ID マッピング モード\)** の値によって、SAP ユーザーと Azure AD 要求の間のリンクが決まることに注意してください。  

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

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver のテスト ユーザーの作成

    このセクションでは、SAP NetWeaver で B.simon というユーザーを作成します。 社内の SAP 専門家チームまたは組織の SAP パートナーと協力して、SAP NetWeaver プラットフォームにユーザーを追加してください。

## <a name="test-sso"></a>SSO のテスト

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

## <a name="configure-sap-netweaver-for-oauth"></a>SAP NetWeaver の OAuth 向け構成

1. SAP によって文書化されたプロセスが「[NetWeaver Gateway サービスの有効化と OAuth 2.0 スコープの作成](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)」に記載されています。

2. SPRO に移動し、 **[Activate and Maintain services]\(サービスのアクティブ化と管理\)** を探します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth01.png)

3. この例では、OAuth を使用した OData サービス `DAAG_MNGGRP` を Azure AD SSO に接続します。 テクニカル サービス名の検索を使用して、`DAAG_MNGGRP` というサービスを探し、まだアクティブ ([ICF nodes]\(ICF ノード\) タブで `green` 状態を確認) になっていない場合はアクティブ化します。 システム エイリアス (サービスが実際に実行される接続先バックエンド システム) が正しいことを確認してください。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth02.png)

    * 次に、最上部にあるボタン バーの **[OAuth]** ボタンをクリックし、`scope` を割り当てます (既定の名前をそのまま使用してください)。

4. この例のスコープは `DAAG_MNGGRP_001` です。これは、番号を自動的に追加することによってサービス名から生成されます。 レポート `/IWFND/R_OAUTH_SCOPES` は、スコープの名前の変更または作成を手動で行うために使用できます。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > `soft state status is not supported` というメッセージは問題ないので無視してかまいません。 詳細については、[こちら](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)を参照してください。

### <a name="create-a-service-user-for-the-oauth-20-client"></a>OAuth 2.0 クライアントのサービス ユーザーを作成する

1. OAuth2 では、`service ID` を使用してエンドユーザーのアクセス トークンを代わりに取得します。 OAuth の設計上の重要な制限として、`OAuth 2.0 Client ID` は、OAuth 2.0 クライアントがアクセス トークンを要求する際のログインに使用される `username` と一致している必要があります。 そのため、この例では OAuth 2.0 クライアントを CLIENT1 という名前で登録したうえで、同じ名前 (CLIENT1) のユーザーが SAP システムに存在することを前提条件とし、また照会元のアプリケーションでそのユーザーが使用されるように構成します。 

2. OAuth クライアントを登録する際は、`SAML Bearer Grant type` を使用します。

    >[!NOTE]
    >詳細については、[こちら](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)の「SAML ベアラー付与タイプでの OAuth 2.0 クライアントの登録」を参照してください

3. tcod: SU01 で、CLIENT1 というユーザーを `System type` として作成してパスワードを割り当て、保存します。必要に応じてその資格情報を API プログラマに提供します。API プログラマはそれをユーザー名と共に、呼び出し元のコードに書き込む必要があります。 プロファイルやロールを割り当てる必要はありません。

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>新しい OAuth 2.0 クライアント ID を作成ウィザードで登録する

1. 新しい **OAuth 2.0 クライアント**を登録するために、トランザクション **SOAUTH2** を開始します。 このトランザクションは、既に登録されている OAuth 2.0 クライアントについての概要を表示します。 この例では CLIENT1 という名前の新しい OAuth クライアントのために、 **[Create]\(作成\)** を選択してウィザードを開始します。

2. T-Code: **SOAUTH2** に移動して説明を入力し、 **[next]\(次へ\)** をクリックします。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth04.png)

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth05.png)

3. あらかじめ追加されている **[SAML2 IdP - Azure AD]** をドロップダウン リストから選択して保存します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth06.png)

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth07.png)

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth08.png)

4. [Scope Assignment]\(スコープ割り当て\) の下の **[Add]\(追加\)** をクリックして、前に作成されたスコープ (`DAAG_MNGGRP_001`) を追加します。

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth09.png)

    ![Configure single sign-on](./media/sapnetweaver-tutorial/oauth10.png)

5. **[finish]\(完了\)** をクリックします。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で SAP NetWeaver を試す](https://aad.portal.azure.com/)