---
title: VM ネットワーク ルーティングの問題を診断する - Azure PowerShell
titleSuffix: Azure Network Watcher
description: この記事では、Azure Network Watcher の次ホップ機能を使用して、仮想マシン ネットワークのルーティングの問題を診断する方法について説明します。
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 6569a99ec851da478151665921e7689e1c3488f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020213"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>仮想マシンのネットワーク ルーティングに関する問題を診断する - Azure PowerShell

この記事では、仮想マシン (VM) をデプロイし、IP アドレスおよび URL との通信を確認します。 通信障害の原因と解決方法を特定します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用する場合、この記事では Azure PowerShell `Az` モジュールが必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。



## <a name="create-a-vm"></a>VM の作成

VM を作成する前に、その VM を含めるリソース グループを作成する必要があります。 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して Azure リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。 この手順の実行時に、資格情報の入力を求められます。 入力した値は、VM のユーザー名とパスワードとして構成されます。

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

VM の作成には数分かかります。 VM が作成されて、PowerShell から出力が返されるまでは、次の手順に進まないでください。

## <a name="test-network-communication"></a>ネットワーク通信をテストする

Network Watcher を使用してネットワーク通信をテストするには、最初にテスト対象の VM が含まれるリージョンにおいて Network Watcher を有効にしてから、Network Watcher の次ホップ機能を使用して通信をテストする必要があります。

## <a name="enable-network-watcher"></a>ネットワーク ウォッチャーを有効にする

米国東部リージョンで Network Watcher を既に有効にしてある場合は、[Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) を使ってそのネットワーク ウォッチャーを取得します。 次の例では、*NetworkWatcherRG* リソース グループ内にある *NetworkWatcher_eastus* という名前の既存のネットワーク ウォッチャーを取得しています。

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

米国東部リージョンで Network Watcher をまだ有効にしていない場合は、[New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) を使って、米国東部リージョンにネットワーク ウォッチャーを作成します。

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>次ホップの使用

Azure では、既定の接続先へのルートを自動的に作成します。 既定のルートをオーバーライドするカスタム ルートを作成できます。 場合によっては、カスタム ルートが通信の失敗の原因になることがあります。 VM からのルーティングをテストするには、[Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) コマンドを使用して、トラフィックが特定のアドレスに送信されるときの次のルーティング ホップを特定します。

VM から www.bing.com のいずれかの IP アドレスへの送信通信をテストします。

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

数秒後、 **[NextHopType]** が **[インターネット]** であり、 **[RouteTableId]** が **[System Route]\(システム ルート\)** であることが出力からわかります。 この結果から、接続先に対して有効なルートが存在することがわかります。

VM から 172.31.0.100 への送信通信をテストします。

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

返される出力は、 **[NextHopType]** が **[なし]** であり、 **[RouteTableId]** が **[System Route]\(システム ルート\)** であることを示しています。 この結果から、送信先への有効なシステム ルートはあるものの、接続先にトラフィックをルーティングする次ホップはないことがわかります。

## <a name="view-details-of-a-route"></a>ルートの詳細の表示

さらに詳細にルーティングを分析するには、[Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) コマンドを使用してネットワーク インターフェイスの有効なルートを確認します。

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

次のテキストが含まれる出力が返されます。

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

前の出力からわかるように、**AddressPrefix** が **0.0.0.0/0** であるルートでは、**インターネット** の次ホップを持つ他のルート アドレス プレフィックス内のアドレスを送信先としていないすべてのトラフィックがルーティングされます。 出力でもわかるように、アドレス 172.31.0.100 を含む、172.16.0.0/12 プレフィックスへの既定のルートがあるにも関わらず、**[nextHopType]** は **[なし]** になっています。 Azure では、172.16.0.0/12 への既定のルートを作成しますが、理由がない限り次ホップの種類は指定しません。 たとえば、仮想ネットワークのアドレス空間に 172.16.0.0/12 アドレス範囲を追加した場合は、ルートの **[nextHopType]** を **[仮想ネットワーク]** に変更します。 **[nextHopType]** として **[仮想ネットワーク]** にチェック マークが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

この記事では、VM を作成し、VM からのネットワークのルーティングを診断しました。 Azure では複数の既定のルートが作成されることを学習し、2 つの異なる送信先へのルーティングをテストしました。 詳細については、[Azure でのルーティング](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)と[カスタム ルートを作成する](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route)方法に関するページを参照してください。

送信 VM 接続の場合は、Network Watcher の[接続のトラブルシューティング](network-watcher-connectivity-powershell.md)機能を使用することにより、待機時間に加えて、VM とエンドポイント間で許可/拒否されているネットワーク トラフィックを確認することもできます。 Network Watcher 接続監視機能を使用すると、VM とエンドポイント (IP アドレスや URL など) の間の通信を継時的に監視することができます。 方法については、[ネットワーク接続の監視](connection-monitor.md)に関するページを参照してください。
