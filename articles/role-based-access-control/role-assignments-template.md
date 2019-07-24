---
title: RBAC と Azure Resource Manager テンプレートを使用して Azure リソースへのアクセスを管理する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) と Azure Resource Manager テンプレートを使用してユーザー、グループ、アプリケーションの Azure リソースへのアクセスを管理する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 537ee35e96a41cd02605319e244d39c6567c3bf1
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337205"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>RBAC と Azure Resource Manager テンプレートを使用して Azure リソースへのアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 Azure PowerShell または Azure CLI を使う以外に、RBAC と [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使って Azure リソースへのアクセスを管理することもできます。 リソースを一貫して繰り返しデプロイする場合は、テンプレートが便利です。 この記事では、RBAC とテンプレートを使ってアクセスを管理する方法について説明します。

## <a name="example-template-to-create-a-role-assignment"></a>ロールの割り当てを作成するためのテンプレートの例

RBAC でアクセス権を付与するには、ロールの割り当てを作成します。 以下のテンプレートでは次のことを示します。
- リソース グループのスコープでユーザー、グループ、またはアプリケーションにロールを割り当てる方法
- 所有者、共同作成者、閲覧者のロールをパラメーターとして指定する方法

テンプレートを使うには、次の入力を指定する必要があります。
- リソース グループの名前
- ロールを割り当てるユーザー、グループ、またはアプリケーションの一意識別子
- 割り当てるロール
- ロールの割り当てに使われる一意識別子

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

以下では、テンプレートをデプロイした後でユーザーに閲覧者ロールを割り当てる例を示します。

![テンプレートを使用したロールの割り当て](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Azure PowerShell を使用してテンプレートを展開する

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Azure PowerShell を使用して前のテンプレートをデプロイするには、以下の手順のようにします。

1. rbac-rg.json という名前の新しいファイルを作成し、前に示したテンプレートをコピーします。

1. [Azure PowerShell](/powershell/azure/authenticate-azureps) にサインインします。

1. ユーザー、グループ、またはアプリケーションの一意識別子を取得します。 たとえば、[Get-AzADUser](/powershell/module/az.resources/get-azaduser) コマンドを使って Azure AD ユーザーの一覧を表示できます。

    ```azurepowershell
    Get-AzADUser
    ```

1. GUID ツールを使って、ロールの割り当てに使う一意識別子を生成します。 この識別子の形式は `11111111-1111-1111-1111-111111111111` になります。

1. リソース グループの例を作成します。

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) コマンドを使って、デプロイを開始します。

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    必要なパラメーターの指定を求められます。 出力の例を次に示します。

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="deploy-template-using-the-azure-cli"></a>Azure CLI を使用してテンプレートを展開する

Azure CLI を使って前記のテンプレートをデプロイするには、以下の手順のようにします。

1. rbac-rg.json という名前の新しいファイルを作成し、前に示したテンプレートをコピーします。

1. [Azure CLI](/cli/azure/authenticate-azure-cli) にサインインします。

1. ユーザー、グループ、またはアプリケーションの一意識別子を取得します。 たとえば、[az ad user list](/cli/azure/ad/user#az-ad-user-list) コマンドを使って Azure AD ユーザーの一覧を表示できます。

    ```azurecli
    az ad user list
    ```

1. GUID ツールを使って、ロールの割り当てに使う一意識別子を生成します。 この識別子の形式は `11111111-1111-1111-1111-111111111111` になります。

1. リソース グループの例を作成します。

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) コマンドを使って、デプロイを開始します。

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    必要なパラメーターの指定を求められます。 出力の例を次に示します。

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```
    
## <a name="next-steps"></a>次の手順

- [クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Azure Resource Manager テンプレートの構造と構文の詳細](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?term=rbac)
