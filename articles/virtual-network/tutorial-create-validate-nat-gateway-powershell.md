---
title: NAT ゲートウェイの作成とテスト - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: このチュートリアルでは、Azure PowerShell を使用して NAT ゲートウェイを作成し、NAT サービスをテストする方法について説明します
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: 3eaade678142a26be562d6c216f9932bcbaf2c39
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054034"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>チュートリアル:Azure PowerShell を使用した NAT ゲートウェイの作成と NAT サービスのテスト

このチュートリアルでは、Azure 内の仮想マシンに送信接続を提供する NAT ゲートウェイを作成します。 NAT ゲートウェイをテストするために、ソースと宛先の仮想マシンをデプロイします。 パブリック IP アドレスへの送信接続を行って、NAT ゲートウェイをテストします。 これらの接続は、ソースから宛先の仮想マシンに対して確立されます。 このチュートリアルでは、わかりやすくするために、同じリソース グループ内の 2 つの異なる仮想ネットワークにソースと宛先をデプロイしています。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

このチュートリアルは、Azure Cloud Shell を使用して行うことができます。また、それぞれのコマンドをローカルで実行してもかまいません。  Azure Cloud Shell を使用したことがない場合は、[今すぐサインイン](https://shell.azure.com)してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](https://docs.microsoft.com/cli/azure/group) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、**myResourceGroupNAT** という名前のリソース グループを **eastus2** という場所に作成します。


```azurepowershell-interactive
$rgname = 'myResourceGroupNAT'
$loc = 'eastus2'

$rg = New-AzResourceGroup -Name $rgname -Location $loc

```

## <a name="create-the-nat-gateway"></a>NAT ゲートウェイの作成

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

インターネットにアクセスするには、NAT ゲートウェイに 1 つ以上のパブリック IP アドレスが必要です。 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) を使用して、**myResourceGroupNAT** に **myPublicIPsource** というパブリック IP アドレス リソースを作成します。 このコマンドの結果は、後で使用できるように **$publicIPsource** という名前の変数に格納されます。

```azurepowershell-interactive
$pipname = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pipname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -Sku $sku -Location $rg.Location

```

### <a name="create-a-public-ip-prefix"></a>パブリック IP プレフィックスの作成

 [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) を使用して、**myResourceGroupNAT** に **myPublicIPprefixsource** というパブリック IP プレフィックス リソースを作成します。  このコマンドの結果は、後で使用できるように **$publicIPPrefixsource** という名前の変数に格納されます。

```azurepowershell-interactive
$prefixname = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prefixname -ResourceGroupName $rg.ResourceGroupName -PrefixLength 31 -Location $rg.Location

```

### <a name="create-a-nat-gateway-resource"></a>NAT ゲートウェイ リソースの作成

このセクションでは、NAT ゲートウェイ リソースを使用して、次の NAT サービスのコンポーネントを作成して構成する方法について説明します。
  - NAT ゲートウェイ リソースによって変換される送信フローに使用する、パブリック IP プールとパブリック IP プレフィックス。
  - アイドル タイムアウトを既定値の 4 分から 10 分に変更します。

[New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) を使用して、グローバル Azure NAT ゲートウェイを作成します。 このコマンドの結果、パブリック IP アドレス **myPublicIPsource** とパブリック IP プレフィックス **myPublicIPprefixsource** を使用する **myNATgateway** という名前のゲートウェイ リソースが作成されます。 アイドル タイムアウトは 10 分に設定されています。  このコマンドの結果は、後で使用できるように **$natGateway** という名前の変数に格納されます。

```azurepowershell-interactive
$sku = 'Standard'
$natname = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $natname -ResourceGroupName $rg.ResourceGroupName -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Sku $sku -IdleTimeoutInMinutes 10 -Location $rg.Location

  ```

この時点で、NAT ゲートウェイは機能しており、ほかに必要なことは、これを使用する仮想ネットワークのサブネットを構成することだけです。

## <a name="prepare-the-source-for-outbound-traffic"></a>送信トラフィックのソースの準備

ここでは、完全なテスト環境のセットアップ手順を説明します。 オープンソース ツールを使用して、NAT ゲートウェイを検証するテストをセットアップしましょう。 まず、ソースの作業から始めます。先ほど作成した NAT ゲートウェイが使用されます。

### <a name="configure-virtual-network-for-source"></a>ソースの仮想ネットワークの構成

仮想ネットワークを作成し、サブネットをゲートウェイに関連付けます。

[New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) を使用して **myVnetsource** という名前の仮想ネットワークを作成し、[New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) を使用して、**myResourceGroupNAT** 内に **mySubnetsource** という名前のサブネットを作成します。 この仮想ネットワークの IP アドレス空間は **192.168.0.0/16** です。 この仮想ネットワーク内のサブネットは **192.168.0.0/24** です。  コマンドの結果は、後で使用できるように **$subnetsource** および **$vnetsource** という名前の変数に格納されます。

```azurepowershell-interactive
$subnetname = 'mySubnetsource'
$subnetprefix = '192.168.0.0/24'
$vnetname = 'myVnetsource'
$vnetprefix = '192.168.0.0/16'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $subnetname -AddressPrefix $subnetprefix -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnetname -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vnetprefix -Subnet $subnetsource -Location $rg.Location

```

インターネットを宛先とするすべての送信トラフィックが、この NAT サービスを使用するようになります。  UDR を構成する必要はありません。

NAT ゲートウェイをテストする前に、ソース VM を作成する必要があります。  外部からこの VM にアクセスするためのインスタンスレベルのパブリック IP として、パブリック IP アドレス リソースを割り当てます。 このアドレスは、テスト アクセスのみを目的としたものです。  NAT サービスが他の送信オプションよりも優先される方法を示します。

演習として、この VM をパブリック IP なしで作成し、ジャンプボックスとして使用する別の VM をパブリック IP なしで作成することもできます。

### <a name="create-public-ip-for-source-vm"></a>ソース VM のパブリック IP の作成

VM へのアクセスに使用するパブリック IP を作成します。  [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) を使用して、**myResourceGroupNAT** に **myPublicIPVM** というパブリック IP アドレス リソースを作成します。  このコマンドの結果は、後で使用できるように **$publicIpsourceVM** という名前の変数に格納されます。

```azurepowershell-interactive
$sku = 'Standard'
$pipvmname = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvmname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>NSG の作成と VM の SSH エンドポイントの公開

Standard パブリック IP アドレスは "既定でセキュリティ保護" されているため、SSH の受信アクセスを許可する NSG を作成します。 NAT サービスはフロー方向を認識します。 NAT ゲートウェイが同じサブネット上に構成された後は、この NSG は送信に使用されません。 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) を使用して、**myNSGsource** という名前の NSG リソースを作成します。 [AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) を使用して、**myResourceGroupNAT** に **ssh** という名前の SSH アクセス用の NSG ルールを作成します。 このコマンドの結果は、後で使用できるように **$nsgsource** という名前の変数に格納されます。

```azurepowershell-interactive
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule -Location $rg.Location

```

### <a name="create-nic-for-source-vm"></a>ソース VM の NIC の作成

[New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) を使用して、**myNicsource** という名前のネットワーク インターフェイスを作成します。 このコマンドにより、パブリック IP アドレスとネットワーク セキュリティ グループが関連付けられます。 このコマンドの結果は、後で使用できるように **$nicsource** という名前の変数に格納されます。

```azurepowershell-interactive
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-source-vm"></a>ソース VM の作成

#### <a name="create-ssh-key-pair"></a>SSH キー ペアの作成

このクイック スタートを完了するには、SSH キー ペアが必要です。 既存の SSH キーの組がある場合は、この手順をスキップできます。

ssh-keygen を使用して SSH キー ペアを作成します。

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048

```
PuTTy の使用を含む SSH キー ペアの作成方法の詳細については、[Windows で SSH キーを使用する方法](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)に関するページを参照してください。

Cloud Shell を使用して SSH キー ペアを作成した場合、キー ペアはコンテナー イメージに格納されます。 この[ストレージ アカウントは自動的に作成されます](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)。 キーを取得するまでは、ストレージ アカウントまたは内部のファイル共有を削除しないでください。

#### <a name="create-vm-configuration"></a>VM 構成の作成

PowerShell で VM を作成するには、使用するイメージ、サイズ、認証オプションなど、各種設定を含んだ構成を作成します。 この構成を使用して VM を作成することになります。

SSH の資格情報、OS の情報、VM のサイズを定義します。 この例では、SSH キーは ~/.ssh/id_rsa.pub に格納されています。

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
構成定義を組み合わせて、[New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) を使用して **myResourceGroupNAT** に **myVMsource** という名前の VM を作成します。

```azurepowershell-interactive
New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigsource -Location $rg.Location

```

このコマンドはすぐに制御を戻しますが、VM がデプロイされるまでに数分かかることがあります。

## <a name="prepare-destination-for-outbound-traffic"></a>送信トラフィックの宛先の準備

今度は、NAT サービスをテストできるよう、NAT サービスによって変換された送信トラフィックの宛先を作成しましょう。

### <a name="configure-virtual-network-for-destination"></a>宛先の仮想ネットワークの構成

宛先の仮想マシンを配置する仮想ネットワークを作成する必要があります。  これらのコマンドは、ソース VM の手順と同じです。 宛先エンドポイントを公開するための若干の変更が追加されています。

[New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) を使用して **myVnetdestination** という名前の仮想ネットワークを作成し、[New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) を使用して、**myResourceGroupNAT** 内に **mySubnetdestination** という名前のサブネットを作成します。 この仮想ネットワークの IP アドレス空間は **192.168.0.0/16** です。 この仮想ネットワーク内のサブネットは **192.168.0.0/24** です。  コマンドの結果は、後で使用できるように **$subnetdestination** および **$vnetdestination** という名前の変数に格納されます。

```azurepowershell-interactive
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vpfx -Subnet $subnetdestination -Location $rg.Location

```

### <a name="create-public-ip-for-destination-vm"></a>宛先 VM のパブリック IP の作成

宛先 VM へのアクセスに使用するパブリック IP を作成します。  [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) を使用して、**myResourceGroupNAT** に **myPublicIPdestinationVM** というパブリック IP アドレス リソースを作成します。  このコマンドの結果は、後で使用できるように **$publicIpdestinationVM** という名前の変数に格納されます。

```azurepowershell-interactive
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $all -Sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>NSG の作成と VM の SSH および HTTP エンドポイントの公開

Standard パブリック IP アドレスは "既定でセキュリティ保護" されているため、SSH の受信アクセスを許可する NSG を作成します。 [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) を使用して、**myNSGdestination** という名前の NSG リソースを作成します。 [AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) を使用して、**ssh** という名前の SSH アクセス用の NSG ルールを作成します。  [AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) を使用して、**http** という名前の HTTP アクセス用の NSG ルールを作成します。 両方のルールが **myResourceGroupNAT** に作成されます。 このコマンドの結果は、後で使用できるように **$nsgdestination** という名前の変数に格納されます。

```azurepowershell-interactive
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule,$httprule -Location $rg.Location

```

### <a name="create-nic-for-destination-vm"></a>宛先 VM の NIC の作成

[New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) を使用して、**myNicdestination** という名前のネットワーク インターフェイスを作成します。 このコマンドにより、パブリック IP アドレスとネットワーク セキュリティ グループが関連付けられます。 このコマンドの結果は、後で使用できるように **$nicdestination** という名前の変数に格納されます。

```azurepowershell-interactive
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-destination-vm"></a>宛先 VM の作成

#### <a name="create-vm-configuration"></a>VM 構成の作成

PowerShell で VM を作成するには、使用するイメージ、サイズ、認証オプションなど、各種設定を含んだ構成を作成します。 この構成を使用して VM を作成することになります。

SSH の資格情報、OS の情報、VM のサイズを定義します。 この例では、SSH キーは ~/.ssh/id_rsa.pub に格納されています。

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vmd = 'myVMdestination'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
構成定義を組み合わせて、[New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) を使用して **myResourceGroupNAT** に **myVMdestination** という名前の VM を作成します。

```azurepowershell-interactive

New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigdestination -Location $rg.Location

```

このコマンドはすぐに制御を戻しますが、VM がデプロイされるまでに数分かかることがあります。

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>宛先 VM における Web サーバーの準備とペイロードのテスト

まず、宛先 VM の IP アドレスを検出する必要があります。  VM のパブリック IP アドレスを取得するには、[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) を使用します。 

```azurepowershell-interactive
$pipname = 'myPublicIPdestinationVM'
  
$destip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$destip

``` 

>[!IMPORTANT]
>パブリック IP アドレスをコピーしたら、後続の手順で使用できるようにメモ帳に貼り付けます。 これが宛先の仮想マシンであることがわかるようにしておいてください。

### <a name="sign-in-to-destination-vm"></a>宛先 VM へのサインイン

Cloud Shell には、前の操作で生成した SSH の資格情報が格納されているはずです。  ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) を開きます。 前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。 

```azurepowershell-interactive
ssh azureuser@$destip

```

ログインしたら、次のコマンドをコピーして貼り付けます。  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

これらのコマンドによって仮想マシンが更新され、nginx がインストールされて、100 キロバイトのファイルが作成されます。 このファイルは、NAT サービスを使用してソース VM から取得されます。

宛先 VM との SSH セッションを閉じます。

## <a name="prepare-test-on-source-vm"></a>ソース VM におけるテストの準備

まず、ソース VM の IP アドレスを検出する必要があります。  VM のパブリック IP アドレスを取得するには、[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) を使用します。 

```azurepowershell-interactive
$pipname = 'myPublicIPsourceVM'

$srcip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$srcip

``` 

>[!IMPORTANT]
>パブリック IP アドレスをコピーしたら、後続の手順で使用できるようにメモ帳に貼り付けます。 これがソースの仮想マシンであることがわかるようにしておいてください。

### <a name="log-into-source-vm"></a>ソース VM へのログイン

この場合も、SSH の資格情報は Cloud Shell に格納されます。 ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) の新しいタブを開きます。  前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。 

```azurepowershell-interactive
ssh azureuser@$srcip

```

NAT サービスのテスト準備を行うために、次のコマンドをコピーして貼り付けます。

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

これらのコマンドによって仮想マシンが更新され、go がインストールされ、GitHub から [hey](https://github.com/rakyll/hey) がインストールされて、シェル環境が更新されます。

これで、NAT サービスをテストする準備が整いました。

## <a name="validate-nat-service"></a>NAT サービスの検証

ソース VM にログインしている間、**curl** と **hey** を使用して、宛先 IP アドレスへの要求を生成できます。

curl を使用して 100 キロバイトのファイルを取得します。  下の例の **\<ip-address-destination>** は、先ほどコピーした宛先 IP アドレスに置き換えてください。  **--output** パラメーターは、取得したファイルが破棄されることを示します。

```bash
curl http://<ip-address-destination>/100k --output /dev/null

```

**hey** を使用して、一連の要求を生成することもできます。 この場合も、 **\<ip-address-destination>** は、先ほどコピーした宛先 IP アドレスに置き換えてください。

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k

```

このコマンドは、100 件の要求を生成するものです。一度に 10 件ずつ生成し、タイムアウトは 30 秒とします。 TCP 接続は再利用されません。  それぞれの要求で 100 キロバイトが取得されます。  実行の最後に、NAT サービスの稼動状態についていくつかの統計情報が **hey** からレポートされます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) コマンドを使用して、リソース グループと、その内部に含まれているすべてのリソースを削除できます。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name $rg.ResourceGroupName

```

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、NAT ゲートウェイを作成し、ソース VM と宛先 VM を作成した後、NAT ゲートウェイをテストしました。

Azure Monitor でメトリックを見て、NAT サービスの稼動状態を確認しましょう。 利用可能な SNAT ポートのリソース枯渇などの問題を診断します。  SNAT ポートのリソース枯渇は、新しいパブリック IP アドレス リソースかパブリック IP プレフィックス リソース、またはその両方を追加することで簡単に解決できます。

- [Virtual Network NAT](./nat-overview.md) について学習する。
- [NAT ゲートウェイ リソース](./nat-gateway-resource.md)について学習する。
- [Azure CLI を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-cli.md)をデプロイするためのクイックスタート。
- [Azure PowerShell を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-powershell.md)をデプロイするためのクイックスタート。
- [Azure portal を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-portal.md)をデプロイするためのクイックスタート。

> [!div class="nextstepaction"]

