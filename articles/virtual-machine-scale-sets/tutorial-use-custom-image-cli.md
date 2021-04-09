---
title: チュートリアル - Azure CLI を使用してスケール セットでカスタム VM イメージを使用する
description: Azure CLI を使用して仮想マシン スケール セットをデプロイするためのカスタム VM イメージを作成する方法について説明します
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: b12715e299f523d7ace56a72b0098b5d7ffac0ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683057"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>チュートリアル:Azure CLI を使用した仮想マシン スケール セットのカスタム イメージの作成および使用
スケール セットを作成するときは、VM インスタンスのデプロイ時に使用するイメージを指定します。 VM インスタンスをデプロイした後のタスクの数を減らすには、カスタム VM イメージを使用できます。 このカスタム VM イメージには、すべての必要なアプリケーション インストールまたは構成が含まれます。 スケール セットで作成されたすべての VM インスタンスは、カスタム VM イメージを使用し、アプリケーション トラフィックを処理できる状態になります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Shared Image Gallery を作成する
> * 特殊化されたイメージ定義を作成する
> * イメージ バージョンを作成する
> * 特殊化されたイメージからスケール セットを作成する
> * イメージ ギャラリーを共有する


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.4.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="overview"></a>概要

[共有イメージ ギャラリー](../virtual-machines/shared-image-galleries.md)により、組織全体でのカスタム イメージの共有が簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成などの構成のブートストラップを実行するために使用できます。 

共有イメージ ギャラリーを使用すると、カスタム VM イメージを他のユーザーと共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 

## <a name="create-and-configure-a-source-vm"></a>ソース VM の作成と構成

最初に [az group create](/cli/azure/group) を使用してリソース グループを作成し、次に [az vm create](/cli/azure/vm) を使用して VM を作成します。 この VM は、イメージのソースとして使用されます。 次の例では、*myResourceGroup* という名前のリソース グループに *myVM* という名前の VM を作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> VM の **ID** は、[az vm create](/cli/azure/vm) コマンドの出力に示されます。 このチュートリアルの後半で使用できるように、これを安全な場所にコピーします。

VM のパブリック IP アドレスも、[az vm create](/cli/azure/vm) コマンドの出力に示されます。 次に示すように、VM のパブリック IP アドレスに SSH 接続します。

```console
ssh azureuser@<publicIpAddress>
```

VM をカスタマイズするために、基本的な Web サーバーをインストールしてみましょう。 スケール セット内の VM インスタンスがデプロイされると、Web アプリケーションの実行に必要なパッケージがすべて揃うことになります。 次のように `apt-get` を使用して *NGINX* をインストールします。

```bash
sudo apt-get install -y nginx
```

終了したら、「`exit`」と入力して SSH 接続を切断します。

## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 

ギャラリー名で許可されている文字は、英字 (大文字または小文字)、数字、ドット、およびピリオドです。 ギャラリー名にダッシュを含めることはできません。   ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 

[az sig create](/cli/azure/sig#az-sig-create) を使用してイメージ ギャラリーを作成します。 次の例では、*myGalleryRG* という名前のリソース グループを "*米国東部*" に作成し、*myGallery* という名前のギャラリーを作成します。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>イメージ定義を作成する

イメージ定義では、イメージの論理グループを作成します。 これは、その中に作成されるイメージ バージョンに関する情報を管理するために使用されます。 

イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 

イメージ定義の種類が適切であることを確認します。 (Windows の場合は Sysprep、Linux の場合は waagent -deprovision を使用して) VM を一般化している場合は、`--os-state generalized` を使用して、一般化されたイメージ定義を作成する必要があります。 既存のユーザー アカウントを削除せずに VM を使用する場合は、`--os-state specialized` を使用して、特殊化されたイメージ定義を作成します。

イメージ定義に指定できる値の詳細については、[イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)に関するページを参照してください。

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) を使用して、ギャラリー内にイメージ定義を作成します。

この例では、イメージ定義は *myImageDefinition* という名前で、[特殊化された](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) Linux OS イメージ用です。 Windows OS を使用してイメージの定義を作成するには、`--os-type Windows` を使用します。 

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

> [!IMPORTANT]
> イメージ定義の **ID** は、コマンドの出力に示されます。 このチュートリアルの後半で使用できるように、これを安全な場所にコピーします。


## <a name="create-the-image-version"></a>イメージ バージョンの作成

[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用して、VM からイメージ バージョンを作成します。  

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例では、イメージのバージョンは *1.0.0* であり、"*米国中南部*" リージョンに 1 個のレプリカ、および "*米国東部 2*" リージョンに 1 個のレプリカを作成しています。 レプリケーション リージョンには、ソース VM が配置されているリージョンが含まれている必要があります。

この例の `--managed-image` の値を、前の手順の VM の ID で置き換えます。

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> また、イメージ バージョンを作成するときに、`--storage-account-type  premium_lrs` を追加してイメージを Premium ストレージに格納することも、`--storage-account-type  standard_zrs` を追加して[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>




## <a name="create-a-scale-set-from-the-image"></a>イメージからスケール セットを作成する
[`az vmss create`](/cli/azure/vmss#az-vmss-create) を使用して、特殊化されたイメージからスケール セットを作成します。 

イメージが特殊化されたイメージであることを示す --specialized パラメーターを使用した [`az vmss create`](/cli/azure/vmss#az-vmss-create) を使用して、スケール セットを作成します。 

`--image` にイメージ定義 ID を使用して、使用可能なイメージの最新バージョンからスケール セット インスタンスを作成します。 また、`--image` にイメージ バージョン ID を指定して、特定のバージョンからスケール セット インスタンスを作成することもできます。 

前に作成した *myImageDefinition* イメージの最新バージョンから *myScaleSet* という名前のスケール セットを作成します。

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="test-your-scale-set"></a>スケール セットのテスト
トラフィックがスケール セットに到達できるようにし、Web サーバーが正常に動作することを確認するには、[az network lb rule create](/cli/azure/network/lb/rule) を使用してロード バランサー規則を作成します。 次の例では、*TCP* ポート *80* のトラフィックを許可する *myLoadBalancerRuleWeb* という名前の規則を作成します。

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

スケール セットが動作していることを確認するには、[az network public-ip show](/cli/azure/network/public-ip) を使用してロード バランサーのパブリック IP アドレスを取得します。 次の例では、スケール セットの一部として作成された *myScaleSetLBPublicIP* の IP アドレスを取得します。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

このパブリック IP アドレスを Web ブラウザーに入力します。 次の例に示すように、既定の NGINX Web ページが表示されます。

![カスタム VM イメージから実行されている Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>ギャラリーを共有する

Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、サブスクリプション全体でイメージを共有できます。 イメージは、ギャラリー、イメージ定義、またはイメージ バージョンで共有できます。 イメージ バージョンへの読み取りアクセス許可があるユーザーは、サブスクリプション間でも、そのイメージ バージョンを使用して VM をデプロイできます。

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

Azure RBAC を使用してリソースを共有する方法の詳細については、「[Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-cli.md)」をご覧ください。


## <a name="clean-up-resources"></a>リソースをクリーンアップする
スケール セットと追加のリソースを削除するには、[az group delete](/cli/azure/group) を使用して、リソース グループとそのすべてのリソースを削除します。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure CLI を使用してスケール セットにカスタム VM イメージを作成して使用する方法について学習しました。

> [!div class="checklist"]
> * Shared Image Gallery を作成する
> * 特殊化されたイメージ定義を作成する
> * イメージ バージョンを作成する
> * 特殊化されたイメージからスケール セットを作成する
> * イメージ ギャラリーを共有する

次のチュートリアルに進み、アプリケーションをスケール セットにデプロイする方法を学習してください。

> [!div class="nextstepaction"]
> [アプリケーションをスケール セットにデプロイする](tutorial-install-apps-cli.md)