---
title: Azure PowerShell を使用して Marketplace 購入プラン情報を指定する
description: Shared Image Gallery でイメージを作成するとき、Azure Marketplace 購入プラン詳細を指定する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9df1f6b02e6572c8f2153016c0142912e24fcfe8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562539"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>イメージ作成時、Azure Marketplace 購入プラン情報を提供する

もともと Azure Marketplace イメージから作成されたソースを利用し、共有ギャラリーでイメージを作成するとき、場合によっては、購入プラン情報を追跡する必要があります。 この記事では、VM の購入プラン情報を見つけ、イメージ定義の作成時にその情報を使用する方法について説明します。 あるイメージの VM を作成するとき、イメージ定義からの情報を利用して購入プラン情報入力を簡単にする方法についても触れます。

Marketplace イメージの検索と使用の詳細については、[Azure Marketplace で Windows VM イメージを検索する](./windows/cli-ps-findimage.md)方法に関するページを参照してください。


## <a name="get-the-source-vm-information"></a>ソース VM 情報を取得する
元の VM がまだある場合は、Get-AzVM を使用して、その VM からプラン名、発行元、および製品情報を取得できます。 この例では、*myResourceGroup* リソース グループの *myVM* という名前の VM を取得し、その VM の購入プラン情報を表示します。

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>イメージ定義を作成する

イメージの格納に使用するイメージ ギャラリーを取得します。 すべてのギャラリーを最初に一覧表示できます。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

次に、使用するギャラリーの変数を作成します。 この例では、*myGalleryRG* リソース グループの *myGallery* に `$gallery` という名前の変数を作成します。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

`-PurchasePlanPublisher`、`-PurchasePlanProduct`、`-PurchasePlanName` パラメーターを使用してイメージ定義を作成します。

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

次に、[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) を使用してイメージ バージョンを作成します。 [VM](image-version-vm-powershell.md#create-an-image-version)、[マネージド イメージ](image-version-managed-image-powershell.md#create-an-image-version)、[VHD またはスナップショット](image-version-snapshot-powershell.md#create-an-image-version)、あるいは[別のイメージ バージョン](image-version-another-gallery-powershell.md#create-the-image-version)からイメージ バージョンを作成できます。 


## <a name="create-the-vm"></a>VM の作成

イメージから VM を作成するとき、[Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) を使用し、イメージ定義からの情報をパブリッシャー情報として渡すことができます。


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>次のステップ

Marketplace イメージの検索と使用の詳細については、[Azure Marketplace で Windows VM イメージを検索する](./windows/cli-ps-findimage.md)方法に関するページを参照してください。
