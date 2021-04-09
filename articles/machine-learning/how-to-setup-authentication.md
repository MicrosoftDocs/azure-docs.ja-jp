---
title: 認証の設定
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で、さまざまなリソースとワークフローの認証を設定して構成する方法について説明します。
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 30e4fede72df8eaf922745e7781c9e0d11f7ddb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210820"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning のリソースとワークフローの認証を設定する


お使いの Azure Machine Learning のワークスペースに認証を設定する方法について説明します。 お使いの Azure Machine Learning ワークスペースには、多くの場合、__Azure Active Directory__ (Azure AD) を使用して認証されます。 ワークスペースに接続するときに使用できる認証ワークフローには、一般に次の 3 つがあります。

* __対話型__:Azure Active Directory でアカウントを使用して、直接認証するか、認証に使用されるトークンを取得します。 対話型認証は、"_実験および反復開発_" 時に使用します。 対話型認証では、(Web サービスなどの) リソースへのアクセスを、ユーザーごとに制御できます。

* __サービス プリンシパル__: Azure Active Directory でサービス プリンシパル アカウントを作成し、それを使用して認証を行うか、トークンを取得します。 サービス プリンシパルは、ユーザーが操作をせず、サービスに "_自動認証されるプロセス_" が必要な場合に使用します。 たとえば、トレーニング コードが変更されるたびにモデルをトレーニングおよびテストする継続的インテグレーションとデプロイ スクリプトです。

* __マネージド ID__:Azure Machine Learning SDK を "_Azure 仮想マシンで_" 使用する場合は Azure のマネージド ID を使用できます。 このワークフローでは、Python コードに資格情報を保存したり、ユーザーに認証を求めずに、マネージド ID を使用して VM をワークスペースに接続したりすることが許可されます。 "_モデルのトレーニング時_" に、Azure Machine Learning コンピューティング クラスターをマネージド ID を使用してワークスペースにアクセスするように構成することもできます。

> [!IMPORTANT]
> Azure ロールベースのアクセス制御 (Azure RBAC) は、使用する認証のワークフローに関係なく、リソースに対して許可するアクセス レベル (認証) の範囲の設定に使用します。 たとえば、管理者または自動化プロセスには、コンピューティング インスタンスを作成するアクセス権は持っていても、それを使用できない場合があり、それを使用できるデータ サイエンティストが、それを削除または作成できない場合があります。 詳細については、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)を作成します。
* [開発環境を構成](how-to-configure-environment.md)して Azure Machine Learning SDK をインストールするか、SDK が既にインストールされている [Azure Machine Learning コンピューティング インスタンス](concept-azure-machine-learning-architecture.md#compute-instance)を使用します。

## <a name="azure-active-directory"></a>Azure Active Directory

お使いのワークスペースのすべての認証ワークフローは、Azure Active Directory に依存しています。 ユーザーに個人アカウントを使用して認証してもらいたい場合、自分の Azure AD にアカウントが必要です。 サービス プリンシパルを使用する場合は、それらが自分の Azure AD にある必要があります。 マネージド ID は Azure AD の機能でもあります。 

Azure AD の詳細については、「[Azure Active Directory 認証とは](..//active-directory/authentication/overview-authentication.md)」を参照してください。

Azure AD アカウントを作成したら、ワークスペースにアクセスする方法および Azure Machine Learning でのその他の操作に関する情報を、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」でご確認ください。

## <a name="configure-a-service-principal"></a>サービス プリンシパルの構成

サービス プリンシパル (SP) を使用するには、最初に SP を作成し、それにお使いのワークスペースに対するアクセス許可を付与する必要があります。 既に説明したように、Azure のロールベースのアクセス制御 (Azure RBAC) を使用してアクセスを制御するため、SP に付与するアクセス権も決定する必要があります。

> [!IMPORTANT]
> サービス プリンシパルを使用する場合は、使用する __タスクに必要な最小限のアクセス権__ を付与します。 たとえば、サービス プリンシパルが Web デプロイのアクセス トークンを読み取るためにのみ使用される場合は、所有者または共同作成者のアクセス権は付与しません。
>
> 最小限のアクセス権を付与する理由は、サービス プリンシパルがパスワードを使用して認証を行うためであり、パスワードがオートメーション スクリプトの一部として格納される可能性があるからです。 パスワードが漏洩した場合、特定のタスクに必要な最小限のアクセス権のみを保持しているため、SP が悪意を持って使用される可能性が最小限に抑えられます。

SP を作成し、ワークスペースへのアクセス権を付与する最も簡単な方法は、[Azure CLI](/cli/azure/install-azure-cli)を使用することです。 サービス プリンシパルを作成して、ワークスペースへのアクセス権を付与するには、次の手順に従います。

> [!NOTE]
> これらのすべての手順を実行するには、サブスクリプションの管理者である必要があります。

1. Azure サブスクリプションに対して認証を行います。

    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザーを開き、コマンド ラインの指示に従う必要があります。 この手順では、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスして認証コードを入力する必要があります。

    Azure のサブスクリプションが複数ある場合、`az account set -s <subscription name or ID>` コマンドを使用してサブスクリプションを設定できます。 詳しくは、「[Use multiple Azure subscriptions (複数の Azure サブスクリプションを使用する)](/cli/azure/manage-azure-subscriptions-azure-cli)」をご覧ください。

    その他の認証方法については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

1. Azure Machine Learning 拡張機能をインストールします。

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. サービス プリンシパルを作成します。 次の例では、**ml auth** という名前の SP が作成されます。

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    出力は次のような JSON になります。 この記事の他の手順で必要になるため、`clientId`、`clientSecret`、`tenantId` の各フィールドをメモしておきます。

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. 前の手順で返された `clientId` 値を使用して、サービス プリンシパルの詳細を取得します。

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    次の JSON は、コマンドからの出力を簡略化した例です。 次の手順で値が必要になるため、`objectId` フィールドをメモしておきます。

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. SP が Azure Machine Learning ワークスペースにアクセスできるようにします。 `-w` と `-g` のパラメーターに、それぞれワークスペース名とリソース グループ名が必要になります。 `--user` パラメーターには、前の手順の `objectId` 値を使用します。 `--role` パラメーターを使用すると、サービス プリンシパルのアクセス ロールを設定できます。 次の例では、SP に **所有者** ロールが割り当てられています。 

    > [!IMPORTANT]
    > 所有者アクセスを使用すると、サービス プリンシパルは、ワークスペースでほぼすべての操作を実行できます。 このドキュメントでは、アクセス権を付与する方法を説明するために使用されています。運用環境では、目的のロールを実行するために必要な最小限のアクセス権をサービス プリンシパルに付与することをお勧めします。 自分のシナリオに必要なアクセス権を持つカスタム ロールの作成に関する情報については、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」を参照してください。

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    この呼び出しでは、成功しても出力は生成されません。

## <a name="configure-a-managed-identity"></a>マネージド ID の構成

> [!IMPORTANT]
> マネージド ID は、Azure Machine Learning SDK を Azure Virtual Machine から、または Azure Machine Learning コンピューティング クラスターを使用して使用する場合のみサポートされています。 コンピューティング クラスターでのマネージド ID の使用は、現在プレビュー段階です。

### <a name="managed-identity-with-a-vm"></a>VM でのマネージド ID

1. [VM 上の Azure リソースに対して、システムで割り当てられたマネージド ID を有効にしてください](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。

1. [Azure portal](https://portal.azure.com) からお使いのワークスペースを選択し、 __[アクセス制御 (IAM)]__ 、 __[ロールの割り当ての追加]__ を順に選択し、 __[アクセスの割り当て先]__ ドロップダウンから __[仮想マシン]__ を選択します。 最後にお使いの VM の ID を選択します。

1. この ID に割り当てるロールを選択します。 たとえば、共同作成者やカスタム ロールです。 詳細については、[リソースへのアクセスの制御](how-to-assign-roles.md)に関するページを参照してください。

### <a name="managed-identity-with-compute-cluster"></a>コンピューティング クラスターでのマネージド ID

詳細については、[コンピューティング クラスターでのマネージド ID の設定](how-to-create-attach-compute-cluster.md#managed-identity)に関する説明を参照してください。

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>対話型認証の使用

> [!IMPORTANT]
> 対話型認証では、ブラウザーが使用され、Cookie (サードパーティの Cookie を含む) が必要です。 Cookie を無効にしている場合は、"サインインできませんでした" などのエラーを受け取る場合があります。 このエラーは、[Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) を有効にしている場合にも発生する場合があります。

ドキュメントとサンプルのほとんどの例では、対話型認証が使用されています。 たとえば、SDK を使用する場合は、UI ベースの認証フローを使用して自動的に入力を求める関数呼び出しが 2 つあります。

* `from_config()` 関数を呼び出すと、プロンプトが表示されます。

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    `from_config()` 関数は、ワークスペースの接続情報を含む JSON ファイルを検索します。

* `Workspace` コンストラクターを使用して、サブスクリプション、リソース グループ、およびワークスペースの情報を指定する場合も、対話型認証が求められます。

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> 複数のテナントにアクセスできる場合は、クラスをインポートし、ターゲットとするテナントを明示的に定義する必要があります。 `InteractiveLoginAuthentication` のコンストラクターを呼び出すと、上記の呼び出しと同様にログインするよう求められます。
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Azure CLI を使用する場合、CLI セッションの認証には `az login` コマンドを使用します。 詳細については、[Azure CLI の概要](/cli/azure/get-started-with-azure-cli)に関するページをご覧ください。

> [!TIP]
> 以前 Azure CLI を使用して対話的に認証した環境から SDK を使用している場合は、CLI にキャッシュされた資格情報を使用して `AzureCliAuthentication` クラスで、ワークスペースに認証できます。
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>サービス プリンシパル認証を使用する

サービス プリンシパルを使用して SDK からお使いのワークスペースに認証するには、`ServicePrincipalAuthentication` クラス コンストラクターを使用します。 サービス プロバイダーの作成時に取得した値をパラメーターとして使用します。 `tenant_id` パラメーターは上記の `tenantId` にマップされ、`service_principal_id` は `clientId` にマップされ、`service_principal_password` は `clientSecret` にマップされます。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp` 変数は、SDK で直接使用する認証オブジェクトを保持するようになりました。 一般に、次のコードに示すように、上記で使用した ID/シークレットを環境変数に格納することをお勧めします。 環境変数に格納すると、情報が誤って GitHub リポジトリにチェックインされるのを防ぐことができます。

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Python で実行され、SDK を主に使用する自動化されたワークフローの場合は、ほとんどの場合、このオブジェクトをそのまま使用して認証を行うことができます。 次のコードでは、作成した auth オブジェクトを使用してワークスペースに対して認証を行います。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Azure CLI からのサービス プリンシパルの使用

サービス プリンシパルは Azure CLI コマンドに使用できます。 詳細については、「[サービス プリンシパルを使用したサインイン](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)」を参照してください。

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>REST API でのサービス プリンシパルの使用 (プレビュー)

サービス プリンシパルを使用して、Azure Machine Learning [REST API](/rest/api/azureml/) (プレビュー) に対する認証を行うこともできます。 Azure Active Directory の[クライアント資格情報付与フロー](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)を使用します。これにより、自動化されたワークフローでヘッドレス認証に対するサービス間の呼び出しが許可されます。 これらの例は、Python と Node.js の両方で [ADAL ライブラリ](../active-directory/azuread-dev/active-directory-authentication-libraries.md)と共に実装されていますが、OpenID Connect 1.0 をサポートする任意のオープンソース ライブラリを使用することもできます。

> [!NOTE]
> MSAL js は ADAL より新しいライブラリですが、MSAL.js によってクライアント資格情報を使用したサービス間認証を行うことはできません。これは、主に、特定のユーザーに関連付けられた対話型/UI 認証を目的とするクライアント側ライブラリであるためです。 次に示すように、ADAL を使用して、REST API で自動化されたワークフローを構築することをお勧めします。

#### <a name="nodejs"></a>Node.js

Node.js を使用して認証トークンを生成するには、次の手順に従います。 現在の環境で、`npm install adal-node` を実行します。 次に、前の手順で作成したサービス プリンシパルの `tenantId`、`clientId`、および `clientSecret` を、次のスクリプトで一致する変数の値として使用します。

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

変数 `tokenResponse` は、トークンと関連メタデータ (有効期限など) を含むオブジェクトです。 トークンは 1 時間有効ですが、同じ呼び出しを再度実行して新しいトークンを取得することによって更新できます。 応答のサンプル スニペットを次に示します。

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

`accessToken` プロパティを使用して、認証トークンをフェッチします。 トークンを使用して API を呼び出す方法の例については、[REST API のドキュメント](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)を参照してください。

#### <a name="python"></a>Python

Python を使用して認証トークンを生成するには、次の手順に従います。 現在の環境で、`pip install adal` を実行します。 次に、前の手順で作成したサービス プリンシパルの `tenantId`、`clientId`、および `clientSecret` を、次のスクリプトで適切な変数の値として使用します。

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

変数 `token_response` は、トークンと関連メタデータ (有効期限など) を含むディクショナリです。 トークンは 1 時間有効ですが、同じ呼び出しを再度実行して新しいトークンを取得することによって更新できます。 応答のサンプル スニペットを次に示します。

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

`token_response["accessToken"]` を使用して、認証トークンをフェッチします。 トークンを使用して API を呼び出す方法の例については、[REST API のドキュメント](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)を参照してください。

#### <a name="java"></a>Java

Java では、標準の REST 呼び出しを使用してベアラー トークンを取得します。

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

上記のコードでは、 例外と `200 OK` 以外の状態コードを処理する必要がありますが、パターンが示されています。 

- クライアント ID とシークレットを使用して、お使いのプログラムがアクセス可能であることを確認する
- 自分のテナント ID を使用して、`login.microsoftonline.com` の参照先を指定する
- 認証トークンのソースとして Azure Resource Manager を使用する

## <a name="use-managed-identity-authentication"></a>マネージド ID 認証を使用する

マネージド ID を使用して構成されている VM またはコンピューティング クラスターからワークスペースに認証するには、`MsiAuthentication` クラスを使用します。 次の例は、このクラスを使用してワークスペースに対して認証する方法を示しています。

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>次のステップ

* [トレーニングでシークレットを使用する方法](how-to-use-secrets-in-runs.md)。
* [Web サービスとしてデプロイされたモデルの認証を構成する方法](how-to-authenticate-web-service.md).
* [Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する](how-to-consume-web-service.md)。
