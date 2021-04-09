---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Pulse Secure Virtual Traffic Manager の統合 | Microsoft Docs
description: Azure Active Directory と Pulse Secure Virtual Traffic Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: 1e295075a5c1ae8daa6673757770bbef01208c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92505728"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Pulse Secure Virtual Traffic Manager の統合

このチュートリアルでは、Pulse Secure Virtual Traffic Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Pulse Secure Virtual Traffic Manager を統合すると、次のことができます。

* Pulse Secure Virtual Traffic Manager にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Pulse Secure Virtual Traffic Manager に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Pulse Secure Virtual Traffic Manager でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Pulse Secure Virtual Traffic Manager では、**SP** Initiated SSO がサポートされます

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>ギャラリーからの Pulse Secure Virtual Traffic Manager の追加

Azure AD への Pulse Secure Virtual Traffic Manager の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Pulse Secure Virtual Traffic Manager を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Pulse Secure Virtual Traffic Manager**」と入力します。
1. 結果のパネルから **[Pulse Secure Virtual Traffic Manager]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Pulse Secure Virtual Traffic Manager の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Pulse Secure Virtual Traffic Manager に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Pulse Secure Virtual Traffic Manager の関連ユーザーとの間にリンク関係を確立する必要があります。

Pulse Secure Virtual Traffic Manager に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Pulse Secure Virtual Traffic Manager の SSO の構成](#configure-pulse-secure-virtual-traffic-manager-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Pulse Secure Virtual Traffic Manager のテスト ユーザーの作成](#create-pulse-secure-virtual-traffic-manager-test-user)** - Pulse Secure Virtual Traffic Manager で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Pulse Secure Virtual Traffic Manager** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<published virtual server FQDN>/saml/consume`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<published virtual server FQDN>/saml/metadata`

    c. **[応答 URL]** ボックスに、`https://<published virtual server FQDN>/saml/consume` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Pulse Secure Virtual Traffic Manager クライアント サポート チーム](mailto:support@pulsesecure.net)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Pulse Secure Virtual Traffic Manager のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Pulse Secure Virtual Traffic Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Pulse Secure Virtual Traffic Manager]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Pulse Secure Virtual Traffic Manager で SSO を構成する

このセクションでは、Pulse Virtual Traffic Manager で Azure AD SAML 認証を有効にするために必要な構成について説明します。 構成のすべての変更は、管理 Web UI を使用して、Pulse Virtual Traffic Manager に対して行われます。 

#### <a name="create-a-saml-trusted-identity-provider"></a>信頼できる SAML ID プロバイダーを作成する

a。 **Pulse Virtual Traffic Manager アプライアンス管理 UI > [Catalog]\(カタログ\) > [SAML] > [Trusted Identity Providers Catalog]\(信頼できる ID プロバイダー カタログ\)** ページに移動し、 **[Edit]\(編集\)** をクリックします。

![SAML カタログ ページ](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. 新しい信頼できる SAML ID プロバイダーの詳細を追加し、Azure AD エンタープライズ アプリケーションのシングル サインオンの設定ページから情報をコピーして、 **[Create New Trusted Identity Provider]\(信頼できる ID プロバイダーの新規作成\)** をクリックします。

![信頼できる ID プロバイダーを新しく作成する](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* **[Name]\(名前\)** ボックスに、信頼できる ID プロバイダーの名前を入力します。 

* **[Entity_id]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を入力します。  

* **[URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を入力します。 

* Azure portal からダウンロードした **証明書** をメモ帳で開き、その内容を **[Certificate]\(証明書\)** ボックスに貼り付けます。

c. 新しい SAML ID プロバイダーが正常に作成されたことを確認します。 

![信頼できる ID プロバイダーを確認する](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Azure AD Authentication を使用するよう仮想サーバーを構成する

a。 **Pulse Virtual Traffic Manager アプライアンス管理 UI > [Services]\(サービス\) > [Virtual Servers]\(仮想サーバー\)** ページに移動し、前に作成した仮想サーバーの隣の **[Edit]\(編集\)** をクリックします。

![仮想サーバーの編集](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. **[Authentication]\(認証\)** セクションで、 **[Edit]\(編集\)** をクリックします。 

![認証セクション](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. 仮想サーバーに対して次の認証設定を構成します。 

1. 認証 -

    ![仮想サーバーの認証設定](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a。 **[Auth!type]\(認証の種類\)** で、 **[SAML Service Provider]\(SAML サービス プロバイダー\)** を選択します 

    b. 認証の問題のトラブルシューティングを行う場合は **[Auth!verbose]\(認証詳細\)** を [Yes]\(はい\) に設定し、それ以外の場合は既定値の [No]\(いいえ\) をそのまま使用します 

2. 認証セッションの管理 -

    ![認証セッションの管理](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a。 **[Auth!session!cookie_name]\(認証セッション Cookie 名\)** は、既定値の "VS_SamlSP_Auth" をそのまま使用します 

    b. **[auth!session!timeout]\(認証セッション タイムアウト\)** は、既定値の "7200" をそのまま使用します 

    c. 認証の問題のトラブルシューティングを行う場合は **[auth!session!log_external_state]\(認証セッションで外部の状態をログに記録する\)** を [Yes]\(はい\) に設定し、それ以外の場合は既定値の [No]\(いいえ\) をそのまま使用します 

    d. **[auth!session!cookie_attributes]\(認証セッションの Cookie の属性\)** を、"HTTPOnly" に変更します 

3. SAML サービス プロバイダー -

    ![SAML サービス プロバイダー](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a。 **[auth!saml!sp_entity_id]\(認証 SAML SP エンティティ ID\)** ボックスを、Azure AD シングル サインオン構成の識別子 (エンティティ ID) として使用される URL に設定します。 `https://pulseweb.labb.info/saml/metadata` などです。 

    b. **[auth!saml!sp_acs_url]\(認証 SAML SP ACS URL\)** を、Azure AD シングル サインオン構成の応答 URL (Assertion Consumer Service URL) として使用されているものと同じ URL に設定します。 `https://pulseweb.labb.info/saml/consume` などです。 

    c. **[auth!saml!idp]\(認証 SAML IDP\)** で、前のステップで作成した **信頼できる ID プロバイダー** を選択します。 

    d. [auth!saml!time_tolerance]\(認証 SAML 許容時間\) では、既定値の "5" 秒をそのまま使用します。 

    e. [auth!saml!nameid_format]\(認証 SAML 名前 ID 形式\) では、 **[unspecified]\(指定なし\)** を選択します。

    f. ページの下部にある **[Update]\(更新\)** をクリックして、変更を適用します。
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Pulse Secure Virtual Traffic Manager のテスト ユーザーを作成する

このセクションでは、Pulse Secure Virtual Traffic Manager で Britta Simon というユーザーを作成します。 [Pulse Secure Virtual Traffic Manager サポート チーム](mailto:support@pulsesecure.net)と連携し、Pulse Secure Virtual Traffic Manager プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Pulse Secure Virtual Traffic Manager のサインオン URL にリダイレクトされます。 

2. Pulse Secure Virtual Traffic Manager のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネルで [Pulse Secure Virtual Traffic Manager] タイルをクリックすると、Pulse Secure Virtual Traffic Manager のサインオン URL にリダイレクトされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Pulse Secure Virtual Traffic Manager を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。