---
title: リンクされたテンプレートとしてテンプレート スペックをデプロイする
description: リンクされたデプロイに既存のテンプレート スペックをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 8d4ccd77c8b37a696fab7494a8d3f8052fc89b35
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889265"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>チュートリアル:リンクされたテンプレートとしてテンプレート スペックをデプロイする (プレビュー)

[リンクされたデプロイ](linked-templates.md#linked-template)を使用して既存の[テンプレート スペック](template-specs.md)をデプロイする方法について説明します。 テンプレート スペックは、ARM テンプレートを組織内の他のユーザーと共有するために使用します。 テンプレート スペックを作成したら、Azure PowerShell または Azure CLI を使用してテンプレート スペックをデプロイできます。 リンクされたテンプレートを使用し、ソリューションの一部としてテンプレート スペックをデプロイすることもできます。

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> Template Specs は現在プレビューの段階です。 Azure PowerShell でこれを使用するには、[バージョン 5.0.0 以降](/powershell/azure/install-az-ps)をインストールする必要があります。 Azure CLI でこれを使用するには、[バージョン 2.14.2 以降](/cli/azure/install-azure-cli)を使用します。

## <a name="create-a-template-spec"></a>テンプレート スペックを作成する

[クイック スタート: テンプレート スペックの作成とデプロイ](quickstart-create-template-specs.md)に従って、ストレージ アカウントをデプロイするためのテンプレート スペックを作成します。 次の項では、テンプレート スペックのリソース グループ名、テンプレート スペック名、およびテンプレート スペックのバージョンが必要となります。

## <a name="create-the-main-template"></a>メイン テンプレートを作成する

ARM テンプレートにテンプレート スペックをデプロイするには、メイン テンプレートに[デプロイ リソース](/azure/templates/microsoft.resources/deployments)を追加します。 `templateLink` プロパティにテンプレート スペックのリソース ID を指定します。**azuredeploy. json** という名前の次の JSON を使用して、テンプレートを作成します。 このチュートリアルでは、パス **c:\Templates\deployTS\azuredeploy.json** に保存したことを想定していますが、任意のパスを使用できます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

テンプレート スペック ID は、[`resourceID()`](template-functions-resource.md#resourceid) 関数を使用して生成します。 templateSpec が現在のデプロイと同じリソース グループ内にある場合、resourceID () 関数のリソース グループ引数は省略できます。  リソース ID をパラメーターとして直接渡すこともできます。 ID を取得するには、次の構文を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Windows PowerShell におけるテンプレート スペック ID の取得と変数への割り当てには既知の問題があります。

---

テンプレート スペックにパラメーターを渡す構文は次のとおりです。

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> `Microsoft.Resources/deployments` の apiVersion は 2020-06-01 以降である必要があります。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

リンクされたテンプレートをデプロイすると、Web アプリケーションとストレージ アカウントの両方がデプロイされます。 デプロイは、他の ARM テンプレートのデプロイと同じです。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>次のステップ

リンクされたテンプレートを含むテンプレート スペックを作成する方法については、「[リンクされたテンプレートのテンプレート スペックを作成する](template-specs-create-linked.md)」を参照してください。
