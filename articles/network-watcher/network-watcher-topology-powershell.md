---
title: "Azure Network Watcher トポロジの確認 - PowerShell | Microsoft Docs"
description: "この記事では、PowerShell を使用してネットワーク トポロジを照会する方法を説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c8d5fe6177b36aa73febe308b47b1fc330181924
ms.lasthandoff: 03/22/2017

---

# <a name="view-network-watcher-topology-with-powershell"></a>PowerShell を使用した Network Watcher トポロジの確認

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Network Watcher のトポロジ機能では、サブスクリプションのネットワーク リソースを視覚的に表現します。 ポータルに、視覚化された情報が自動的に表示されます。 ポータルのトポロジ ビューの背後の情報は、PowerShell を使用して取得できます。
この機能により、データを他のツールで視覚化して使用できるため、トポロジの情報の汎用性が高まります。

相互接続は、2 つのリレーションシップでモデル化されます。

- **含有** - 例: VNet にサブネットが含まれ、サブネットに NIC が含まれる
- **関連付け** - 例: NIC が VM に関連付けられている

Topology REST API のクエリを実行したときに返されるプロパティは次のとおりです。

* **name** - リソースの名前。
* **id** - リソースの URI。
* **location** - リソースのある場所。
* **associations** - 参照されたオブジェクトへの関連付けのリスト。
    * **name** - 参照されたリソースの名前。
    * **resourceId** - 関連付けで参照されているリソースの URI。
    * **associationType** - 子オブジェクトと親のリレーションシップを参照する値。 有効な値は **Contains** または **Associated**。

## <a name="before-you-begin"></a>開始する前に

このシナリオでは、`Get-AzureRmNetworkWatcherTopology` コマンドレットでトポロジ情報を取得します。 [REST API でネットワーク トポロジを取得する](network-watcher-topology-rest.md)方法を説明している記事もあります。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。

## <a name="scenario"></a>シナリオ

この記事で取り上げているシナリオでは、特定のリソース グループのトポロジの応答を取得します。

## <a name="retrieve-network-watcher"></a>Network Watcher の取得

最初の手順では、Network Watcher インスタンスを取得します。 `$networkWatcher` 変数は、`Get-AzureRmNetworkWatcherTopology` コマンドレットに渡されます。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>トポロジの取得

`Get-AzureRmNetworkWatcherTopology` コマンドレットは、特定のリソース グループのトポロジを取得します。

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>結果

返される結果には "Resources" というプロパティ名が付いています。これには、`Get-AzureRmNetworkWatcherTopology` コマンドレットの JSON 応答の本文が含まれます。  この応答には、ネットワーク セキュリティ グループのリソースとその関連付け (Contains、Associated) が含まれています。

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>次のステップ

[Power BI による NSG フロー ログの視覚化](network-watcher-visualize-nsg-flow-logs-power-bi.md)に関するページから、Power BI で NSG フロー ログを視覚化する方法について確認する



