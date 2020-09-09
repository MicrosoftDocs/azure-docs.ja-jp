---
title: NAT ゲートウェイの作成 - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: このクイックスタートでは、Azure PowerShell を使用して NAT ゲートウェイを作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: de4e32d79cf4dfb3a5f54544c65544297a2c0232
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054155"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用した NAT ゲートウェイの作成

このチュートリアルでは、Azure Virtual Network NAT サービスを使用する方法について説明します。 Azure 内の仮想マシンに送信接続を提供する NAT ゲートウェイを作成しましょう。 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

このチュートリアルは、Azure Cloud Shell を使用して行うか、コマンドをローカルで実行して行うことができます。  Azure Cloud Shell を使用したことがない場合は、[今すぐサインイン](https://shell.azure.com)してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest) を使用して Azure リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、**myResourceGroupNAT** という名前のリソース グループを **eastus2** という場所に作成します。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>NAT ゲートウェイの作成

NAT ゲートウェイのパブリック IP オプションは次のとおりです。

* **パブリック IP アドレス**
* **パブリック IP プレフィックス**

どちらも NAT ゲートウェイで使用できます。

このシナリオでは、デモンストレーションとしてパブリック IP アドレスとパブリック IP プレフィックスを追加します。

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

インターネットにアクセスするには、NAT ゲートウェイに 1 つ以上のパブリック IP アドレスが必要です。 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) を使用して、**myResourceGroupNAT** に **myPublicIP** というパブリック IP アドレス リソースを作成します。 このコマンドの結果は、後で使用できるように **$publicIP** 変数に格納されます。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>パブリック IP プレフィックスの作成

[New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) を使用して、**myResourceGroupNAT** に **myPublicIPprefix** というパブリック IP プレフィックス リソースを作成します。  このコマンドの結果は、後で使用できるように **$publicIPPrefix** という名前の変数に格納されます。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT ゲートウェイ リソースの作成

このセクションでは、NAT ゲートウェイ リソースを使用して、次の NAT サービスのコンポーネントを作成して構成する方法について説明します。
  - NAT ゲートウェイ リソースによって変換される送信フローに使用する、パブリック IP プールとパブリック IP プレフィックス。
  - アイドル タイムアウトを既定値の 4 分から 10 分に変更します。

[New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) を使用して、グローバル Azure NAT ゲートウェイを作成します。 このコマンドの結果、パブリック IP アドレス **myPublicIP** とパブリック IP プレフィックス **myPublicIPprefix** を使用する **myNATgateway** という名前のゲートウェイ リソースが作成されます。 アイドル タイムアウトは 10 分に設定されています。  このコマンドの結果は、後で使用できるように **$natGateway** という名前の変数に格納されます。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

この時点で、NAT ゲートウェイは機能しており、ほかに必要なことは、これを使用する仮想ネットワークのサブネットを構成することだけです。

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

仮想ネットワークを作成し、サブネットをゲートウェイに関連付けます。

[New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) を使用して **myVnet** という名前の仮想ネットワークを作成し、[New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) を使用して、**myResourceGroup** 内に **mySubnet** という名前のサブネットを作成します。 この仮想ネットワークの IP アドレス空間は **192.168.0.0/16** です。 この仮想ネットワーク内のサブネットは **192.168.0.0/24** です。  コマンドの結果は、後で使用できるように **$subnet** および **$vnet** という名前の変数に格納されます。

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

インターネットを宛先とするすべての送信トラフィックが、この NAT サービスを使用するようになります。  UDR を構成する必要はありません。

## <a name="create-a-vm-to-use-the-nat-service"></a>NAT サービスを使用する VM の作成

今度は、NAT サービスを使用する VM を作成します。  この VM には、ユーザーが VM にアクセスできるようにするための、インスタンスレベルのパブリック IP として使用するパブリック IP があります。  NAT サービスはフロー方向を認識し、サブネットの既定のインターネットの宛先を置き換えます。 VM のパブリック IP アドレスは、送信接続には使用されません。

### <a name="create-public-ip-for-source-vm"></a>ソース VM のパブリック IP の作成

VM へのアクセスに使用するパブリック IP を作成します。  [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) を使用して、**myResourceGroupNAT** に **myPublicIPVM** というパブリック IP アドレス リソースを作成します。  このコマンドの結果は、後で使用できるように **$publicIpVM** という名前の変数に格納されます。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>NSG の作成と VM の SSH エンドポイントの公開

Standard パブリック IP アドレスは "既定でセキュリティ保護" されているため、SSH の受信アクセスを許可する NSG を作成する必要があります。 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) を使用して、**myNSG** という名前の NSG リソースを作成します。 [AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) を使用して、**myResourceGroupNAT** に **ssh** という名前の SSH アクセス用の NSG ルールを作成します。  このコマンドの結果は、後で使用できるように **$nsg** という名前の変数に格納されます。

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>VM の NIC の作成

[New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) を使用して、**myNic** という名前のネットワーク インターフェイスを作成します。 このコマンドにより、パブリック IP アドレスとネットワーク セキュリティ グループが関連付けられます。 このコマンドの結果は、後で使用できるように **$nic** という名前の変数に格納されます。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>VM を作成する

#### <a name="create-ssh-key-pair"></a>SSH キー ペアの作成

このクイック スタートを完了するには、SSH キー ペアが必要です。 既存の SSH キーの組がある場合は、この手順をスキップできます。

ssh-keygen を使用して SSH キー ペアを作成します。

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
PuTTy の使用を含む SSH キー ペアの作成方法の詳細については、[Windows で SSH キーを使用する方法](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)に関するページを参照してください。

Cloud Shell を使用して SSH キー ペアを作成した場合、キー ペアはコンテナー イメージに格納されます。 この[ストレージ アカウントは自動的に作成されます](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。 キーを取得するまでは、ストレージ アカウントまたは内部のファイル共有を削除しないでください。

#### <a name="create-vm-configuration"></a>VM 構成の作成

PowerShell で VM を作成するには、使用するイメージ、サイズ、認証オプションの各種設定を含んだ構成を作成します。 この構成を使用して VM を作成します。

SSH の資格情報、OS の情報、VM のサイズを定義します。 この例では、SSH キーは ~/.ssh/id_rsa.pub に格納されています。

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
構成定義を組み合わせて、[New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) を使用して **myResourceGroupNAT** に **myVM** という名前の VM を作成します。

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

VM がデプロイされる準備が整うまで待ってから、残りの手順に進んでください。

## <a name="discover-the-ip-address-of-the-vm"></a>VM の IP アドレスの検出

まず、作成した VM の IP アドレスを検出する必要があります。 VM のパブリック IP アドレスを取得するには、[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) を使用します。 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>パブリック IP アドレスをコピーし、VM へのアクセスに使用できるようにメモ帳に貼り付けます。

### <a name="sign-in-to-vm"></a>VM へのサインイン

Cloud Shell には、前の操作で生成した SSH の資格情報が格納されているはずです。  ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) を開きます。 前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。

```bash
ssh azureuser@<ip-address-destination>
```

これで、NAT サービスを使用する準備が整いました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) コマンドを使用して、リソース グループと、その内部に含まれているすべてのリソースを削除できます。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、NAT ゲートウェイとそれを使用する VM を作成しました。 

Azure Monitor でメトリックを見て、NAT サービスの稼動状態を確認しましょう。 利用可能な SNAT ポートのリソース枯渇などの問題を診断します。  SNAT ポートのリソース枯渇は、追加のパブリック IP アドレス リソースかパブリック IP プレフィックス リソース、またはその両方を追加することで解決できます。


- [Azure Virtual Network NAT](./nat-overview.md) について学習する。
- [NAT ゲートウェイ リソース](./nat-gateway-resource.md)について学習する。
- [Azure CLI を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-cli.md)をデプロイするためのクイックスタート。
- [Azure PowerShell を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-powershell.md)をデプロイするためのクイックスタート。
- [Azure portal を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-portal.md)をデプロイするためのクイックスタート。
> [!div class="nextstepaction"]


