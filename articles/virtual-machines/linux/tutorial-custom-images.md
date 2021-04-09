---
title: チュートリアル - Azure CLI を使用したカスタム VM イメージの作成
description: このチュートリアルでは、Azure CLI を使用して、Azure でカスタム仮想マシン イメージを作成する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 40ec86030e136a06fe240e473a469681dcfa7c4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557763"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>チュートリアル:Azure CLI を使用して Azure VM のカスタム イメージを作成する

カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成などの構成のブートストラップを実行するために使用できます。 このチュートリアルでは、Azure 仮想マシンの独自のカスタム イメージを作成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Shared Image Gallery を作成する
> * イメージ定義を作成する
> * イメージ バージョンを作成する
> * イメージから VM を作成する 
> * イメージ ギャラリーを共有する


このチュートリアルでは、[Azure Cloud Shell](../../cloud-shell/overview.md) で CLI を使用します。このバージョンは常に更新され最新になっています。 Cloud Shell を開くには、コード ブロックの上部にある **[使ってみる]** を選択します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.4.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="overview"></a>概要

[共有イメージ ギャラリー](../shared-image-galleries.md)により、組織全体でのカスタム イメージの共有が簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成などの構成のブートストラップを実行するために使用できます。 

共有イメージ ギャラリーを使用すると、カスタム VM イメージを他のユーザーと共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 

共有イメージ ギャラリー機能には、次のような複数のリソースの種類があります。

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>開始する前に

以下の手順では、既存の VM を取得し、新しい VM インスタンスの作成に使用できる再利用可能なカスタム イメージに変換する方法について詳しく説明します。

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、[CLI クイックスタート](quick-create-cli.md)を参照して、このチュートリアルで使用する VM を作成できます。 このチュートリアルを実行するときは、リソース名を適宜置き換えてください。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 

ギャラリー名で許可されている文字は、英字 (大文字または小文字)、数字、ドット、およびピリオドです。 ギャラリー名にダッシュを含めることはできません。   ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 

[az sig create](/cli/azure/sig#az-sig-create) を使用してイメージ ギャラリーを作成します。 次の例では、*myGalleryRG* という名前のリソース グループを "*米国東部*" に作成し、*myGallery* という名前のギャラリーを作成します。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>VM に関する情報を取得する

利用できる VM は、[az vm list](/cli/azure/vm#az-vm-list) を使用して一覧表示できます。 

```azurecli-interactive
az vm list --output table
```

VM の名前とそれが属しているリソース グループがわかったら、[az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) を使用して VM の ID を取得します。 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

後で使用するために VM の ID をコピーします。

## <a name="create-an-image-definition"></a>イメージ定義を作成する

イメージ定義では、イメージの論理グループを作成します。 これは、その中に作成されるイメージ バージョンに関する情報を管理するために使用されます。 

イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 

イメージ定義に指定できる値の詳細については、[イメージ定義](../shared-image-galleries.md#image-definitions)に関するページを参照してください。

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) を使用して、ギャラリー内にイメージ定義を作成します。 

この例では、イメージ定義は *myImageDefinition* という名前で、[特殊化された](../shared-image-galleries.md#generalized-and-specialized-images) Linux OS イメージ用です。 

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

後で使用するために、出力からイメージ定義の ID をコピーします。

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
> また、イメージ バージョンを作成するときに、`--storage-account-type  premium_lrs` を追加してイメージを Premium ストレージに格納することも、`--storage-account-type  standard_zrs` を追加して[ゾーン冗長ストレージ](../../storage/common/storage-redundancy.md)に格納することもできます。
>

 
## <a name="create-the-vm"></a>VM の作成

イメージが特殊化されたイメージであることを示す --specialized パラメーターを使用した [az vm create](/cli/azure/vm#az-vm-create) を使用して、VM を作成します。 

`--image` にイメージ定義 ID を指定して、使用可能なイメージの最新バージョンから VM を作成します。 また、`--image` にイメージ バージョン ID を指定して、特定のバージョンから VM を作成することもできます。 

この例では、*myImageDefinition* イメージの最新バージョンから VM を作成しています。

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>ギャラリーを共有する

Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、サブスクリプション全体でイメージを共有できます。 イメージは、ギャラリー、イメージ定義、またはイメージ バージョンのレベルで共有できます。 イメージ バージョンへの読み取りアクセス許可があるユーザーは、サブスクリプション間でも、そのイメージ バージョンを使用して VM をデプロイできます。

他のユーザーとは、ギャラリー レベルで共有することをお勧めします。 ギャラリーのオブジェクト ID を取得するには、[az sig show](/cli/azure/sig#az-sig-show) を使用します。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

電子メール アドレスおよび [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) と共に、オブジェクト ID をスコープとして使用して、ユーザーに共有イメージ ギャラリーへのアクセスを付与します。 `<email-address>` と `<gallery iD>` は、実際の情報に置き換えてください。

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Azure RBAC を使用してリソースを共有する方法の詳細については、「[Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-cli.md)」をご覧ください。

## <a name="azure-image-builder"></a>Azure Image Builder

Azure では、Packer 上に構築された [Azure VM Image Builder](../image-builder-overview.md) サービスも提供しています。 テンプレートにカスタマイズを記述するだけで、イメージの作成が処理されます。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、カスタム VM イメージを作成しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Shared Image Gallery を作成する
> * イメージ定義を作成する
> * イメージ バージョンを作成する
> * イメージから VM を作成する 
> * イメージ ギャラリーを共有する

次のチュートリアルに進み、可用性が高い仮想マシンについて学習してください。

> [!div class="nextstepaction"]
> [高可用性 VM の作成](tutorial-availability-sets.md)