---
title: PowerShell - Shared Image Gallery 内のスナップショットまたは Managed Disk からイメージを作成する
description: PowerShell を使用して、Shared Image Gallery 内のスナップショットまたは Managed Disk からイメージを作成する方法について説明します。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 70ef194ab7f7403f31ad431eba25db5f71dfb580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556905"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>PowerShell を使用して Shared Image Gallery 内の Managed Disk またはスナップショットからイメージを作成する

Shared Image Gallery に移行したい既存のスナップショットまたは Managed Disk がある場合は、Managed Disk またはスナップショットから直接 Shared Image Gallery イメージを作成できます。 新しいイメージをテストしたら、ソースの Managed Disk またはスナップショットを削除できます。 [Azure CLI](image-version-snapshot-cli.md) を使用して、Shared Image Gallery 内の Managed Disk またはスナップショットからイメージを作成することもできます。

イメージ ギャラリー内のイメージには 2 つのコンポーネントがあります。この例ではそれを作成します。
- **イメージ定義** には、イメージに関する情報とそれを使用するための要件が含まれます。 これには、イメージの OS (Windows または Linux)、形態 (特殊化または一般化)、リリース ノート、最小メモリ要件、最大メモリ要件が含まれます。 これは、イメージの種類の定義です。 
- **イメージ バージョン** は、Shared Image Gallery の使用時に VM の作成に使用されるものです。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 VM を作成するとき、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。


## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、スナップショットまたは Managed Disk が必要です。 

データ ディスクを含める場合、データ ディスクのサイズが 1 TB を超えてはなりません。

この記事の手順に出現するリソース名は適宜置き換えてください。


## <a name="get-the-snapshot-or-managed-disk"></a>スナップショットまたは Managed Disk を取得する

リソース グループで利用できるスナップショットは、[Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) を使用して一覧表示できます。 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

スナップショットの名前とそれが属するリソース グループがわかったら、もう一度 `Get-AzSnapshot` を使用してスナップショット オブジェクトを取得し、後で使用できるよう変数に格納することができます。 この例では、*mySnapshot* という名前のスナップショットを "myResourceGroup" リソース グループから取得し、変数 *$source* に割り当てています。 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

スナップショットの代わりに Managed Disk を使用することもできます。 Managed Disk を取得するには、[Get-AzDisk](/powershell/module/az.compute/get-azdisk) を使用します。 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

次に、Managed Disk を取得して、`$source` 変数に割り当てます。

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

同じコマンドレットを使用して、イメージに含めるデータ ディスクを取得できます。 それらを変数に割り当てて、後でイメージ バージョンを作成するときにこれらの変数を使用します。


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

イメージ定義を作成する際は、正しい情報がすべて含まれていることを確認してください。 この例では、スナップショットまたは Managed Disk が使用中の VM からのものであり、一般化されていないことを前提としています。 (Windows では Sysprep、Linux では [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` または `-deprovision+user` を実行した後の) 一般化された OS から Managed Disk またはスナップショットを取得した場合は、`-OsState` を `generalized` に変更します。 

イメージ定義に指定できる値の詳細については、[イメージ定義](./shared-image-galleries.md#image-definitions)に関するページを参照してください。

イメージの定義は、[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) を使用して作成します。 この例では、イメージ定義は *myImageDefinition* という名前で、特殊化された Windows OS 用です。 Linux OS を使用してイメージの定義を作成するには、`-OsType Linux` を使用します。 

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

### <a name="purchase-plan-information"></a>購入プラン情報

場合によっては、Azure Marketplace イメージに基づいたイメージから VM を作成するときに、購入プラン情報を渡す必要があります。 このような場合は、購入プラン情報をイメージ定義に含めることをお勧めします。 この場合は、[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)に関する記事を参照してください。


## <a name="create-an-image-version"></a>イメージ バージョンを作成する

イメージ バージョンは、[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) を使用してスナップショットから作成します。 

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

OS ディスクに加えてデータ ディスクをイメージに含める場合は、`-DataDiskImage` パラメーターを追加して、データ ディスクのスナップショットまたは Managed Disk の ID をこのパラメーターに設定します。

この例のイメージ バージョンは *1.0.0* で、"*米国中西部*" と "*米国中南部*" の両方のデータセンターにレプリケートされます。 レプリケーションのターゲット リージョンを選択するときに、レプリケーションのターゲットとして、"*ソース*" リージョンも含める必要があることに注意してください。


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

すべてのターゲット リージョンにイメージがレプリケートされるまでにしばらく時間がかかることがあるため、進行状況を追跡できるようジョブを作成しました。 ジョブの進行状況を確認するには、「`$job.State`」と入力します。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 同じスナップショットを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。 
>
> イメージ バージョンを作成するときに、`-StorageAccountType Standard_ZRS` を追加することによって、イメージ バージョンを[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>

## <a name="delete-the-source"></a>ソースを削除する

新しいイメージ バージョンが正常に動作していることを確認したら、[Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) または [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk) のどちらかを使用してイメージのソースを削除できます。


## <a name="next-steps"></a>次のステップ

レプリケーションが完了したことを確認できたら、[特殊化されたイメージ](vm-specialized-image-version-powershell.md)から VM を作成できます。

購入プラン情報を提供する方法については、[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)に関する記事を参照してください。