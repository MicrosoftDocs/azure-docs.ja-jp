---
title: "Azure Network Watcher のセキュリティ グループ ビューを使用したネットワーク セキュリティの分析 - Azure CLI | Microsoft Docs"
description: "この記事では、Azure CLI を使用して、セキュリティ グループ ビューで仮想マシンのセキュリティを分析する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 9c539f5885a5014f98ccc1083c93daa4eee3646c
ms.lasthandoff: 03/28/2017

---

# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Azure CLI を使用して、セキュリティ グループ ビューで仮想マシンのセキュリティを分析する

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

セキュリティ グループ ビューは、仮想マシンに適用される構成済みの効果的なネットワーク セキュリティ規則を返します。 この機能は、ネットワーク セキュリティ グループおよび VM に構成されている規則を監査および診断して、トラフィックが正常に許可または拒否されていることを確認する際に役立ちます。 この記事では、Azure CLI を使用して、構成済みで効果的なセキュリティ規則を仮想マシン用に取得する方法を説明します。

この記事では、Windows、Mac、Linux で使用できるクロスプラット フォーム Azure CLI 1.0 を使います。 Network Watcher では、CLI サポートの Azure CLI 1.0 が使用されています。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順を参照して、Network Watcher を作成済みであることを前提としています。

## <a name="scenario"></a>シナリオ

この記事で取り上げているシナリオでは、特定の仮想マシンにおいて構成済みで効果的なセキュリティ規則を取得します。

## <a name="get-a-vm"></a>VM の取得

`vm list` コマンドレットを実行するには仮想マシンが必要です。 次のコマンドはリソース グループ内の仮想マシンを一覧表示したものです。

```azurecli
azure vm list -g resourceGroupName
```

仮想マシンについて理解したら、`vm show` コマンドレットを使用してリソース ID を取得できます。

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>セキュリティ グループ ビューの取得

次の手順では、セキュリティ グループ ビューの結果を取得します。 "-json" フラグを追加することによって、結果の書式を json 形式に設定します。

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>結果の表示

次の例は、返された結果の応答を短縮したものです。 この結果には、仮想マシンに適用される効果的なセキュリティ規則のすべてが **NetworkInterfaceSecurityRules**、**DefaultSecurityRules**、**EffectiveSecurityRules** の各グループに細分化されて示されています。

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

[Network Watcher を使用したネットワーク セキュリティ グループ (NSG) の監査](network-watcher-nsg-auditing-powershell.md)にアクセスして、ネットワーク セキュリティ グループの自動検証の方法を確認する。

[セキュリティ グループ ビューの概要](network-watcher-security-group-view-overview.md)に関するページで、ネットワーク リソースに適用されるセキュリティ ルールを確認する

