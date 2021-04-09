---
title: CLI - Shared Image Gallery 内のスナップショットまたはマネージド ディスクからイメージを作成する
description: Azure CLI を使用して、Shared Image Gallery 内のスナップショットまたはマネージド ディスクからイメージを作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c809edd3699d0b9827fe15da53d5d18b12cbe6e6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556963"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Azure CLI を使用して、Shared Image Gallery 内のマネージド ディスクまたはスナップショットからイメージを作成する

Shared Image Gallery に移行したい既存のスナップショットまたはマネージド ディスクがある場合は、マネージド ディスクまたはスナップショットから直接 Shared Image Gallery イメージを作成できます。 新しいイメージをテストしたら、ソースのマネージド ディスクまたはスナップショットを削除できます。 [Azure PowerShell](image-version-snapshot-powershell.md) を使用して、Shared Image Gallery 内のマネージド ディスクまたはスナップショットからイメージを作成することもできます。

イメージ ギャラリー内のイメージには 2 つのコンポーネントがあります。この例ではそれを作成します。
- **イメージ定義** には、イメージに関する情報とそれを使用するための要件が含まれます。 これには、イメージの OS (Windows または Linux)、形態 (特殊化または一般化)、リリース ノート、最小メモリ要件、最大メモリ要件が含まれます。 これは、イメージの種類の定義です。 
- **イメージ バージョン** は、Shared Image Gallery の使用時に VM の作成に使用されるものです。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 VM を作成するとき、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。


## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、スナップショットまたは Managed Disk が必要です。 

データ ディスクを含める場合、データ ディスクのサイズが 1 TB を超えてはなりません。

この記事の手順に出現するリソース名は適宜置き換えてください。

## <a name="find-the-snapshot-or-managed-disk"></a>スナップショットまたはマネージド ディスクを見つける 

リソース グループで利用できるスナップショットは、[az snapshot list](/cli/azure/snapshot#az-snapshot-list) を使用して一覧表示できます。 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

スナップショットの代わりにマネージド ディスクを使用することもできます。 マネージド ディスクを取得するには、[az disk list](/cli/azure/disk#az-disk-list) を使用します。 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

スナップショットまたはマネージド ディスクの ID を取得したら、後で使用するために `$source` という名前の変数に割り当てます。

同じプロセスを使用して、イメージに含めるデータ ディスクを取得できます。 それらを変数に割り当てて、後でイメージ バージョンを作成するときにこれらの変数を使用します。


## <a name="find-the-gallery"></a>ギャラリーを見つける

イメージ定義を作成する目的で、イメージ ギャラリーに関する情報が必要です。

[az sig list](/cli/azure/sig#az-sig-list) を使用して、利用可能なイメージ ギャラリーに関する情報を一覧表示します。 ギャラリーが属するリソース グループで後に使用されるギャラリー名をメモします。

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>イメージ定義を作成する

イメージ定義では、イメージの論理グループを作成します。 イメージに関する情報を管理する目的で使用されます。 イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 

イメージ定義を作成する際は、正しい情報がすべて含まれていることを確認してください。 この例では、スナップショットまたは Managed Disk が使用中の VM からのものであり、一般化されていないことを前提としています。 (Windows では Sysprep、Linux では [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` または `-deprovision+user` を実行した後の) 一般化された OS から Managed Disk またはスナップショットを取得した場合は、`-OsState` を `generalized` に変更します。 

イメージ定義に指定できる値の詳細については、[イメージ定義](./shared-image-galleries.md#image-definitions)に関するページを参照してください。

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) を使用して、ギャラリー内にイメージ定義を作成します。

この例では、イメージ定義は *myImageDefinition* という名前で、[特殊化された](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS イメージ用です。 Windows OS を使用してイメージの定義を作成するには、`--os-type Windows` を使用します。 

この例では、ギャラリーに *myGallery* という名前が付いており、*myGalleryRG* リソース グループに入っており、イメージ定義名は *mImageDefinition* です。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>イメージ バージョンの作成

[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用してイメージ バージョンを作成します。 

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例では、イメージのバージョンは *1.0.0* であり、ゾーン冗長ストレージを使用して "*米国中南部*" リージョンに 1 個のレプリカ、および "*米国東部 2*" リージョンに 1 個のレプリカを作成しています。 レプリケーションのターゲット リージョンを選択するときに、レプリケーションのターゲットとして、マネージド ディスクまたはスナップショットの "*ソース*" リージョンも含める必要があることに注意してください。

スナップショットまたはマネージド ディスクの ID は `--os-snapshot` パラメーターで渡します。


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

イメージにデータ ディスクを含める場合、LUN 番号に設定された `--data-snapshot-luns` パラメーターと、データ ディスクまたはスナップショットの ID に設定された `--data-snapshots` の両方を含める必要があります。

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> イメージ バージョンを作成するときに、`--storage-account-type standard_zrs` を追加することによって、すべてのイメージ バージョンのレプリカを[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>

## <a name="next-steps"></a>次のステップ

[特殊化されたイメージ バージョン](vm-specialized-image-version-cli.md)から VM を作成します。

購入プラン情報を提供する方法については、「[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)」を参照してください。