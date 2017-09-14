---
title: "Azure リソース ポリシーの割り当てと管理 | Microsoft Docs"
description: "Azure のリソース ポリシーをサブスクリプションとリソース グループに適用する方法、およびリソース ポリシーを表示する方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: f461efbc2a23f85e8b6d3fdec156a0df1636708a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="assign-and-manage-resource-policies"></a>リソース ポリシーの割り当てと管理

ポリシーを実装するには、次の手順を実行する必要があります。

1. ポリシー定義をチェックして、要件を満たすポリシー定義がサブスクリプションに既に存在するかどうかを確認します (Azure によって提供される組み込みのポリシーもチェックしてください)。
2. 存在する場合は、その名前を取得します。
3. 存在しない場合は、JSON でポリシー規則を定義して、サブスクリプションにポリシー定義として追加します。 この手順では、ポリシーが割り当て可能になりますが、お使いのサブスクリプションに規則は適用されません。
4. どちらの場合も、ポリシーをスコープ (サブスクリプションやリソース グループなど) に割り当てます。 これで、ポリシーの規則が設定されました。

この記事では、REST API、PowerShell、または Azure CLI を使用して、ポリシー定義を作成し、その定義をスコープに割り当てる手順を中心に説明します。 ポータルを使用してポリシーを割り当てる方法については、「[Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md)」(Azure Portal によるリソース ポリシーの割り当てと管理) を参照してください。 この記事では、ポリシー定義を作成する構文については説明しません。 ポリシーの構文については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。

## <a name="rest-api"></a>REST API

### <a name="create-policy-definition"></a>ポリシー定義の作成

[ポリシー定義用の REST API](/rest/api/resources/policydefinitions)を使用して、ポリシーを作成できます。 REST API を使用すると、ポリシー定義の作成と削除、既存の定義に関する情報の取得を実行できます。

ポリシー定義を作成するには、以下を実行します。

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

要求の本文は次の例のようになります。

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>ポリシーの割り当て

[ポリシーの割り当て用 REST API](/rest/api/resources/policyassignments)で、目的のスコープに対してポリシー定義を適用します。 REST API を使用すると、ポリシー割り当ての作成と削除、既存の割り当てに関する情報の取得を実行できます。

ポリシー割り当てを作成するには、次を実行します。

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

ポリシー割り当ての名前は、{policy-assignment} です。

要求の本文は次の例のようになります。

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>ポリシーの表示
ポリシーを取得するには、[ポリシー定義の取得](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get)操作を使用します。

### <a name="get-aliases"></a>エイリアスの取得
REST API からエイリアスを取得できます。

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

次の例はエイリアスの定義を示しています。 ご覧のように、プロパティ名が変更された場合も、エイリアスはさまざまな API バージョンでのパスを定義します。 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

PowerShell の例に進む前に、Azure PowerShell の[最新バージョンをインストール](/powershell/azure/install-azurerm-ps)していることを確認してください。 ポリシーのパラメーターは、バージョン 3.6.0 で追加されました。 以前のバージョンがインストールされていると、パラメーターが見つからないことを示すエラーが返されます。

### <a name="view-policy-definitions"></a>ポリシー定義の表示
サブスクリプション内のすべてのポリシー定義を表示するには、次のコマンドを使用します。

```powershell
Get-AzureRmPolicyDefinition
```

組み込みのポリシーを含め、すべての使用可能なポリシー定義が返されます。 各ポリシーは、次の形式で返されます。

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

ポリシー定義の作成に進む前に、組み込みのポリシーを調べます。 必要な制限を適用する組み込みのポリシーが見つかった場合は、ポリシー定義の作成をスキップできます。 代わりに、組み込みのポリシーを目的のスコープに割り当てます。

### <a name="create-policy-definition"></a>ポリシー定義の作成
`New-AzureRmPolicyDefinition` コマンドレットを使用してポリシー定義を作成することができます。

```powershell
$definition = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

出力は、ポリシー割り当ての際に使用される `$definition` オブジェクトに格納されます。 

JSON をパラメーターとして指定するよりも、ポリシーの規則を含む .json ファイルへのパスを指定するのがよいでしょう。

```powershell
$definition = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy "c:\policies\coolAccessTier.json"
```

次の例では、パラメーターを含むポリシー定義を作成しています。

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>ポリシーの割り当て

`New-AzureRmPolicyAssignment` コマンドレットを使用して、目的のスコープでポリシーを適用します。 次の例は、ポリシーをリソース グループに割り当てます。

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

パラメーターが必要なポリシーを割り当てるには、それらの値を持つオブジェクトを作成します。 次の例は、組み込みのポリシーを取得してパラメーターの値を渡します。

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>ポリシーの割り当ての表示

特定のポリシーの割り当てを取得するには、次のコマンドを使用します。

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

ポリシー定義用のポリシー規則を表示するには、次のコマンドを使用します。

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>ポリシーの割り当ての削除 

ポリシー割り当てを削除するには、次のコマンドを使用します。

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Azure CLI

### <a name="view-policy-definitions"></a>ポリシー定義の表示
サブスクリプション内のすべてのポリシー定義を表示するには、次のコマンドを使用します。

```azurecli
az policy definition list
```

組み込みのポリシーを含め、すべての使用可能なポリシー定義が返されます。 各ポリシーは、次の形式で返されます。

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

ポリシー定義の作成に進む前に、組み込みのポリシーを調べます。 必要な制限を適用する組み込みのポリシーが見つかった場合は、ポリシー定義の作成をスキップできます。 代わりに、組み込みのポリシーを目的のスコープに割り当てます。

### <a name="create-policy-definition"></a>ポリシー定義の作成

Azure CLI でポリシー定義コマンドを使用して、ポリシー定義を作成できます。

```azurecli
az policy definition create --name coolAccessTier --description "Policy to specify access tier." --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>ポリシーの割り当て

ポリシーの割り当てコマンドを使用して、ポリシーを目的のスコープに適用できます。 次の例は、ポリシーをリソース グループに割り当てます。

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>ポリシーの割り当ての表示

ポリシーの割り当てを表示するには、ポリシーの割り当て名とスコープを指定します。

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>ポリシーの割り当ての削除 

ポリシー割り当てを削除するには、次のコマンドを使用します。

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>次のステップ
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。


