---
title: Azure の Windows VM にセキュリティとポリシーを適用する
description: Azure Resource Manager Windows 仮想マシンにポリシーを適用する方法
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 4a4e54510c4683dc1be9da09b96d6289136a26f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550265"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Azure Resource Manager を使用して Windows VM にポリシーを適用する
ポリシーを使用すると、さまざまな習慣や規則を企業全体に適用できます。 望ましい行動を強制することによって、組織の成功に貢献しつつ、リスクを軽減することができます。 この記事では、Azure Resource Manager ポリシーを使用して、組織の Virtual Machines の望ましい行動を定義する方法について説明します。

ポリシーの概要については、「[Azure Policy とは](../../governance/policy/overview.md)」をご覧ください。

## <a name="permitted-virtual-machines"></a>許可されている仮想マシン
組織の仮想マシンがアプリケーションと互換性があることを保証するために、許可されるオペレーティング システムを制限することができます。 次のポリシーの例は、Windows Server 2012 R2 Datacenter Virtual Machines の作成のみを許可します。

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
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

任意の Windows Server Datacenter イメージを許可するには、ワイルドカードを使用して前のポリシーを変更します。

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

任意の Windows Server 2012 R2 Datacenter 以降のイメージを許可するには、anyOf を使用して前のポリシーを変更します。

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
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


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

オンプレミス ライセンスをお持ちの場合、仮想マシンにかかるライセンス料金を節約できます。 ライセンスをお持ちでない場合は、このオプションを禁止することをお勧めします。 次のポリシーでは、Azure Hybrid Use Benefit (AHUB) の使用を禁止しています。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
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
