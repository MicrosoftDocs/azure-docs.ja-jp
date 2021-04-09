---
title: 静的プライベート IP アドレスを持つ VM を作成する - Azure PowerShell
description: PowerShell を使用してプライベート IP アドレスを持つ仮想マシンを作成する方法を説明します。
services: virtual-network
documentationcenter: na
author: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 43c2e2c8905ca60e59caed3fdf1b4920f509fd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98783786"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>PowerShell を使用して静的プライベート IP アドレスを持つ仮想マシンを作成する

静的プライベート IP アドレスを持つ仮想マシン (VM) を作成できます。 サブネットのどのアドレスを VM に割り当てるかを選択する場合は、動的アドレスではなく、静的プライベート IP アドレスを割り当てます。 [静的プライベート IP アドレス](./public-ip-addresses.md#allocation-method)の詳細を参照してください。 既存の VM に割り当てられたプライベート IP アドレスの動的から静的への変更、またはパブリック IP アドレスの操作については、[IP アドレスの追加、変更、または削除](virtual-network-network-interface-addresses.md)に関する記事を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

ローカル コンピューターから、または Azure Cloud Shell を使用して、次の手順を行うことができます。 ローカル コンピューターを使用する場合は、[Azure PowerShell がインストールされている](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)ことを確認してください。 Azure Cloud Shell を使用する場合は、任意のコマンド ボックスの右上隅にある **[使ってみる]** を選択します。 Cloud Shell によって、Azure にサインインされます。

1. Cloud Shell を使用する場合は、手順 2 に進みます。 コマンド セッションを開き、`Connect-AzAccount` で Azure にサインインします。
2. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 次の例では、Azure の米国東部リージョンにリソース グループを作成します。

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) と [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) の各コマンドを使用して、サブネット構成と仮想ネットワークを作成します。

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) と [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) の各コマンドを使用して、仮想ネットワーク内にネットワーク インターフェイスを作成し、サブネットからそのネットワーク インターフェイスにプライベート IP アドレスを割り当てます。

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig) を使用して VM 構成を作成し、[New-AzVM](/powershell/module/az.Compute/New-azVM) を使用して VM を作成します。 メッセージが表示されたら、VM のサインイン資格情報として使用するユーザー名とパスワードを入力します。

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> プライベート IP アドレスの設定をオペレーティング システムに追加できますが、この操作は、[オペレーティング システムへのプライベート IP アドレスの追加](virtual-network-network-interface-addresses.md#private)に関する記事を読んだ後に行うことをお勧めします。
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> インターネットから VM にアクセスするには、パブリック IP アドレスを VM に割り当てる必要があります。 動的プライベート IP アドレスの割り当てを静的な割り当てに変更することもできます。 詳細については、[IP アドレスの追加または変更](virtual-network-network-interface-addresses.md)に関するページを参照してください。 さらに、ネットワーク セキュリティ グループをネットワーク インターフェイス、ネットワーク インターフェイスを作成したサブネット、またはその両方に関連付けることによって、VM へのネットワーク トラフィックを制限することをお勧めします。 詳しくは、[ネットワーク セキュリティ グループの管理](manage-network-security-group.md)に関するページをご覧ください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

- [プライベート IP アドレス](./private-ip-addresses.md)と Azure 仮想マシンへの[静的プライベート IP アドレス](virtual-network-network-interface-addresses.md#add-ip-addresses)の割り当ての詳細を確認します。
- [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンの作成の詳細を確認します。
