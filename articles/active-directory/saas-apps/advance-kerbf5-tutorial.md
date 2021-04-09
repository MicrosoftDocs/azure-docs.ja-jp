---
title: チュートリアル:Azure Active Directory シングル サインオンと F5 の統合 | Microsoft Docs
description: この記事では、F5 と Azure Active Directory (Azure AD) を統合するために必要な手順について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: db8977e484e8d1f2cf4b30427d47ba45969f2147
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654461"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>チュートリアル:Azure Active Directory (AD) シングル サインオン (SSO) と F5 の統合

このチュートリアルでは、F5 と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と F5 を統合すると、次のことができます。

* F5 にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して F5 に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* F5 でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

F5 では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

F5 SSO は、次の 3 つの異なる方法で構成できます。

- [Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する](headerf5-tutorial.md)

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>ギャラリーからの F5 の追加

Azure AD への F5 の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に F5 を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**F5**」と入力します。
1. 結果のパネルから **[F5]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>F5 の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、F5 に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと F5 の関連ユーザーとの間にリンク関係を確立する必要があります。

F5 に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[F5 SSO の構成](#configure-f5-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[F5 テスト ユーザーの作成](#create-f5-test-user)** - F5 で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **F5** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<YourCustomFQDN>.f5.com/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<YourCustomFQDN>.f5.com/` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<YourCustomFQDN>.f5.com/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[F5 クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[F5 のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に F5 へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[F5]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-f5-sso"></a>F5 SSO の構成

- [ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する](headerf5-tutorial.md)

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Advanced Kerberos アプリケーション用に F5 シングル サインオンを構成する

1. 新しい Web ブラウザー ウィンドウを開き、F5 (Advanced Kerberos) 企業サイトに管理者としてサインインして、次の手順を実行します。

1. 後で設定プロセスで使用するメタデータ証明書を F5 (Advanced Kerberos) にインポートする必要があります。 **[System]\(システム\) > [Certificate Management]\(証明書の管理\) > [Traffic Certificate Management]\(トラフィック証明書の管理\) > [SSL Certificate List]\(SSL 証明書の一覧\)** の順に移動します。 右隅にある **[Import]\(インポート\)** をクリックします。

    ![メタデータ証明書をインポートするための [インポート] ボタンが強調表示されたスクリーンショット。](./media/advance-kerbf5-tutorial/configure01.png)
 
1. SAML IDP を設定するには、 **[Access]\(アクセス\) > [Federation]\(フェデレーション\) > [SAML Service Provider]\(SAML サービス プロバイダー\) > [Create]\(作成\) > [From Metadata]\(メタデータから\)** の順に移動します。

    ![メタデータから SAML IDP を作成する方法が強調表示されたスクリーンショット。](./media/advance-kerbf5-tutorial/configure02.png)

    ![[Create New SAML IdP Connector]\(新しい SAML IdP コネクタの作成\) 画面を示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 (Advanced Kerberos) の構成](./media/advance-kerbf5-tutorial/configure04.png)

    ![[Single Sign On Service Settings]\(シングル サインオン サービス設定\) 画面を示すスクリーンショット。 ](./media/advance-kerbf5-tutorial/configure05.png)
 
1. タスク 3 からアップロードした証明書を指定します

    ![[Edit SAML IdP Connector]\(SAML IdP コネクタの編集\) 画面を示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure06.png)

    ![[Single Logout Service Settings]\(シングル ログアウト サービス設定\) 画面を示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure07.png)

 1. SAML SP を設定するには、 **[Access]\(アクセス\) > [Federation]\(フェデレーション\) > [SAML Service Federation]\(SAML サービス フェデレーション\) > [Local SP Services]\(ローカル SP サービス\) > [Create]\(作成\)** の順に移動します。

    ![ローカル SP サービスを作成する画面を示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure08.png)
 
1. **[OK]** をクリックします。

1. SP 構成を選択し、 **[Bind/UnBind IdP Connectors]\(IdP コネクタのバインドまたはバインド解除\)** をクリックします。

     ![SAML サービス プロバイダーを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. **[Add New Row]\(新しい行の追加\)** をクリックし、前の手順で作成した **外部 IdP コネクタ** を選択します。

    ![[Add New Row]\(新しい行の追加\) ボタンが強調表示されているスクリーンショット。](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Kerberos SSO を構成する場合は、 **[Access]\(アクセス\) > [Single Sign-on]\(シングル サインオン\) > [Kerberos]**

    >[!Note]
    >Kerberos 委任アカウントを作成し、指定する必要があります。 KCD セクションを参照してください (変数リファレンスについては、付録を参照してください)

    •   ユーザー名のソース  `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    •   ユーザー領域のソース  `session.logon.last.domain`

    ![[Access]\(アクセス\) > [Single Sign On]\(シングル サインオン\) が強調表示されているスクリーンショット。](./media/advance-kerbf5-tutorial/configure11.png)

1. アクセス プロファイルを構成する場合は、 **[Access]\(アクセス\) > [Profile/Policies]\(プロファイルまたはポリシー\) > [Access Profile (per session policies)]\(アクセス プロファイル (セッション ポリシーごと)\)** 。

    ![[Profiles/Policies]\(プロファイルおよびポリシー\) メニュー オプションの下の [Properties]\(プロパティ\) タブが強調表示されているスクリーンショット。](./media/advance-kerbf5-tutorial/configure12.png)

    ![[SSO/Auth Domains]\(SSO および認証ドメイン\) タブを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure13.png)

    ![[Access Policy]\(アクセス ポリシー\) タブを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure14.png)

    ![[Access Policy]\(アクセス ポリシー\) の [Properties]\(プロパティ\) タブを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure15.png)

    ![[Variable Assign]\(変数割り当て\) のプロパティを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![[AD Query]\(AD クエリ\) のプロパティを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![[Branch Rules]\(分岐の規則\) タブと [Check Account]\(アカウントの確認\) 規則を示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure18.png)

    ![カスタム変数およびカスタム式のテキスト ボックスを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![[SSO Token Name]\(SSO トークン名\) および [SSO Token Password]\(SSO トークン パスワード\) フィールドの値を示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. 新しいノードを追加する場合は、 **[Local Traffic]\(ローカル トラフィック\) > [Nodes]\(ノード\) > [Node List]\(ノードの一覧\) > [+]** 。

    ![[Local Traffic]\(ローカル トラフィック\) > [Nodes]\(ノード\) が強調表示されているスクリーンショット。](./media/advance-kerbf5-tutorial/configure21.png)
 
1. 新しいプールを追加するには、 **[Local Traffic]\(ローカル トラフィック\) > [Pools]\(プール\) > [Pool List]\(プールの一覧\) > [Create]\(作成\)** の順に移動します。

     ![[Local Traffic]\(ローカル トラフィック\) > [Pools]\(プール\) が強調表示されているスクリーンショット。](./media/advance-kerbf5-tutorial/configure22.png)

 1. 新しい仮想サーバーを作成するには、 **[Local Traffic]\(ローカル トラフィック\) > [Virtual Servers]\(仮想サーバー\) > [Virtual Server List]\(仮想サーバーの一覧\) > [+]** の順に移動します。

    ![[Local Traffic]\(ローカル トラフィック\) > [Virtual Servers]\(仮想サーバー\) が強調表示されているスクリーンショット。](./media/advance-kerbf5-tutorial/configure23.png)

1. 前の手順で作成したアクセス プロファイルを指定します。

    ![作成したアクセス プロファイルを指定する場所を示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Kerberos 委任の設定 

>[!Note]
>詳細については、[こちら](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)を参照してください

* **ステップ 1:委任アカウントの作成**

    * 例
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **手順 2:SPN の設定 (APM 委任アカウント上で)**

    *  例
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **ステップ 3:SPN 委任 (App Service アカウントに対して)**

    * F5 委任アカウントに適切な委任を設定します。
    * 次の例では、FRP-App1.superdemo.live アプリの KCD に対して APM 委任アカウントが構成されています。

        ![[APM Delegation Account Properties]\(APM 委任アカウント プロパティ\) > [Delegation]\(委任\) タブを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure25.png)

1. 上記の参照ドキュメントの[ここ](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)に記載されているように詳細を指定します

1. 付録 - SAML – F5 BIG-IP 変数マッピングを以下に示します。

    ![[Overview]\(概要\) > [Active Sessions]\(アクティブなセッション\) タブを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure26.png)

    ![変数とセッション キーを示すスクリーンショット。](./media/advance-kerbf5-tutorial/configure27.png) 

1. 既定の SAML 属性の完全な一覧を次に示します。 GivenName は、次の文字列を使用して表されます。
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| Session | 属性 |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>F5 テスト ユーザーの作成

このセクションでは、F5 で B.Simon というユーザーを作成します。 [F5 クライアント サポート チーム](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)と連携して、F5 プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [F5] タイルをクリックすると、SSO を設定した F5 に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で F5 を試す](https://aad.portal.azure.com/)

- [ヘッダー ベースのアプリケーション用に F5 シングル サインオンを構成する](headerf5-tutorial.md)

- [Kerberos アプリケーション用に F5 シングル サインオンを構成する](kerbf5-tutorial.md)

- [F5 BIG-IP APM と Azure AD の統合による安全なハイブリッド アクセス](../manage-apps/f5-aad-integration.md)

- [セキュリティで保護されたハイブリッド アクセスのために F5 BIG-IP Virtual Edition VM を Azure IaaS にデプロイするチュートリアル](../manage-apps/f5-bigip-deployment-guide.md)

- [パスワードレス VPN 用の Azure Active Directory シングル サインオンと F5 BIG-IP の統合](../manage-apps/f5-aad-password-less-vpn.md)