---
title: VM からイメージを作成する
description: Azure PowerShell を使用し、Azure の既存 VM から Shared Image Gallery にイメージを作成する方法について説明します。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f7afc671dbb3d4ef8aa30222c4f235c9f7869d02
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556844"
---
# <a name="create-an-image-from-a-vm"></a>VM からイメージを作成する

既存の VM を使用し、同じ VM を複数作成する場合、Azure PowerShell を利用し、その VM から Shared Image Gallery にイメージを作成できます。 [Azure CLI](image-version-vm-cli.md) を利用して VM からイメージを作成することもできます。

Azure PowerShell を利用し、[特殊な VM と一般の VM](./shared-image-galleries.md#generalized-and-specialized-images) の両方からイメージをキャプチャできます。 

イメージ ギャラリー内のイメージには 2 つのコンポーネントがあります。この例ではそれを作成します。
- **イメージ定義** には、イメージに関する情報とそれを使用するための要件が含まれます。 これには、イメージの OS (Windows または Linux)、形態 (特殊化または一般化)、リリース ノート、最小メモリ要件、最大メモリ要件が含まれます。 これは、イメージの種類の定義です。 
- **イメージ バージョン** は、Shared Image Gallery の使用時に VM の作成に使用されるものです。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 VM を作成するとき、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。


## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、既存の Shared Image Gallery と、ソースとして使用する Azure の既存 VM が必要です。 

VM にデータ ディスクが取り付けられている場合、データ ディスクのサイズが 1 TB を超えることはできません。

この記事の作業に出現するリソース名は適宜置き換えてください。


## <a name="get-the-gallery"></a>ギャラリーを取得する

ギャラリーとイメージの定義はすべて、名前で一覧表示できます。 結果は `gallery\image definition\image version` 形式になります。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

適切なギャラリーとイメージの定義を見つけたら、後で使用する変数をそれらから作成します。 この例の場合、*myResourceGroup* リソース グループで *myGallery* という名前のギャラリーを取得します。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>VM を取得する

リソース グループで利用できる VM は、[Get-AzVM](/powershell/module/az.compute/get-azvm) を使用して一覧表示できます。 VM の名前とそれが属するリソース グループがわかったら、もう一度 `Get-AzVM` を使用して VM オブジェクトを取得し、後で使用できるよう変数に格納することができます。 この例では、*sourceVM* という名前の VM を "myResourceGroup" リソース グループから取得し、変数 *$sourceVm* に割り当てています。 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

[Stop-AzVM](/powershell/module/az.compute/stop-azvm) を利用し、イメージを作成する前に VM を停止 (または割り当て解除) することをお勧めします。

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>イメージ定義を作成する 

イメージ定義では、イメージの論理グループを作成します。 イメージに関する情報を管理する目的で使用されます。 イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 

イメージ定義を作成するとき、すべての情報が正しくなるようにしてください。 (Windows の場合は Sysprep、Linux の場合は waagent -deprovision を使用して) VM を一般化している場合は、`-OsState generalized` を使用してイメージ定義を作成する必要があります。 VM を一般化していない場合、`-OsState specialized` を利用してイメージ定義を作成します。

イメージ定義に指定できる値の詳細については、[イメージ定義](./shared-image-galleries.md#image-definitions)に関するページを参照してください。

イメージの定義は、[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) を使用して作成します。 

この例では、イメージ定義は *myImageDefinition* という名前で、Windows を実行する特殊な VM 用です。 Linux を使用してイメージの定義を作成するには、`-OsType Linux` を使用します。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>イメージ バージョンを作成する

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) を使用してイメージ バージョンを作成します。 

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例のイメージ バージョンは *1.0.0* で、"*米国中西部*" と "*米国中南部*" の両方のデータセンターにレプリケートされます。 レプリケーションのターゲット リージョンを選択するときに、レプリケーションのターゲットとして、"*ソース*" リージョンも含める必要があることに注意してください。

VM からイメージ バージョンを作成するには、`-SourceImageId` に `$vm.Id.ToString()` を使用します。

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

すべてのターゲット リージョンにイメージがレプリケートされるまでにしばらく時間がかかることがあるため、進行状況を追跡できるようジョブを作成しました。 ジョブの進行状況を確認するには、「`$job.State`」と入力します。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> また、イメージ バージョンを作成するときに、`-StorageAccountType Premium_LRS` を追加してイメージを Premium ストレージに格納することも、`-StorageAccountType Standard_ZRS` を追加して[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>

## <a name="next-steps"></a>次のステップ

新しいイメージ バージョンが正しく機能することが確認できたら、VM を作成できます。 [一般化されたイメージ バージョン](vm-specialized-image-version-powershell.md)または[特殊化されたイメージ バージョン](vm-generalized-image-version-powershell.md)から VM を作成します。

購入プラン情報を提供する方法については、[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)に関する記事を参照してください。