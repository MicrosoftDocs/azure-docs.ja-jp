---
title: "Azure Network Watcher の IP フロー検証を使用したトラフィックの検証 - PowerShell | Microsoft Docs"
description: "この記事では、PowerShell を使用して、仮想マシンから送受信されるトラフィックが許可されているか拒否されているかを確認する方法について説明します"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 68860006266a60bf8e87f72d8669fb26ed3a5486
ms.lasthandoff: 03/04/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Azure Network Watcher のコンポーネントである IP フロー検証を使用して、VM から送受信されるトラフィックが許可されているか拒否されているかを確認する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

IP フロー検証は Network Watcher の機能であり、仮想マシンから送受信されるトラフィックが許可されているかどうかを確認できます。 このシナリオは仮想マシンが現在、外部リソースまたはバックエンドと通信可能な状態にあるかを確認する際に役立ちます。 IP フロー検証を使用すると、ネットワーク セキュリティ グループ (NSG) 規則が適切に構成されているかを検証し、NSG 規則によってブロックされているフローのトラブルシューティングを行うことができます。 ブロック対象のトラフィックが NSG により適切にブロックされているかどうかを確認することも、IP フロー検証を使用する別の理由として挙げられます。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順に従って Network Watcher を作成済みであること、または既存の Network Watcher インスタンスを保持していることを前提としています。 また、有効な仮想マシンのあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

このシナリオでは、IP フロー検証を使用して仮想マシンが既知の Bing IP アドレスと通信可能かどうかを検証します。 トラフィックが拒否されている場合は、そのトラフィックを拒否するセキュリティ規則が返されます。 IP フロー検証の詳細については、[IP フロー検証の概要](network-watcher-ip-flow-verify-overview.md)をご覧ください。

## <a name="retrieve-network-watcher"></a>Network Watcher の取得

最初の手順では、Network Watcher インスタンスを取得します。 `$networkWatcher` 変数は、IP フロー検証のコマンドレットに渡されます。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>VM の取得

IP フロー検証では、仮想マシン上の IP アドレスとリモートの通信先間のトラフィックをテストします。 このコマンドレットには、仮想マシンの ID が必要です。 使用する仮想マシンの ID が既にわかっている場合、この手順は省略できます。

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>NIC の取得

仮想マシンの NIC の IP アドレスが必要になります。この例では、仮想マシン上の NIC を取得します。 仮想マシン上のテスト対象 IP アドレスが既にわかっている場合、この手順は省略できます。

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkInterfaceIDs.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>IP フロー検証の実行

コマンドレットの実行に必要な情報が揃ったため、`Test-AzureRmNetworkWatcherIPFlow` コマンドレットを実行してトラフィックをテストします。 この例では、1 番目の NIC の最初の IP アドレスを使用します。

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> IP フロー検証では、VM リソースを割り当てて実行する必要があります。

## <a name="review-results"></a>結果の確認

`Test-AzureRmNetworkWatcherIPFlow` を実行すると結果が返されます。以下の例は、前の手順で返された結果です。

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>次のステップ

ブロックされるべきでないトラフィックがブロックされている場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照して、ネットワーク セキュリティ グループと定義済みのセキュリティ規則を突き止めます。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














