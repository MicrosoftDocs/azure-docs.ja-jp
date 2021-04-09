---
title: イメージ バージョンからマネージド ディスクを作成する
description: 共有イメージ ギャラリーのイメージ バージョンからマネージド ディスクを作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 359816bbf3fb93a4ec2bfa3092adbb2236ae2f07
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562556"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>イメージ バージョンからマネージド ディスクを作成する

必要に応じて、イメージ バージョンからの OS または単一のデータ ディスクを、Shared Image Gallery に格納されているイメージ バージョンからのマネージド ディスクとしてエクスポートすることができます。


## <a name="cli"></a>CLI

[az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) を使用して、ギャラリーにあるイメージ バージョンを一覧表示します。 この例では、*myImageDefinition* イメージ定義の一部であるイメージ バージョンを、*myGallery* イメージ ギャラリーですべて検索しています。

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

`source` 変数をイメージ バージョンの ID に設定してから、[az disk create](/cli/azure/disk#az_disk_create) を使用してマネージド ディスクを作成します。 

この例では、イメージ バージョンの OS ディスクをエクスポートすることで、*myManagedOSDisk* という名前のマネージド ディスクを、*EastUS* リージョンの *myResourceGroup* という名前のリソース グループ内に作成します。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



イメージ バージョンからデータ ディスクをエクスポートする場合は、`--gallery-image-reference-lun` を追加して、エクスポートするデータ ディスクの LUN の場所を指定します。 

この例では、イメージ バージョンの LUN 0 にあるデータ ディスクをエクスポートすることで、*myManagedDataDisk* という名前のマネージド ディスクを、*EastUS* リージョンの *myResourceGroup* という名前のリソース グループ内に作成します。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

[Get-AzResource](/powershell/module/az.resources/get-azresource) を使用して、ギャラリーにあるイメージ バージョンを一覧表示します。 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

必要な情報がすべて用意できたら、[Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) を使用して、使用するソース イメージ バージョンを取得し、それを変数に割り当てることができます。 この例では、`myImageDefinition` 定義の `1.0.0` イメージ バージョンを、`myGallery` ソース ギャラリーの `myResourceGroup` リソース グループに取得しています。

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

`source` 変数をイメージ バージョンの ID に設定したら、[New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) を使用してディスク構成を作成し、[New-AzDisk](/powershell/module/az.compute/new-azdisk) を使用してディスクを作成します。 

この例では、イメージ バージョンの OS ディスクをエクスポートすることで、*myManagedOSDisk* という名前のマネージド ディスクを、*EastUS* リージョンの *myResourceGroup* という名前のリソース グループ内に作成します。 

ディスク構成を作成します。
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

ディスクを作成します。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

イメージ バージョンのデータ ディスクをエクスポートする場合は、ディスク構成に LUN ID を追加して、エクスポートするデータ ディスクの LUN の場所を指定します。 

この例では、イメージ バージョンの LUN 0 にあるデータ ディスクをエクスポートすることで、*myManagedDataDisk* という名前のマネージド ディスクを、*EastUS* リージョンの *myResourceGroup* という名前のリソース グループ内に作成します。 

ディスク構成を作成します。
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

ディスクを作成します。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>次の手順

[Azure CLI](image-version-managed-image-cli.md) または [PowerShell](image-version-managed-image-powershell.md) を使用して、マネージド ディスクからイメージ バージョンを作成することもできます。


