---
title: クイック スタート:VM ネットワーク トラフィック フィルターの問題を診断する - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Azure Network Watcher の IP フローの確認機能を使って、仮想マシンのネットワーク トラフィック フィルターの問題を診断するために、Azure PowerShell を使用する方法について学習します。
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: de46d71127f992ea573d1f2d778afcb7f46ed3e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013313"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>クイック スタート:仮想マシン ネットワーク トラフィック フィルターの問題を診断する - Azure PowerShell

このクイック スタートでは、仮想マシン (VM) を展開してから、IP アドレスと URL への通信および IP アドレスからの通信をチェックします。 通信障害の原因と解決方法を特定します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカル環境にインストールして使用する場合、このクイック スタートでは Azure PowerShell `Az` モジュールが必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。



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

Network Watcher を使ってネットワーク通信をテストするには、最初にテスト対象の VM が含まれるリージョンにおいて Network Watcher を有効にしてから、Network Watcher の IP フローの確認機能を使って通信をテストする必要があります。

### <a name="enable-network-watcher"></a>ネットワーク ウォッチャーを有効にする

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

### <a name="use-ip-flow-verify"></a>IP フローの確認を使用する

VM を作成すると、Azure は既定に従って、VM との間でやり取りされるネットワーク トラフィックを許可および拒否します。 後で Azure の既定値をオーバーライドして、他の種類のトラフィックを許可または拒否する場合があります。 さまざまな送信先へのトラフィックまたは送信元 IP アドレスからのトラフィックが許可または拒否されるかどうかをテストするには、[Test-AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow) コマンドを使います。

VM から www.bing.com のいずれかの IP アドレスへの送信通信をテストします。

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

数秒後に、**AllowInternetOutbound** という名前のセキュリティ規則によってアクセスが許可されていることを示す結果が返されます。

VM から 172.31.0.100 への送信通信をテストします。

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

返される結果では、**DefaultOutboundDenyAll** という名前のセキュリティ規則によってアクセスが拒否されることが示されています。

172.31.0.100 から VM への受信通信をテストします。

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

返される結果では、**DefaultInboundDenyAll** という名前のセキュリティ規則のためにアクセスが拒否されることが示されています。 VM へのトラフィックまたは VM からのトラフィックを許可または拒否しているセキュリティ規則がわかったので、問題を解決する方法を決定できます。

## <a name="view-details-of-a-security-rule"></a>セキュリティ規則の詳細を表示する

「[ネットワーク通信をテストする](#test-network-communication)」の規則が通信を許可または拒否している理由を特定するには、[Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup) を使って、ネットワーク インターフェイスに対して有効なセキュリティ規則を確認します。

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

返される出力には、「[IP フロー検証を使用する](#use-ip-flow-verify)」で www.bing.com への送信アクセスを許可した **AllowInternetOutbound** 規則に関する次のテキストが含まれます。

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

出力では、**DestinationAddressPrefix** が **Internet** であることがわかります。 しかし、「[IP フローの確認を使用する](#use-ip-flow-verify)」でテストしたアドレス 13.107.21.200 が **Internet** にどのように関連するのかはっきりしません。 **ExpandedDestinationAddressPrefix** には複数のアドレス プレフィックスが列記されています。 リストのプレフィックスの 1 つは **12.0.0.0/6** であり、これは 12.0.0.1-15.255.255.254 の IP アドレス範囲を包含します。 13.107.21.200 はそのアドレス範囲内にあるため、**AllowInternetOutBound** 規則は送信トラフィックを許可します。 さらに、`Get-AzEffectiveNetworkSecurityGroup` によって返される出力の中には、この規則をオーバーライドする **優先順位の高い** (小さい値の) 規則はありません。 13.107.21.200 への送信通信を拒否するには、その IP アドレスへのポート 80 での送信を拒否する、優先順位の高いセキュリティ規則を追加します。

「[IP フローの確認を使用する](#use-ip-flow-verify)」で `Test-AzNetworkWatcherIPFlow` コマンドを実行して 172.131.0.100 への送信通信をテストしたとき、出力では **DefaultOutboundDenyAll** 規則が通信を拒否したことが示されました. **DefaultOutboundDenyAll** 規則は、`Get-AzEffectiveNetworkSecurityGroup` コマンドからの次の出力で示されている **DenyAllOutBound** 規則と同じです。

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

ルールでは、**DestinationAddressPrefix** として **0.0.0.0/0** が含まれます。 172.131.0.100 は `Get-AzEffectiveNetworkSecurityGroup` コマンドからの出力の他のどの送信規則の **DestinationAddressPrefix** にも含まれないので、規則はこのアドレスへの送信通信を拒否します。 この送信通信を許可するには、172.131.0.100 のポート 80 への送信トラフィックを許可する、優先順位の高いセキュリティ規則を追加します。

「[IP フローの確認を使用する](#use-ip-flow-verify)」で `Test-AzNetworkWatcherIPFlow` コマンドを実行して 172.131.0.100 からの受信通信をテストしたとき、出力では **DefaultInboundDenyAll** 規則が通信を拒否したことが示されました. **DefaultInboundDenyAll** 規則は、`Get-AzEffectiveNetworkSecurityGroup` コマンドからの次の出力で示されている **DenyAllInBound** 規則と同じです。

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

出力で示されているように、`Get-AzEffectiveNetworkSecurityGroup` コマンドからの出力には、172.131.0.100 から VM へのポート 80 での受信を許可する優先順位の高い規則が他に存在しないため、**DenyAllInBound** 規則が適用されます。 この受信通信を許可するには、ポート 80 での 172.131.0.100 からの受信を許可する優先順位の高いセキュリティ規則を追加します。

このクイック スタートのチェックでは、Azure の構成をテストしました。 チェックから予想どおりの結果が返ったにもかかわらず、まだネットワークの問題がある場合は、VM と通信対象のエンドポイントとの間にファイアウォールが存在しないこと、および VM のオペレーティング システムに通信を許可または拒否するファイアウォールが含まれないことを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、VM を作成し、受信および送信ネットワーク トラフィック フィルターを診断しました。 ネットワーク セキュリティ グループの規則が、VM との間でやり取りされるトラフィックを許可または拒否することを学習しました。 [セキュリティ規則](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)および[セキュリティ規則を作成する](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule)方法について、さらに詳しく学習してください。

適切なネットワーク トラフィック フィルターを適用してもまだ、ルーティングの構成のために VM への通信が失敗する可能性があります。 VM ネットワークのルーティングの問題を診断する方法については、「[VM のルーティングに関する問題を診断する](diagnose-vm-network-routing-problem-powershell.md)」をご覧ください。送信ルーティング、待機時間、およびトラフィック フィルター処理の問題を 1 つのツールで診断する方法については、[接続のトラブルシューティング](network-watcher-connectivity-powershell.md)に関するページをご覧ください。