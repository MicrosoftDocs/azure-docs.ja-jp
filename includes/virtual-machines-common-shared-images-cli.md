---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 547e0b08b2a57b4b9192f11907f77160142d4466
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192127"
---
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="before-you-begin"></a>開始する前に

この記事の例を完了するには、汎用 VM の既存のマネージド イメージを保持している必要があります。 詳細については、[Azure CLI 2.0 を使用して Azure VM のカスタム イメージを作成する方法](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)に関するチュートリアルを参照してください。 

## <a name="preview-register-the-feature"></a>更新:機能を登録する

共有イメージ ギャラリーはプレビュー段階ですが、使用するには、事前に機能を登録しておく必要があります。 共有イメージ ギャラリー機能を登録するには、次のコマンドを実行します。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

機能の登録には数分かかる場合があります。 次のコマンドを使用して、進行状況を確認できます。

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 [az sig create](/cli/azure/sig#az-sig-create) を使用してイメージ ギャラリーを作成します。 次の例では、*myGalleryRG* に *myGallery* という名前のギャラリーを作成します。

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>イメージ定義を作成する

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) を使用して、ギャラリー内に最初のイメージ定義を作成します。

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>イメージ バージョンを作成する 
 
[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用して、必要に応じてイメージのバージョンを作成します。 イメージ バージョンを作成するためにベースラインとして使用する、マネージド イメージの ID を渡す必要があります。 [az image list](/cli/azure/image?view#az-image-list) を使用して、リソース グループ内にあるイメージに関する情報を取得できます。 次の例では、イメージのバージョンは *1.0.0* であり*米国中西部*, *米国中南部*、および米国東部 2* リージョンに、合計 5 個のレプリカを作成しています。

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

