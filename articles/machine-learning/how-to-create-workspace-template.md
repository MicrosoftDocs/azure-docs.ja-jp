---
title: Azure Resource Manager テンプレートでワークスペースを作成する
titleSuffix: Azure Machine Learning
description: Azure Resource Manager テンプレートを使用して新しい Azure Machine Learning ワークスペースを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.openlocfilehash: 9df8a67fd3dfbf23986f1cc5ed18392463fc7ecb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522208"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成します。


<br>

この記事では、Azure Resource Manager テンプレートを使用して Azure Machine Learning ワークスペースを作成するさまざまな方法について説明します。 Resource Manager テンプレートを使用すると、1 つの調整された操作でリソースを簡単に作成できます。 テンプレートは、デプロイに必要なリソースを定義する JSON ドキュメントです。 デプロイ パラメーターを指定することもできます。 パラメーターは、テンプレートの使用時に入力値を指定するために使用します。

詳細については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* CLI からテンプレートを使用するには、[Azure PowerShell](/powershell/azure/) または [Azure CLI](/cli/azure/install-azure-cli) が必要です。

* 一部のシナリオでは、サポート チケットを開く必要があります。 たとえば、カスタマーマネージド キーを使用する Private Link 対応ワークスペースを使います。 詳細については、[クォータの管理と増加](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)に関するページを参照してください。

## <a name="limitations"></a>制限事項

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="workspace-resource-manager-template"></a>ワークスペースの Resource Manager テンプレート

このドキュメント全体で使用する Azure Resource Manager テンプレートは、Azure クイックスタート テンプレートの GitHub リポジトリの [201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) ディレクトリにあります。

このテンプレートでは、次の Azure サービスが作成されます。

* Azure Storage アカウント
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning ワークスペース

リソース グループは、サービスを保持するコンテナーです。 Azure Machine Learning ワークスペースにはさまざまなサービスが必要です。

サンプルのテンプレートには、次に示す 2 つの **必須** パラメーターがあります。

* **location** は、リソースが作成される場所です。

    このテンプレートでは、大部分のリソース用に選択する場所が使用されます。 例外は Application Insights サービスです。このサービスは、他のサービスが存在するすべての場所で使用できません。 使用できない場所を選択すると、米国中南部の場所にサービスが作成されます。

* **workspaceName** は、Azure Machine Learning ワークスペースのフレンドリ名です。

    > [!NOTE]
    > ワークスペース名では、大文字と小文字は区別されません。

    その他のサービスの名前はランダムに生成されます。

> [!TIP]
> このドキュメントに関連付けられているテンプレートでは新しい Azure コンテナー レジストリが作成されますが、コンテナー レジストリを作成せずに新しいワークスペースを作成することもできます。 コンテナー レジストリが必要な操作を実行すると、それが 1 つ作成されます。 たとえば、モデルのトレーニングやデプロイなどです。
>
> 新しいコンテナー レジストリまたはストレージ アカウントを作成するのではなく、Azure Resource Manager テンプレートで既存のものを参照することもできます。 これを行う場合、[マネージド ID を使用するか](how-to-use-managed-identities.md) (プレビュー)、コンテナー レジストリの[管理者アカウントを有効にする](../container-registry/container-registry-authentication.md#admin-account)必要があります。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

テンプレートの詳細については、次の記事を参照してください。

* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices resource types (Microsoft.MachineLearningServices リソースの種類)](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、リソース グループを作成する必要があります。

グラフィカル ユーザー インターフェイスを使用する場合は、[Azure portal](#use-the-azure-portal) に関するセクションを参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

リソース グループが正常に作成されたら、次のコマンドを使用してテンプレートをデプロイします。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

既定では、テンプレートの一部として作成されるすべてのリソースは新規です。 ただし、既存のリソースを使用することもできます。 テンプレートに追加のパラメーターを指定することで、既存のリソースを使用できます。 たとえば、既存のストレージ アカウントを使用する場合は、**storageAccountOption** の値を **existing** に設定し、**storageAccountName** パラメーターにストレージ アカウントの名前を指定します。

> [!IMPORTANT]
> 既存の Azure Storage アカウントを使用する場合は、Premium アカウント (Premium_LRS と Premium_GRS) にすることはできません。 また、階層的名前空間 (Azure Data Lake Storage Gen2 で使用されます) を含めることもできません。 ワークスペースの既定のストレージ アカウントでは、Premium Storage と階層型名前空間はサポートされていません。 ワークスペースの _既定の_ ストレージ アカウントでは、Premium Storage と階層型名前空間はサポートされていません。 "_既定以外_" のストレージ アカウントでは、Premium Storage または階層型名前空間を使用できます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>暗号化ワークスペースのデプロイ

次のテンプレート例では、3 つの設定を使用してワークスペースを作成する方法を示します。

* ワークスペースの高機密性の設定を有効にする。 これにより、新しい Cosmos DB インスタンスが作成されます。
* ワークスペースの暗号化を有効にする。
* 既存の Azure Key Vault を使用して、カスタマー マネージド キーを取得する。 カスタマー マネージド キーは、ワークスペースの新しい Cosmos DB インスタンスを作成するために使用されます。

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> ワークスペースが作成されたら、機密データ、暗号化、Key Vault ID、またはキー識別子の設定を変更することはできません。 これらの値を変更するには、新しい値を使用して新しいワークスペースを作成する必要があります。

詳細については、「[保存時の暗号化](concept-data-encryption.md#encryption-at-rest)」を参照してください。

> [!IMPORTANT]
> このテンプレートを使用する前に、サブスクリプションで満たしている必要がある特定の要件がいくつかあります。
> * 暗号化キーを含む既存の Azure Key Vault が必要です。
> * Azure Key Vault は、Azure Machine Learning ワークスペースの作成を計画しているリージョンと同じリージョンにある必要があります。
> * Azure Key Vault の ID と、暗号化キーの URI を指定する必要があります。

`cmk_keyvault` (Key Vault の ID) およびこのテンプレートで必要な `resource_cmk_uri` (キー URI) パラメーターの __値を取得するには__、次の手順を使用します。    

1. Key Vault ID を取得するには、次のコマンドを使用します。  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    このコマンドでは `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` のような値が返されます。  

1. カスタマー マネージド キーの URI の値を取得するには、次のコマンドを使用します。    

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    このコマンドでは `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` のような値が返されます。 

> [!IMPORTANT]  
> ワークスペースが作成されたら、機密データ、暗号化、Key Vault ID、またはキー識別子の設定を変更することはできません。 これらの値を変更するには、新しい値を使用して新しいワークスペースを作成する必要があります。

カスタマー マネージド キーの使用を有効にするには、テンプレートをデプロイするときに次のパラメーターを設定します。

* **encryption_status** を **Enabled** に
* **cmk_keyvault** を、前の手順で取得した `cmk_keyvault` の値に
* **resource_cmk_uri** を、前の手順で取得した `resource_cmk_uri` の値に

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

カスタマー マネージド キーを使用する場合、Azure Machine Learning により、Cosmos DB インスタンスを含むセカンダリ リソース グループが作成されます。 詳細については、「[保存時の暗号化 - Cosmos DB](concept-data-encryption.md#encryption-at-rest)」を参照してください。

データに対して提供できる追加の構成は、**confidential_data** パラメーターを **true** に設定することです。 これにより、次の処理が行われます。

* サブスクリプションで以前にクラスターを作成していない場合に、Azure Machine Learning コンピューティング クラスターに対してローカル スクラッチ ディスクの暗号化を開始します。 以前にサブスクリプションでクラスターを作成したことがある場合は、サポート チケットを開き、ご利用のコンピューティング クラスターでスクラッチ ディスクの暗号化を有効にします。
* 実行間でローカル スクラッチ ディスクをクリーンアップします。
* キー コンテナーを使用して、ストレージ アカウント、コンテナー レジストリ、SSH アカウントの資格情報を実行層からコンピューティング クラスターに安全に渡します。
* AzureMachineLearningService 以外の外部サービスから基になる Batch プールを呼び出すことができないように、IP フィルタリングを有効にします。

    > [!IMPORTANT]
    > ワークスペースが作成されたら、機密データ、暗号化、Key Vault ID、またはキー識別子の設定を変更することはできません。 これらの値を変更するには、新しい値を使用して新しいワークスペースを作成する必要があります。

  詳細については、「[保存時の暗号化](concept-data-encryption.md#encryption-at-rest)」を参照してください。

## <a name="deploy-workspace-behind-a-virtual-network"></a>仮想ネットワークの背後にワークスペースをデプロイする

`vnetOption` パラメーターの値を `new` または `existing` に設定すると、ワークスペースが使用するリソースを仮想ネットワークの背後に作成できます。

> [!IMPORTANT]
> コンテナー レジストリでは、"Premium" SKU のみがサポートされます。

> [!IMPORTANT]
> Application Insights では、仮想ネットワークの背後へのデプロイはサポートされていません。

### <a name="only-deploy-workspace-behind-private-endpoint"></a>プライベート エンドポイントの背後にのみワークスペースをデプロイする

関連付けられたリソースが仮想ネットワークの背後にない場合、**privateEndpointType** パラメーターを `AutoAproval` または `ManualApproval` に設定すると、ワークスペースをプライベート エンドポイントの背後にデプロイできます。 これは、新規および既存のワークスペースの両方に対して行うことができます。 既存のワークスペースを更新する場合は、テンプレート パラメーターに既存のワークスペースの情報を入力します。

> [!IMPORTANT]
> Azure Government リージョンでは、プライベート リンクで Azure Machine Learning ワークスペースを使用することはできません。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>新しい仮想ネットワークを使用する

新しい仮想ネットワークの背後にリソースをデプロイするには、それぞれのリソースの仮想ネットワーク設定と共に **vnetOption** を **new** に設定します。 次のデプロイは、ワークスペースをストレージ アカウント リソースと共に新しい仮想ネットワークの背後にデプロイする方法を示しています。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

または、複数のリソースまたはすべての依存リソースを仮想ネットワークの背後にデプロイできます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>既存の仮想ネットワークとリソースを使用する

既存の関連付けられたリソースと共にワークスペースをデプロイするには、サブネット パラメーターと共に **vnetOption** パラメーターを **existing** に設定する必要があります。 ただし、デプロイの **前に**、各リソースの仮想ネットワークにサービス エンドポイントを作成する必要があります。 新しい仮想ネットワークのデプロイと同様に、1 つまたはすべてのリソースを仮想ネットワークの背後にデプロイできます。

> [!IMPORTANT]
> サブネットには `Microsoft.Storage` サービス エンドポイントが必要です。

> [!IMPORTANT]
> サブネットでは、プライベート エンドポイントの作成は許可されていません。 サブネットを有効にするには、プライベート エンドポイントを無効にします。

1. リソースのサービス エンドポイントを有効にします。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. ワークスペースをデプロイする

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. 「[カスタム テンプレートからリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)」の手順に従います。 __[テンプレートの選択]__ 画面に到達したら、**201-machine-learning-advanced** テンプレートをドロップダウンから選択します。
1. テンプレートを使用するには、 __[テンプレートの選択]__ を選択します。 デプロイのシナリオに応じて、以下の必須情報とその他のパラメーターを指定します。

   * サブスクリプション:これらのリソースに使用する Azure サブスクリプションを選択します。
   * リソース グループ: サービスが含まれるリソース グループを選択または作成します。
   * リージョン:リソースが作成される Azure リージョンを選択します。
   * ワークスペース名: 作成される Azure Machine Learning ワークスペースに使用する名前。 ワークスペース名は 3 から 33 文字で指定する必要があります。 使用できるのは英数字と "-" のみです。
   * 場所:リソースを作成する場所を選択します。
1. __[Review + create]\(レビュー + 作成\)__ を選択します。
1. __[Review + create]\(レビュー + 作成\)__ 画面で、表示された使用条件に同意して __[作成]__ を選択します。

詳細については、「[カスタム テンプレートからリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault アクセス ポリシーと Azure Resource Manager テンプレート

これは、Azure Resource Manager テンプレートを使用してワークスペースおよび関連付けられたリソース (Azure Key Vault など) を複数回作成する場合があります。 たとえば、継続的インテグレーションおよびデプロイ パイプラインの一部として同じパラメーターを指定して、テンプレートを複数回使用する場合が挙げられます。

テンプレートによるリソース作成操作のほとんどはべき等操作ですが、テンプレートを使用するたびに Key Vault によってアクセス ポリシーはクリアされます。 アクセス ポリシーをクリアすると、それを使用している既存のワークスペース用の Key Vault へのアクセスは中断されます。 たとえば、Azure Notebooks VM の停止/作成機能が失敗することがあります。  

この問題を回避するには、次のいずれかのアプローチをお勧めします。

* パラメーターが同じである場合は、テンプレートを複数回デプロイしないでください。 または、テンプレートを使用してリソースを作成する前に、既存の同じものを削除してください。

* Key Vault のアクセス ポリシーを調べ、これらのポリシーを使用してテンプレートの `accessPolicies` プロパティを設定します。 アクセス ポリシーを表示するには、次の Azure CLI コマンドを使用します。

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    テンプレートの `accessPolicies` セクションの使用方法の詳細については、[AccessPolicyEntry オブジェクトのリファレンス](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)を参照してください。

* Key Vault リソースが既に存在しているかどうかを確認します。 存在している場合は、テンプレートを使ってそれを再作成しないでください。 たとえば、新規作成するのではなく、既存の Key Vault を使用するには、テンプレートに次の変更を加えます。

    * 既存の Key Vault リソースの ID を受け入れるパラメーターを **追加** します。

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * Key Vault リソースを作成するセクションを **削除** します。

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * ワークスペースの `dependsOn` セクションから `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` の行を **削除** します。 また、`keyVaultId` パラメーターを参照するために、ワークスペースの `properties` セクションの `keyVault` エントリを **変更** します。

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    これらの変更が完了したら、テンプレートを実行するときに既存の Key Vault リソースの ID を指定できます。 その後、テンプレートは、ワークスペースの `keyVault` プロパティをその ID に設定することによって、Key Vault を再利用します。

    Key Vault の ID を取得するには、元のテンプレート実行の出力を参照するか、Azure CLI を使用します。 次のコマンドは、Azure CLI を使用して Key Vault リソースの ID を取得する例です。

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    このコマンドにより、次のテキストのような値が返されます。

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>プライベート DNS ゾーンにリンクされていない仮想ネットワーク

プライベート エンドポイントを使用してワークスペースを作成する場合、テンプレートによって __privatelink.api.azureml.ms__ という名前のプライベート DNS ゾーンが作成されます。 __仮想ネットワーク リンク__ が、このプライベート DNS ゾーンに自動的に追加されます。 このリンクは、リソース グループに作成する最初のワークスペースとプライベート エンドポイントに対してのみ追加されます。同じリソース グループ内にプライベート エンドポイントを持つ別の仮想ネットワークとワークスペースを作成した場合、2 番目の仮想ネットワークがプライベート DNS ゾーンに追加されないことがあります。

プライベート DNS ゾーンに既に存在する仮想ネットワーク リンクを表示するには、次の Azure CLI コマンドを使用します。

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

別のワークスペースとプライベート エンドポイントを含む仮想ネットワークを追加するには、次の手順を使用します。

1. 追加するネットワークの仮想ネットワーク ID を検索するには、次のコマンドを使用します。

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    このコマンドは、"/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" のような値を返します。 この値を保存し、次の手順で使用します。

2. privatelink.api.azureml.ms プライベート DNS ゾーンに仮想ネットワーク リンクを追加するには、次のコマンドを使用します。 `--virtual-network` パラメーターには、前のコマンドの出力を使用します。

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>次のステップ

* [Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-rest.md)。
* [Visual Studio での Azure リソース グループの作成とデプロイ](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。
* [Azure Machine Learning に関連するその他のテンプレートについては、Azure クイックスタート テンプレート リポジトリを参照してください。](https://github.com/Azure/azure-quickstart-templates)