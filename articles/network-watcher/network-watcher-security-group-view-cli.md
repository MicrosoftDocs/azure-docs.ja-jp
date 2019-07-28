---
title: Azure Network Watcher のセキュリティ グループ ビューを使用したネットワーク セキュリティの分析 - Azure CLI | Microsoft Docs
description: この記事では、Azure CLI を使用して、セキュリティ グループ ビューで仮想マシンのセキュリティを分析する方法について説明します。
services: network-watcher
documentationcenter: na
author: kumudd
manager: twooley
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 68222a90eb60ab4f84a34b5e46833128ea081ec1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724440"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Azure CLI を使用して、セキュリティ グループ ビューで仮想マシンのセキュリティを分析する

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

セキュリティ グループ ビューは、仮想マシンに適用される構成済みの効果的なネットワーク セキュリティ規則を返します。 この機能は、ネットワーク セキュリティ グループおよび VM に構成されている規則を監査および診断して、トラフィックが正常に許可または拒否されていることを確認する際に役立ちます。 この記事では、Azure CLI を使用して、構成済みで効果的なセキュリティ規則を仮想マシン用に取得する方法を説明します。

この記事の手順を実行するには、[Mac、Linux、Windows 用の Azure コマンドライン インターフェイス (CLI) をインストール](/cli/azure/install-azure-cli)する必要があります。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順を参照して、Network Watcher を作成済みであることを前提としています。

## <a name="scenario"></a>シナリオ

この記事で取り上げているシナリオでは、特定の仮想マシンにおいて構成済みで効果的なセキュリティ規則を取得します。

## <a name="get-a-vm"></a>VM の取得

`vm list` コマンドレットを実行するには仮想マシンが必要です。 次のコマンドは、リソース グループ内の仮想マシンを一覧表示します。

```azurecli
az vm list -resource-group resourceGroupName
```

仮想マシンについて理解したら、`vm show` コマンドレットを使用してリソース ID を取得できます。

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>セキュリティ グループ ビューの取得

次に、セキュリティ グループ ビューの結果を取得します。

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>結果の表示

次の例は、返された結果の応答を短縮したものです。 この結果には、仮想マシンに適用される効果的なセキュリティ規則のすべてが **NetworkInterfaceSecurityRules**、**DefaultSecurityRules**、**EffectiveSecurityRules** の各グループに細分化されて示されています。

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[Network Watcher を使用したネットワーク セキュリティ グループ (NSG) の監査](network-watcher-nsg-auditing-powershell.md)にアクセスして、ネットワーク セキュリティ グループの自動検証の方法を確認する。

[セキュリティ グループ ビューの概要](network-watcher-security-group-view-overview.md)に関するページで、ネットワーク リソースに適用されるセキュリティ ルールを確認する
