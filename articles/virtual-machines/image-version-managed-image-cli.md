---
title: Azure CLI を使用して、マネージド イメージをイメージ バージョンに複製する
description: Azure CLI を使用して、マネージド イメージを Shared Image Gallery のイメージ バージョンに複製する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: cae82072785838d410453b2eb83685905b0ba04e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553784"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Azure CLI を使用してマネージド イメージをイメージ バージョンに複製する
Shared Image Gallery に複製する既存のマネージド イメージがある場合は、マネージド イメージから直接 Shared Image Gallery イメージを作成できます。 新しいイメージをテストしたら、ソースのマネージド イメージを削除できます。 [PowerShell](image-version-managed-image-powershell.md) を使用して、マネージド イメージから Shared Image Gallery に移行することもできます。

イメージ ギャラリー内のイメージには 2 つのコンポーネントがあります。この例ではそれを作成します。
- **イメージ定義** には、イメージに関する情報とそれを使用するための要件が含まれます。 これには、イメージの OS (Windows または Linux)、形態 (特殊化または一般化)、リリース ノート、最小メモリ要件、最大メモリ要件が含まれます。 これは、イメージの種類の定義です。 
- **イメージ バージョン** は、Shared Image Gallery の使用時に VM の作成に使用されるものです。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 VM を作成するとき、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。


## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、既存の [Shared Image Gallery](shared-images-cli.md) が必要です。 

この記事の例を完了するには、汎用 VM の既存のマネージド イメージを保持している必要があります。 詳細については、[マネージド イメージのキャプチャ](./linux/capture-image.md)に関するページをご覧ください。 マネージド イメージにデータ ディスクが含まれている場合、データ ディスクのサイズが 1 TB を超えることはできません。

この記事の作業に出現するリソース グループと VM の名前は適宜置き換えてください。



## <a name="create-an-image-definition"></a>イメージ定義を作成する

マネージド イメージは常に一般化されたイメージであるため、一般化されたイメージ用の `--os-state generalized` を使用してイメージ定義を作成します。

イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 

イメージ定義に指定できる値の詳細については、[イメージ定義](./shared-image-galleries.md#image-definitions)に関するページを参照してください。

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) を使用して、ギャラリー内にイメージ定義を作成します。

この例では、イメージ定義は *myImageDefinition* という名前で、[一般化された](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS イメージ用です。 Windows OS を使用してイメージの定義を作成するには、`--os-type Windows` を使用します。 

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
   --os-state generalized
```


## <a name="create-the-image-version"></a>イメージ バージョンの作成

[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用して、バージョンを作成します。 イメージ バージョンを作成するためにベースラインとして使用する、マネージド イメージの ID を渡す必要があります。 イメージの ID を取得するには、[az image list](/cli/azure/image?view#az-image-list) を使用します。 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例では、イメージのバージョンは *1.0.0* であり、ゾーン冗長ストレージを使用して "*米国中南部*" リージョンに 1 個のレプリカ、および "*米国東部 2*" リージョンに 1 個のレプリカを作成しています。 レプリケーションのターゲット リージョンを選択するときに、レプリケーションのターゲットとして、"*ソース*" リージョンも含める必要があることに注意してください。

マネージド イメージの ID は `--managed-image` パラメーターで渡します。


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> イメージ バージョンを作成するときに、`--storage-account-type standard_zrs` を追加することによって、すべてのイメージ バージョンのレプリカを[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>

## <a name="next-steps"></a>次のステップ

[一般化されたイメージ バージョン](vm-generalized-image-version-cli.md)から VM を作成する。

購入プラン情報を提供する方法については、[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)に関する記事を参照してください。