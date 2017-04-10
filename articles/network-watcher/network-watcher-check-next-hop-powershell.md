---
title: "Azure Network Watcher Next Hop を使用して次ホップを検索する - PowerShell | Microsoft Docs"
description: "この記事では、PowerShell で Next Hop を使用して、次ホップの種類と IP アドレスを確認する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 9f53c824b6368dc2a6251fd880f1cabefef884b8
ms.lasthandoff: 03/31/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>PowerShell で Azure Network Watcher の Next Hop 機能を使用して次ホップの種類を確認する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Next Hop は Network Watcher の機能の&1; つであり、指定された仮想マシンに基づいて次ホップの種類と IP アドレスを取得できます。 この機能は、仮想マシンからのトラフィックがゲートウェイ、インターネット、または仮想ネットワークを経由して宛先に到達するかどうかを判断する際に役立ちます。

## <a name="before-you-begin"></a>開始する前に

このシナリオでは、Azure Portal を使用して次ホップの種類と IP アドレスを確認します。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。 また、有効な仮想マシンがあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

この記事で説明するシナリオでは、リソースの次ホップの種類と IP アドレスを調べる、Next Hop という Network Watcher の機能を使用します。 Next Hop の詳細については、[Next Hop の概要](network-watcher-next-hop-overview.md)に関する記事をご覧ください。

## <a name="retrieve-network-watcher"></a>Network Watcher の取得

まず、Network Watcher インスタンスを取得します。 `$networkWatcher` 変数は、次ホップ確認コマンドレットに渡されます。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>仮想マシンの取得

Next Hop では、仮想マシンの次ホップと次ホップの IP アドレスを返します。 このコマンドレットには、仮想マシンの ID が必要です。 使用する仮想マシンの ID が既にわかっている場合、この手順は省略できます。

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> Next Hop では、実行する VM リソースが割り当てられている必要があります。

## <a name="get-the-network-interfaces"></a>ネットワーク インターフェイスの取得

仮想マシンの NIC の IP アドレスが必要になります。この例では、仮想マシン上の NIC を取得します。 仮想マシン上のテスト対象の IP アドレスが既にわかっている場合、この手順は省略できます。

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkInterfaceIDs.ForEach({$_})}
```

## <a name="get-next-hop"></a>次ホップの取得

次に、`Get-AzureRmNetworkWatcherNextHop` コマンドレットを呼び出します。 このコマンドレットには、Network Watcher、仮想マシン ID、送信元 IP アドレス、宛先 IP アドレスを渡します。 この例では、宛先 IP アドレスは別の仮想ネットワークにある VM です。 2 つの仮想ネットワークの間には、仮想ネットワーク ゲートウェイがあります。

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>結果の確認

完了すると、結果が表示されます。 次ホップの IP アドレスと、リソースの種類が返されます。 このシナリオでは、仮想ネットワーク ゲートウェイのパブリック IP アドレスです。

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

現在利用可能な NextHopType の値は次のとおりです。

**次ホップの種類**

* インターネット
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* なし

## <a name="next-steps"></a>次のステップ

[Network Watcher での NSG 監査](network-watcher-nsg-auditing-powershell.md)に関する記事を参照して、ネットワーク セキュリティ グループの設定をプログラムで確認する方法を見る


















