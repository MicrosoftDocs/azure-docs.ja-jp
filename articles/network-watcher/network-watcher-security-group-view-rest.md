---
title: ネットワーク セキュリティを分析する - セキュリティ グループ ビュー - Azure REST API
titleSuffix: Azure Network Watcher
description: この記事では、Azure REST API を使用して、セキュリティ グループ ビューで仮想マシンのセキュリティを分析する方法について説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960603"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>REST API を使用してセキュリティ グループ ビューで仮想マシンのセキュリティを分析する

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> セキュリティ グループ ビュー API は管理されなくなったため、間もなく非推奨となる予定です。 同じ機能を提供する[有効なセキュリティ ルール機能](./network-watcher-security-group-view-overview.md)を使用してください。 

セキュリティ グループ ビューは、仮想マシンに適用される構成済みの効果的なネットワーク セキュリティ規則を返します。 この機能は、ネットワーク セキュリティ グループおよび VM に構成されている規則を監査および診断して、トラフィックが正常に許可または拒否されていることを確認する際に役立ちます。 この記事では、REST API を使用して、仮想マシンに適用される効果的なセキュリティ規則を取得する方法を説明します。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>開始する前に

このシナリオでは、Network Watcher Rest API を呼び出して、仮想マシンのセキュリティ グループ ビューを取得します。 PowerShell を使用して REST API を呼び出すには、ARMClient を使用します。 ARMClient は、[Chocolatey 上の ARMClient](https://chocolatey.org/packages/ARMClient) に関するページの chocolatey 上にあります。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順を参照して、Network Watcher を作成済みであることを前提としています。 また、有効な仮想マシンがあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

この記事で取り上げているシナリオでは、特定の仮想マシンに適用される効果的なセキュリティ規則を取得します。

## <a name="log-in-with-armclient"></a>ARMClient でログインする

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>仮想マシンの取得

次のスクリプトを実行して、仮想マシンを取得します。次のコードに必要な変数は次のとおりです。

- **subscriptionId** - サブスクリプション ID は **Get-AzSubscription** コマンドレットで取得することもできます。
- **resourceGroupName** - 仮想マシンを含むリソース グループの名前。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

必要な情報は、次の例に示す応答の "type" (`Microsoft.Compute/virtualMachines`) の下の **id** です。

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>仮想マシンのセキュリティ グループ ビューの取得

次の例では、対象となる仮想マシンのセキュリティ グループ ビューを要求します。 この例の結果を使用して、組織で定義されている規則およびセキュリティと比較し、構成のずれを探すことができます。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>応答の表示

前のコマンドから返される応答のサンプルを次に示します。 この結果には、仮想マシンに適用される効果的なセキュリティ規則のすべてが **NetworkInterfaceSecurityRules**、**DefaultSecurityRules**、**EffectiveSecurityRules** の各グループに細分化されて示されています。

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

[Network Watcher を使用したネットワーク セキュリティ グループ (NSG) の監査](network-watcher-security-group-view-powershell.md)にアクセスして、ネットワーク セキュリティ グループの自動検証の方法を確認する。