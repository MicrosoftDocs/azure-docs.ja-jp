---
title: "タグに関する Azure リソース ポリシー | Microsoft Docs"
description: "リソースのタグを管理するためのリソース ポリシーの例を示します"
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
ms.date: 08/24/2017
ms.author: tomfitz
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-for-tags"></a>タグに関するリソース ポリシーを適用する

このトピックでは、リソースのタグが一貫性を持って使用されることを保証するために適用できる一般的なポリシー ルールについて説明します。

既存のリソースがあるリソース グループまたはサブスクリプションにタグ ポリシーを適用しても、これらのリソースにポリシーがさかのぼって適用されることはありません。 これらのリソースにポリシーを適用するには、既存のリソースに対する更新をトリガーします。 この記事には、更新をトリガーするための PowerShell の例が含まれています。

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>リソース グループ内のすべてのリソースにタグ/値があることを確認する

一般的な要件は、リソース グループ内のすべてのリソースに特定のタグと値があることです。 多くの場合、この要件は、部門別にコストを追跡するために必要です。 次の条件を満たす必要があります。

* タグを持たない新規および更新されたリソースには、必要なタグと値が追加されます。
* 既存のリソースから必要なタグと値を削除することはできません。

この要件は、リソース グループに次の 2 つの組み込みポリシーを適用することで対応できます。

| ID | Description |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | ユーザーによって指定されていない場合に、必要なタグとその既定値を適用します。 |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | 必要なタグとその値を強制的に適用します。 |

### <a name="powershell"></a>PowerShell

次の PowerShell スクリプトを実行すると、リソース グループに 2 つの組み込みポリシー定義が割り当てられます。 スクリプトを実行する前に、リソース グループに必要なすべてのタグを割り当てます。 リソース グループの各タグは、グループ内のリソースに必要です。 サブスクリプション内のすべてのリソース グループに割り当てるには、リソース グループを取得するときに `-Name` パラメーターを指定しないようにします。

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

ポリシーを割り当てたら、既存のすべてのリソースへの更新をトリガーし、追加したタグのポリシーを強制的に適用することができます。 次のスクリプトを実行すると、リソースに存在していたその他のタグがすべて保持されます。

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="require-tags-for-a-resource-type"></a>リソースの種類に対してタグを要求する
次の例は、論理演算子を入れ子にして、特定の種類のリソース (ここではストレージ アカウント) にのみアプリケーション タグを要求する方法を示しています。

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>タグを要求する
次のポリシーは、"costCenter" キーを含むタグがない要求を拒否します (任意の値を適用できます)。

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>次のステップ
* (上記の例で示すように) ポリシー規則を定義した後、ポリシー定義を作成してスコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポータルでポリシーを割り当てる方法については、「[Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md)」(Azure Portal によるリソース ポリシーの割り当てと管理) を参照してください。 REST API、PowerShell、Azure CLI でポリシーを割り当てる方法については、「[Assign and manage policies through script](resource-manager-policy-create-assign.md)」(スクリプトによるポリシーの割り当てと管理) を参照してください。
* リソース ポリシーの概要については、[リソース ポリシーの概要](resource-manager-policy.md)に関するページを参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。

