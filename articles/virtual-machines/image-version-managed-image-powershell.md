---
title: マネージド イメージを Shared Image Gallery に複製する
description: Azure PowerShell を使用して、マネージド イメージから Shared Image Gallery のイメージ バージョンに複製する方法について説明します。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: cec4f258cfaa0584c24f2cfc92ec1a536f6277cc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556982"
---
# <a name="clone-a-managed-image-to-a-shared-image-gallery-image"></a>マネージド イメージを Shared Image Gallery に複製する

Shared Image Gallery に複製して移行する既存のマネージド イメージがある場合は、マネージド イメージから直接 Shared Image Gallery イメージを作成できます。 新しいイメージをテストしたら、ソースのマネージド イメージを削除できます。 [Azure CLI](image-version-managed-image-cli.md) を使用して、マネージド イメージから Shared Image Gallery に移行することもできます。

イメージ ギャラリー内のイメージには 2 つのコンポーネントがあります。この例ではそれを作成します。
- **イメージ定義** には、イメージに関する情報とそれを使用するための要件が含まれます。 これには、イメージの OS (Windows または Linux)、形態 (特殊化または一般化)、リリース ノート、最小メモリ要件、最大メモリ要件が含まれます。 これは、イメージの種類の定義です。 
- **イメージ バージョン** は、Shared Image Gallery の使用時に VM の作成に使用されるものです。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 VM を作成するとき、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。


## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、既存のマネージド イメージが必要です。 マネージド イメージにデータ ディスクが含まれている場合、データ ディスクのサイズが 1 TB を超えることはできません。

この記事の作業に出現するリソース グループと VM の名前は適宜置き換えてください。

## <a name="get-the-gallery"></a>ギャラリーを取得する

ギャラリーとイメージの定義はすべて、名前で一覧表示できます。 結果は `gallery\image definition\image version` の形式になります。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

適切なギャラリーが見つかったら、後ほど使用する変数を作成します。 この例の場合、*myResourceGroup* リソース グループで *myGallery* という名前のギャラリーを取得します。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>イメージ定義を作成する 

イメージ定義では、イメージの論理グループを作成します。 イメージに関する情報を管理する目的で使用されます。 イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 

イメージ定義を作成する際は、正しい情報がすべて含まれていることを確認してください。 マネージド イメージは常に一般化されているため、`-OsState generalized` を設定する必要があります。 

イメージ定義に指定できる値の詳細については、[イメージ定義](./shared-image-galleries.md#image-definitions)に関するページを参照してください。

イメージの定義は、[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) を使用して作成します。 この例では、イメージ定義は *myImageDefinition* という名前で、一般化された Windows OS 用です。 Linux OS を使用してイメージの定義を作成するには、`-OsType Linux` を使用します。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>マネージド イメージを取得する

リソース グループで利用できるイメージは、[Get-AzImage](/powershell/module/az.compute/get-azimage) を使用して一覧表示できます。 イメージの名前とそれが属しているリソース グループがわかったら、もう一度 `Get-AzImage` を使用してイメージ オブジェクトを取得し、後で使用できるよう変数に格納することができます。 この例では、*myImage* という名前のイメージを "myResourceGroup" リソース グループから取得し、変数 *$managedImage* に割り当てています。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>イメージ バージョンを作成する

イメージ バージョンは、[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) を使用してマネージド イメージから作成します。 

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例のイメージ バージョンは *1.0.0* で、"*米国中西部*" と "*米国中南部*" の両方のデータセンターにレプリケートされます。 レプリケーションのターゲット リージョンを選択するときに、レプリケーションのターゲットとして、"*ソース*" リージョンも含める必要があることに注意してください。 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

すべてのターゲット リージョンにイメージがレプリケートされるまでにしばらく時間がかかることがあるため、進行状況を追跡できるようジョブを作成しました。 進行状況を確認するには、「`$job.State`」と入力します。

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。 
>
> また、イメージ バージョンを作成するときに、`-StorageAccountType Premium_LRS` を追加してイメージを Premium ストレージに格納することも、`-StorageAccountType Standard_ZRS` を追加して[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>

## <a name="delete-the-managed-image"></a>マネージド イメージを削除する

新しいイメージ バージョンが正しく機能することが確認できたら、マネージド イメージを削除できます。

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>次のステップ

レプリケーションが完了したことを確認できたら、[一般化されたイメージ](vm-generalized-image-version-powershell.md)から VM を作成できます。

購入プラン情報を提供する方法については、[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)に関する記事を参照してください。