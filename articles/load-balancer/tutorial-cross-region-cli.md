---
title: 'チュートリアル: Azure CLI を使用してリージョン間ロード バランサーを作成する'
titleSuffix: Azure Load Balancer
description: このチュートリアルでは、Azure CLI を使用してリージョン間 Azure ロード バランサーのデプロイを開始します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: 83efb428a94d49b77ecd923d4868afe034374b5f
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225185"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>チュートリアル: Azure CLI を使用してリージョン間 Azure ロード バランサーを作成する

リージョン間ロード バランサーを使うと、サービスが複数の Azure リージョンにわたってグローバルに利用可能になります。 1 つのリージョンで障害が発生した場合、トラフィックは次の最も近い正常なリージョンのロード バランサーにルーティングされます。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リージョン間ロード バランサーを作成します。
> * ロード バランサー規則を作成します。
> * 2 つのリージョン ロード バランサーを含むバックエンド プールを作成します。
> * ロード バランサーをテストします。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。
- 2 つの異なる Azure リージョンにデプロイされたバックエンド プールを備えた 2 つの **Standard** SKU Azure ロード バランサー。
    - バックエンド プール用にリージョンの Standard ロード バランサーと仮想マシンを作成する方法については、「[クイックスタート: Azure CLI を使用して VM の負荷を分散するパブリック ロード バランサーを作成する](quickstart-load-balancer-standard-public-cli.md)」を参照してください。
        - 各リージョンのロード バランサーと仮想マシンの名前の末尾に、 **-R1** と **-R2** を追加します。 
- ローカルにインストールされた Azure CLI または Azure Cloud Shell。

CLI をローカルにインストールして使用する場合、このクイックスタートでは Azure CLI バージョン 2.0.28 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sign-in-to-azure-cli"></a>Azure CLI へのサインイン

Azure CLI にサインインします。

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>リージョン間ロード バランサーを作成する

このセクションでは、リージョン間ロード バランサー、パブリック IP アドレス、負荷分散ルールを作成します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az-group-create) を使用して、次のようにリソース グループを作成します。

* 名前は **myResourceGroupLB-CR** にします。
* 場所は **westus** にします。

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[az network cross-region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create) を使用して、リージョン間ロード バランサーを作成します。

* 名前は **myLoadBalancer-CR** にします。
* フロントエンド プールの名前は **myFrontEnd-CR** にします。
* バックエンド プールの名前は **myBackEndPool-CR** にします。

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[az network lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create) を使用して、リージョン間ロード バランサー ルールを作成します。

* 名前は **myHTTPRule-CR** にします。
* フロントエンド プール **myFrontEnd-CR** で **ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool-CR** に送信します。 
* プロトコルは **TCP** にします。

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>バックエンド プールの作成

このセクションでは、リージョン間ロード バランサーのバックエンド プールに 2 つのリージョン Standard ロード バランサーを追加します。

> [!IMPORTANT]
> これらの手順を完了するには、2 つのリージョン ロード バランサーとバックエンド プールを自分のサブスクリプションにデプロイしておく必要があります。  詳細については、「 **[クイックスタート: Azure CLI を使用して VM の負荷を分散するパブリック ロード バランサーを作成する](quickstart-load-balancer-standard-public-cli.md)** 」を参照してください。

### <a name="add-the-regional-frontends-to-load-balancer"></a>ロード バランサーにリージョン フロントエンドを追加する

このセクションでは、2 つのリージョン間ロード バランサー フロントエンドのリソース ID を変数に代入します。  その後で、その変数を使ってリージョン間ロード バランサーのバックエンド アドレス プールにフロントエンドを追加します。

[az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show) を使用して、リソース ID を取得します。

[az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) を使用して、変数に代入したフロントエンドをリージョン間ロード バランサーのバックエンド プールに追加します。

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

このセクションでは、リージョン間ロード バランサーをテストします。 Web ブラウザーで、パブリック IP アドレスに接続します。  いずれかのリージョン ロード バランサーのバックエンド プールにある仮想マシンを停止し、フェールオーバーを観察します。

1. ロード バランサーのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

3. いずれかのリージョン ロード バランサーのバックエンド プールにある仮想マシンを停止します。

4. Web ブラウザーを最新の状態に更新し、別のリージョン ロード バランサーへの接続のフェールオーバーを観察します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、ロード バランサー、およびすべての関連リソースを削除します。

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行いました。

* リージョン間ロード バランサーを作成しました。
* 負荷分散規則を作成しました。
* リージョン間ロード バランサーのバックエンド プールにリージョン ロード バランサーを追加しました。
* ロード バランサーをテストしました。

次の記事に進み、以下の方法を学習してください。
> [!div class="nextstepaction"]
> [可用性ゾーン間での VM の負荷分散](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
