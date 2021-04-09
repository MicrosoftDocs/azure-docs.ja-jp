---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 32978257c3e209dc78a29c6e8ae0d1c4ae016a5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561572"
---
## <a name="update-resources"></a>リソースの更新

更新可能な対象については、いくつか制限があります。 次の項目を更新できます。 

共有イメージ ギャラリー:
- 説明

イメージ定義:
- 推奨される vCPU:
- 推奨されるメモリ
- 説明
- 有効期限の終了日

イメージ バージョン:
- リージョンのレプリカ数
- ターゲット リージョン
- 最新バージョンからの除外
- 有効期限の終了日

レプリカ リージョンを追加する予定の場合は、ソース マネージド イメージを削除しないでください。 追加リージョンにイメージ バージョンをレプリケートするには、ソース マネージド イメージが必要です。 

ギャラリーの説明を更新するには、[Update-AzGallery](/powershell/module/az.compute/update-azgallery) を使用します。

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

この例では、[Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) を使用して、イメージ定義の有効期限の日付を更新します。

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

この例では、[Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) を使用して、このイメージ バージョンが "*最新*" のイメージとして使用されないようにする方法を示します。

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

この例では、[Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) を使用して、このイメージ バージョンを "*最新*" のイメージと見なして含める方法を示します。

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースを削除するときは、入れ子になったリソース (イメージ バージョン) の最後の項目から始める必要があります。 バージョンを削除したら、イメージ定義を削除できます。 ギャラリーに含まれるすべてのリソースを削除するまで、ギャラリーを削除することはできません。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```