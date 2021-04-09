---
title: 'チュートリアル: NAT ゲートウェイの作成 - PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Azure PowerShell を使用した NAT ゲートウェイの作成を開始します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102619885"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用した NAT ゲートウェイの作成

このチュートリアルでは、Azure Virtual Network NAT サービスを使用する方法について説明します。 Azure 内の仮想マシンに送信接続を提供する NAT ゲートウェイを作成しましょう。 

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * 仮想ネットワークを作成します。
> * 仮想マシンを作成します。
> * NAT ゲートウェイを作成し、仮想ネットワークに関連付けます。
> * 仮想マシンに接続し、NAT IP アドレスを確認します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、**myResourceGroupNAT** という名前のリソース グループを **eastus2** という場所に作成します。

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>NAT ゲートウェイの作成

このセクションでは、NAT ゲートウェイとサポート リソースを作成します。

* インターネットにアクセスするには、NAT ゲートウェイに 1 つ以上のパブリック IP アドレスが必要です。 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**myResourceGroupNAT** に **myPublicIP** というパブリック IP アドレス リソースを作成します。 

* [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway) を使用して、グローバル Azure NAT ゲートウェイを作成します。 このコマンドの結果、パブリック IP アドレス **myPublicIP** を使用する **myNATgateway** という名前のゲートウェイ リソースが作成されます。 アイドル タイムアウトは 10 分に設定されています。  

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して **myVnet** という名前の仮想ネットワークを作成し、[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) を使用して、**myResourceGroup** 内に **mySubnet** という名前のサブネットを作成します。 この仮想ネットワークの IP アドレス空間は **10.1.0.0/16** です。 この仮想ネットワーク内のサブネットは **10.1.0.0/24** です。

* 仮想マシンにアクセスするための **myBastionHost** という名前の Azure Bastion ホストを作成します。 [New-AzBastion](/powershell/module/az.network/new-azbastion) を使用して、bastion ホストを作成します。 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、bastion ホストのパブリック IP アドレスを作成します。

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>仮想マシン

このセクションでは、NAT ゲートウェイをテストする仮想マシンを作成し、アウトバウンド接続のパブリック IP アドレスを確認します。

* [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用してネットワーク インターフェイスを作成します。

* [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

* 次のコマンドを使用して、仮想マシンを作成します。
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

仮想マシンの作成が完了するのを待って次のセクションに進みます。

## <a name="test-nat-gateway"></a>NAT ゲートウェイをテストする

このセクションでは、NAT ゲートウェイをテストします。 まず、NAT ゲートウェイのパブリック IP を検出します。 次に、テスト仮想マシンに接続し、NAT ゲートウェイ経由のアウトバウンド接続を確認します。
    
1. [Azure ポータル](https://portal.azure.com)

1. **[概要]** 画面で、NAT ゲートウェイのパブリック IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** 、 **[myPublicIP]** の順に選択します。

2. パブリック IP アドレスを書き留めておきます。

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT ゲートウェイのパブリック IP アドレスを検出する" border="true":::

3. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myResourceGroupNAT]** リソース グループにある **[myVM]** を選択します。

4. **[概要]** ページで **[接続]** 、 **[要塞]** の順に選択します。

5. 青色の **[Bastion を使用する]** ボタンを選択します。

6. VM 作成時に入力したユーザー名とパスワードを入力します。

7. **myTestVM** で **Internet Explorer** 開きます。

8. アドレス バーに「 **https://whatsmyip.com** 」と入力します。

9. 表示された IP アドレスが前の手順でメモしておいた NAT ゲートウェイのアドレスと一致していることを確認します。

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer に表示された外部アウトバウンド IP" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使う予定がなければ、次の手順を使用して、仮想ネットワーク、仮想マシン、および NAT ゲートウェイを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>次のステップ

Azure Virtual Network NAT の詳細については、以下を参照してください。
> [!div class="nextstepaction"]
> [Virtual Network NAT の概要](nat-overview.md)
