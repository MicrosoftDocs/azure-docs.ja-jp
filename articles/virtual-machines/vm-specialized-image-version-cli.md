---
title: Azure CLI を使用して、特殊化されたイメージ バージョンから VM を作成する
description: Azure CLI を使用して、Shared Image Gallery 内の特殊化されたイメージ バージョンから VM を作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe081b0e74acf771e10406c15a3dea4e09956c37
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560967"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Azure CLI を使用して、特殊化されたイメージ バージョンから VM を作成する

Shared Image Gallery に格納されている[特殊化されたイメージ バージョン](./shared-image-galleries.md#generalized-and-specialized-images)から VM を作成します。 一般化されたイメージ バージョンを使用して VM を作成する場合は、[一般化されたイメージ バージョンからの VM の作成](vm-generalized-image-version-cli.md)に関する記事をご覧ください。

必要に応じて、この例のリソース名を置き換えてください。 

[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) を使用してギャラリー内のイメージ定義を一覧表示すると、定義の名前と ID を確認できます。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

イメージが特殊化されたイメージであることを示す --specialized パラメーターを指定した [az vm create](/cli/azure/vm#az-vm-create) を使用して、VM を作成します。 

`--image` にイメージ定義 ID を指定して、使用可能なイメージの最新バージョンから VM を作成します。 また、`--image` にイメージ バージョン ID を指定して、特定のバージョンから VM を作成することもできます。 

この例では、*myImageDefinition* イメージの最新バージョンから VM を作成しています。

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>次のステップ
[Azure Image Builder (プレビュー)](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./linux/image-builder-gallery-update-image-version.md)に使用することさえできます。 

共有イメージ ギャラリー リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [共有イメージ ギャラリーを作成する](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [共有イメージ ギャラリーにイメージ定義を作成する](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [共有イメージ ギャラリーにイメージのバージョンを作成する](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [イメージ バージョンから VM を作成する](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)