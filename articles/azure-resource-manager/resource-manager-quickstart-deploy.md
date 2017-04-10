---
title: "Azure にリソースをデプロイする | Microsoft Docs"
description: "Azure PowerShell または Azure CLI を使用して、Azure にリソースをデプロイします。 リソースは Resource Manager テンプレートで定義されます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 41d9476d4ce6b7afc2ba9c757e96db7e8e72d5ae
ms.openlocfilehash: a77354d0719240e5916b5ec945dad75e534802d3
ms.lasthandoff: 02/21/2017


---
# <a name="deploy-resources-to-azure"></a>Azure にリソースをデプロイする

このトピックでは、Azure サブスクリプションにリソースをデプロイする方法について説明します。 Azure PowerShell または Azure CLI を使用して、ソリューションのインフラストラクチャを定義した Resource Manager テンプレートをデプロイできます。

Resource Manager の概念については、「[Azure Resource Manager の概要](resource-group-overview.md)」をご覧ください。

## <a name="steps-for-deployment"></a>デプロイの手順

ここでは、このトピックの[サンプル ストレージ テンプレート](#example-storage-template)をデプロイすることを想定しています。 別のテンプレートを使用することもできますが、渡すパラメーターはこのトピックで示すパラメーターとは異なります。

テンプレートの作成後、テンプレートをデプロイする一般的な手順のとおりです。

1. アカウントへのログイン
2. 使用するサブスクリプションの選択 (複数のサブスクリプションがあり、既定のサブスクリプション以外のサブスクリプションを使用する場合にのみ必要)
3. リソース グループの作成
4. テンプレートのデプロイ
5. デプロイの状態の確認

以降のセクションでは、[PowerShell](#powershell) または [Azure CLI](#azure-cli) を使用してこの手順を実行する方法について説明します。

## <a name="powershell"></a>PowerShell

1. Azure PowerShell のインストール方法については、「[Azure PowerShell コマンドレットの使用開始](/powershell/azureps-cmdlets-docs)」をご覧ください。

2. デプロイを簡単に開始するには、次のコマンドレットを使用します。

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  `Set-AzureRmContext` コマンドレットは、既定のサブスクリプション以外のサブスクリプションを使用する場合にのみ必要です。 すべてのサブスクリプションとその ID を表示するには、次のコマンドレットを使用します。

  ```powershell
  Get-AzureRmSubscription
  ```

3. デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のようなメッセージが表示されます。

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. サブスクリプションにデプロイされたリソース グループとストレージ アカウントを表示するには、次のコマンドレットを使用します。

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. テンプレートをデプロイするときに、PowerShell のパラメーターとしてテンプレートのパラメーターを指定できます。 前の例にはテンプレートのパラメーターが含まれていないので、テンプレートの既定値が使用されます。 別のストレージ アカウントをデプロイし、ストレージ名のプレフィックスとストレージ アカウント SKU のパラメーター値を指定するには、次のコマンドレットを使用します。

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  これで、リソース グループに&2; つのストレージ アカウントが含まれるようになりました。 

## <a name="azure-cli"></a>Azure CLI

1. Azure CLI のインストール方法については、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](/cli/azure/install-az-cli2)」をご覧ください。

2. デプロイを簡単に開始するには、次のコマンドを使用します。

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  `az account set` コマンドは、既定のサブスクリプション以外のサブスクリプションを使用する場合にのみ必要です。 すべてのサブスクリプションとその ID を表示するには、次のコマンドを使用します。

  ```azurecli
  az account list
  ```

3. デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のようなメッセージが表示されます。

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. サブスクリプションにデプロイされたリソース グループとストレージ アカウントを表示するには、次のコマンドを使用します。

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. テンプレートをデプロイするときに、PowerShell のパラメーターとしてテンプレートのパラメーターを指定できます。 前の例にはテンプレートのパラメーターが含まれていないので、テンプレートの既定値が使用されます。 別のストレージ アカウントをデプロイし、ストレージ名のプレフィックスとストレージ アカウント SKU のパラメーター値を指定するには、次のコマンドを使用します。

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  これで、リソース グループに&2; つのストレージ アカウントが含まれるようになりました。 

## <a name="example-storage-template"></a>サンプル ストレージ テンプレート

次のサンプル テンプレートを使用して、ストレージ アカウントをサブスクリプションにデプロイします。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>次のステップ

* PowerShell を使用したテンプレートのデプロイの詳細については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](/azure/azure-resource-manager/resource-group-template-deploy)」をご覧ください。
* Azure CLI を使用したテンプレートのデプロイの詳細については、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](/azure/azure-resource-manager/resource-group-template-deploy-cli)」をご覧ください。




