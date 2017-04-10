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
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 5560b22f3f92a8e0a7cb8b973ef2e4c66bc32c06
ms.lasthandoff: 03/16/2017


---
# <a name="assign-and-manage-resource-policies"></a>リソース ポリシーの割り当てと管理

ポリシーを実装するには、3 つの手順を行う必要があります。

1. JSON でポリシーの規則を定義します。
2. 前の手順で作成した JSON から、サブスクリプションにポリシー定義を作成します。 この手順では、ポリシーが割り当て可能になりますが、お使いのサブスクリプションに規則は適用されません。
3. スコープ (サブスクリプションまたはリソース グループなど) にポリシーを割り当てます。 これで、ポリシーの規則が設定されました。

Azure には、いくつか定義済みのポリシーが用意されているので、定義が必要なポリシーの数を減らすことができます。 定義済みのポリシーが自分のシナリオで動作する場合は、最初の 2 つの手順をスキップし、スコープに定義済みのポリシーを割り当てます。

このトピックでは、ポリシー定義を作成してその定義をスコープに割り当てる手順について説明します。 ポリシー定義を作成する構文については説明しません。 ポリシーの構文については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。

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

### <a name="create-policy-definition"></a>ポリシー定義の作成
`New-AzureRmPolicyDefinition` コマンドレットを使用してポリシー定義を作成することができます。 以下の例では、リソースを北ヨーロッパと西ヨーロッパに限定するポリシー定義を作成します。

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{
   "if": {
     "not": {
       "field": "location",
       "in": "[parameters(''allowedLocations'')]"
     }
   },
   "then": {
     "effect": "deny"
   }
 }' -Parameter '{
     "allowedLocations": {
       "type": "array",
       "metadata": {
         "description": "An array of permitted locations for resources.",
         "strongType": "location",
         "displayName": "List of locations"
       }
     }
 }'
```            

出力は、ポリシー割り当ての際に使用される `$policy` オブジェクトに格納されます。 

JSON をパラメーターとして指定するよりも、ポリシーの規則を含む .json ファイルへのパスを指定するのがよいでしょう。

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>ポリシーの割り当て

`New-AzureRmPolicyAssignment` コマンドレットを使用して、目的のスコープでポリシーを適用します。

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$array = @("West US", "West US 2")
$param = @{"allowedLocations"=$array}
New-AzureRMPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId -PolicyDefinition $policy -PolicyParameterObject $param
```

### <a name="view-policies"></a>ポリシーの表示

すべてのポリシー割り当てを取得するには、次のコマンドを使用します。

```powershell
Get-AzureRmPolicyAssignment
```

特定のポリシーを取得するには、次のコマンドを使用します。

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId
```

ポリシー定義用のポリシー規則を表示するには、次のコマンドを使用します。

```powershell
(Get-AzureRmPolicyDefinition -Name regionPolicyDefinition).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>ポリシーの割り当ての削除 

ポリシー割り当てを削除するには、次のコマンドを使用します。

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20"></a>Azure CLI 2.0

### <a name="create-policy-definition"></a>ポリシー定義の作成

Azure CLI 2.0 でポリシー定義コマンドを使用して、ポリシー定義を作成できます。 以下の例では、リソースを北ヨーロッパと西ヨーロッパに限定できるポリシーを作成します。

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>ポリシーの割り当て

ポリシーの割り当てコマンドを使用して、ポリシーを目的のスコープに適用できます。

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>ポリシー定義の表示
ポリシー定義を取得するには、次のコマンドを使用します。

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>ポリシーの割り当ての削除 

ポリシー割り当てを削除するには、次のコマンドを使用します。

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0

### <a name="create-policy-definition"></a>ポリシー定義の作成

Azure CLI でポリシー定義コマンドを使用して、ポリシー定義を作成できます。 以下の例では、リソースを北ヨーロッパと西ヨーロッパに限定できるポリシーを作成します。

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

ポリシー インラインを指定するのではなく、ポリシーが含まれている .json ファイルのパスを指定できます。

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>ポリシーの割り当て

ポリシーの割り当てコマンドを使用して、ポリシーを目的のスコープに適用できます。

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

ここでのスコープは、指定するリソース グループの名前です。 policy-definition-id パラメーターの値が不明な場合は、Azure CLI を使用して値を取得できます。 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>ポリシーの表示
ポリシーを取得するには、次のコマンドを使用します。

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>ポリシーの割り当ての削除 

ポリシー割り当てを削除するには、次のコマンドを使用します。

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>次のステップ
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。


