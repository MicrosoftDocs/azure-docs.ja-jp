---
title: Azure CLI を使用したワークスペースの作成
titleSuffix: Azure Machine Learning
description: Azure CLI を使用して、新しい Azure Machine Learning ワークスペースを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 08/30/2019
ms.openlocfilehash: 75487906e4323ea12a47d75164617212bd3e65d9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002645"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Azure CLI を使用して Azure Machine Learning のワークスペースを作成する

この記事では、Azure CLI を使用して Azure Machine Learning ワークスペースを作成する方法について説明します。 Azure CLI には、Azure リソースを管理するためのコマンドが用意されています。 CLI の機械学習拡張機能には、Azure Machine Learning リソースを操作するためのコマンドが用意されています。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning ](https://aka.ms/AMLFree)をお試しください。

* ご使用の**ローカル環境**からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) が必要です。

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) を使用する場合は、ブラウザーを使用してクラウド内に存在する CLI にアクセスします。

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure サブスクリプションへの CLI の接続

> [!IMPORTANT]
> Azure Cloud Shell を使用している場合は、このセクションを省略できます。 Cloud Shell では、Azure サブスクリプションにログインするアカウントを使用して自動的に認証が行われます。

CLI から Azure サブスクリプションを認証するには、いくつかの方法があります。 最も基本的な方法は、ブラウザーを使用して対話形式で認証することです。 対話形式で認証するには、コマンド ラインまたはターミナルを開き、次のコマンドを使用します。

```azurecli
az login
```

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザーを開き、コマンド ラインの指示に従う必要があります。 この手順では、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスして認証コードを入力する必要があります。

その他の認証方法については、「[Azure CLI を使用してサインインする](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」を参照してください。

## <a name="install-the-machine-learning-extension"></a>機械学習拡張機能をインストールする

機械学習拡張機能インストールするには、次のコマンドを使用します。

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、次の Azure サービスまたはエンティティに依存しています。

> [!IMPORTANT]
> 既存の Azure サービスを指定しない場合は、ワークスペースの作成時に自動的に作成されます。 リソース グループは必ず指定する必要があります。

| Service | 既存のインスタンスを指定するパラメーター |
| ---- | ---- |
| **Azure リソース グループ** | `-g <resource-group-name>`
| **Azure Storage アカウント** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>リソース グループの作成

Azure Machine Learning ワークスペースは、リソース グループ内に作成する必要があります。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。 __新しいリソース グループを作成__するには、次のコマンドを使用します。 `<resource-group-name>` をこのリソース グループに使用する名前に置き換えます。 `<location>` をこのリソース グループに使用する Azure リージョンに置き換えます。

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

リソース グループの操作の詳細については、「[az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)」を参照してください。

### <a name="automatically-create-required-resources"></a>必要なリソースを自動的に作成する

__サービスが自動的に作成__される新しいワークスペースを作成するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
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

### <a name="use-existing-resources"></a>既存のリソースの使用

既存のリソースを使用するワークスペースを作成するには、そのリソースの ID を指定する必要があります。 次のコマンドを使用して、サービスの ID を取得します。

> [!IMPORTANT]
> 既存のリソースをすべて指定する必要はありません。 1 つ以上を指定できます。 たとえば、既存のストレージ アカウントを指定すると、ワークスペースによって他のリソースが作成されます。

+ **Azure Storage アカウント**: `az storage account show --name <storage-account-name> --query "id"`

    このコマンドからの応答は、次のテキストのようになり、ご使用のストレージ アカウントの ID になります。

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application Insights**:

    1. Application Insights 拡張機能をインストールします。

        ```bash
        az extension add -n application-insights
        ```

    2. Application Insights サービスの ID を取得します。

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        このコマンドからの応答は次のテキストのようになり、Application Insights サービスの ID になります。

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"

    このコマンドからの応答は、次のテキストのようになり、ご使用のキー コンテナーの ID になります。

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    このコマンドからの応答は、次のテキストのようになり、ご使用のコンテナー レジストリの ID になります。

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > コンテナー レジストリを Azure Machine Learning ワークスペースで使用するには、事前に[管理者アカウント](/azure/container-registry/container-registry-authentication#admin-account)が有効になっている必要があります。

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

詳細については、[az ml workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) のドキュメントをご覧ください。

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

詳細については、[az ml workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) のドキュメントをご覧ください。

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

詳細については、「[az ml workspace update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)」のドキュメントをご覧ください。

## <a name="share-a-workspace-with-another-user"></a>別のユーザーとワークスペースの共有

サブスクリプションの別のユーザーとワークスペースを共有するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Azure Machine Learning を使用したロールベースのアクセス制御 (RBAC) の詳細については、[ユーザーとロールの管理](how-to-assign-roles.md)に関するページを参照してください。

詳細については、[az ml workspace share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) のドキュメントをご覧ください。

## <a name="sync-keys-for-dependent-resources"></a>依存リソースのキーの同期

ワークスペースで使用されているリソースのいずれかのアクセス キーを変更する場合は、次のコマンドを使用して、新しいキーとワークスペースを同期します。

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

キーの変更の詳細については、「[ストレージ アカウント キーの再生成](how-to-change-storage-access-key.md)」を参照してください。

詳細については、[az ml workspace sync-keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) のドキュメントをご覧ください。

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

詳細については、[az ml workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) のドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順

機械学習の Azure CLI 拡張機能の詳細については、[az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) のドキュメントを参照してください。
