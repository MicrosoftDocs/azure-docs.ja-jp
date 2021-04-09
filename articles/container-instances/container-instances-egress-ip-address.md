---
title: 静的送信 IP を構成する
description: イングレスとエグレスにファイアウォールのパブリック IP アドレスを使用する Azure Container Instances ワークロード用の Azure ファイアウォールとユーザー定義ルートを構成する
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89566570"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>コンテナー グループへの送信と受信トラフィック用の 1 つのパブリック IP アドレスを構成する

外部接続用の IP アドレスを使って[コンテナー グループ](container-instances-container-groups.md)を設定することにより、外部クライアントはその IP アドレスを使用してグループ内のコンテナーにアクセスすることができます。 たとえば、ブラウザーは、コンテナーで実行されている Web アプリにアクセスできます。 ただし、現在、コンテナー グループでは送信トラフィックに対して異なる IP アドレスが使用されています。 このエグレス IP アドレスはプログラムによって公開されないため、コンテナー グループの監視とクライアント ファイアウォール規則の構成がより複雑になります。

この記事では、[Azure Firewall](../firewall/overview.md) と統合された[仮想ネットワーク](container-instances-virtual-network-concepts.md)でコンテナー グループを構成する手順について説明します。 コンテナー グループへのユーザー定義ルートとファイアウォール規則を設定することにより、コンテナー グループとの間のトラフィックをルーティングおよび識別できます。 コンテナー グループのイングレスとエグレスでは、ファイアウォールのパブリック IP アドレスが使用されます。 Azure Container Instances に委任された仮想ネットワークのサブネットにデプロイされた複数のコンテナー グループで 1 つのエグレス IP アドレスを使用することができます。

この記事では、Azure CLI を使用して、このシナリオ用のリソースを作成します。

* [仮想ネットワーク内](container-instances-vnet.md)の委任されたサブネットにデプロイされたコンテナー グループ 
* 静的パブリック IP アドレスを持つネットワークにデプロイされた Azure ファイアウォール
* コンテナー グループのサブネット上のユーザー定義ルート
* ファイアウォール イングレス用の NAT 規則とエグレス用のアプリケーション規則

次に、ファイアウォールを経由するサンプル コンテナー グループからのイングレスとエグレスを検証します。

## <a name="deploy-aci-in-a-virtual-network"></a>仮想ネットワークに ACI をデプロイする

一般的なケースでは、コンテナー グループをデプロイする Azure 仮想ネットワークが既に存在している可能性があります。 デモンストレーションを目的として、次のコマンドでは、コンテナー グループの作成時に仮想ネットワークとサブネットを作成します。 サブネットは Azure Container Instances に委任されます。 

コンテナー グループは、`aci-helloworld` イメージから小さな Web アプリを実行します。 ドキュメントの他の記事で示されているように、このイメージには、静的な HTML ページを返す、Node.js で作成された小さな Web アプリがパッケージされています。

必要な場合は、最初に [az group create][az-group-create] コマンドを使用して Azure リソース グループを作成します。 次に例を示します。

```azurecli
az group create --name myResourceGroup --location eastus
```

次のコマンド例を簡略化するために、リソース グループの名前に環境変数を使用します。

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

[az container create][az-container-create] コマンドを使用してコンテナー グループを作成します。

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> `--subnet address-prefix` の値は、サブネットに必要な IP アドレス空間に合わせて調整します。 サポートされる最小サブネットは /29 で、これにより 8 つの IP アドレスが提供されます。 一部の IP アドレスは、Azure で使用するために予約されています。

後の手順で使用するために、[az container show][az-container-show] コマンドを実行して、コンテナー グループのプライベート IP アドレスを取得します。

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Azure Firewall をネットワークにデプロイする

次のセクションでは、Azure CLI を使用して、Azure ファイアウォールを仮想ネットワークにデプロイします。 背景については、「[チュートリアル: Deploy and configure Azure Firewall using the Azure portal (チュートリアル: Azure portal を使用して Azure Firewall のデプロイと構成を行う)](../firewall/deploy-cli.md)」を参照してください。

最初に、[az network vnet subnet create][az-network-vnet-subnet-create] を使用して、ファイアウォール用の AzureFirewallSubnet という名前のサブネットを追加します。 AzureFirewallSubnet は、このサブネットの "*必須*" の名前です。

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

サブネットにファイアウォールを作成するには、次の [Azure CLI コマンド](../firewall/deploy-cli.md)を使用します。

まだインストールされていない場合は、[az extension add][az-extension-add] コマンドを使用して、ファイアウォール拡張機能を Azure CLI に追加します。

```azurecli
az extension add --name azure-firewall
```

ファイアウォール リソースを作成します。

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

[az network firewall update][az-network-firewall-update] コマンドを使用して、ファイアウォールの構成を更新します。

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

[az network firewall ip-config list][az-network-firewall-ip-config-list] コマンドを使用して、ファイアウォールのプライベート IP アドレスを取得します。 このプライベート IP アドレスは、後のコマンドで使用します。


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
[az network public-ip show][az-network-public-ip-show] コマンドを使用して、ファイアウォールのパブリック IP アドレスを取得します。 このパブリック IP アドレスは、後のコマンドで使用します。

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>ACI サブネットにユーザー定義ルートを定義する

Azure ファイアウォールにトラフィックを転送するために、ACI サブネットにユーザー定義ルートを定義します。 詳細については、[ネットワーク トラフィックのルーティング](../virtual-network/tutorial-create-route-table-cli.md)に関する記事を参照してください。 

### <a name="create-route-table"></a>ルート テーブルを作成する

最初に、次の [az network route-table create][az-network-route-table-create] コマンドを実行して、ルート テーブルを作成します。 ルート テーブルは、仮想ネットワークと同じリージョンに作成します。

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>ルートを作成する

[az network-route-table route create][az-network-route-table-route-create] を実行して、ルート テーブルにルートを作成します。 トラフィックをファイアウォールにルーティングするには、次ホップの種類を `VirtualAppliance` に設定し、ファイアウォールのプライベート IP アドレスを次ホップ アドレスとして渡します。

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>ルート テーブルを ACI サブネットに関連付ける

[az network vnet subnet update][az-network-vnet-subnet-update] コマンドを実行して、Azure Container Instances に委任されたサブネットにルート テーブルを関連付けます。

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>ファイアウォールで規則を構成する

既定では、Azure Firewall は、受信および送信トラフィックを拒否 (ブロック) します。 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>ファイアウォールで ACI サブネットに対する NAT 規則を構成する

ネットワーク内で以前に開始したアプリケーション コンテナーへの受信インターネット トラフィックを変換およびフィルター処理するために、ファイアウォールに [NAT 規則](../firewall/rule-processing.md)を作成します。 詳細については、[Azure Firewall DNAT を使用したインバウンド インターネット トラフィックのフィルター処理](../firewall/tutorial-firewall-dnat.md)に関する記事を参照してください。

[az network firewall nat-rule create][az-network-firewall-nat-rule-create] コマンドを使用して、NAT 規則とコレクションを作成します。

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

必要に応じて NAT 規則を追加して、サブネット内の他の IP アドレスへのトラフィックをフィルター処理します。 たとえば、サブネット内の他のコンテナー グループが受信トラフィック用の IP アドレスを公開したり、再起動後に他の内部 IP アドレスをコンテナー グループに割り当てたりすることができます。

### <a name="create-outbound-application-rule-on-the-firewall"></a>ファイアウォールで送信アプリケーション規則を作成する

次の [az network firewall application-rule create][az-network-firewall-application-rule-create] コマンドを実行して、ファイアウォールにアウトバウンド規則を作成します。 このサンプル規則により、Azure Container Instances に委任されたサブネットから FQDN `checkip.dyndns.org` へのアクセスが許可されます。 このサイトへの HTTP アクセスは、Azure Container Instances からのエグレス IP アドレスを確認するために後の手順で使用されます。

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>ファイアウォール経由のコンテナー グループ アクセスをテストする

次のセクションでは、Azure Container Instances に委任されたサブネットが Azure ファイアウォールの内側で適切に構成されていることを確認します。 前の手順では、サブネットへの受信トラフィックと、サブネットからの送信トラフィックの両方をファイアウォール経由でルーティングしました。

### <a name="test-ingress-to-a-container-group"></a>コンテナー グループへのイングレスをテストする

ファイアウォールのパブリック IP アドレスを参照して、仮想ネットワークで実行されている *appcontainer* への受信アクセスをテストします。 前に、パブリック IP アドレスを変数 $FW_PUBLIC_IP に格納しました。

```bash
echo $FW_PUBLIC_IP
```

出力は次のようになります。

```console
52.142.18.133
```

ファイアウォールの NAT 規則が適切に構成されている場合は、ブラウザーにファイアウォールのパブリック IP アドレスを入力すると、次のような内容が表示されます。

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="ファイアウォールのパブリック IP アドレスを参照する":::

### <a name="test-egress-from-a-container-group"></a>コンテナー グループからのエグレスをテストする


次のサンプル コンテナーを仮想ネットワークにデプロイします。 実行されると、1 つの HTTP 要求が `http://checkip.dyndns.org` に送信され、送信元の IP アドレス (エグレス IP アドレス) が表示されます。 ファイアウォールのアプリケーション規則が適切に構成されている場合は、ファイアウォールのパブリック IP アドレスが返されます。

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

コンテナーのログを表示して、IP アドレスがファイアウォールのパブリック IP アドレスと同じであることを確認します。

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

出力は次のようになります。

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure ファイアウォールの内側の仮想ネットワークにコンテナー グループを設定しました。 ファイアウォールにユーザー定義ルートと NAT およびアプリケーション規則を構成しました。 この構成を使用して、Azure Container Instances からのイングレスおよびエグレス用に 1 つの静的 IP アドレスを設定しました。

トラフィックの管理と Azure リソースの保護の詳細については、[Azure Firewall](../firewall/index.yml) のドキュメントを参照してください。



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






