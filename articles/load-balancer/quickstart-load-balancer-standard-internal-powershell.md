---
title: クイック スタート:内部ロード バランサーの作成 - Azure PowerShell
titleSuffix: Azure Load Balancer
description: このクイックスタートでは、Azure PowerShell を使用して内部ロード バランサーを作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 15609435c7bc099d0ffe40759ea0f323b58a4545
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087582"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して VM の負荷を分散する内部ロード バランサーを作成する

Azure PowerShell を使用して内部ロード バランサーと 2 つの仮想マシンを作成することにより、Azure Load Balancer の使用を開始します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、次のようにリソース グループを作成します。

* 名前は **myResourceGroupLB** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Standard SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>運用環境のワークロードには、Standard SKU ロード バランサーをお勧めします。 SKU の詳細については、「 **[Azure Load Balancer の SKU](skus.md)** 」を参照してください。

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>仮想ネットワークと Azure Bastion ホストを作成する

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* リソース グループは **myResourceGroupLB** にします。
* サブネットの名前は **myBackendSubnet** にします。
* 仮想ネットワークは **10.0.0.0/16** にします。
* サブネットは **10.0.0.0/24** にします。
* サブネットの名前は **AzureBastionSubnet** にします。
* サブネットは **10.0.1.0/24** にします。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Azure Bastion ホストのパブリック IP アドレスを作成します。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、bastion ホストのパブリック IP アドレスを作成します。

* 名前は **myPublicIPBastion** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 割り当て方法は **static** にします。
* SKU は **Standard** にします。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Azure Bastion ホストを作成する

[New-AzBastion](/powershell/module/az.network/new-azbastion) を使用して、次の bastion ホストを作成します。

* 名前は **myBastion** にします。
* リソース グループは **myResourceGroupLB** にします。
* 仮想ネットワークは **myVNet** にします。
* パブリック IP アドレス **myPublicIPBastion** に関連付けます。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。

### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループを作成して、仮想ネットワークへの受信接続を定義します。

#### <a name="create-a-network-security-group-rule-for-port-80"></a>ポート 80 のネットワーク セキュリティ グループ規則を作成する
[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、次のようにネットワーク セキュリティ グループの規則を作成します。

* 名前は **myNSGRuleHTTP** にします。
* 説明は **Allow HTTP** にします。
* アクセスは **Allow** にします。
* プロトコルは **(*)** にします。
* 方向は **Inbound** にします。
* 優先度は **2000** にします。
* 発信元は **Internet** にします。
* 発信元ポート範囲は **(*)** にします。
* 宛先アドレス プレフィックスは **(*)** にします。
* 宛先は**ポート 80** にします。

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、次のようにネットワーク セキュリティ グループを作成します。

* 名前は **myNSG** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 前の手順で作成したセキュリティ規則を変数に格納します。

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Standard ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-frontend-ip"></a>フロントエンド IP を作成する

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、次のようにフロントエンド IP を作成します。

* 名前は **myFrontEnd** にします。
* プライベート IP アドレスは **10.0.0.4** にします。

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>バックエンド アドレス プールを構成する

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、次のようにバックエンド アドレス プールを作成します。 

* 名前は **myBackEndPool** にします。
* 残りの手順では、VM をこのバックエンド プールにアタッチします。

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) を使用して、次のようにロード バランサー規則を作成します。 

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で**ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT
```
>[!NOTE]
>バックエンド プール内の仮想マシンは、この構成ではアウトバウンド インターネット接続を持ちません。 </br> アウトバウンド接続の提供の詳細については、以下を参照してください。 </br> **[Azure の送信接続](load-balancer-outbound-connections.md)**</br> 接続を提供するためのオプション: </br> **[送信専用のロード バランサーの構成](egress-only.md)** </br> **[Virtual Network NAT とは](https://docs.microsoft.com/azure/virtual-network/nat-overview)**


### <a name="create-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、内部ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします
* 場所は **eastus** にします。
* リソース グループは **myResourceGroupLB** にします。

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>ネットワーク インターフェイスの作成

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、次のように 3 つのネットワーク インターフェイスを作成します。

#### <a name="vm-1"></a>VM 1

* 名前は **myNicVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 名前は **myNicVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell
$cred = Get-Credential
```

以下を使用して、仮想マシンを作成します。

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 名前は **myVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* ゾーンは **Zone 1** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* 名前は **myVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* ゾーンは **Zone 2** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**Basic SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>運用環境のワークロードには、Standard SKU ロード バランサーをお勧めします。 SKU の詳細については、「 **[Azure Load Balancer の SKU](skus.md)** 」を参照してください。

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>仮想ネットワークと Azure Bastion ホストを作成する

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* リソース グループは **myResourceGroupLB** にします。
* サブネットの名前は **myBackendSubnet** にします。
* 仮想ネットワークは **10.0.0.0/16** にします。
* サブネットは **10.0.0.0/24** にします。
* サブネットの名前は **AzureBastionSubnet** にします。
* サブネットは **10.0.1.0/24** にします。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Azure Bastion ホストのパブリック IP アドレスを作成します。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、bastion ホストのパブリック IP アドレスを作成します。

* 名前は **myPublicIPBastion** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 割り当て方法は **static** にします。
* SKU は **Standard** にします。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Azure Bastion ホストを作成する

[New-AzBastion](/powershell/module/az.network/new-azbastion) を使用して、次の bastion ホストを作成します。

* 名前は **myBastion** にします。
* リソース グループは **myResourceGroupLB** にします。
* 仮想ネットワークは **myVNet** にします。
* パブリック IP アドレス **myPublicIPBastion** に関連付けます。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。


### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループを作成して、仮想ネットワークへの受信接続を定義します。

#### <a name="create-a-network-security-group-rule-for-port-80"></a>ポート 80 のネットワーク セキュリティ グループ規則を作成する
[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、次のようにネットワーク セキュリティ グループの規則を作成します。

* 名前は **myNSGRuleHTTP** にします。
* 説明は **Allow HTTP** にします。
* アクセスは **Allow** にします。
* プロトコルは **(*)** にします。
* 方向は **Inbound** にします。
* 優先度は **2000** にします。
* 発信元は **Internet** にします。
* 発信元ポート範囲は **(*)** にします。
* 宛先アドレス プレフィックスは **(*)** にします。
* 宛先は**ポート 80** にします。

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、次のようにネットワーク セキュリティ グループを作成します。

* 名前は **myNSG** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 前の手順で作成したセキュリティ規則を変数に格納します。

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Basic ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-frontend-ip"></a>フロントエンド IP を作成する

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、次のようにフロントエンド IP を作成します。

* 名前は **myFrontEnd** にします。
* プライベート IP アドレスは **10.0.0.4** にします。

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>バックエンド アドレス プールを構成する

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、次のようにバックエンド アドレス プールを作成します。 

* 名前は **myBackEndPool** にします。
* 残りの手順では、VM をこのバックエンド プールにアタッチします。

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) を使用して、次のようにロード バランサー規則を作成します。 

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で**ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```

### <a name="create-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、次のようにパブリック ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします
* 場所は **eastus** にします。
* リソース グループは **myResourceGroupLB** にします。

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>ネットワーク インターフェイスの作成

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、次のように 3 つのネットワーク インターフェイスを作成します。

#### <a name="vm-1"></a>VM 1

* 名前は **myNicVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 名前は **myNicVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>仮想マシンの可用性セットを作成する

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) を使用して、次のように仮想マシンの可用性セットを作成します。

* 名前は **myAvSet** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'myResourceGroupLB'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell
$cred = Get-Credential
```

以下を使用して、仮想マシンを作成します。

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 名前は **myVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* 場所は **eastus** にします。
* 可用性セットは **myAvSet** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* 名前は **myVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* 場所は **eastus** にします。
* 可用性セットは **myAvSet** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

3 つの VM を作成して構成するには数分かかります。

---

## <a name="install-iis"></a>IIS のインストール

[Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension?view=latest) を使用して、カスタム スクリプト拡張機能をインストールします。 

この拡張機能によって PowerShell Add-WindowsFeature Web-Server が実行され、IIS Web サーバーがインストールされます。次に、VM のホスト名を表示するように Default.htm ページが更新されます。

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

### <a name="create-network-interface"></a>ネットワーク インターフェイスを作成する

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、次のネットワーク インターフェイスを作成します。

#### <a name="mytestvm"></a>myTestVM

* 名前は **myNicTestVM** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>仮想マシンの作成

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell
$cred = Get-Credential
```

次のコマンドを使用して、仮想マシンを作成します。

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* 名前は **myTestVM** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicTestVM** に接続します。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>テスト

1. Azure portal に[サインイン](https://portal.azure.com)します。

1. **[概要]** 画面で、ロード バランサーのプライベート IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択して、**myLoadBalancer** を選択します。

2. **myLoadBalancer** の **[概要]** で、 **[プライベート IP アドレス]** の横にあるアドレスを書き留めるか、コピーしておきます。

3. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択して、リソースの一覧から **myResourceGroupLB** リソース グループにある **myTestVM** を選択します。

4. **[概要]** ページで **[接続]** 、 **[要塞]** の順に選択します。

6. VM 作成時に入力したユーザー名とパスワードを入力します。

7. **myTestVM** で **Internet Explorer** 開きます。

8. 前の手順の IP アドレスをブラウザーのアドレス バーに入力します。 IIS Web サーバーの既定のページがブラウザーに表示されます。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="標準の内部ロード バランサーを作成する" border="true":::
   
ロード バランサーが 3 つの VM すべてにトラフィックを分散していることを確認するには、各 VM の IIS Web サーバーの既定のページをカスタマイズした後、クライアント マシンで Web ブラウザーを強制的に最新の情報に更新します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなれば、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループやロード バランサー、その他のリソースを削除できます。

```azurepowershell-interactive
## Variable for command. ##
$rg = 'myResourceGroupLB'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、

* Standard または Basic の内部ロード バランサーを作成しました。
* 仮想マシンを接続しました。 
* ロード バランサーのトラフィック規則と正常性プローブを構成しました。
* ロード バランサーをテストしました。

Azure Load Balancer の詳細については、「[Azure Load Balancer の概要](load-balancer-overview.md)」および「[Load Balancer に関してよく寄せられる質問](load-balancer-faqs.md)」を参照してください。

* [Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)について理解を深めます。


