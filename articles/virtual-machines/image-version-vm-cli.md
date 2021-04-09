---
title: Azure CLI を使用して VM からイメージを作成する
description: Azure の VM から Shared Image Gallery にイメージを作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c35be8821b6763531b43ec85b10325e91f8bc5f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556862"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Azure CLI を使用して Azure の VM からイメージ バージョンを作成する

既存の VM を使用して同じ VM を複数作成する場合は、Azure CLI を利用して、その VM から Shared Image Gallery にイメージを作成できます。 [Azure PowerShell](image-version-vm-powershell.md) を利用して VM からイメージを作成することもできます。

**イメージ バージョン** は、Shared Image Gallery の使用時に VM の作成に使用されるものです。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 イメージ バージョンを使用して VM を作成する場合、イメージ バージョンは新しい VM 用のディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。


## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、既存の Shared Image Gallery が必要です。 

また、ギャラリーがあるのと同じリージョンに Azure の既存の VM も必要です。 

VM にデータ ディスクがアタッチされている場合、データ ディスクのサイズが 1 TB を超えることはできません。

この記事の手順に出現するリソース名は適宜置き換えてください。

## <a name="get-information-about-the-vm"></a>VM に関する情報を取得する

利用できる VM は、[az vm list](/cli/azure/vm#az-vm-list) を使用して一覧表示できます。 

```azurecli-interactive
az vm list --output table
```

VM の名前とそれが属しているリソース グループがわかったら、[az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) を使用して VM の ID を取得します。 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>イメージ定義を作成する

イメージ定義では、イメージの論理グループを作成します。 これは、その中に作成されるイメージ バージョンに関する情報を管理するために使用されます。 

イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 

イメージ定義の種類が適切であることを確認します。 (Windows の場合は Sysprep、Linux の場合は waagent -deprovision を使用して) VM を一般化している場合は、`--os-state generalized` を使用して、一般化されたイメージ定義を作成する必要があります。 既存のユーザー アカウントを削除せずに VM を使用する場合は、`--os-state specialized` を使用して、特殊化されたイメージ定義を作成します。

イメージ定義に指定できる値の詳細については、[イメージ定義](./shared-image-galleries.md#image-definitions)に関するページを参照してください。

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) を使用して、ギャラリー内にイメージ定義を作成します。

この例では、イメージ定義は *myImageDefinition* という名前で、[特殊化された](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS イメージ用です。 Windows OS を使用してイメージの定義を作成するには、`--os-type Windows` を使用します。 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>イメージ バージョンの作成

[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用して、VM からイメージ バージョンを作成します。  

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例では、イメージのバージョンは *1.0.0* であり、ゾーン冗長ストレージを使用して "*米国中西部*" リージョンに 2 個のレプリカ、"*米国中南部*" リージョンに 1 個のレプリカ、および "*米国東部 2*" リージョンに 1 個のレプリカを作成しています。 レプリケーション リージョンには、ソース VM が配置されているリージョンが含まれている必要があります。

この例の `--managed-image` の値を、前の手順の VM の ID で置き換えます。

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> また、イメージ バージョンを作成するときに、`--storage-account-type  premium_lrs` を追加してイメージを Premium ストレージに格納することも、`--storage-account-type  standard_zrs` を追加して[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>

## <a name="next-steps"></a>次のステップ

Azure CLI を使用して、[一般化されたイメージ](vm-generalized-image-version-cli.md)から VM を作成します。

購入プラン情報を提供する方法については、「[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)」を参照してください。