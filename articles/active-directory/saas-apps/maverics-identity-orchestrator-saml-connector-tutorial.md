---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Maverics Identity Orchestrator SAML Connector を統合する | Microsoft Docs
description: Azure Active Directory と Maverics Identity Orchestrator SAML Connector の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: 116ee7c8db3070a667c21a052bec739fd397a2dd
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825564"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>チュートリアル:Azure AD シングル サインオンと Maverics Identity Orchestrator SAML Connector を統合する

Strata は、認証とアクセス制御のために、オンプレミス アプリケーションを Azure Active Directory (Azure AD) と簡単に統合する方法を提供しています。

この記事では、Maverics Identity Orchestrator を次のように構成する方法について説明します。
* 従来のオンプレミス アプリケーションへのログイン時に、オンプレミスの ID システムから Azure AD にユーザーを段階的に移行する。
* CA SiteMinder や Oracle Access Manager など、レガシの Web アクセス管理製品からのログイン要求を Azure AD にルーティングする。
* Azure AD に対してユーザーを認証した後、HTTP ヘッダーまたは独自のセッション Cookie を使用して保護されているオンプレミス アプリケーションに対してユーザーを認証する。

Strata には、オンプレミスまたはクラウドにデプロイできるソフトウェアが用意されています。 これは ID プロバイダーを検出、接続し、それら全体の調整を行って、ハイブリッドおよびマルチクラウド企業向けの分散 ID 管理を作成するのに役立ちます。

このチュートリアルでは、レガシの Web アクセス管理製品 (CA SiteMinder) によって現在保護されているオンプレミス Web アプリケーションを移行して、認証とアクセス制御に Azure AD を使用する方法について説明します。 基本的な手順は次のとおりです。
1. Maverics Identity Orchestrator をインストールします。
2. エンタープライズ アプリケーションを Azure AD に登録し、SAML ベースのシングル サインオン (SSO) に Maverics Azure AD SAML Zero Code Connector を使用するように構成します。
3. Maverics を SiteMinder および Lightweight Directory Access Protocol (LDAP) ユーザー ストアと統合します。
4. Azure Key Vault を設定し、これをシークレット管理プロバイダーとして使用するように Maverics を構成します。
5. オンプレミスの Java Web アプリケーションへのアクセスを提供するために、Maverics を使用したユーザーの移行とセッションの抽象化を実行します。

インストールと構成のその他の手順については、[Strata の Web サイト](https://strata.io/docs)を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
- Maverics Identity Orchestrator SAML Connector での SSO が有効なサブスクリプション。 Maverics ソフトウェアを入手するには、[Strata の営業担当者](mailto:sales@strata.io)にお問い合わせください。

## <a name="install-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator をインストールする

Maverics Identity Orchestrator のインストールを開始するには、[インストール手順](https://strata.io/docs)を参照してください。

### <a name="system-requirements"></a>システム要件
* サポートされるオペレーティング システム
  * RHEL 7+
  * CentOS 7+

* 依存関係
  * systemd

### <a name="installation"></a>インストール

1. 最新の Maverics Redhat Package Manager (RPM) パッケージを入手します。 Maverics ソフトウェアのインストール先となるシステムにパッケージをコピーします。

2. `maverics.rpm` を実際のファイル名に置き換えて、Maverics パッケージをインストールします。

    `sudo rpm -Uvf maverics.rpm`

3. Maverics をインストールすると、`systemd` でサービスとして実行されます。 サービスが実行されていることを確認するには、次のコマンドを実行します。

    `sudo systemctl status maverics`

既定では、Maverics は */usr/local/bin* ディレクトリにインストールされます。

Maverics をインストールすると、既定の *maverics. yaml* ファイルが */etc/maverics* ディレクトリに作成されます。 構成を編集して `workflows` と `connectors` を含める前の構成ファイルは次のようになります。

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>構成オプション
### <a name="version"></a>Version
`version` フィールドでは、使用する構成ファイルのバージョンを宣言します。 バージョンが指定されていない場合は、最新の構成バージョンが使用されます。

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` では、Orchestrator がリッスンするアドレスを宣言します。 アドレスのホスト セクションが空白の場合、Orchestrator は、ローカル システムの使用可能なすべてのユニキャストおよびエニーキャスト IP アドレスをリッスンします。 アドレスのポート セクションが空白の場合は、ポート番号が自動的に選択されます。

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls` フィールドでは、トランスポート層セキュリティ (TLS) オブジェクトのマップを宣言します。 TLS オブジェクトは、コネクタと Orchestrator サーバーで使用できます。 使用可能なすべての TLS オプションについては、`transport` のパッケージのドキュメントをご覧ください。

SAML ベースの SSO を使用している場合、Microsoft Azure は TLS 経由の通信を必要とします。 証明書の生成の詳細については、[Let's Encrypt Web サイト](https://letsencrypt.org/getting-started/)を参照してください。

`maverics` キーは、Orchestrator サーバー用に予約されています。 他のすべてのキーは使用可能であり、TLS オブジェクトを特定のコネクタに挿入するために使用できます。

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>インクルード ファイル

`connectors` と `workflows` は、独自の個別の構成ファイルで定義し、次の例のように `includeFiles` を使用して *maverics.yaml* ファイル内で参照できます。

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

このチュートリアルでは、単一の *maverics.yaml* 構成ファイルを使用します。

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>シークレット プロバイダーとして Azure Key Vault を使用する

### <a name="manage-secrets"></a>シークレットを管理する

シークレットを読み込むために、Maverics をさまざまなシークレット管理ソリューションと統合できます。 現在統合できるのは、ファイル、Hashicorp Vault、Azure Key Vault などです。 シークレット管理ソリューションが指定されていない場合、Maverics は既定で *maverics.yaml* ファイルからプレーンテキストでシークレットを読み込みます。

*maverics.yaml* 構成ファイルで値をシークレットとして宣言するには、シークレットを山かっこで囲みます。

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>ファイルからシークレットを読み込む

1. ファイルからシークレットを読み込むには、以下を使用して、 */etc/maverics/maverics.env* ファイルに環境変数 `MAVERICS_SECRET_PROVIDER` を追加します。

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. 以下を実行して、Maverics サービスを再起動します。

   `sudo systemctl restart maverics`

*secrets.yaml* ファイルには、任意の数の `secrets` を入力できます。

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Azure Key Vault を設定する

Azure portal または Azure CLI のいずれかを使用して、Azure Key Vault を設定できます。

**Azure Portal の使用**
1. [Azure portal](https://portal.azure.com) にサインインします。
1. [新しいキー コンテナーを作成します](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)。
1. [キー コンテナーにシークレットを追加します](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)。
1. [アプリケーションを Azure AD に登録します](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。
1. [アプリケーションによるシークレットの使用を承認します](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)。

**Azure CLI の使用**

1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を開き、次のコマンドを入力します。

    ```shell
    az login
    ```

1. 次のコマンドを実行して、新しいキー コンテナーを作成します。
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. 次のコマンドを実行して、キー コンテナーにシークレットを追加します。
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. 次のコマンドを実行して、Azure AD にアプリケーションを登録します。
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. 次のコマンドを実行して、アプリケーションによるシークレットの使用を承認します。
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Azure Key Vault からシークレットを読み込むには、次の形式で、*azure-credentials.json* ファイルにある資格情報を使用して */etc/maverics/maverics.env* ファイルに環境変数 `MAVERICS_SECRET_PROVIDER` を設定します。
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Maverics サービスを再起動します: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>SAML ベースの SSO のために Azure AD でアプリケーションを構成する

1. Azure AD テナントで、 **[エンタープライズ アプリケーション]** にアクセスし、**Maverics Identity Orchestrator SAML Connector** を検索して選択します。

1. Maverics Identity Orchestrator SAML Connector の **[プロパティ]** ペインで、 **[ユーザーの割り当てが必要ですか?]** を **[いいえ]** に設定して、新しく移行したユーザーがアプリケーションを使用できるようにします。

1. Maverics Identity Orchestrator SAML Connector の **[概要]** ペインで、 **[シングル サインオンを設定する]** を選択してから、 **[SAML]** を選択します。

1. Maverics Identity Orchestrator SAML Connector の **[SAML ベースのサインオン]** ペインで、 **[編集]** (鉛筆アイコン) ボタンを選択して **[基本的な SAML 構成]** を編集します。

   !["基本的な SAML 構成" 編集ボタンのスクリーンショット。](common/edit-urls.png)

1. 次の形式で URL を入力して **[エンティティ ID]** を入力します: `https://<SUBDOMAIN>.maverics.org`。 [エンティティ ID] は、テナント内のアプリ間で一意である必要があります。 ここで入力した値を保存して、Maverics の構成に含めます。

1. 次の形式で **[応答 URL]** を入力します: `https://<AZURECOMPANY.COM>/<MY_APP>/`。 

1. 次の形式で **[サインオン URL]** を入力します: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>`。 

1. **[保存]** を選択します。

1. **[SAML 署名証明書]** セクションで、 **[コピー]** ボタンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーし、お使いのコンピューターに保存します。

    ![[SAML 署名証明書] コピー ボタンのスクリーンショット。](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Maverics Identity Orchestrator Azure AD SAML Connector を構成する

Maverics Identity Orchestrator Azure AD Connector では、OpenID Connect と SAML Connect がサポートされています。 コネクタを構成するには、以下を実行します。 

1. SAML ベースの SSO を有効にするには、`authType: saml` を設定します。

1. 次の形式で `samlMetadataURL` の値を作成します: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`。

1. ユーザーが Azure の資格情報でログインした後にアプリでのリダイレクト先となる URL を定義します。 次の形式を使用します: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`。

1. 前に構成した EntityID から値をコピーします: `samlEntityID: https://<SUBDOMAIN>.maverics.org`。

1. Azure AD で SAML 応答の POST に使用される応答 URL から値をコピーします: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. [OpenSSL ツール](https://www.openssl.org/source/)を使用して、Maverics Identity Orchestrator セッション情報の保護に使用される JSON Web トークン (JWT) 署名キーを生成します。

    ```shell 
    openssl rand 64 | base64
    ```
1. `jwtSigningKey` 構成プロパティに応答をコピーします: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`。

## <a name="attributes-and-attribute-mapping"></a>属性と属性マッピング
属性マッピングは、ユーザーがセットアップされた後でオンプレミスのソース ユーザー ディレクトリから Azure AD テナントへのユーザー属性のマッピングを定義するために使用されます。

要求でアプリケーションに返されるか、セッション Cookie に渡されるか、または HTTP ヘッダー変数でアプリケーションに渡されるユーザー データが、属性によって決定されます。

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Maverics Identity Orchestrator Azure AD SAML Connector の YAML ファイルを構成する

Maverics Identity Orchestrator Azure AD Connector の構成は次のようになります。

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>ユーザーを Azure AD テナントに移行する

この構成に従って、CA SiteMinder、Oracle Access Manager、IBM Tivoli などの Web アクセス管理製品からユーザーを段階的に移行します。 Lightweight Directory Access Protocol (LDAP) ディレクトリまたは SQL データベースから移行することもできます。

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>ユーザーを作成するために Azure AD でアプリケーションのアクセス許可を構成する

1. Azure AD テナントで、`App registrations` に移動し、 **[Maverics Identity Orchestrator SAML Connector]** アプリケーションを選択します。

1. **Maverics Identity Orchestrator SAML Connector の [証明書とシークレット]** ペインで、`New client secret` を選択し、有効期限オプションを選択します。 **[コピー]** ボタンを選択してシークレットをコピーし、お使いのコンピューターに保存します。

1. **Maverics Identity Orchestrator SAML Connector の [API のアクセス許可]** ペインで、 **[アクセス許可の追加]** を選択し、 **[API アクセス許可の要求]** ペインで、 **[Microsoft Graph]** および **[アプリケーションのアクセス許可]** を選択します。 

1. 次の画面で、 **[User.ReadWrite.All]** を選択し、 **[アクセス許可の追加]** を選択します。 

1. **[API のアクセス許可]** ペインに戻り、 **[管理者の同意を与える]** を選択します。

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>ユーザーの移行用に Maverics Identity Orchestrator SAML Connector の YAML ファイルを構成する

ユーザー移行ワークフローを有効にするには、これらの追加のプロパティを構成ファイルに追加します。
1. 次の形式で **Azure Graph URL** を入力します: `graphURL: https://graph.microsoft.com`。
1. 次の形式で **OAuth トークン URL** を入力します: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`。
1. 次の形式で、以前に生成したクライアント シークレットを入力します: `oauthClientSecret: <CLIENT SECRET>`。


Maverics Identity Orchestrator Azure AD Connector の最終的な構成ファイルは次のようになります。

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>SiteMinder 用の Maverics Zero Code Connector を構成する

SiteMinder コネクタを使用して、ユーザーを Azure AD テナントに移行します。 新しく作成した Azure AD の ID と資格情報を使用して、SiteMinder によって保護されているレガシ オンプレミス アプリケーションにユーザーをログインします。

このチュートリアルでは、SiteMinder は、フォームベース認証と `SMSESSION` Cookie を使用してレガシ アプリケーションを保護するように構成されています。 HTTP ヘッダーを介して認証とセッション情報を使用するアプリと統合するには、ヘッダー エミュレーション構成をコネクタに追加する必要があります。

この例では、`username` 属性を `SM_USER` HTTP ヘッダーにマップします。

```yaml
  headers:
    SM_USER: username
```

`proxyPass` を、要求がプロキシ処理される場所に設定します。 通常、この場所は保護されたアプリケーションのホストです。

`loginPage` は、認証のためにユーザーをリダイレクトするときに、SiteMinder によって現在使用されているログイン フォームの URL と一致する必要があります。

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>LDAP 用の Maverics Zero Code Connector を構成する

アプリケーションが SiteMinder などの Web アクセス管理 (WAM) 製品によって保護されている場合、通常、ユーザーの ID と属性は LDAP ディレクトリに格納されます。

このコネクタ構成は、LDAP ディレクトリに接続する方法を示しています。 このコネクタは、移行ワークフローで正しいユーザー プロファイル情報を収集し、対応するユーザーを Azure AD に作成できるように、SiteMinder のユーザー ストアとして構成されます。

* `baseDN` では、LDAP 検索の実行対象となるディレクトリ内の場所を指定します。

* `url` は、接続先の LDAP サーバーのアドレスとポートです。

* `serviceAccountUsername` は、LDAP サーバーへの接続に使用されるユーザー名です。通常はバインド DN として表されます (例: `CN=Directory Manager`)。

* `serviceAccountPassword` は、LDAP サーバーへの接続に使用するパスワードです。 この値は、以前に構成した Azure Key Vault インスタンスに格納されます。  

* `userAttributes` では、クエリの対象となるユーザー関連の属性のリストを定義します。 これらの属性は、対応する Azure AD 属性に後でマップされます。

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>移行ワークフローを構成する

移行ワークフロー構成では、Maverics が SiteMinder または LDAP から Azure AD にユーザーを移行する方法を決定します。

このワークフロー:
- SiteMinder コネクタを使用して、SiteMinder ログインをプロキシ経由にします。 ユーザー資格情報は、SiteMinder の認証によって検証され、ワークフローの後続のステップに渡されます。
- SiteMinder ユーザー ストアからユーザー プロファイル属性を取得します。
- Microsoft Graph API に要求して、Azure AD テナントにユーザーを作成します。

移行ワークフローを構成するには、次の手順を実行します。

1. ワークフローに名前を付けます (例: **SiteMinder to Azure AD Migration**)。
1. `endpoint` を指定します。これは、ワークフローが公開される HTTP パスであり、要求に応答してそのワークフローの `actions` をトリガーします。 通常、`endpoint` はプロキシ処理されるアプリに対応します (例: `/my_app`)。 値には、先頭と末尾の両方のスラッシュを含める必要があります。
1. ワークフローに適切な `actions` を追加します。

   a. SiteMinder コネクタの `login` メソッドを定義します。 connector 値は、コネクタ構成の name 値と一致する必要があります。

   b. LDAP コネクタの `getprofile` メソッドを定義します。

   c.  AzureAD コネクタの `createuser` メソッドを定義します。

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>移行ワークフローを検証する

1. Maverics サービスがまだ実行されていない場合は、 コマンドを実行して開始します。 

   `sudo systemctl start maverics`

1. プロキシ処理されたログイン URL (`http://host.company.com/my_app`) に移動します。
1. SiteMinder によって保護されているアプリケーションへのログインに使用されるユーザー資格情報を指定します。
4. **[ホーム]**  >  **[ユーザー]、[すべてのユーザー]** の順に移動して、ユーザーが Azure AD テナントに作成されていることを確認します。  

### <a name="configure-the-session-abstraction-workflow"></a>セッション抽象化ワークフローを構成する

セッション抽象化ワークフローでは、レガシのオンプレミス Web アプリケーションの認証とアクセス制御を Azure AD テナントに移行します。

Azure コネクタは、セッションが存在しないという想定で、`login` メソッドを使用してユーザーをログイン URL にリダイレクトします。

認証が完了すると、結果として作成されたセッション トークンが Maverics に渡されます。 SiteMinder コネクタの `emulate` メソッドを使用して、Cookie ベースのセッションやヘッダー ベースのセッションがエミュレートされ、アプリケーションに必要な追加の属性で要求が装飾されます。

1. ワークフローに名前を付けます (例: **SiteMinder Session Abstraction**)。
1. `endpoint` を指定します。これはプロキシ処理されるアプリに対応します。 値には、先頭と末尾の両方のスラッシュを含める必要があります (例: `/my_app/`)。
1. ワークフローに適切な `actions` を追加します。

   a. Azure コネクタの `login` メソッドを定義します。 `connector` 値は、コネクタ構成の `name` 値と一致する必要があります。

   b. SiteMinder コネクタの `emulate` メソッドを定義します。

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>セッション抽象化ワークフローを検証する

1. プロキシ処理されたアプリケーション URL (`https://<AZURECOMPANY.COM>/<MY_APP>`) に移動します。 
    
    プロキシ処理されたログイン ページにリダイレクトされます。

1. Azure AD ユーザーの資格情報を入力します。

   SiteMinder によって直接認証されたかのようにアプリケーションにリダイレクトされます。
