---
title: Azure CLI を使用したワークスペースの作成
titleSuffix: Azure Machine Learning
description: 機械学習のための Azure CLI 拡張機能を使用して、新しい Azure Machine Learning ワークスペースを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: b6b23e792aaef4d70e9ffc9be3667f0abef49e81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489550"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Azure CLI を使用して Azure Machine Learning のワークスペースを作成する


この記事では、Azure CLI を使用して Azure Machine Learning ワークスペースを作成する方法について説明します。 Azure CLI には、Azure リソースを管理するためのコマンドが用意されています。 CLI の機械学習拡張機能には、Azure Machine Learning リソースを操作するためのコマンドが用意されています。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* ご使用の **ローカル環境** からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](/cli/azure/install-azure-cli) が必要です。

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) を使用する場合は、ブラウザーを使用してクラウド内に存在する CLI にアクセスします。

## <a name="limitations"></a>制限事項

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure サブスクリプションへの CLI の接続

> [!IMPORTANT]
> Azure Cloud Shell を使用している場合は、このセクションを省略できます。 Cloud Shell では、Azure サブスクリプションにログインするアカウントを使用して自動的に認証が行われます。

CLI から Azure サブスクリプションを認証するには、いくつかの方法があります。 最も簡単な方法は、ブラウザーを使用して対話形式で認証することです。 対話形式で認証するには、コマンド ラインまたはターミナルを開き、次のコマンドを使用します。

```azurecli-interactive
az login
```

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザーを開き、コマンド ラインの指示に従う必要があります。 この手順では、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスして認証コードを入力する必要があります。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

その他の認証方法については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

## <a name="install-the-machine-learning-extension"></a>機械学習拡張機能をインストールする

機械学習拡張機能インストールするには、次のコマンドを使用します。

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、次の Azure サービスまたはエンティティに依存しています。

> [!IMPORTANT]
> 既存の Azure サービスを指定しない場合は、ワークスペースの作成時に自動的に作成されます。 リソース グループは必ず指定する必要があります。 独自のストレージ アカウントをアタッチする場合は、次の条件を満たしていることを確認してください。
>
> * ストレージ アカウントが Premium アカウント (Premium_LRS と Premium_GRS) "_ではない_"
> * Azure Blob と Azure File の両方の機能が有効になっている
> * 階層型名前空間 (ADLS Gen 2) が無効になっている
>
> これらの要件は、ワークスペースによって使用される "_既定_" のストレージ アカウントにのみ使用されます。

| サービス | 既存のインスタンスを指定するパラメーター |
| ---- | ---- |
| **Azure リソース グループ** | `-g <resource-group-name>`
| **Azure Storage アカウント** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) では現在、リソース グループ名に Unicode 文字を使用することはできません。 この問題を軽減するには、これらの文字が含まれていないリソース グループを使用します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure Machine Learning ワークスペースは、リソース グループ内に作成する必要があります。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。 __新しいリソース グループを作成__ するには、次のコマンドを使用します。 `<resource-group-name>` をこのリソース グループに使用する名前に置き換えます。 `<location>` をこのリソース グループに使用する Azure リージョンに置き換えます。

> [!TIP]
> Azure Machine Learning が使用可能なリージョンを選択する必要があります。 詳細については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)」を参照してください。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

このコマンドからの応答は、次の JSON のようになります。

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

リソース グループの操作の詳細については、「[az group](/cli/azure/group)」を参照してください。

### <a name="automatically-create-required-resources"></a>必要なリソースを自動的に作成する

__サービスが自動的に作成__ される新しいワークスペースを作成するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> ワークスペース名では、大文字と小文字は区別されません。

このコマンドの出力は、次の JSON のようになります。

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>仮想ネットワークとプライベート エンドポイント

> [!IMPORTANT]
> Azure Government リージョンでは、プライベート リンクで Azure Machine Learning ワークスペースを使用することはできません。

ワークスペースへのアクセスを仮想ネットワークに制限する場合、次のパラメーターを使用できます。

* `--pe-name`:作成するプライベート エンドポイントの名前。
* `--pe-auto-approval`:ワークスペースへのプライベート エンドポイント接続を自動的に承認するかどうか。
* `--pe-resource-group`:プライベート エンドポイントを作成するリソース グループ。 仮想ネットワークが含まれているグループと同じグループでなければなりません。
* `--pe-vnet-name`:プライベート エンドポイントを作成する既存の仮想ネットワーク。
* `--pe-subnet-name`:プライベート エンドポイントを作成するサブネットの名前。 既定値は `default` です。

お使いのワークスペースでのプライベート エンドポイントと仮想ネットワークの使用の詳細については、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」を参照してください。

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>カスタマー マネージド キーと High Business Impact ワークスペース

ワークスペースのメタデータは、既定で Microsoft が管理する Azure Cosmos DB インスタンスに格納されます。 このデータは Microsoft のマネージド キーで暗号化されます。

> [!NOTE]
> Azure Cosmos DB は、モデルのパフォーマンス、実験によってログに記録された情報、またはモデル デプロイからログに記録された情報などの情報を格納するために使用されることは __ありません__。 これらの項目の監視の詳細については、アーキテクチャと概念に関する記事の「[監視とログ記録](concept-azure-machine-learning-architecture.md)」セクションを参照してください。

Microsoft のマネージド キーを使用する代わりに、独自のキーを指定することもできます。 これを行うと、自分の Azure サブスクリプションにメタデータを格納する Azure Cosmos DB インスタンスが作成されます。 `--cmk-keyvault` パラメーターを使用して、キーを格納する Azure Key Vault を指定し、`--resource-cmk-uri` を使用してコンテナー内のキーの URL を指定します。

`--cmk-keyvault` と `--resource-cmk-uri` パラメーターを使用する前に、まず次のアクションを実行する必要があります。

1. サブスクリプションに対する共同作成者のアクセス許可を使用して、__Machine Learning アプリ__ (ID とアクセスの管理) を承認します。
1. [カスタマー マネージド キーの構成](../cosmos-db/how-to-setup-cmk.md)に関する記事の手順に従って以下を行います。
    * Azure Cosmos DB プロバイダーを登録する
    * Azure Key Vault を作成して構成する
    * キーを生成する

Azure Cosmos DB インスタンスを手動で作成する必要はありません。ワークスペースの作成時に作成されます。 この Azure Cosmos DB インスタンスは、`<your-resource-group-name>_<GUID>` というパターンに基づく名前を使用して、別のリソース グループ内に作成されます。

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

お客様のワークスペースで Microsoft が収集するデータを制限するには、`--hbi-workspace` パラメーターを使用します。 

> [!IMPORTANT]
> High Business Impact の選択は、ワークスペースの作成時にのみ実行できます。 ワークスペースの作成後にこの設定を変更することはできません。

カスタマー マネージド キーと High Business Impact ワークスペースの詳細については、「[Azure Machine Learning のエンタープライズ セキュリティ](concept-data-encryption.md#encryption-at-rest)」を参照してください。

### <a name="use-existing-resources"></a>既存のリソースの使用

既存のリソースを使用するワークスペースを作成するには、そのリソースの ID を指定する必要があります。 次のコマンドを使用して、サービスの ID を取得します。

> [!IMPORTANT]
> 既存のリソースをすべて指定する必要はありません。 1 つ以上を指定できます。 たとえば、既存のストレージ アカウントを指定すると、ワークスペースによって他のリソースが作成されます。

+ **Azure Storage アカウント**: `az storage account show --name <storage-account-name> --query "id"`

    このコマンドからの応答は、次のテキストのようになり、ご使用のストレージ アカウントの ID になります。

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > 既存の Azure Storage アカウントを使用する場合は、Premium アカウント (Premium_LRS と Premium_GRS) にすることはできません。 また、階層的名前空間 (Azure Data Lake Storage Gen2 で使用されます) を含めることもできません。 ワークスペースの "_既定_" のストレージ アカウントでは、Premium Storage と階層型名前空間のどちらもサポートされていません。 "_既定以外_" のストレージ アカウントでは、Premium Storage または階層型名前空間を使用できます。

+ **Azure Application Insights**:

    1. Application Insights 拡張機能をインストールします。

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Application Insights サービスの ID を取得します。

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        このコマンドからの応答は次のテキストのようになり、Application Insights サービスの ID になります。

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    このコマンドからの応答は、次のテキストのようになり、ご使用のキー コンテナーの ID になります。

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    このコマンドからの応答は、次のテキストのようになり、ご使用のコンテナー レジストリの ID になります。

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > コンテナー レジストリを Azure Machine Learning ワークスペースで使用するには、事前に[管理者アカウント](../container-registry/container-registry-authentication.md#admin-account)が有効になっている必要があります。

ワークスペースで使用するリソースの ID を取得したら、ベース コマンド `az workspace create -w <workspace-name> -g <resource-group-name>` を使用して、既存のリソースのパラメーターと ID を追加します。 たとえば、次のコマンドでは、既存のコンテナー レジストリを使用するワークスペースが作成されます。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>ワークスペースの一覧表示

Azure サブスクリプションのすべてのワークスペースを一覧表示するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace list
```

このコマンドの出力は、次の JSON のようになります。

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

詳細については、[az ml workspace list](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-list) のドキュメントをご覧ください。

## <a name="get-workspace-information"></a>ワークスペース情報の取得

ワークスペースに関する情報を取得するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

詳細については、[az ml workspace show](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-show) のドキュメントをご覧ください。

## <a name="update-a-workspace"></a>ワークスペースの更新

ワークスペースを更新するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

このコマンドの出力は、次の JSON のようになります。

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

詳細については、「[az ml workspace update](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-update)」のドキュメントをご覧ください。

## <a name="share-a-workspace-with-another-user"></a>別のユーザーとワークスペースの共有

サブスクリプションの別のユーザーとワークスペースを共有するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Azure Machine Learning を使用した Azure ロールベースのアクセス制御 (Azure RBAC) の詳細については、[ユーザーとロールの管理](how-to-assign-roles.md)に関するページを参照してください。

詳細については、[az ml workspace share](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-share) のドキュメントをご覧ください。

## <a name="sync-keys-for-dependent-resources"></a>依存リソースのキーの同期

ワークスペースに使用されているいずれかのリソースのアクセス キーを変更すると、ワークスペースが新しいキーと同期されるまでに約 1 時間かかります。 ワークスペースで新しいキーを直ちに同期させるには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

キーの変更の詳細については、「[ストレージ アカウント キーの再生成](how-to-change-storage-access-key.md)」を参照してください。

詳細については、[az ml workspace sync-keys](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-sync-keys) のドキュメントをご覧ください。

## <a name="delete-a-workspace"></a>ワークスペースを削除する

不要になったワークスペースを削除するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> ワークスペースを削除しても、ワークスペースで使用されている Application Insights、ストレージ アカウント、キー コンテナー、またはコンテナー レジストリは削除されません。

リソース グループを削除することもできます。これにより、ワークスペースとリソース グループ内の他のすべての Azure リソースが削除されます。 リソース グループを削除するには、次のコマンドを使用します。

```azurecli-interactive
az group delete -g <resource-group-name>
```

詳細については、[az ml workspace delete](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-delete) のドキュメントをご覧ください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>ワークスペースの移動

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry の削除

Azure Machine Learning ワークスペースでは、一部の操作に対して Azure Container Registry (ACR) が使用されます。 これにより ACR インスタンスは、最初に必要になったときに自動的に作成されます。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>次のステップ

機械学習の Azure CLI 拡張機能の詳細については、[az ml](/cli/azure/ext/azure-cli-ml/ml) のドキュメントを参照してください。