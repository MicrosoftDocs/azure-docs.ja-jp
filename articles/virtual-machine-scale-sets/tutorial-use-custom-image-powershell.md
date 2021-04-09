---
title: チュートリアル - Azure PowerShell を使用してスケール セットでカスタム VM イメージを使用する
description: Azure PowerShell を使用して仮想マシン スケール セットをデプロイするためのカスタム VM イメージを作成する方法について説明します
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 803bbf485d783fc160d1f2644a56e1339f73690e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678307"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用した仮想マシン スケール セットのカスタム イメージの作成および使用

スケール セットを作成するときは、VM インスタンスのデプロイ時に使用するイメージを指定します。 VM インスタンスをデプロイした後のタスクの数を減らすには、カスタム VM イメージを使用できます。 このカスタム VM イメージには、すべての必要なアプリケーション インストールまたは構成が含まれます。 スケール セットで作成されたすべての VM インスタンスは、カスタム VM イメージを使用し、アプリケーション トラフィックを処理できる状態になります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Shared Image Gallery を作成する
> * イメージ定義を作成する
> * イメージ バージョンを作成する
> * イメージからスケールセットを作成する 
> * イメージ ギャラリーを共有する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

以下の手順では、既存の VM を取得し、新しいスケール セットの作成に使用できる再利用可能なカスタム イメージに変換する方法について詳しく説明します。

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、[PowerShell クイックスタート](quick-create-powershell.md)を参照して、このチュートリアルで使用する VM を作成できます。 このチュートリアルを実行するときは、リソース名を適宜置き換えてください。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。


## <a name="get-the-vm"></a>VM を取得する

リソース グループで利用できる VM は、[Get-AzVM](/powershell/module/az.compute/get-azvm) を使用して一覧表示できます。 VM の名前とリソース グループがわかったら、もう一度 `Get-AzVM` を使用して VM オブジェクトを取得し、後で使用できるよう変数に格納することができます。 この例では、*sourceVM* という名前の VM を "myResourceGroup" リソース グループから取得し、変数 *$vm* に割り当てています。 

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

イメージ定義では、イメージの論理グループを作成します。 これは、その中に作成されるイメージ バージョンに関する情報を管理するために使用されます。 イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 イメージ定義に指定できる値の詳細については、[イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)に関するページを参照してください。

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

## <a name="create-a-scale-set-from-the-image"></a>イメージからスケール セットを作成する
[New-AzVmss](/powershell/module/az.compute/new-azvmss) を使用して、スケール セットを作成します。このとき、`-ImageName` パラメーターを使用して、前の手順で作成したカスタム VM イメージを定義します。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 ロード バランサーには、TCP ポート 80 上のトラフィックを分散するルールだけでなく、TCP ポート 3389 上のリモート デスクトップ トラフィックと TCP ポート 5985 上の PowerShell リモート処理を許可するルールも含まれています。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を指定します。

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $galleryImage.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig 

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


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

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure では、Packer 上に構築された [Azure VM Image Builder](../virtual-machines/image-builder-overview.md) サービスも提供しています。 テンプレートにカスタマイズを記述するだけで、イメージの作成が処理されます。 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure PowerShell を使用してスケール セットにカスタム VM イメージを作成して使用する方法について学習しました。

> [!div class="checklist"]
> * Shared Image Gallery を作成する
> * イメージ定義を作成する
> * イメージ バージョンを作成する
> * イメージからスケールセットを作成する 
> * イメージ ギャラリーを共有する

次のチュートリアルに進み、アプリケーションをスケール セットにデプロイする方法を学習してください。

> [!div class="nextstepaction"]
> [アプリケーションをスケール セットにデプロイする](tutorial-install-apps-powershell.md)