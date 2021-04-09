---
title: Azure の Linux VM にセキュリティとポリシーを適用する
description: Azure Resource Manager Linux 仮想マシンにポリシーを適用する方法
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: c1fb80001f0669672e9f6e8a567685b799314df1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549823"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Azure Resource Manager を使用して Linux VM にポリシーを適用する
ポリシーを使用すると、さまざまな習慣や規則を企業全体に適用できます。 望ましい行動を強制することによって、組織の成功に貢献しつつ、リスクを軽減することができます。 この記事では、Azure Resource Manager ポリシーを使用して、組織の Virtual Machines の望ましい行動を定義する方法について説明します。

ポリシーの概要については、「[Azure Policy とは](../../governance/policy/overview.md)」をご覧ください。

## <a name="permitted-virtual-machines"></a>許可されている仮想マシン
組織の仮想マシンがアプリケーションと互換性があることを保証するために、許可されるオペレーティング システムを制限することができます。 次のポリシーの例は、Ubuntu 14.04.2-LTS 仮想マシンの作成のみを許可します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
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

任意の Ubuntu LTS イメージを許可するには、ワイルドカードを使用して前のポリシーを変更します。 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

ポリシーのフィールドについては、[ポリシーのエイリアス](../../governance/policy/concepts/definition-structure.md#aliases)に関するページをを参照してください。

## <a name="managed-disks"></a>マネージド ディスク

必ずマネージド ディスクを使用するように設定するには、次のポリシーを使用します。

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>仮想マシンのイメージ

セキュリティ上の理由から、承認済みのカスタム イメージのみを環境にデプロイすることを必須にすることができます。 また、承認済みのイメージを含むリソース グループ、または特定の承認済みイメージを指定できます。

次の例では、承認済みリソース グループのイメージを必須にしています。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

次の例では、承認済みイメージ ID を指定しています。

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>仮想マシン拡張機能

特定の種類の拡張機能について使用を禁止することができます。 たとえば、ある拡張機能が特定のカスタム仮想マシンのイメージと互換性がない場合などです。 特定の拡張機能をブロックする例を次に示します。 発行元と種類を使用して、ブロックする拡張機能を決定します。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>次のステップ
* (上記の例で示すように) ポリシー規則を定義した後、ポリシー定義を作成してスコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポリシーを割り当てるには、[Azure Portal を使用したリソース ポリシーの割り当てと管理](../../governance/policy/assign-policy-portal.md)、[PowerShell を使用したポリシーの割り当て](../../governance/policy/assign-policy-powershell.md)、または [Azure CLI を使用したポリシーの割り当て](../../governance/policy/assign-policy-azurecli.md)に関する各ページをご覧ください。
* リソース ポリシーの概要については、「[Azure Policy とは](../../governance/policy/overview.md)」をご覧ください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。
