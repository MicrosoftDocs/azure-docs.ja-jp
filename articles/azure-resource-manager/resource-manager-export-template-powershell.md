---
title: "Azure PowerShell を使用した Resource Manager テンプレートのエクスポート | Microsoft Docs"
description: "Azure Resource Manager と Azure PowerShell を使用して、リソース グループからテンプレートをエクスポートします。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2017
ms.author: tomfitz
ms.openlocfilehash: 7543811eb9448222b6e7c266756e68debc7d54be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>PowerShell を使用した Azure Resource Manager テンプレートのエクスポート

Resource Manager を使用すると、サブスクリプション内の既存のリソースから Resource Manager テンプレートをエクスポートできます。 この生成されたテンプレートを使用すると、テンプレートの構文を学習したり、必要に応じてソリューションの再デプロイを自動化したりすることができます。

テンプレートのエクスポートには 2 つの異なる方法があることに注意する必要があります。

* デプロイに使用した実際のテンプレートをエクスポートできます。 エクスポートしたテンプレートには、元のテンプレートで定義されたのと同じパラメーターと変数がすべて含まれます。 この方法は、テンプレートを取得する必要がある場合に役立ちます。
* リソース グループの現在の状態を表すテンプレートをエクスポートできます。 エクスポートしたテンプレートは、デプロイに使用したテンプレートに基づいていません。 代わりに、リソース グループのスナップショットであるテンプレートが作成されます。 エクスポートしたテンプレートにはハードコーディングされた多くの値が含まれ、おそらく、通常定義するのと同程度のパラメーターは含まれません。 この方法は、リソース グループを変更した場合に役立ちます。 そのリソース グループをテンプレートとしてキャプチャする必要が生じた場合に役に立ちます。

このトピックでは、両方の方法を示します。

## <a name="deploy-a-solution"></a>ソリューションのデプロイ

両方のテンプレート エクスポート方法を説明するために、まず、ソリューションをサブスクリプションにデプロイしましょう。 エクスポートするリソース グループがサブスクリプションに既にある場合は、このソリューションをデプロイする必要はありませんが、 この記事では、このソリューションのテンプレートを参照します。 サンプル スクリプトによってストレージ アカウントがデプロイされます。

```powershell
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>デプロイ履歴からのテンプレートの保存

デプロイ履歴からテンプレートを取得するには、[Save-AzureRmResourceGroupDeploymentTemplate](/powershell/module/azurerm.resources/save-azurermresourcegroupdeploymenttemplate) コマンドを使用します。 次の例では、前にデプロイしたテンプレートを保存します。

```powershell
Save-AzureRmResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

これによりテンプレートの場所が返されます。

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

ファイルを開きます。これがデプロイに使用したテンプレートであることに注意してください。 パラメーターと変数は、GitHub のテンプレートと一致します。 このテンプレートを再デプロイできます。

## <a name="export-resource-group-as-template"></a>リソース グループをテンプレートとしてエクスポート

デプロイ履歴からテンプレートを取得するのではなく、リソース グループの現在の状態を表すテンプレートを取得するには、[Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) コマンドを使用します。 このコマンドは、リソース グループを大幅に変更し、すべての変更が反映されたテンプレートがないときに使用します。

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleGroup
```

これによりテンプレートの場所が返されます。

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

ファイルを開きます。これが GitHub のテンプレートと異なることに注意してください。 パラメーターが異なっており、変数は含まれません。 ストレージ SKU と場所は、値にハードコーディングされています。 次の例は、エクスポートされたテンプレートを示していますが、ご利用のテンプレートではパラメーター名が若干異なります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

このテンプレートを再デプロイできますが、ストレージ アカウントの一意の名前を推測する必要があります。 パラメーターの名前は若干異なります。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
```

## <a name="customize-exported-template"></a>エクスポートされたテンプレートのカスタマイズ

このテンプレートはさらに使いやすく、柔軟性のあるものに変更できます。 さらに多くの場所を許可するには、リソース グループと同じ場所を使用するように location プロパティを変更します。

```json
"location": "[resourceGroup().location]",
```

ストレージ アカウントの一意の名前を推測しなくてもすむようにするには、ストレージ アカウント名のパラメーターを削除します。 ストレージ名のサフィックスのパラメーター、およびストレージ SKU を追加します。

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

uniqueString 関数でストレージ アカウント名を構成する変数を追加します。

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

ストレージ アカウントの名前を変数に設定します。

```json
"name": "[variables('storageAccountName')]",
```

SKU をパラメーターに設定します。

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

ここまでの作業が終わった時点で、テンプレートは以下のようになります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

変更したテンプレートを再デプロイします。

## <a name="next-steps"></a>次のステップ
* ポータルを使用したテンプレートのエクスポートについては、「[既存のリソースから Azure Resource Manager テンプレートをエクスポートする](resource-manager-export-template.md)」を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
