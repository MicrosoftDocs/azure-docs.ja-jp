---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Maverics Identity Orchestrator SAML Connector の統合 | Microsoft Docs
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
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518930"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Maverics Identity Orchestrator SAML Connector の統合

## <a name="introduction"></a>はじめに

Strata は、認証とアクセス制御のために、オンプレミス アプリケーションを Azure AD と簡単に統合する方法を提供しています。

このガイドでは、Maverics Identity Orchestrator&trade; を次のように構成する方法について説明します。
* 従来のオンプレミス アプリケーションへのログイン時に、オンプレミスの ID システムから Azure AD にユーザーを段階的に移行する。
* CA SiteMinder や Oracle Access Manager など、従来の Web アクセス管理製品からのログイン要求を Azure AD にルーティングする。
* Azure AD に対してユーザーを認証した後、http ヘッダーまたは独自のセッション Cookie を使用して保護されているオンプレミス アプリケーションに対してユーザーを認証する。

Strata は、ID プロバイダーを検出、接続し、それら全体の調整を行って、ハイブリッドおよびマルチクラウド企業向けの分散 ID 管理を作成するために、オンプレミスまたはクラウドにデプロイするソフトウェアを提供しています。

このチュートリアルでは、従来の Web アクセス管理製品 (CA SiteMinder) によって現在保護されているオンプレミス Web アプリケーションを移行して、認証とアクセス制御に Azure AD を使用する方法について説明します。
1. Maverics Identity Orchestrator&trade; をインストールします。
2. エンタープライズ アプリケーションを Azure AD に登録し、SAML ベースの SSO に Maverics Azure AD SAML Zero Code Connector&trade; を使用するように構成します。
3. Maverics を SiteMinder および LDAP ユーザー ストアと統合します。
4. Azure Key Vault を設定し、これをシークレット管理プロバイダーとして使用するように Maverics を構成します。
5. オンプレミスの Java Web アプリケーションへのアクセスを提供するために、Maverics を使用したユーザーの移行とセッションの抽象化を実行します。

インストールと構成のその他の手順については、 https://strata.io/docs を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
- Maverics Identity Orchestrator SAML Connector でのシングル サインオン (SSO) が有効なサブスクリプション。 Maverics ソフトウェアを入手するには、sales@strata.io までお問い合わせください。

## <a name="install-maverics-identity-orchestratortrade"></a>Maverics Identity Orchestrator&trade; をインストールする

Maverics Identity Orchestrator のインストールを開始するには、 https://strata.io/docs のインストール手順を参照してください。

## <a name="system-requirements"></a>システム要件
### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>依存関係
* systemd

## <a name="installation"></a>インストール

1. 最新の Maverics RPM パッケージを入手します。 Maverics ソフトウェアのインストール先となるシステムにパッケージをコピーします。

2. `maverics.rpm` を実際のファイル名に置き換えて、Maverics パッケージをインストールします。

    `sudo rpm -Uvf maverics.rpm`

3. Maverics をインストールすると、`systemd` でサービスとして実行されます。 サービスが実行されていることを確認するには、次のコマンドを実行します。

    `sudo systemctl status maverics`

既定では、Maverics は `/usr/local/bin` ディレクトリにインストールされます。

Maverics をインストールすると、`/etc/maverics` ディレクトリに既定の `maverics.yaml` ファイルが作成されます。 構成を編集して `workflows` と `connectors` を含める前の構成ファイルは次のようになります。

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>構成オプション
### <a name="version"></a>Version
`version` フィールドでは、使用する構成ファイルのバージョンを宣言します。 指定されていない場合は、最新の構成バージョンが使用されます。

```yaml
version: 0.1
```
### <a name="listen-address"></a>Listen Address
`listenAddress` では、Orchestrator がリッスンするアドレスを宣言します。 アドレスのホスト セクションが空白の場合、Orchestrator は、ローカル システムの使用可能なすべてのユニキャストおよびエニーキャスト IP アドレスをリッスンします。 アドレスのポート セクションが空白の場合は、ポート番号が自動的に選択されます。

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls` フィールドでは、トランスポート層セキュリティ オブジェクトのマップを宣言します。 TLS オブジェクトは、コネクタと Orchestrator サーバーで使用できます。 使用可能なすべての TLS オプションについては、`transport` のパッケージのドキュメントをご覧ください。

Microsoft Azure では、SAML ベースの SSO を使用する際に、TLS を介して通信する必要があります。証明書の生成の詳細については、[こちら](https://letsencrypt.org/getting-started/)をご覧ください。

`maverics` キーは、Orchestrator サーバー用に予約されています。 他のすべてのキーは使用可能であり、TLS オブジェクトを特定のコネクタに挿入するために使用できます。

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>インクルード ファイル

`connectors` と `workflows` は、独自の個別の構成ファイルで定義し、次の例のように `includeFiles` を使用して `maverics.yaml` で参照できます。
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

このチュートリアルでは、単一の `maverics.yaml` 構成ファイルを使用します。

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>シークレット プロバイダーとして Azure Key Vault を使用する

### <a name="secret-management"></a>シークレットの管理

Maverics は、シークレットを読み込むために、さまざまなシークレット管理ソリューションと統合できます。 現在統合できるのは、ファイル、Hashicorp Vault、Azure Key Vault などです。 シークレット管理ソリューションが指定されていない場合、Maverics は既定で `maverics.yaml` からプレーンテキストでシークレットを読み込みます。
`maverics.yaml` 構成ファイルで値をシークレットとして宣言するには、シークレットを山かっこで囲みます。

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>ファイル

ファイルからシークレットを読み込むには、次のように指定して、`/etc/maverics/maverics.env` ファイルに環境変数 `MAVERICS_SECRET_PROVIDER` を追加します。

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

次に、Maverics サービスを再起動します: `sudo systemctl restart maverics`

`secrets.yaml` ファイルには、任意の数の `secrets` を入力できます。
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

次の手順は、[Azure portal](https://portal.azure.com) または [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用して、Azure Key Vault を設定する方法を示しています。

1. Azure portal を使用して[ログイン](https://portal.azure.com)します (または CLI コマンドを使用)。
    ```shell
    az login
    ```

2. [新しいコンテナーを作成](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)します (または CLI コマンドを使用)。
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Key Vault にシークレットを追加](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)します (または CLI コマンドを使用)。
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Azure Active Directory にアプリケーションを登録](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)します (または CLI コマンドを使用)。
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [アプリケーションがシークレットを使用することを承認](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)します (または CLI コマンドを使用)。
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Azure Key Vault からシークレットを読み込むには、次のパターンを使用して、azure-credentials.json ファイルにある資格情報で `/etc/maverics/maverics.env` ファイルに環境変数 `MAVERICS_SECRET_PROVIDER` を設定します: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

次に、Maverics サービスを再起動します: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>SAML ベースの SSO のために Azure AD でアプリケーションを構成する

1. ご自分の Azure Active Directory テナントで、`Enterprise applications` に移動し、`Maverics Identity Orchestrator SAML Connector` を検索して選択します。

2. Maverics Identity Orchestrator SAML Connector の [プロパティ] ページで、`User assignment required?` を [いいえ] に設定して、新しく移行したユーザーがアプリケーションを使用できるようにします。

3. Maverics Identity Orchestrator SAML Connector の [概要] ページで、`Setup single sign-on` を選択してから、`SAML` を選択します。

4. Maverics Identity Orchestrator SAML Connector の [SAML ベースのサインオン] で、[基本的な SAML 構成] を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

5. `https://<SUBDOMAIN>.maverics.org` のパターンで URL を入力して、`Entity ID` を設定します。 `Entity ID` は、テナント内のアプリ間で一意である必要があります。 ここで入力した値を保存して、Maverics の構成に含めます。

6. `https://<AZURECOMPANY.COM>/<MY_APP>/` のパターンを使用して、応答 URL を設定します。 

7. `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` のパターンを使用してサインオン URL を設定し、[保存] をクリックします。

8. [SAML 署名証明書] セクションに移動します。コピー ボタンをクリックしてアプリのフェデレーション メタデータ URL をコピーし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Maverics Identity Orchestrator Azure AD SAML Connector の構成

Maverics Identity Orchestrator Azure AD Connector では、以下がサポートされています。 
- OpenID Connect
- SAML Connect 

1. SAML ベースの SSO を有効にするには、`authType: saml` を設定します。

1. `samlMetadataURL` の値を作成します: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. 次に、Azure の資格情報でログインした後にアプリでのリダイレクト先となる URL を定義します。
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. 前に構成した EntityID から値をコピーします: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Azure AD で SAML 応答の POST に使用される応答 URL から値をコピーします。
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. [OpenSSL ツール](https://www.openssl.org/source/)を使用して、Maverics Identity Orchestrator&trade; セッション情報の保護に使用される JWT 署名キーを生成します。

    ```shell 
    openssl rand 64 | base64
    ```
1. `jwtSigningKey` 構成プロパティに応答をコピーします: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>属性と属性マッピング
属性マッピングは、ユーザーがプロビジョニングされるときに、オンプレミスのソース ユーザー ディレクトリから Azure AD へのユーザー属性のマッピングを定義するために使用されます。

要求でアプリケーションに返されるか、セッション Cookie に渡されるか、または http ヘッダー変数でアプリケーションに渡されるユーザー データが属性によって決定されます。

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Maverics Identity Orchestrator Azure AD SAML Connector の yaml を構成する

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

## <a name="migrate-users-to-azure-ad"></a>ユーザーを Azure AD に移行する

この構成に従って、CA SiteMinder、Oracle Access Manager、IBM Tivoli などの Web アクセス管理製品、LDAP ディレクトリ、または SQL データベースからユーザーを段階的に移行します。

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>ユーザーを作成するために Azure AD でアプリケーションのアクセス許可を構成する

1. Azure Active Directory テナントで、`App registrations` に移動し、[Maverics Identity Orchestrator SAML Connector] アプリケーションを選択します。

2. Maverics Identity Orchestrator SAML Connector の [証明書とシークレット] で、`New client secret` を選択し、有効期限オプションを選択します。 コピー ボタンをクリックしてシークレットをコピーし、コンピューターに保存します。

3. Maverics Identity Orchestrator SAML Connector の [API のアクセス許可] で、`Add permission` を選択し、[API アクセス許可の要求] で、`Microsoft Graph`、`Application permissions` の順に選択します。 次の画面で、`User.ReadWrite.All` を選択してから `Add permissions` を選択します。 これにより、[API のアクセス許可] に戻るので、そこで `Grant admin consent` を選択します。


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>ユーザーの移行用に Maverics Identity Orchestrator SAML Connector の yaml を構成する

ユーザー移行ワークフローを有効にするには、この追加のプロパティを構成ファイルに含めます。
1. Azure Graph の URL を設定します: `graphURL: https://graph.microsoft.com`
1. `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` のパターンに従って、OAuth トークン URL を設定します。
1. 前に生成されたクライアント シークレットを設定します: `oauthClientSecret: <CLIENT SECRET>`


Maverics Identity Orchestrator Azure AD Connector の最終的な構成は次のようになります。
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

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>SiteMinder 用の Maverics Zero Code Connector&trade; を構成する

SiteMinder コネクタは、ユーザーを Azure AD に移行し、新しく作成された Azure AD の ID と資格情報を使用して、SiteMinder によって保護されている従来のオンプレミス アプリケーションにユーザーをログインさせるために使用されます。

このチュートリアルでは、SiteMinder は、フォームベース認証と `SMSESSION` Cookie を使用してレガシ アプリケーションを保護するように構成されています。 http ヘッダーを介して認証とセッションを使用するアプリと統合するには、ヘッダー エミュレーション構成をコネクタに追加する必要があります。

この例では、`username` 属性を `SM_USER` http ヘッダーにマップします。
```yaml
  headers:
    SM_USER: username
```

`proxyPass` を、要求がプロキシ処理される場所に設定します。 通常、これは保護されたアプリケーションのホストです。

`loginPage` は、認証のためにユーザーをリダイレクトするときに、SiteMinder によって現在使用されているログイン フォームの URL と一致する必要があります。

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>LDAP 用の Maverics Zero Code Connector&trade; を構成する

アプリケーションが SiteMinder などの WAM 製品によって保護されている場合、通常、ユーザーの ID と属性は LDAP ディレクトリに格納されます。

このコネクタ構成は、移行ワークフローで正しいユーザー プロファイル情報を収集し、対応するユーザーを Azure AD に作成できるように、SiteMinder のユーザー ストアとして構成された LDAP ディレクトリに接続する方法を示しています。

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

## <a name="configure-the-migration-workflow"></a>移行ワークフローを構成する

移行ワークフロー構成では、Maverics が SiteMinder または LDAP から Azure AD にユーザーを移行する方法を決定します。

このワークフロー:
- SiteMinder コネクタを使用して、SiteMinder ログインをプロキシ経由にします。 ユーザー資格情報は、SiteMinder の認証によって検証され、ワークフローの後続のステップに渡されます。
- SiteMinder ユーザー ストアからユーザー プロファイル属性を取得します。
- Microsoft Graph API に要求して、Azure AD テナントにユーザーを作成します。

手順:
1. ワークフローに名前を付けます (例: SiteMinder から Azure AD Migration)。
2. `endpoint` を指定します。これは、ワークフローが公開される HTTP パスであり、要求に応答してそのワークフローの `actions` をトリガーします。 通常、`endpoint` はプロキシ処理されるアプリに対応します (例: `/my_app`)。 値には、先頭と末尾の両方のスラッシュを含める必要があります。
3. ワークフローに適切な `actions` を追加します。
    - SiteMinder コネクタの `login` メソッドを定義します。 connector 値は、コネクタ構成の name 値と一致する必要があります。
     - LDAP コネクタの `getprofile` メソッドを定義します。
     - AzureAD コネクタの `createuser` を定義します。

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

1. Maverics サービスがまだ実行されていない場合は、`sudo systemctl start maverics` コマンドを実行して開始します。

2. プロキシ処理されたログイン URL (`http://host.company.com/my_app`) に移動します。
3. SiteMinder によって保護されているアプリケーションへのログインに使用されるユーザー資格情報を指定します。
4. [ホーム]、[ユーザー]、[すべてのユーザー] の順に移動して、ユーザーが Azure AD テナントに作成されていることを確認します。  

## <a name="configure-the-session-abstraction-workflow"></a>セッション抽象化ワークフローを構成する

セッション抽象化ワークフローでは、従来のオンプレミス Web アプリケーションの認証とアクセス制御を Azure AD に移行します。

Azure コネクタは、セッションが存在しないという想定で、`login` メソッドを使用してユーザーをログイン URL にリダイレクトします。

認証が完了すると、結果として作成されたセッション トークンが Maverics に渡されます。SiteMinder コネクタの `emulate` メソッドを使用して、Cookie ベースのセッションやヘッダー ベースのセッションがエミュレートされ、アプリケーションに必要な追加の属性で要求が装飾されます。

1. ワークフローに名前を付けます (例: SiteMinder Session Abstraction)。
2. `endpoint` を指定します。これはプロキシ処理されるアプリに対応します。 値には、先頭と末尾の両方のスラッシュを含める必要があります (例: `/my_app/`)。
3. ワークフローに適切な `actions` を追加します。
    - Azure コネクタの `login` メソッドを定義します。 `connector` 値は、コネクタ構成の `name` 値と一致する必要があります。
    - SiteMinder コネクタの `emulate` メソッドを定義します。

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

1. プロキシ処理されたアプリケーション URL (`https://<AZURECOMPANY.COM>/<MY_APP>`) に移動します。 ユーザーは、プロキシ処理されたログイン ページにリダイレクトされます。
2. Azure AD ユーザーの資格情報を入力します。
3. ユーザーは、SiteMinder によって直接認証されたかのようにアプリケーションにリダイレクトされます。
