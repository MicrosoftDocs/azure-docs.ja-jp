---
title: PowerShell を使用して別のギャラリーからイメージをコピーする
description: Azure PowerShell を使用して別のギャラリーからイメージをコピーします。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d9bbe40e35bdad6fac5c5ccb0b15b909e77b938c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564018"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>PowerShell を使用して別のギャラリーからイメージをコピーする

組織内に複数のギャラリーがある場合は、他のギャラリーに保存されているイメージからイメージを作成できます。 たとえば、新しいイメージを作成してテストするための開発およびテスト ギャラリーがあるとします。 運用環境で使用する準備ができたら、この例を使用して、それらのイメージを運用環境ギャラリーにコピーできます。 また、[Azure CLI](image-version-another-gallery-cli.md) を使用して、別のギャラリーのイメージからイメージを作成することもできます。


## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、既存のソース ギャラリー、イメージ定義、およびイメージ バージョンが必要です。 また、コピー先ギャラリーも必要です。 

ソース イメージ バージョンは、コピー先ギャラリーのあるリージョンにレプリケートする必要があります。 

コピー先ギャラリーに、新しいイメージ定義とイメージ バージョンが作成されます。


この記事の手順に出現するリソース名は適宜置き換えてください。


## <a name="get-the-source-image"></a>ソース イメージを取得する 

コピー先ギャラリーにコピーを作成できるように、ソース イメージ定義からの情報が必要になります。

[Get-AzResource](/powershell/module/az.resources/get-azresource) コマンドレットを使用して、既存のギャラリー、イメージ定義、およびイメージ バージョンに関する情報を一覧表示します。

結果は `gallery\image definition\image version` の形式になります。

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

必要な情報がすべて揃ったら、[Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) を使用してソース イメージ バージョンの ID を取得できます。 この例では、`myImageDefinition` 定義の `1.0.0` イメージ バージョンを、`myGallery` ソース ギャラリーの `myResourceGroup` リソース グループに取得しています。

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>イメージ定義を作成する 

ソースのイメージ定義と一致する新しいイメージ定義を作成する必要があります。 [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition) を使用して、イメージ定義を再作成するために必要なすべての情報を確認できます。

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


出力は次のようになります。

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) コマンドレットと上記の出力の情報を使用して、対象のギャラリーに新しいイメージ定義を作成します。


この例では、*myDestinationGallery* というギャラリー内のイメージ定義に *myDestinationImgDef* という名前が付けられています。


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>イメージ バージョンの作成

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) を使用してイメージ バージョンを作成します。 対象のギャラリーにイメージ バージョンを作成するには、`--managed-image` パラメーターでソース イメージの ID を渡す必要があります。 

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例では、*米国西部* の場所にある *myDestinationRG* リソース グループに属する対象のギャラリーに *myDestinationGallery* という名前が付けられています。 このイメージのバージョンは *1.0.0* です。これから、*米国中南部* リージョンに 1 つのレプリカ、*米国西部* リージョンに 2 つのレプリカを作成します。 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
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

[一般化された](vm-generalized-image-version-powershell.md)イメージ バージョンまたは[特殊化された](vm-specialized-image-version-powershell.md)イメージ バージョンから VM を作成します。

[Azure Image Builder (プレビュー)](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./linux/image-builder-gallery-update-image-version.md)に使用することさえできます。 

購入プラン情報を提供する方法については、「[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)」を参照してください。