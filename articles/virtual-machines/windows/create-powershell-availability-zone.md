---
title: Azure PowerShell を使用したゾーン VM の作成
description: Azure PowerShell を使用して可用性ゾーン内に仮想マシンを作成する
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 7167c5806fde883a84878d4947dba9fd8c095f69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558833"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-powershell"></a>Azure PowerShell を使用して可用性ゾーン内に仮想マシンを作成する

この記事では、Azure PowerShell を使用して、Windows Server 2016 を実行する Azure 仮想マシンを Azure 可用性ゾーン内に作成する方法を詳しく説明します。 [可用性ゾーン](../../availability-zones/az-overview.md)とは、1 つの Azure リージョン内で物理的に分離されたゾーンのことです。 可用性ゾーンは、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護するために使用します。

可用性ゾーンを使用するには、[サポートされている Azure リージョン](../../availability-zones/az-region.md)に仮想マシンを作成します。

 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>提供されている VM SKU の確認
提供されている VM サイズ (SKU) は、リージョンやゾーンによって異なる場合があります。 可用性ゾーンの使用計画を立てやすくするために、提供されている VM SKU を Azure リージョンとゾーンごとに一覧表示することができます。 そうすることで適切な VM サイズを選び、必要な回復性をすべてのゾーンにわたって確保することができます。 さまざまな VM の種類とサイズについて詳しくは、[VM サイズの概要](../sizes.md)に関するページを参照してください。

使用可能な VM SKU は、[Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) コマンドで確認できます。 次の例では、*eastus2* リージョンで提供されている VM SKU を一覧表示しています。

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

次に示したのは、その出力例の抜粋です。これは各 VM サイズが提供されている可用性ゾーンを確認できます。

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この例では、*myResourceGroup* という名前のリソース グループが *eastus2* リージョンに作成されます。 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>ネットワーク リソースの作成

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>仮想ネットワーク、サブネット、パブリック IP アドレスを作成する 
これらのリソースは、仮想マシンへのネットワーク接続を提供し、その仮想マシンをインターネットに接続するために使用されます。 IP アドレスを可用性ゾーン (この例では *2*) 内に作成します。 後の手順で、IP アドレスの作成に使用したのと同じゾーンに VM を作成します。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)" -Sku Standard
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>ネットワーク セキュリティ グループとネットワーク セキュリティ グループの規則を作成する 
ネットワーク セキュリティ グループは、受信規則と送信規則を使用して仮想マシンを保護します。 この場合、受信規則はポート 3389 に作成され、受信リモート デスクトップ接続を許可します。 さらにポート 80 に対して、受信 Web トラフィックを許可する受信規則を作成します。

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>仮想マシン用のネットワーク カードを作成する 
[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、仮想マシン用のネットワーク カードを作成します。 ネットワーク カードは、仮想マシンをサブネット、ネットワーク セキュリティ グループ、パブリック IP アドレスに接続します。

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンの構成を作成します。 この構成には、仮想マシンのデプロイ時に使用される設定 (仮想マシンのイメージ、サイズ、認証構成など) が含まれています。 この例の *Standard_DS1_v2* サイズは、可用性ゾーンでサポートされています。 この構成では、IP アドレスを作成するときに設定した可用性ゾーンも指定します。 この手順の実行時に、資格情報の入力を求められます。 入力した値は、仮想マシンのユーザー名とパスワードとして構成されます。

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して、仮想マシンを作成します。

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>マネージド ディスクのゾーンの確認

VM と同じ可用性ゾーンに VM の IP アドレス リソースを作成しました。 VM のマネージド ディスク リソースは、同じ可用性ゾーンに作成されます。 このことは、[Get-AzDisk](/powershell/module/az.compute/get-azdisk) で確認できます。

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

この出力は、マネージド ディスクが VM と同じ可用性ゾーン内にあることを示しています。

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>次のステップ

この記事では、可用性ゾーン内に VM を作成する方法を説明しました。 Azure VM の[可用性](../availability.md)の詳細を確認してください。