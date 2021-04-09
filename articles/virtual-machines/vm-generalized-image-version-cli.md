---
title: Azure CLI を使用して、一般化されたイメージから VM を作成する
description: Azure CLI を使用して、一般化されたイメージ バージョンから VM を作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2bcaf85f61a4d8cf4d23c9c5be7f46d765d77dbb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551044"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>CLI を使用して、一般化されたイメージ バージョンから VM を作成する

Shared Image Gallery に格納されている[一般化されたイメージ バージョン](./shared-image-galleries.md#generalized-and-specialized-images)から VM を作成します。 特殊化されたイメージから VM を作成する場合は、[特殊化されたイメージからの VM の作成](vm-specialized-image-version-powershell.md)に関するページをご覧ください。 


## <a name="get-the-image-id"></a>イメージ ID を取得する

[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) を使用してギャラリー内のイメージ定義を一覧表示すると、定義の名前と ID を確認できます。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>VM の作成

[az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 イメージの最新バージョンを使用するには、`--image` にイメージ定義の ID を指定します。 

必要に応じて、この例のリソース名を置き換えてください。 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

`--image` パラメーターにイメージ バージョン ID を使用して、特定のバージョンを使用することも可能です。 たとえば、イメージ バージョン *1.0.0* を使用するには、次のように入力します: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`。

## <a name="next-steps"></a>次のステップ

[Azure Image Builder (プレビュー)](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./linux/image-builder-gallery-update-image-version.md)に使用することさえできます。