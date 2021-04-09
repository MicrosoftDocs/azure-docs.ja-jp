---
title: チュートリアル - Azure PowerShell を使用してカスタム VM イメージを作成する
description: このチュートリアルでは、Azure PowerShell を使用して、Azure 共有イメージ ギャラリーに格納される Windows カスタム仮想マシン イメージを作成する方法を学習します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 145b7300272243099cfccad0c99d8f69e83e6762
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555824"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して Windows VM イメージを作成する

イメージを使用してデプロイのブートストラップを実行し、複数の VM で一貫性を確保することができます。 このチュートリアルでは、PowerShell を使用して Azure 仮想マシンの独自の特殊化されたイメージを作成し、共有イメージ ギャラリーに格納します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Shared Image Gallery を作成する
> * イメージ定義を作成する
> * イメージ バージョンを作成する
> * イメージから VM を作成する 
> * イメージ ギャラリーを共有する



## <a name="before-you-begin"></a>開始する前に

以下の手順では、既存の VM を取得し、新しい VM の作成に使用できる再利用可能なカスタム イメージに変換する方法について詳しく説明します。

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、[PowerShell クイックスタート](quick-create-powershell.md)を参照して、このチュートリアルで使用する VM を作成できます。 このチュートリアルを実行するときは、リソース名を適宜置き換えてください。

## <a name="overview"></a>概要

[共有イメージ ギャラリー](../shared-image-galleries.md)により、組織全体でのカスタム イメージの共有が簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成などの構成のブートストラップを実行するために使用できます。 

共有イメージ ギャラリーを使用すると、カスタム VM イメージを他のユーザーと共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 

共有イメージ ギャラリー機能には、次のような複数のリソースの種類があります。

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="get-the-vm"></a>VM を取得する

リソース グループで利用できる VM は、[Get-AzVM](/powershell/module/az.compute/get-azvm) を使用して一覧表示できます。 VM の名前とリソース グループがわかったら、もう一度 `Get-AzVM` を使用して VM オブジェクトを取得し、後で使用できるよう変数に格納することができます。 この例では、*sourceVM* という名前の VM を "myResourceGroup" リソース グループから取得し、変数 *$sourceVM* に割り当てています。 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myGalleryRG* という名前のリソース グループが *EastUS* リージョンに作成されます。

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 ギャラリー名で許可されている文字は、英字 (大文字または小文字)、数字、ドット、およびピリオドです。 ギャラリー名にダッシュを含めることはできません。 ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 

イメージ ギャラリーは、[New-AzGallery](/powershell/module/az.compute/new-azgallery) を使用して作成します。 次の例では、*myGalleryRG* リソース グループに *myGallery* という名前のギャラリーを作成します。

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>イメージ定義を作成する 

イメージ定義では、イメージの論理グループを作成します。 これは、その中に作成されるイメージ バージョンに関する情報を管理するために使用されます。 イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 イメージ定義に指定できる値の詳細については、[イメージ定義](../shared-image-galleries.md#image-definitions)に関するページを参照してください。

イメージの定義は、[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) を使用して作成します。 この例では、ギャラリー イメージは *myGalleryImage* という名前で、特殊化されたイメージ用に作成されています。 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>イメージ バージョンを作成する

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) を使用して、VM からイメージ バージョンを作成します。 

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例のイメージ バージョンは *1.0.0* で、"*米国東部*" と "*米国中南部*" の両方のデータセンターにレプリケートされます。 レプリケーションのターゲット リージョンを選択するときは、レプリケーションのターゲットとして "*ソース*" リージョンを含める必要があります。

VM からイメージ バージョンを作成するには、`-Source` に `$vm.Id.ToString()` を使用します。

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

イメージをすべてのターゲット リージョンにレプリケートするにはしばらく時間がかかる場合があります。


## <a name="create-a-vm"></a>VM の作成 

特殊化されたイメージが用意できたら、新しい VM を 1 つ以上作成できます。 [New-AzVM](/powershell/module/az.compute/new-azvm) コマンドレットの使用。 このイメージを使用するには、`Set-AzVMSourceImage` を使用し、`-Id` をイメージ定義 ID (この場合は $galleryImage.Id) に設定して、常に最新のイメージ バージョンを使用します。 

必要に応じて、この例のリソース名を置き換えてください。 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>ギャラリーを共有する

イメージ ギャラリー レベルでアクセスを共有することをお勧めします。 電子メール アドレスと [Get-AzADUser](/powershell/module/az.resources/get-azaduser) コマンドレットを使用して、ユーザーのオブジェクト ID を取得し、[New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) を使用して、ギャラリーへのアクセス権を付与します。 この例のサンプル電子メール alinne_montes@contoso.com を独自の情報に置き換えます。

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```
   
## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用して、リソース グループおよびすべての関連リソースを削除できます。

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure では、Packer 上に構築された [Azure VM Image Builder](../image-builder-overview.md) サービスも提供しています。 テンプレートにカスタマイズを記述するだけで、イメージの作成が処理されます。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、特殊化された VM イメージを作成しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Shared Image Gallery を作成する
> * イメージ定義を作成する
> * イメージ バージョンを作成する
> * イメージから VM を作成する 
> * イメージ ギャラリーを共有する

次のチュートリアルに進み、高可用性仮想マシンを作成する方法について学習してください。

> [!div class="nextstepaction"]
> [高可用性 VM の作成](tutorial-availability-sets.md)