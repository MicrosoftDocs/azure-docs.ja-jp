---
title: ネットワーク トラフィックをフィルター処理する - Azure PowerShell | Microsoft Docs
description: この記事では、ネットワーク セキュリティ グループと PowerShell を使用して、サブネットに対するネットワーク トラフィックをフィルター処理する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 8ab22e7960e233d6ae934fb52989aa73a494b33a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791512"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>ネットワーク セキュリティ グループと PowerShell を使用してネットワーク トラフィックをフィルター処理する

ネットワーク セキュリティ グループを使用して、仮想ネットワーク サブネットとの間で送受信されるネットワーク トラフィックをフィルター処理できます。 ネットワーク セキュリティ グループには、IP アドレス、ポート、およびプロトコルでネットワーク トラフィックをフィルター処理するセキュリティ規則が含まれています。 セキュリティ規則は、サブネットに展開されたリソースに適用されます。 この記事では、次の方法について説明します。

* ネットワーク セキュリティ グループと規則を作成する
* 仮想ネットワークを作成し、ネットワーク セキュリティ グループをサブネットに関連付ける
* 仮想マシン (VM) をサブネットに展開する
* トラフィック フィルターをテストする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用する場合、この記事では Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

ネットワーク セキュリティ グループにはセキュリティ規則が含まれています。 セキュリティ規則には、送信元と送信先が指定されています。 送信元と送信先にはアプリケーション セキュリティ グループを指定できます。

### <a name="create-application-security-groups"></a>アプリケーション セキュリティ グループを作成する

まず、[New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、この記事で作成したすべてのリソースのリソース グループを作成します。 次の例では、*eastus* の場所にリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) を使用して、アプリケーション セキュリティ グループを作成します。 アプリケーション セキュリティ グループを使用すると、同様のポート フィルター処理要件を持つサーバーをグループ化できます。 次の例では、2 つのアプリケーション セキュリティ グループを作成します。

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>セキュリティ規則を作成する

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用してセキュリティ規則を作成します。 次の例では、インターネットから *myWebServers* アプリケーション セキュリティ グループへの、ポート 80 および 443 経由の受信トラフィックを許可する規則を作成します。

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

この記事では、RDP (ポート 3389) が *myAsgMgmtServers* VM のインターネットに公開されています。 運用環境では、ポート 3389 をインターネットに公開せずに、[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または[プライベート](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ネットワーク接続を使用して、管理する Azure リソースに接続することをお勧めします。

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。 次の例では、*myNsg* という名前のネットワーク セキュリティ グループを作成します。

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) を使用してサブネット構成を作成し、[Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) を使用してサブネット構成を仮想ネットワークに書き込みます。 次の例では、*mySubnet* という名前のサブネットを仮想ネットワークに追加し、それに *myNsg* ネットワーク セキュリティ グループを関連付けます。

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

VM を作成する前に、[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用して、サブネットと共に仮想ネットワーク オブジェクトを取得します。

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、各 VM のパブリック IP アドレスを作成します。

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) によって 2 つのネットワーク インターフェイスを作成し、パブリック IP アドレスをネットワーク インターフェイスに割り当てます。 次の例では、ネットワーク インターフェイスを作成し、それに *myVmWeb* パブリック IP アドレスを関連付けて、*myAsgWebServers* アプリケーション セキュリティ グループのメンバーにします。

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

次の例では、ネットワーク インターフェイスを作成し、それに *myVmMgmt* パブリック IP アドレスを関連付けて、*myAsgMgmtServers* アプリケーション セキュリティ グループのメンバーにします。

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

仮想ネットワークに 2 つの VM を作成して、後でトラフィックのフィルター処理を検証できるようにします。

[New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) を使用して VM 構成を作成し、[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。 次の例では、Web サーバーとして機能する VM を作成します。 `-AsJob` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

管理サーバーとして機能する VM を作成します。

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

仮想マシンの作成には、数分かかります。 Azure で VM の作成が完了するまで、次の手順に進まないでください。

## <a name="test-traffic-filters"></a>トラフィック フィルターをテストする

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、VM のパブリック IP アドレスを返します。 次の例では、*myVmMgmt* VM のパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

次のコマンドを使用して、お使いのローカル コンピューターから、*myVmMgmt* VM でリモート デスクトップ セッションを作成します。 `<publicIpAddress>` を前のコマンドで返された IP アドレスに置き換えます。

```
mstsc /v:<publicIpAddress>
```

ダウンロードされた RDP ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。

VM の作成時に指定したユーザー名とパスワードを入力し (VM の作成時に入力した資格情報を指定するために、 **[その他]** 、 **[別のアカウントを使う]** の選択が必要になる場合があります)、 **[OK]** を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続処理を続行します。

インターネットから、*myVmMgmt* VM に接続されているネットワーク インターフェイスが含まれている *myAsgMgmtServers* アプリケーション セキュリティ グループに対する送信でポート 3389 が許可されているため、この接続は成功します。

PowerShell から次のコマンドを使用して、*myVmMgmt* VM から *myVmWeb* VM へのリモート デスクトップ接続を作成します。

``` 
mstsc /v:myvmWeb
```

各ネットワーク セキュリティ グループ内の既定のセキュリティ規則では、仮想ネットワーク内のすべての IP アドレス間で、すべてのポートを介したトラフィックが許可されるため、この接続は成功します。 *myAsgWebServers* のセキュリティ規則は、インターネットからのポート 3389 を介した受信を許可しないため、インターネットから *myVmWeb* VM へのリモート デスクトップ接続を作成することはできません。

PowerShell から次のコマンドを使用して、Microsoft IIS を *myVmWeb* VM にインストールします。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

IIS のインストールが完了したら、*myVmWeb* VM から切断します。*myVmMgmt* VM のリモート デスクトップ接続は保持されます。 IIS のようこそ画面を表示するには、インターネット ブラウザーを開き、http:\//myVmWeb にアクセスします。

*myVmMgmt* VM から切断します。

コンピューターで PowerShell から次のコマンドを入力し、*myVmWeb* サーバーのパブリック IP アドレスを取得します。

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Azure の外部から *myVmWeb* Web サーバーにアクセスできることを確認するには、コンピューターでインターネット ブラウザーを開き、`http://<public-ip-address-from-previous-step>` にアクセスします。 インターネットから、*myVmWeb* VM に接続されているネットワーク インターフェイスが含まれている *myAsgWebServers* アプリケーション セキュリティ グループに対する送信でポート 80 が許可されているため、この接続は成功します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

この記事では、ネットワーク セキュリティ グループを作成し、それを仮想ネットワーク サブネットに関連付けました。 ネットワーク セキュリティ グループについて詳しくは、[ネットワーク セキュリティ グループの概要](./network-security-groups-overview.md)と[ネットワーク セキュリティ グループの管理](manage-network-security-group.md)に関する記事を参照してください。

Azure の既定では、サブネット間でトラフィックがルーティングされます。 代わりに、たとえばファイアウォールとして機能する VM を介してサブネット間でトラフィックをルーティングすることもできます。 その方法については、[ルート テーブルの作成](tutorial-create-route-table-powershell.md)に関する記事を参照してください。