---
title: クイックスタート - Azure CLI を使用して Azure Private Link サービスを作成する
description: このクイックスタートでは、Azure CLI を使用して Azure Private Link サービスを作成する方法について説明します
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 76fd959c28203132be4695031d96315f258cf53f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563083"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>クイックスタート: Azure CLI を使用して Private Link サービスを作成する

自分のサービスを参照する Private Link サービスを作成してみましょう。  Private Link には、Azure Standard Load Balancer の背後にデプロイされたサービスやリソースへのアクセス権を付与することができます。  サービスのユーザーは、各自の仮想ネットワークからプライベートにアクセスすることができます

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- このクイックスタートには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、次のようにリソース グループを作成します。

* 名前は **CreatePrivLinkService-rg** にします。 
* 場所は **eastus** にします。

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>内部ロード バランサーを作成します。

このセクションでは、仮想ネットワークと内部 Azure Load Balancer を作成します。

### <a name="virtual-network"></a>仮想ネットワーク

このセクションでは、Private Link サービスにアクセスするロード バランサーのホストとなる仮想ネットワークとサブネットを作成します。

[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* アドレス プレフィックスは **10.1.0.0/16** にします。
* サブネットの名前は **mySubnet** にします。
* サブネット プレフィックスは **10.1.0.0/24** にします。
* **CreatePrivLinkService-rg** リソース グループ内に作成します。
* 場所は **eastus2** にします。
* サブネット上の Private Link サービスのネットワーク ポリシーを無効にします。

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Private Link サービスのネットワーク ポリシーを無効にするようにサブネットを更新するには、[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使用します。

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Standard ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-the-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[az network lb create](/cli/azure/network/lb#az-network-lb-create) を使用して、次のようにパブリック ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします。
* フロントエンド プールの名前は **myFrontEnd** にします。
* バックエンド プールの名前は **myBackEndPool** にします。
* 仮想ネットワーク **myVNet** に関連付けます。
* バックエンド サブネット **mySubnet** に関連付けます。

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) を使用して、次のようにロード バランサー規則を作成します。

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で **ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。
* アイドル タイムアウトは **15 分** とします。
* TCP リセットを有効にします。

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>プライベート リンク サービスを作成する

このセクションでは、前の手順で作成された Azure Load Balancer を使用する Private Link サービスを作成します。

[az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create) を使用し、Standard Load Balancer フロントエンド IP 構成を使用して Private Link サービスを作成します。

* 名前は **myPrivateLinkService** にします。
* 仮想ネットワークは **myVNet** にします。
* Standard Load Balancer **myLoadBalancer** とフロントエンド構成 **myFrontEnd** に関連付けます。
* 場所は **eastus2** にします。
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Private Link サービスが作成され、トラフィックを受信できるようになります。 トラフィック フローを確認する場合は、ご利用のアプリケーションを Standard ロード バランサーの背後に構成します。


## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

このセクションでは、プライベート リンク サービスをプライベート エンドポイントにマップします。 仮想ネットワークには、プライベート リンク サービス用のプライベート エンドポイントが含まれています。 この仮想ネットワークには、ご利用のプライベート リンク サービスにアクセスするリソースが含まれています。

### <a name="create-private-endpoint-virtual-network"></a>プライベート エンドポイントの仮想ネットワークを作成する

[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNetPE** にします。
* アドレス プレフィックスは **11.1.0.0/16** にします。
* サブネットの名前は **mySubnetPE** にします。
* サブネット プレフィックスは **11.1.0.0/24** にします。
* **CreatePrivLinkService-rg** リソース グループ内に作成します。
* 場所は **eastus2** にします。

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

プライベート エンドポイントのネットワーク ポリシーを無効にするようにサブネットを更新するには、[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使用します。

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>エンドポイントと接続を作成する

* [az network private-link-service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) を使用して Private Link サービスのリソース ID を取得します。 このコマンドでは、後で使用するためにリソース ID を変数に格納します。

* [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) を使用して、前に作成した仮想ネットワーク内にプライベート エンドポイントを作成します。

* 名前は **MyPrivateEndpoint** にします。
* **CreatePrivLinkService-rg** リソース グループ内に作成します。
* 接続名は **myPEconnectiontoPLS** にします。
* 場所は **eastus2** にします。
* 仮想ネットワーク **myVNetPE** とサブネット **mySubnetPE** 内に作成します。

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、Private Link サービス、ロード バランサー、すべての関連リソースを削除します。

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは次の作業を行います。

* 仮想ネットワークと内部 Azure Load Balancer を作成しました。
* Private Link サービスを作成しました。

Azure プライベート エンドポイントについてさらに学習するには、次の記事に進んでください。
> [!div class="nextstepaction"]
> [クイックスタート: Azure CLI を使用してプライベート エンドポイントを作成する](create-private-endpoint-cli.md)
