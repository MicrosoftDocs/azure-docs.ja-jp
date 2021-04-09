---
title: コンテナー グループでマネージド ID を有効にする
description: ここでは、他のサービスで認証できるマネージド ID を Azure Container Instances で有効にする方法を説明します。
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: a0d029e39122ca7bb858103f4d7f88e2536850d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198321"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Azure Container Instances でマネージド ID を使用する方法

[Azure リソース用のマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用して、他の Azure サービスと対話する Azure Container Instances 内のコードを実行します。こうするとシークレットや資格情報をコード内に保持する必要がありません。 この機能により、Azure Active Directory で自動管理される ID が Azure Container Instances のデプロイに提供されます。

この記事では、Azure Container Instances でのマネージド ID の詳細について、および次の点について説明します。

> [!div class="checklist"]
> * コンテナー グループでユーザー割り当て ID またはシステム割り当て ID を有効にする
> * ID に Azure Key Vault へのアクセス権を付与する
> * マネージド ID を使用して、実行中のコンテナーから Key Vault にアクセスする

サンプルを調整することにより、Azure Container Instances 内の ID を有効にし、それを使って他の Azure サービスにアクセスします。 これらのサンプルは対話型です。 ただし実際には、コンテナー イメージがコードを実行して Azure サービスにアクセスします。
 
> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。 現時点では、Azure Container Instances のマネージド ID は Linux コンテナーでのみサポートされており、Windows コンテナーではまだサポートされていません。

## <a name="why-use-a-managed-identity"></a>マネージド ID を使用する理由

実行中のコンテナーでマネージド ID を使用すると、資格情報をコンテナー コード内で管理しなくても、[Azure AD 認証をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)に対して認証することができます。 AD 認証をサポートしないサービスについては、Azure Key Vault の中にシークレットを格納し、マネージド ID を使って Key Vault にアクセスして資格情報を取得できます。 マネージド ID の使用法の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

### <a name="enable-a-managed-identity"></a>マネージド ID の有効化

 コンテナー グループを作成するときに、[ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) プロパティを設定することで 1 つまたは複数のマネージド ID を有効にできます。 また、コンテナー グループが実行状態になった後でマネージド ID を有効化または更新できます。どちらの場合も、それによりコンテナー グループが再起動します。 新規または既存のコンテナー グループで ID を設定するには、Azure CLI、Resource Manager テンプレート、YAML ファイル、または別の Azure ツールを使用します。 

Azure Container Instances は、ユーザー割り当て、システム割り当ての両方の種類の Azure マネージド ID をサポートしています。 各コンテナー グループで、1 つのシステム割り当て ID、1 つまたは複数のユーザー割り当て ID、または両方の種類の ID を有効化できます。 Azure リソースのマネージド ID の基本点な事柄については、[概要](../active-directory/managed-identities-azure-resources/overview.md)を参照してください。

### <a name="use-a-managed-identity"></a>マネージド ID の使用

マネージド ID を使用するには、サブスクリプション内の 1 つ以上の Azure サービス リソース (Web アプリ、キー コンテナー、ストレージ アカウントなど) へのアクセス権を ID に付与する必要があります。 実行中のコンテナーでマネージド ID を使用する方法は、Azure VM での ID の使用と似ています。 [トークン](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)、[Azure PowerShell または Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)、または [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md) の使用に関する VM ガイダンス資料を参照してください。

### <a name="limitations"></a>制限事項

* 現在、仮想ネットワークにデプロイされたコンテナー グループ内でマネージド ID を使用することはできません。
* マネージド ID を使用して、コンテナー グループの作成時に Azure Container Registry からイメージをプルすることはできません。 ID は、実行中のコンテナー内でのみ使用できます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.49 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

この記事の例では、Azure Container Instances でマネージド ID を使用して Azure Key Vault シークレットにアクセスします。 

まず、次の [az group create](/cli/azure/group#az-group-create) コマンドを使用して、*myResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

キー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) コマンドを使います。 必ず一意のキー コンテナー名を指定してください。 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

[az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) コマンドを使って、サンプルのシークレット例をキー コンテナーに格納します。

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

次に示す例に従い、Azure Container Instances のユーザー定義/システム定義のいずれかのマネージド ID を使って Key Vault にアクセスします。

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>例 1:ユーザー割り当ての ID を使用して Azure Key Vault にアクセスする

### <a name="create-an-identity"></a>ID の作成

まず [az identity create](/cli/azure/identity#az-identity-create) コマンドを使用して、サブスクリプション内で ID を作成します。 Key Vault の作成に使ったのと同じリソース グループを使用することも、別のものを使用することもできます。

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

後続のステップで ID を使用するために、[az identity show](/cli/azure/identity#az-identity-show) コマンドを使って ID のサービス プリンシパル ID とリソース ID を変数に格納します。

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Key Vault へのアクセス権をユーザー割り当て ID に付与する

次の [az keyvault set-policy](/cli/azure/keyvault) コマンドを実行して、キー コンテナーに対するアクセス ポリシーを設定します。 次の例では、ユーザー割り当て ID にキー コンテナーからのシークレットの取得を許可します。

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>コンテナー グループでユーザー割り当て ID を有効にする

次の [az container create](/cli/azure/container#az-container-create) コマンドを実行して、Microsoft の `azure-cli` イメージに基づくコンテナー インスタンスを作成します。 この例で作成される単一コンテナーのグループを使用すると、Azure CLI を実行して他の Azure サービスに対話的にアクセスできます。 このセクションでは、基本のオペレーティング システムのみが使用されます。 コンテナーで Azure CLI を使用する例については、「[コンテナー グループでシステム割り当て ID を有効にする](#enable-system-assigned-identity-on-a-container-group)」を参照してください。 

`--assign-identity` パラメーターは、ユーザー割り当てのマネージド ID をグループに渡します。 実行時間の長いこのコマンドにより、コンテナーの実行状態が続きます。 この例では、Key Vault の作成に使ったのと同じリソース グループを使用していますが、別のものを指定することもできます。

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

数秒以内に、Azure CLI からデプロイが完了したことを示す応答を受信します。 [az container show](/cli/azure/container#az-container-show) コマンドを使用して、その状態を確認します。

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

出力の `identity` セクションが次のように表示されたら、コンテナー グループで ID が設定されています。 `userAssignedIdentities` の下の `principalID` は、Azure Active Directory で作成した ID のサービス プリンシパルです。

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>ユーザー定義 ID を使用して Key Vault からシークレットを取得する

これで、実行中のコンテナー インスタンス内でマネージド ID を使って Key Vault にアクセスすることができます。 まずコンテナーで bash シェルを次のように起動します。

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

コンテナーの bash シェルで、次のコマンドを実行します。 Azure Active Directory を使って Key Vault に認証するためのアクセス トークンを取得するには、次のコマンドを実行します。

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

出力:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

後続のコマンドで認証に使うためにアクセス トークンを変数に格納するには、次のコマンドを実行します。

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

次に、アクセス トークンを使って Key Vault に対して認証し、シークレットを読み取ります。 URL 内のキー コンテナー名を必ず置き換えてください (*https:\//mykeyvault.vault.azure.net/...* )。

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

応答では次のようにシークレットが表示されます。 コードの中でこの出力を解析して、シークレットを取得できます。 その後、後続の操作でシークレットを使用して、別の Azure リソースにアクセスします。

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>例 2:システム割り当ての ID を使用して Azure Key Vault にアクセスする

### <a name="enable-system-assigned-identity-on-a-container-group"></a>コンテナー グループでシステム割り当て ID を有効にする

次の [az container create](/cli/azure/container#az-container-create) コマンドを実行して、Microsoft の `azure-cli` イメージに基づくコンテナー インスタンスを作成します。 この例で作成される単一コンテナーのグループを使用すると、Azure CLI を実行して他の Azure サービスに対話的にアクセスできます。 

`--assign-identity` パラメーター (他の値なし) を指定すると、システム割り当てのマネージド ID がグループで有効になります。 この ID は、コンテナーグループのリソースグループにスコープが設定されています。 実行時間の長いこのコマンドにより、コンテナーの実行状態が続きます。 この例では、ID のスコープ内にある、キー コンテナーの作成に使用したものと同じリソース グループを使用します。

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

数秒以内に、Azure CLI からデプロイが完了したことを示す応答を受信します。 [az container show](/cli/azure/container#az-container-show) コマンドを使用して、その状態を確認します。

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

出力の `identity` セクションが次のように表示され、システム割り当て ID が Azure Active Directory で作成されたことを示します。

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

後続のステップで使用するために、この ID の変数の値を `principalId` (サービス プリンシパル ID) に設定します。

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Key Vault へのアクセス権をコンテナー グループに付与する

次の [az keyvault set-policy](/cli/azure/keyvault) コマンドを実行して、キー コンテナーに対するアクセス ポリシーを設定します。 次の例により、システム定義 ID が Key Vault からシークレットを取得できるようになります。

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>コンテナー グループ ID を使用して Key Vault からシークレットを取得する

これで、実行中のコンテナー インスタンス内でマネージド ID を使って Key Vault にアクセスすることができます。 まずコンテナーで bash シェルを次のように起動します。

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

コンテナーの bash シェルで、次のコマンドを実行します。 まず、マネージド ID を使用して Azure CLI にログインします。

```bash
az login --identity
```

実行中のコンテナーから、キー コンテナーのシークレットを取得します。

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

シークレットの値が取得されます。

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Resource Manager テンプレートを使用してマネージド ID を有効にする

[Resource Manager テンプレート](container-instances-multi-container-group.md)を使用してコンテナー グループのマネージド ID を有効にするには、`ContainerGroupIdentity` オブジェクトを使って `Microsoft.ContainerInstance/containerGroups` オブジェクトの `identity` プロパティを設定します。 次のスニペットは、さまざまなシナリオ別の `identity` プロパティの構成を示しています。 [Resource Manager テンプレート リファレンス](/azure/templates/microsoft.containerinstance/containergroups)を参照してください。 `2018-10-01` を `apiVersion` 以上指定します。

### <a name="user-assigned-identity"></a>ユーザー割り当て ID

ユーザー割り当て ID は、次の形式のリソース ID です。

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

1 つまたは複数のユーザー割り当て ID を有効にすることができます。

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>システム割り当て ID

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>システム割り当て/ユーザー割り当て ID

各コンテナー グループで、1 つのシステム割り当て ID と、1 つまたは複数のユーザー割り当て ID の両方を有効化できます。

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>マネージド ID を有効にするには YAML ファイルを使用します。

[YAML ファイル](container-instances-multi-container-yaml.md)を使ってデプロイされるコンテナー グループでマネージド ID を有効にするには、次の YAML を含めます。
`2018-10-01` を `apiVersion` 以上指定します。

### <a name="user-assigned-identity"></a>ユーザー割り当て ID

ユーザー割り当て ID は次の形式のリソース ID です 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

1 つまたは複数のユーザー割り当て ID を有効にすることができます。

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>システム割り当て ID

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>システム割り当て/ユーザー割り当て ID

各コンテナー グループで、1 つのシステム割り当て ID と、1 つまたは複数のユーザー割り当て ID の両方を有効化できます。

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure Container Instances でのマネージド ID について、および次の方法について説明しました。

> [!div class="checklist"]
> * コンテナー グループでユーザー割り当て ID またはシステム割り当て ID を有効にする
> * ID に Azure Key Vault へのアクセス権を付与する
> * マネージド ID を使用して、実行中のコンテナーから Key Vault にアクセスする

* 詳細については、「[Azure リソースの管理 ID について](../active-directory/managed-identities-azure-resources/index.yml)」を参照してください。

* マネージド ID を使って Azure Container Instances から Key Vault にアクセスする例については、[Azure Go SDK サンプル](https://medium.com/@samkreter/c98911206328)を参照してください。
