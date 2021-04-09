---
title: Azure PowerShell でネットワーク トラフィックをルーティングする | Microsoft Docs
description: この記事では、PowerShell を使用してルート テーブルでネットワーク トラフィックをルーティングする方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5bd52e8865bb704497740851f6a0e3c886ed9d6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790209"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>PowerShell を使用してルート テーブルでネットワーク トラフィックをルーティングする

既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックが自動的にルーティングされます。 Azure の既定のルーティングは、独自のルートを作成してオーバーライドすることができます。 カスタム ルートを作成する機能は、たとえば、サブネット間でネットワーク仮想アプライアンス (NVA) を越えてトラフィックをルーティングしたい場合に便利です。 この記事では、次のことについて説明します。

* ルート テーブルの作成
* ルートの作成
* 複数のサブネットを含んだ仮想ネットワークを作成する
* サブネットへのルート テーブルの関連付け
* トラフィックをルーティングする NVA を作成する
* 仮想マシン (VM) を異なるサブネットに展開する
* NVA を介して、あるサブネットから別のサブネットにトラフィックをルーティングする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用する場合、この記事では Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-route-table"></a>ルート テーブルの作成

ルート テーブルを作成する前に、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用してリソース グループを作成します。 次の例では、この記事で作成されるすべてのリソースに対して、*myResourceGroup* という名前のリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzRouteTable](/powershell/module/az.network/new-azroutetable) を使用してルート テーブルを作成します。 次の例では、*myRouteTablePublic* という名前のルート テーブルを作成します。

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>ルートの作成

[Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) を使用してルート テーブル オブジェクトを取得し、[Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) によってルートを作成します。その後、[Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) を使ってルート構成をルート テーブルに書き込みます。

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

ルート テーブルをサブネットに関連付けるには、仮想ネットワークとサブネットを作成しておく必要があります。 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) を使用して 3 つのサブネット構成を作成することで、3 つのサブネットを作成します。 次の例では、"*パブリック*"、"*プライベート*"、および *DMZ* サブネット用の 3 つのサブネット構成を作成します。

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

[Set-AzureRmVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) を使用して、仮想ネットワークにサブネット構成を書き込みます。これにより、仮想ネットワークにサブネットが作成されます。

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) を使用して *myRouteTablePublic* ルート テーブルを "*パブリック*" サブネットに関連付け、[Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) を使用してサブネット構成を仮想ネットワークに書き込みます。

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $myRouteTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>NVA を作成する

NVA は、ルーティング、ファイアウォール、WAN 最適化などのネットワーク機能を実行する VM です。

VM を作成する前に、ネットワーク インターフェイスを作成してください。

### <a name="create-a-network-interface"></a>ネットワーク インターフェイスの作成

ネットワーク インターフェイスを作成する前に、[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用して仮想ネットワーク ID を取得し、次に [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) を使用してサブネット ID を取得する必要があります。 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、IP 転送が有効な *DMZ* サブネットにネットワーク インターフェイスを作成します。

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>VM の作成

VM を作成して、既存のネットワーク インターフェイスをそこにアタッチするには、[New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) を使用して、まず VM 構成を作成しておく必要があります。 構成には、前の手順で作成したネットワーク インターフェイスを含めます。 ユーザー名とパスワードの入力が求められたら、VM へのログインに使用するユーザー名とパスワードを選択します。

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して、VM 構成を利用した VM を作成します。 次の例では、*myVmNva* という名前の VM を作成します。

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

後の手順で、*パブリック* サブネットからのトラフィックがネットワーク仮想アプライアンス経由で *プライベート* サブネットにルーティングされていることを検証できるように、仮想ネットワークに 2 つの VM を作成します。

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して、*パブリック* サブネット内に VM を作成します。 次の例では、*myVirtualNetwork* 仮想ネットワークの *パブリック* サブネットに、*myVmPublic* という名前の VM を作成します。

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

*プライベート* サブネット内に VM を作成します。

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

VM の作成には数分かかります。 VM が作成され、PowerShell に出力が返されるまでは、次の手順に進まないでください。

## <a name="route-traffic-through-an-nva"></a>NVA を経由するトラフィックのルーティング

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、*myVmPrivate* VM のパブリック IP アドレスを返します。 次の例では、*myVmPrivate* VM のパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

次のコマンドを使用して、お使いのローカル コンピューターから、*myVmPrivate* VM でリモート デスクトップ セッションを作成します。 `<publicIpAddress>` を前のコマンドで返された IP アドレスに置き換えます。

```
mstsc /v:<publicIpAddress>
```

ダウンロードされた RDP ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。

VM の作成時に指定したユーザー名とパスワードを入力し (VM の作成時に入力した資格情報を指定するために、 **[その他]** 、 **[別のアカウントを使う]** の選択が必要になる場合があります)、 **[OK]** を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続処理を続行します。

後の手順では、ルーティングのテストに `tracert.exe` コマンドを使用します。 Tracert は Internet Control Message Protocol (ICMP) を使用していますが、Windows ファイアウォール経由では拒否されます。 *myVmPrivate* VM 上の PowerShell から次のコマンドを入力して、Windows ファイアウォールで ICMP を有効にします。

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

この記事では経路のトレースを使用してルーティングをテストしていますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。

IP 転送の有効化に関するセクションで、VM のネットワーク インターフェイスに対して Azure 内での IP 転送を有効にしました。 VM 内では、オペレーティング システム、または VM 内で実行中のアプリケーションも、ネットワーク トラフィックを転送できる必要があります。 *myVmNva* のオペレーティング システム内での IP 転送を有効にします。

*myVmPrivate* VM でコマンド プロンプトを使用して、*myVmNva* にリモート デスクトップ接続します。

``` 
mstsc /v:myvmnva
```

オペレーティング システム内で IP 転送を有効にするには、*myVmNva* VM 上の PowerShell から次のコマンドを入力します。

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

*myVmNva* VM を再起動します。これにより、リモート デスクトップ セッションも切断されます。

*myVmPrivate* VM に接続されている状態で、*myVmNva* VM の再起動後、*myVmPublic* VM へのリモート デスクトップ セッションを作成します。

``` 
mstsc /v:myVmPublic
```

*myVmPublic* VM 上の PowerShell から次のコマンドを入力して、Windows ファイアウォールで ICMP を有効にします。

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

*myVmPublic* VM から *myVmPrivate* VM へのネットワーク トラフィックのルーティングをテストするには、*myVmPublic* VM 上の PowerShell から次のコマンドを入力します。

```
tracert myVmPrivate
```

応答は次の例のようになります。

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

最初のホップが 10.0.2.4 であることを確認できます。これは、NVA のプライベート IP アドレスです。 2 番目のホップは 10.0.1.4 です。これは、*myVmPrivate* VM のプライベート IP アドレスです。 *myRouteTablePublic* ルート テーブルに追加され、"*パブリック*" サブネットに関連付けられているルートにより、Azure はトラフィックを "*プライベート*" サブネットに直接ルーティングするのではなく、NVA 経由でルーティングするようになります。

*myVmPublic* VM へのリモート デスクトップ セッションを閉じます。*myVmPrivate* VM にはまだ接続されたままです。

*myVmPrivate* VM から *myVmPublic* VM へのネットワーク トラフィックのルーティングをテストするには、*myVmPrivate* VM 上のコマンド プロンプトから次のコマンドを入力します。

```
tracert myVmPublic
```

応答は次の例のようになります。

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

トラフィックは、*myVmPrivate* VM から *myVmPublic* VM に直接ルーティングされていることがわかります。 既定では、サブネット間でトラフィックが直接ルーティングされます。

*myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

この記事では、ルート テーブルを作成し、それをサブネットに関連付けました。 トラフィックをパブリック サブネットからプライベート サブネットにルーティングする単純なネットワーク仮想アプライアンスを作成しました。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) からファイアウォールや WAN 最適化などのネットワーク機能を実行する、さまざまな事前構成されたネットワーク仮想アプライアンスを展開します。 ルーティングの詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)と[ルート テーブルの管理](manage-route-table.md)に関する記事をご覧ください。

仮想ネットワーク内では多数の Azure リソースをデプロイできますが、一部の Azure PaaS サービスのリソースは仮想ネットワークにデプロイできなません。 ただし、一部の Azure PaaS サービスのリソースへのアクセスを、仮想ネットワーク サブネットからのトラフィックのみに制限できます。 方法については、[PaaS リソースへのネットワーク アクセスの制限](tutorial-restrict-network-access-to-resources-powershell.md)に関する記事をご覧ください。
