---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 4392e7f146f13e581f722b94f13038ad8abff0ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244955"
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

[az sig update](/cli/azure/sig#az-sig-update) を使用してギャラリーの説明を更新します。 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


[az sig image-definition update](/cli/azure/sig/image-definition#az-sig-image-definition-update) を使用してイメージ定義の説明を更新します。

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

[az sig image-version update](/cli/azure/sig/image-definition#az-sig-image-definition-update) を使用して、複製先のリージョンを追加するイメージ バージョンを更新します。 イメージが新しいリージョンに複製されるため、この変更には時間がかかります。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

この例では、[az sig image-version update](/cli/azure/sig/image-definition#az-sig-image-definition-update) を使用して、このイメージ バージョンが "*最新*" のイメージとして使用されないようにする方法を示します。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

この例では、[az sig image-version update](/cli/azure/sig/image-definition#az-sig-image-definition-update) を使用して、このイメージ バージョンを "*最新*" のイメージとして見なして含める方法を示します。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>リソースを削除する

最初にイメージのバージョンを削除することによって、逆の順序でリソースを削除する必要があります。 すべてのイメージのバージョンを削除した後、イメージの定義を削除できます。 すべてのイメージの定義を削除した後、ギャラリーを削除できます。 

[az sig image-version delete](/cli/azure/sig/image-version#az-sig-image-version-delete) を使用してイメージのバージョンを削除します。

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

[az sig image-definition delete](/cli/azure/sig/image-definition#az-sig-image-definition-delete) を使用してイメージの定義を削除します。

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


[az sig delete](/cli/azure/sig#az-sig-delete) を使用してイメージのギャラリーを削除します。

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```