---
title: 認証の設定
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で、さまざまなリソースとワークフローの認証を設定して構成する方法について説明します。 サービス内で認証を構成して使用するには、開発またはテストの目的で単純な UI ベースの認証を使用する方法から、完全な Azure Active Directory サービス プリンシパル認証を使用する方法まで、幅広い複数の方法があります。
services: machine-learning
author: larryfr
ms.author: larryfr
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-javascript
ms.openlocfilehash: 9d73492110703e64df5f948ad8a2a1ed8d2c63b9
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904540"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning のリソースとワークフローの認証を設定する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning ワークスペース、および Web サービスとしてデプロイされたモデルに対して認証する方法について説明します。

通常、Azure Machine Learning で使用できる認証には、次の 2 種類があります。

* __対話型__:Azure Active Directory でアカウントを使用して、直接認証するか、認証に使用されるトークンを取得します。 対話型認証は、実験および反復開発中に使用されます。 または、ユーザーごとにリソース (Web サービスなど) へのアクセスを制御する場合に使用されます。
* __サービス プリンシパル__: Azure Active Directory でサービス プリンシパル アカウントを作成し、それを使用して認証を行うか、トークンを取得します。 サービス プリンシパルは、サービスに対する認証を自動化して、ユーザーによる操作を不要にする必要がある場合に使用します。 たとえば、トレーニング コードが変更されるたびにモデルをトレーニングおよびテストする継続的インテグレーションとデプロイ スクリプトです。 サービスのエンド ユーザーに認証を要求しない場合は、サービス プリンシパルを使用して、Web サービスに対して認証を行うためのトークンを取得することもできます。 また、エンド ユーザー認証が Azure Active Directory を使用して直接実行されない場合も同様です。

使用される認証の種類に関係なく、ロールベースのアクセス制御 (RBAC) を使用して、リソースに対して許可されるアクセス レベルの範囲を設定します。 たとえば、デプロイされたモデルのアクセス トークンを取得するために使用されるアカウントには、ワークスペースへの読み取りアクセス権のみが必要となります。 RBAC の詳細については、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)を作成します。
* [開発環境を構成](how-to-configure-environment.md)して Azure Machine Learning SDK をインストールするか、SDK が既にインストールされている [Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance) を使用します。

## <a name="interactive-authentication"></a>対話型認証

> [!IMPORTANT]
> 対話型認証では、ブラウザーが使用され、Cookie (サードパーティの Cookie を含む) が必要です。 Cookie を無効にしている場合は、"サインインできませんでした" などのエラーを受け取る場合があります。 このエラーは、[Azure Multi-Factor Authentication](/azure/active-directory/authentication/concept-mfa-howitworks) を有効にしている場合にも発生する場合があります。

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

## <a name="service-principal-authentication"></a>サービス プリンシパルの認証

サービス プリンシパル (SP) の認証を使用するには、最初に SP を作成して、ワークスペースへのアクセス権を付与する必要があります。 既に説明したように、Azure のロールベースのアクセス制御 (Azure RBAC) を使用してアクセスを制御するため、SP に付与するアクセス権も決定する必要があります。

> [!IMPORTANT]
> サービス プリンシパルを使用する場合は、使用する __タスクに必要な最小限のアクセス権__ を付与します。 たとえば、サービス プリンシパルが Web デプロイのアクセス トークンを読み取るためにのみ使用される場合は、所有者または共同作成者のアクセス権は付与しません。
>
> 最小限のアクセス権を付与する理由は、サービス プリンシパルがパスワードを使用して認証を行うためであり、パスワードがオートメーション スクリプトの一部として格納される可能性があるからです。 パスワードが漏洩した場合、特定のタスクに必要な最小限のアクセス権のみを保持しているため、SP が悪意を持って使用される可能性が最小限に抑えられます。

SP を作成し、ワークスペースへのアクセス権を付与する最も簡単な方法は、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)を使用することです。 サービス プリンシパルを作成して、ワークスペースへのアクセス権を付与するには、次の手順に従います。

> [!NOTE]
> これらのすべての手順を実行するには、サブスクリプションの管理者である必要があります。

1. Azure サブスクリプションに対して認証を行います。

    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザーを開き、コマンド ラインの指示に従う必要があります。 この手順では、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスして認証コードを入力する必要があります。

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    その他の認証方法については、「[Azure CLI を使用してサインインする](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」を参照してください。

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

1. SP が Azure Machine Learning ワークスペースにアクセスできるようにします。 `-w` と `-g` のパラメーターに、それぞれワークスペース名とリソース グループ名が必要になります。 `--user` パラメーターには、前の手順の `objectId` 値を使用します。 `--role` パラメーターを使用すると、サービス プリンシパルのアクセス ロールを設定できます。 次の例では、SP に **所有者**ロールが割り当てられています。 

    > [!IMPORTANT]
    > 所有者アクセスを使用すると、サービス プリンシパルは、ワークスペースでほぼすべての操作を実行できます。 このドキュメントでは、アクセス権を付与する方法を説明するために使用されています。運用環境では、目的のロールを実行するために必要な最小限のアクセス権をサービス プリンシパルに付与することをお勧めします。 詳細については、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」を参照してください。

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    この呼び出しでは、成功しても出力は生成されません。

### <a name="use-a-service-principal-from-the-sdk"></a>SDK からのサービス プリンシパルの使用

サービス プリンシパルを使用して SDK からワークスペースに対して認証を行うには、`ServicePrincipalAuthentication` クラス コンストラクターを使用します。 サービス プロバイダーの作成時に取得した値をパラメーターとして使用します。 `tenant_id` パラメーターは上記の `tenantId` にマップされ、`service_principal_id` は `clientId` にマップされ、`service_principal_password` は `clientSecret` にマップされます。

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

サービス プリンシパルは Azure CLI コマンドに使用できます。 詳細については、「[サービス プリンシパルを使用したサインイン](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#sign-in-using-a-service-principal)」を参照してください。

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>REST API でのサービス プリンシパルの使用 (プレビュー)

サービス プリンシパルを使用して、Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/) (プレビュー) に対する認証を行うこともできます。 Azure Active Directory の[クライアント資格情報付与フロー](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)を使用します。これにより、自動化されたワークフローでヘッドレス認証に対するサービス間の呼び出しが許可されます。 これらの例は、Python と Node.js の両方で [ADAL ライブラリ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)と共に実装されていますが、OpenID Connect 1.0 をサポートする任意のオープンソース ライブラリを使用することもできます。

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

## <a name="web-service-authentication"></a>Web サービス認証

Azure Machine Learning によって作成されたモデル デプロイには、次の 2 つの認証方法があります。

* **キーベース**:静的キーを使用して、Web サービスに対する認証が行われます。
* **トークンベース**:ワークスペースから一時トークンを取得して、Web サービスに対する認証に使用する必要があります。 このトークンは、一定の時間が経過すると期限切れになり、Web サービスの操作を続行するには更新する必要があります。

    > [!NOTE]
    > トークンベースの認証は、Azure Kubernetes Service にデプロイする場合にのみ使用できます。

### <a name="key-based-web-service-authentication"></a>キーベースの Web サービスの認証

Azure Kubernetes Service (AKS) にデプロイされた Web サービスでは、キーベースの認証が既定で*有効*になっています。 Azure Container Instances (ACI) のデプロイ サービスでは、キーベースの認証が既定で*無効*になっていますが、ACI Web サービスの作成時に `auth_enabled=True` を設定して有効にすることができます。 キーベースの認証が有効になっている ACI デプロイ構成を作成するコードの例を次に示します。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

次に、`Model` クラスを使用して、デプロイでカスタム ACI 構成を使用できます。

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

認証キーをフェッチするには、`aci_service.get_keys()` を使用します。 キーを再生成するには、`regen_key()` 関数を使用して、**Primary** または **Secondary** のいずれかを渡します。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

配置済みモデルに対する認証の詳細については、[Web サービスとしてデプロイされたモデルのクライアントの作成](how-to-consume-web-service.md)に関するページを参照してください。

### <a name="token-based-web-service-authentication"></a>トークンベースの Web サービス認証

Web サービスのトークン認証を有効にする場合、ユーザーは、Web サービスにアクセスするために Azure Machine Learning JSON Web トークンを提示する必要があります。 トークンは、指定された期間後に期限切れとなり、呼び出しを続行するには更新する必要があります。

* Azure Kubernetes Service にデプロイする場合、トークン認証は**既定で無効**になります。
* Azure Container Instances にデプロイする場合、トークン認証は**サポートされません**。
* トークン認証は、**キーベースの認証と同時に使用することはできません**。

トークン認証を制御するには、デプロイの作成時や更新時に `token_auth_enabled` パラメーターを使用します。

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

トークン認証が有効になっている場合は、`get_token` メソッドを使用して、JSON Web トークン (JWT) とそのトークンの有効期限を取得できます。

> [!TIP]
> サービス プリンシパルを使用してトークンを取得し、トークンの取得に必要な最小限のアクセス権を付与する場合は、ワークスペースで**閲覧者**ロールに割り当てます。

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> トークンの `refresh_by` 時刻の後に新しいトークンを要求する必要があります。 Python SDK の外部でトークンを更新する必要がある場合は、前に説明したように、サービス プリンシパル認証で REST API を使用して定期的に `service.get_token()` 呼び出しを行う方法があります。
>
> Azure Machine Learning ワークスペースは、ご利用の Azure Kubernetes Service クラスターと同じリージョンに作成することを強くお勧めします。
>
> トークンを使用して認証するために、Web サービスは、Azure Machine Learning ワークスペースの作成先のリージョンに対して呼び出しを行います。 ワークスペースのリージョンが利用不可になった場合、ワークスペースとは異なるリージョンにクラスターがあったとしても、Web サービスのトークンがフェッチできなくなります。 その結果、ワークスペースのリージョンが再び使用可能になるまで Azure AD Authentication は使用できなくなります。
>
> また、クラスターのリージョンとワークスペースのリージョンとの間の距離が長くなるほど、トークンのフェッチにかかる時間も長くなります。

## <a name="next-steps"></a>次のステップ

* [トレーニングでシークレットを使用する方法](how-to-use-secrets-in-runs.md)。
* [画像分類モデルをトレーニングし、デプロイする](tutorial-train-models-with-aml.md)。
* [Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する](how-to-consume-web-service.md)。
