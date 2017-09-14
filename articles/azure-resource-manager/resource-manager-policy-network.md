---
title: "ネットワーク リソース用の Azure リソース ポリシー | Microsoft Docs"
description: "ネットワーク リソースのデプロイを管理するための Azure Resource Manager のポリシーについて説明します。"
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
ms.date: 07/05/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: bca66bbdd9da9b3e4099d0d961f42c9368a17f5e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---
# <a name="apply-resource-policies-to-network-resources"></a>リソース ポリシーをネットワーク リソースに適用する
この記事では、Azure 仮想ネットワーク ゲートウェイに適用できる[リソース ポリシー](resource-manager-policy.md)の例を示します。 このポリシーによって、組織にデプロイされるゲートウェイの一貫性を確保できます。 

## <a name="define-permitted-virtual-network-gateway-sku"></a>許可される仮想ネットワーク ゲートウェイの SKU を定義する

次のポリシーは、仮想ネットワーク ゲートウェイでデプロイできる SKU を制限します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Network/virtualNetworkGateways"
      },
      {
        "not": {
          "field": "Microsoft.Network/virtualNetworkGateways/sku.name",
          "in": [
            "Basic",
            "VpnGw1"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>次のステップ
* (上記の例で示すように) ポリシー規則を定義した後、ポリシー定義を作成してスコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポータルでポリシーを割り当てる方法については、「[Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md)」(Azure Portal によるリソース ポリシーの割り当てと管理) を参照してください。 REST API、PowerShell、Azure CLI でポリシーを割り当てる方法については、「[Assign and manage policies through script](resource-manager-policy-create-assign.md)」(スクリプトによるポリシーの割り当てと管理) を参照してください。 
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。


