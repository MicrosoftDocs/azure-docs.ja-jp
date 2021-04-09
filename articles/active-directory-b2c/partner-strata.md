---
title: Strata を使用するように Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: ユーザー確認のために Azure AD B2C 認証を whoIam と統合する方法について説明します
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 69355b58e36f435b5b5dbe94af72d55d9806b156
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557172"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>オンプレミスのアプリケーションを保護するために、Strata を使用して Azure AD B2C を拡張するためのチュートリアル

このサンプル チュートリアルでは、Azure Active Directory (AD) B2C を Strata の [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) と統合する方法について説明します。
Maverics Identity Orchestrator を使用すると、オンプレミスのアプリケーションを保護するように Azure AD B2C を拡張できます。 任意の ID システムに接続して、ユーザーと資格情報が透過的に移行され、ポリシーと構成が同期され、認証とセッション管理が抽象化されます。 Strata を使用すると、アプリケーションを書き直すことなく、レガシから Azure AD B2C に迅速に移行できます。 このソリューションには次の利点があります。

- **オンプレミスのハイブリッド アプリへのお客様によるシングル サインオン (SSO)** :Azure AD B2C では、お客様が Maverics Identity Orchestrator を使用して SSO を行うことがサポートされます。 ユーザーは、Azure AD B2C またはソーシャル ID プロバイダー (IdP) でホストされているアカウントでサインインします。 Maverics を使用すると、Symantec SiteMinder などの従来の ID システムによって保護されてきたアプリで SSO を使用できるようになります。

- **アプリを書き直すことなく、標準ベースの SSO をアプリで使用できるようにする**:Azure AD B2C を使用してユーザー アクセスを管理し、Maverics Identity Orchestrator の SAML または OIDC コネクタを使用して SSO を有効にします。

- **構成が簡単**:Azure AD B2C には、Maverics Identity Orchestrator の SAML または OIDC コネクタを Azure AD B2C に接続するための簡単なステップバイステップのユーザー インターフェイスが用意されています。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

- Maverics Identity Orchestrator で使用されるシークレットを格納するための [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のインスタンス。 これは、Azure AD B2C またはその他の属性プロバイダー (ライトウェイト ディレクトリ アクセス プロトコル (LDAP) のディレクトリやデータベースなど) に接続するために使用されます。

- Azure 仮想マシンまたは任意のオンプレミス サーバーにインストールされ、実行されている [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) のインスタンス。 ソフトウェアの入手方法およびインストールと構成に関するドキュメントについては、[Strata](https://www.strata.io/contact/) にお問い合わせください。

- レガシ ID システムから Azure AD B2C に移行するオンプレミスのアプリケーション。

## <a name="scenario-description"></a>シナリオの説明

Strata の Maverics との統合には、次のコンポーネントが含まれています。

- **Azure AD B2C**:ユーザーの資格情報の検証を担当する承認サーバー。 認証済みユーザーは、Azure AD B2C ディレクトリに格納されているローカル アカウントを使用して、オンプレミスのアプリにアクセスできます。

- **外部のソーシャルまたはエンタープライズ IdP**:任意の OpenID Connect プロバイダー、Facebook、Google、または GitHub を使用できます。 Azure AD B2C での [外部 IdP](./technical-overview.md#external-identity-providers) の使用に関する情報を参照してください。  

- **Strata の Maverics Identity Orchestrator**:ユーザーのサインオンを調整し、HTTP ヘッダーを介してアプリに ID を透過的に渡すサービス。

次のアーキテクチャの図に、この実装を示します。

![ハイブリッド アプリにアクセスできるようにするための Azure AD B2C と Strata Maverics の統合のアーキテクチャを示す図](./media/partner-strata/strata-architecture-diagram.png)

| 手順 | 説明 |
|:-------|:---------------|
| 1. | ユーザーが、オンプレミスでホストされているアプリケーションへのアクセスを要求します。 ユーザーによってアプリケーションに対して行われた要求が、プロキシである Maverics Identity Orchestrator に送られます。|
| 2. | Orchestrator によって、ユーザーの認証状態が確認されます。 セッション トークンを受け取っていない場合、または渡されたセッション トークンが無効な場合は、認証のためにユーザーが Azure AD B2C に送信されます。|
| 3. | Azure AD B2C によって、構成されているソーシャル IdP に認証要求が送信されます。|
| 4. | IdP によってユーザーに資格情報の入力が求められます。 IdP によっては、ユーザーは多要素認証 (MFA) を行う必要がある場合があります。|
| 5. | IdP によって、認証応答が Azure AD B2C に送信されます。 必要に応じて、この手順の実行中にユーザーが Azure AD B2C ディレクトリにローカル アカウントを作成することができます。|
| 6. | Azure AD B2C によって、Azure AD B2C テナントでの Orchestrator アプリの登録時に指定されたエンドポイントにユーザー要求が送信されます。|
| 7. | Orchestrator によって、アクセス ポリシーが評価され、アプリに転送される HTTP ヘッダーに含められる属性値が計算されます。 この手順では、Orchestrator によって、追加の属性プロバイダーが呼び出され、ヘッダー値を正しく設定するために必要な情報が取得されることがあります。 Orchestrator によって、ヘッダー値が設定され、要求がアプリに送信されます。|
| 8. | これで、ユーザーが認証され、アプリにアクセスできるようになりました。|

## <a name="get-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator を入手する
従来のオンプレミスのアプリを Azure AD B2C と統合するために使用するソフトウェアを入手するには、[Strata](https://www.strata.io/contact/) にお問い合わせください。 ソフトウェアを入手したら、次の手順に従って、Orchestrator 固有の前提条件を確認し、必要なインストールと構成の手順を実行します。

## <a name="configure-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを構成する

1. **アプリケーションの登録**

   a. Azure AD B2C テナントに [Orchestrator をアプリケーションとして登録します](./tutorial-register-applications.md?tabs=app-reg-ga)。
   >[!Note]
   >後で Orchestrator インスタンスを構成するときに、テナント名と識別子、クライアント ID、クライアント シークレット、構成済みの要求、およびリダイレクト URI が必要になります。

   b. Microsoft MS Graph API のアクセス許可をアプリケーションに付与します。 アプリケーションには、`offline_access` と `openid` のアクセス許可が必要です。

   c. アプリケーションのリダイレクト URI を追加します。 この URI は、Orchestrator の Azure AD B2C コネクタ構成の `oauthRedirectURL` パラメーターと一致します (`https://example.com/oidc-endpoint` など)。

2. **ユーザー フローの作成**:[サインアップとサインイン ユーザー フロー](./tutorial-create-user-flows.md)を作成します。

3. **IdP の追加**:ローカル アカウント、あるいはソーシャルまたはエンタープライズ [IdP](./add-identity-provider.md) のいずれかから、ユーザーのサインインを選択します。

4. **ユーザー属性の定義**:サインアップ中に収集される属性を定義します。

5. **アプリケーション要求の指定**:Orchestrator インスタンスを介してアプリケーションに返される属性を指定します。 Orchestrator では、Azure AD B2C から返された要求の属性が使用され、接続されている他の ID システム (LDAP ディレクトリやデータベースなど) から追加の属性を取得できます。 これらの属性は、HTTP ヘッダーに設定され、上流のオンプレミス アプリケーションに送信されます。

## <a name="configure-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator を構成する

以下のセクションでは、Orchestrator インスタンスを構成するために必要な手順について順を追って説明します。 その他のサポートとドキュメントについては、[Strata](https://www.strata.io/contact/) にお問い合わせください。

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics Identity Orchestrator サーバーの要件

Orchestrator インスタンスは、オンプレミスまたはパブリック クラウド インフラストラクチャ (Azure、AWS、GCP などのプロバイダーが提供する) 上の任意のサーバーで実行できます。

- OS: REHL 7.7 以降、CentOS 7 以降

- ディスク:10 GB (小)

- メモリ:16 GB

- ポート:22 (SSH/SCP)、443、80

- インストール/管理タスクでの root アクセス

- Maverics Identity Orchestrator は、`systemd` の下でユーザー `maverics` として実行されます

- Maverics Identity Orchestrator をホストしているサーバーからのネットワーク エグレス (Azure AD テナントに到達可能であること)。

### <a name="install-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator をインストールする

1. 最新の Maverics RPM パッケージを入手します。 Maverics をインストールするシステムにパッケージを配置します。 ファイルをリモート ホストにコピーする場合は、[SCP](https://www.ssh.com/ssh/scp/) を使用すると便利です。

2. Maverics パッケージをインストールするには、`maverics.rpm` を実際のファイル名に置き換えて、次のコマンドを実行します。

   `sudo rpm -Uvf maverics.rpm`

   既定では、Maverics は `/usr/local/bin` ディレクトリにインストールされます。

3. Maverics をインストールすると、`systemd` でサービスとして実行されます。  Maverics サービスが実行されていることを確認するには、次のコマンドを実行します。

   `sudo service maverics status`

  Orchestrator のインストールが成功した場合は、次のようなメッセージが表示されます。

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Maverics サービスが開始されない場合は、次のコマンドを実行して問題を調査します。

   `journalctl --unit=maverics.service --reverse`

   最新のログ エントリが出力の先頭に表示されます。

Maverics をインストールすると、`/etc/maverics` ディレクトリに既定の `maverics.yaml` ファイルが作成されます。

アプリケーションを保護するように Orchestrator を構成します。 Azure AD B2C と統合し、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9) からのシークレットを格納および取得します。 Orchestrator が構成を読み取る場所を定義します。

### <a name="supply-configuration-using-environment-variables"></a>環境変数を使用して構成を設定する

環境変数を使用して、Orchestrator インスタンスに構成を設定します。

`MAVERICS_CONFIG`

この環境変数によって、使用する YAML 構成ファイルと、スタートアップ時または再起動時の検索場所が Orchestrator インスタンスに指示されます。 `/etc/maverics/maverics.env` に環境変数を設定します。

### <a name="create-the-orchestrators-tls-configuration"></a>Orchestrator の TLS 構成を作成する

`maverics.yaml` の `tls` フィールドによって、Orchestrator インスタンスで使用されるトランスポート層のセキュリティ構成が宣言されます。 コネクタでは、TLS オブジェクトと Orchestrator サーバーを使用できます。

`maverics` キーは、Orchestrator サーバー用に予約されています。 他のすべてのキーは使用可能であり、TLS オブジェクトを特定のコネクタに挿入するために使用できます。

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Azure AD B2C コネクタを構成する

Orchestrator は、コネクタを使用して認証および属性プロバイダーと統合されます。 この場合、この Orchestrator App Gateway は、認証と属性の両方のプロバイダーとして Azure AD B2C コネクタを使用します。 Azure AD B2C では、認証のためにソーシャル IdP が使用され、Orchestrator の属性プロバイダーとして機能します。また、HTTP ヘッダーの要求セットに属性が渡されます。  

このコネクタの構成は、Azure AD B2C テナントに登録されているアプリに対応しています。

1. テナントのアプリ構成からクライアント ID、シークレット、およびリダイレクト URI をコピーします。

2. コネクタに名前を付けて (ここでは `azureADB2C` と表示されています)、コネクタの `type` に `azure` を設定します。 コネクタ名は以下の他の構成パラメーターで使用されるため、この値をメモしておきます。

3. この統合の場合は、`authType` に `oidc` を設定する必要があります。

4. ステップ 1 でコピーしたクライアント ID を `oauthClientID` パラメーターの値として設定します。

5. ステップ 1 でコピーしたクライアント シークレットを `oauthClientSecret` パラメーターの値として設定します。

6. ステップ 1 でコピーしたリダイレクト URI を `oauthRedirectURL` パラメーターの値として設定します。

7. Azure AD B2C の OIDC コネクタでは、既知の OIDC エンドポイントを使用して、URL や署名キーなどのメタデータが検出されます。 `oidcWellKnownURL` の値にテナントのエンドポイントを設定します。

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>認証プロバイダーとして Azure AD B2C を定義する

アプリ リソース要求の一部として有効なセッションを提示していないユーザーの認証方法は、認証プロバイダーによって決定されます。 Azure AD B2C テナントの構成によって、ユーザーに資格情報の入力を要求し、追加の認証ポリシーを適用する方法が決まります。 たとえば、認証プロセスを完了するために 2 番目の要素を要求し、認証が成功した後に Orchestrator アプリゲート ウェイに返す必要がある要求を決定します。

`authProvider` の値は、コネクタの `name` 値と一致している必要があります。

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Orchestrator アプリゲート ウェイを使用してオンプレミスのアプリを保護する

Orchestrator のアプリ ゲートウェイ構成では、Azure AD B2C によってアプリケーションが保護される方法と、ユーザーがアプリにアクセスする方法を宣言します。

1. アプリゲート ウェイの名前を作成します。 アプリの識別子として、フレンドリ名または完全修飾ホスト名を使用できます。

2. `location` を設定します。 この例では、アプリのルート (`/`) を使用しますが、アプリケーションの任意の URL パスを指定できます。

3. 「ホスト:ポート」の表記 (`https://example.com:8080`) を使用して、`upstream` に保護されるアプリケーションを定義します。

4. エラー ページおよび認可されていないページの値を設定します。

5. 認証を行い、アプリへのアクセスを制御するためにアプリケーションに渡す必要がある HTTP ヘッダー名と属性値を定義します。 ヘッダー名は任意であり、通常はアプリの構成に対応しています。 属性値には、それらを提供するコネクタの名前空間が付加されます。 次の例では、Azure AD B2C から返される値の先頭に、コネクタ名 `azureADB2C` が付けられています。ここで、サフィックスは `given_name` などの必須の値を含む属性の名前です。

6. 評価および適用されるポリシーを設定します。 `allowUnauthenticated`、`allowAnyAuthenticated`、および `allowIfAny` の 3 つのアクションが定義されています。 各アクションは `resource` に関連付けられており、その `resource` に対してポリシーが評価されます。

>[!NOTE]
>`headers` と `policies` では JavaScript または GoLang サービス拡張機能が使用され、既定の機能が大幅に強化される任意のロジックが実装されます。

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>シークレット プロバイダーとして Azure Key Vault を使用する

Orchestrator で Azure AD B2C およびその他の ID システムへの接続に使用されるシークレットをセキュリティで保護することが重要です。 Maverics では、`maverics.yaml` からシークレットが既定ではプレーンテキストで読み込まれますが、このチュートリアルでは、シークレット プロバイダーとして Azure Key Vault を使用します。

指示に従って、Orchestrator インスタンスでシークレット プロバイダーとして使用される[新しい Key Vault を作成します](../key-vault/secrets/quick-create-portal.md)。 資格情報コンテナーにシークレットを追加し、各シークレットに付けられている `SECRET NAME` をメモしておきます。 たとえば、`AzureADB2CClientSecret` のようにします。

`maverics.yaml` 構成ファイルで値をシークレットとして宣言するには、シークレットを山かっこで囲みます。

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

山かっこ内に指定する値は、Azure Key Vault のシークレットに付けられた `SECRET NAME` に対応している必要があります。

Azure Key Vault からシークレットを読み込むには、次のパターンを使用して、azure-credentials.json ファイルにある資格情報で `/etc/maverics/maverics.env` ファイルに環境変数 `MAVERICS_SECRET_PROVIDER` を設定します。

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>すべてをまとめる

上記の構成を完了すると、Orchestrator の構成は次のようになります。

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>フローをテストする

1. オンプレミスのアプリケーションの URL (`https://example.com/sonar/dashboard`) にアクセスします。

2. Orchestrator によって、ユーザー フローで構成したページにリダイレクトされます。

3. ページの一覧から IdP を選択します。

4. IdP にリダイレクトされたら、要求に従って資格情報を入力します。その IdP によって要求された場合は、MFA トークンを指定します。

5. 正常に認証されると、Azure AD B2C にリダイレクトされ、Azure AD B2C によってアプリの要求が Orchestrator のリダイレクト URI に転送されます。

6. Orchestrator によって、ポリシーが評価され、ヘッダーが計算されて、上流のアプリケーションにユーザーが送信されます。  

7. 要求されたアプリケーションが表示されます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)
