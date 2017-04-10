---
title: "Azure Network Watcher のセキュリティ グループ ビューを使用したネットワーク セキュリティの分析 - PowerShell | Microsoft Docs"
description: "この記事では、PowerShell を使用して、セキュリティ グループ ビューで仮想マシンのセキュリティを分析する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fa79ab0ab80481c8969c6fa653e92b10985cab3b
ms.openlocfilehash: 44a59a43745494eb943711a5afcb6e436a25a44d
ms.lasthandoff: 03/31/2017

---

# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>PowerShell を使用してセキュリティ グループ ビューで仮想マシンのセキュリティを分析する

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

セキュリティ グループ ビューは、仮想マシンに適用される構成済みの効果的なネットワーク セキュリティ規則を返します。 この機能は、ネットワーク セキュリティ グループと VM で構成されている規則を監査および診断して、トラフィックが正しく許可または拒否されていることを確認する際に役立ちます。 この記事では、PowerShell を使用して、仮想マシンに適用される構成済みの効果的なセキュリティ規則を取得する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

このシナリオでは、`Get-AzureRmNetworkWatcherSecurityGroupView` コマンドレットを実行してセキュリティ規則情報を取得します。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。

## <a name="scenario"></a>シナリオ

この記事で取り上げているシナリオでは、特定の仮想マシンに適用される構成済みの効果的なセキュリティ規則を取得します。

## <a name="retrieve-network-watcher"></a>Network Watcher の取得

まず、Network Watcher インスタンスを取得します。 この変数は、`Get-AzureRmNetworkWatcherSecurityGroupView` コマンドレットに渡されます。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>VM の取得

`Get-AzureRmNetworkWatcherSecurityGroupView` コマンドレットの実行対象となる仮想マシンが必要です。 次の例では VM オブジェクトを取得します。

```powershell
$VM = Get-AzurermVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>セキュリティ グループ ビューの取得

次に、セキュリティ グループ ビューの結果を取得します。

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>結果の表示

次の例は、返された結果の応答を短縮したものです。 この結果には、仮想マシンに適用される効果的なセキュリティ規則のすべてが **NetworkInterfaceSecurityRules**、**DefaultSecurityRules**、**EffectiveSecurityRules** の各グループに細分化されて示されています。

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>次のステップ

[Network Watcher を使用したネットワーク セキュリティ グループ (NSG) の監査](network-watcher-nsg-auditing-powershell.md)に関する記事を参照して、ネットワーク セキュリティ グループの自動検証の方法を確認する。



