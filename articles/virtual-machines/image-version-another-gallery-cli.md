---
title: CLI を使用して別のギャラリーからイメージ バージョンをコピーする
description: Azure CLI を使用して、別のギャラリーからイメージ バージョンをコピーします。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 0bea4fbac062b498dabe04e6e58d530d09b16d6d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553104"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Azure CLI を使用して別のギャラリーからイメージをコピーする

組織内に複数のギャラリーがある場合は、他のギャラリーに格納されている既存のイメージ バージョンからイメージ バージョンを作成することもできます。 たとえば、新しいイメージを作成してテストするための開発およびテスト ギャラリーがあるとします。 運用環境で使用する準備ができたら、この例を使用して、それらのイメージを運用環境ギャラリーにコピーできます。 また、[Azure PowerShell](image-version-another-gallery-powershell.md) を使用して、別のギャラリーのイメージからイメージを作成することもできます。



## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、既存のソース ギャラリー、イメージ定義、およびイメージ バージョンが必要です。 また、コピー先ギャラリーも必要です。 

ソース イメージ バージョンは、コピー先ギャラリーのあるリージョンにレプリケートする必要があります。 

この記事の作業に出現するリソース名は適宜置き換えてください。



## <a name="get-information-from-the-source-gallery"></a>ソース ギャラリーからの情報の取得

新しいギャラリーにコピーを作成できるように、ソース イメージ定義からの情報が必要になります。

[az sig list](/cli/azure/sig#az-sig-list) を使用して利用可能なイメージ ギャラリーに関する情報を一覧表示すると、ソース ギャラリーに関する情報を見つけることができます。

```azurecli-interactive 
az sig list -o table
```

[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) を使用して、ギャラリー内のイメージ定義を一覧表示します。 この例では、*myGalleryRG* リソース グループ内の *myGallery* という名前のギャラリーでイメージ定義を検索しています。

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) を使用してギャラリー内のイメージのバージョンを一覧表示すると、新しいギャラリーにコピーするイメージ バージョンを検索できます。 この例では、*myImageDefinition* イメージ定義の一部であるイメージバージョンをすべて検索しています。

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

必要な情報がすべて揃ったら、[az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show) を使用してソース イメージ バージョンの ID を取得できます。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>イメージ定義の作成 

ソース イメージ バージョンのイメージ定義と一致するイメージ定義を作成する必要があります。 [az sig image definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show) を使用して、新しいギャラリーでイメージ定義を再作成するために必要なすべての情報を確認できます。

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
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
  "osType": "Linux",
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

上の出力の情報を使用して、新しいギャラリーに新しいイメージ定義を作成します。


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>イメージ バージョンの作成

[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用して、バージョンを作成します。 イメージ バージョンを作成するためにベースラインとして使用する、マネージド イメージの ID を渡す必要があります。 [az image list](/cli/azure/image?view#az-image-list) を使用して、リソース グループ内にあるイメージに関する情報を取得できます。 

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例では、イメージのバージョンは *1.0.0* であり、ゾーン冗長ストレージを使用して "*米国中南部*" リージョンに 1 個のレプリカ、および "*米国東部*" リージョンに 1 個のレプリカを作成しています。


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> また、イメージ バージョンを作成するときに、`--storage-account-type  premium_lrs` を追加してイメージを Premium ストレージに格納することも、`--storage-account-type  standard_zrs` を追加して[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>

## <a name="next-steps"></a>次のステップ

[一般化された](vm-generalized-image-version-cli.md)イメージ バージョンまたは[特殊化された](vm-specialized-image-version-cli.md)イメージ バージョンから VM を作成します。

また、イメージ バージョンの作成の自動化に役立つ [Azure Image Builder (プレビュー)](./image-builder-overview.md) をお試しください。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./linux/image-builder-gallery-update-image-version.md)にも使用できます。 

購入プラン情報を提供する方法については、「[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)」を参照してください。