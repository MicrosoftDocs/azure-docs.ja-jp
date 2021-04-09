---
title: コンテナー グループの静的 IP アドレス
description: 仮想ネットワーク内にコンテナー グループを作成し、Azure アプリケーション ゲートウェイを使用して、コンテナー化された Web アプリに静的フロントエンド IP アドレスを公開します
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 0131780fdb04a71837d5ae9bf5498bf2bd499f8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035055"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>コンテナー グループの静的 IP アドレスの公開

この記事では、Azure [アプリケーション ゲートウェイ](../application-gateway/overview.md)を使用して[コンテナー グループ](container-instances-container-groups.md)の静的パブリック IP アドレスを公開するための 1 つの方法を紹介します。 Azure Container Instances で実行される、外部に接続されたコンテナー化アプリの静的エントリ ポイントが必要な場合は、これらの手順に従ってください。 

この記事では、Azure CLI を使用して、このシナリオ用のリソースを作成します。

* Azure 仮想ネットワーク
* 小規模な Web アプリをホストする[仮想ネットワーク](container-instances-vnet.md)にデプロイされたコンテナー グループ
* パブリック フロントエンド IP アドレスを持つアプリケーション ゲートウェイ、ゲートウェイで Web サイトをホストするリスナー、バックエンド コンテナー グループへのルート

アプリケーション ゲートウェイが実行され、コンテナー グループがネットワークの委任されたサブネットに安定したプライベート IP アドレスを公開している限り、このパブリック IP アドレスでコンテナー グループにアクセスできます。

> [!NOTE]
> Azure では、ゲートウェイがプロビジョニングされて利用可能になっている時間と、ゲートウェイで処理されるデータの量に基づいて、アプリケーション ゲートウェイの料金が請求されます。 「[価格](https://azure.microsoft.com/pricing/details/application-gateway/)」を参照してください。

## <a name="create-virtual-network"></a>Create virtual network

一般的なケースであれば、Azure 仮想ネットワークは既に使用されている可能性もあります。 お持ちでない場合は、次のコマンド例で示すような方法で作成してください。 仮想ネットワークでは、アプリケーション ゲートウェイとコンテナー グループにそれぞれ個別のサブネットが必要です。

必要な場合は、Azure リソース グループを作成してください。 次に例を示します。

```azureci
az group create --name myResourceGroup --location eastus
```

[az network vnet create][az-network-vnet-create] コマンドを使って、仮想ネットワークを作成します。 このコマンドを実行すると、ネットワークに *myAGSubnet* サブネットが作成されます。

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

[az network vnet subnet create][az-network-vnet-subnet-create] コマンドを使って、バックエンド コンテナー グループ用のサブネットを作成します。 ここでは、*myACISubnet* という名前になっています。

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

[az network public-ip create][az-network-public-ip-create] コマンドを使用して、静的パブリック IP リソースを作成します。 このアドレスは、後の手順で、アプリケーション ゲートウェイのフロントエンドとして構成されます。

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>コンテナー グループを作成する

次の [az container create][az-container-create] を実行して、前の手順で構成した仮想ネットワークにコンテナー グループを作成します。 

このグループは *myACISubnet* サブネットにデプロイされます。このグループには、`aci-helloworld` イメージをプルする、*appcontainer* という単一のインスタンスが含まれます。 ドキュメントの他の記事で示されているように、このイメージには、静的な HTML ページを返す、Node.js で作成された小さな Web アプリがパッケージされています。 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

正常にデプロイされると、コンテナー グループには仮想ネットワーク内でのプライベート IP アドレスが割り当てられます。 たとえば、グループの IP アドレスを取得するには、次の [az container show][az-container-show] コマンドを実行します。

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

`10.0.2.4` のような出力が返されます。

後の手順で使用するために、IP アドレスを環境変数に保存します。

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> コンテナー グループが停止、開始、または再起動された場合、コンテナー グループのプライベート IP が変更される可能性があります。 これが発生した場合は、アプリケーション ゲートウェイの構成を更新する必要があります。

## <a name="create-application-gateway"></a>アプリケーション ゲートウェイの作成

[アプリケーション ゲートウェイのクイックスタート](../application-gateway/quick-create-cli.md)の手順に従って、仮想ネットワークにアプリケーション ゲートウェイを作成します。 次の [az network application-gateway create][az-network-application-gateway-create] コマンドを実行すると、パブリック フロントエンド IP アドレスを持つアプリケーション ゲートウェイと、バックエンド コンテナー グループへのルートが作成されます。 ゲートウェイの設定の詳細については、[Application Gateway のドキュメント](../application-gateway/index.yml)を参照してください。

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Azure によってアプリケーション ゲートウェイが作成されるのに最大 15 分かかる場合があります。 

## <a name="test-public-ip-address"></a>パブリック IP アドレスのテスト
  
次に、アプリケーション ゲートウェイの背後にあるコンテナー グループで実行されている Web アプリへのアクセスをテストします。

[az network public-ip show][az-network-public-ip-show] コマンドを実行して、ゲートウェイのフロントエンド パブリック IP アドレスを取得します。

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

出力は、次のようなパブリック IP アドレスになります: `52.142.18.133`。

正常に構成された場合に、実行中の Web アプリを表示するには、ブラウザーでゲートウェイのパブリック IP アドレスに移動します。 正常にアクセスできた場合、表示は次のようになります。

![Azure コンテナー インスタンスで実行されているアプリケーションを示すブラウザー スクリーンショット](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>次のステップ

* アプリケーション ゲートウェイの背後にあるバックエンド サーバーとして、WordPress コンテナー インスタンスを含んだコンテナー グループを作成するには、[クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)を参照してください。
* SSL 終了の証明書を使ってアプリケーション ゲートウェイを構成することもできます。 [概要](../application-gateway/ssl-overview.md)と[チュートリアル](../application-gateway/create-ssl-portal.md)を参照してください。
* シナリオによっては、他の Azure 負荷分散ソリューションと Azure Container Instances を使用することを検討してください。 たとえば、[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) を使用して、複数のコンテナー インスタンスと複数のリージョンにトラフィックを分散させることもできます。 この [ブログ投稿](https://aaronmsft.com/posts/azure-container-instances/)を参照してください。

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
