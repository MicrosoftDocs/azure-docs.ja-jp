---
title: 'クイック スタート: Azure CLI を使用してルート サーバーを作成および構成する'
description: このクイック スタートでは、Azure CLI を使用して、ルート サーバーを作成して構成する方法について学習します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: e9c583db7493afc04b2c66553801f62d364b0a80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419610"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>クイック スタート: Azure CLI を使用してルート サーバーを作成および構成する 

この記事は、Azure CLI を使用して、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) とピアリングするように Azure Route Server を構成するのに役立ちます。 Azure Route Server では、NVA からルートを学習し、仮想ネットワーク内の仮想マシンに対してそれらをプログラムします。 また、NVA に仮想ネットワーク ルートをアドバタイズします。 詳細については、[Azure Route Server](overview.md) に関するページを参照してください。

> [!IMPORTANT]
> Azure Route Server (プレビュー) は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

##  <a name="prerequisites"></a>前提条件 

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 最新の Azure CLI があることを確認してください。ポータルで Azure Cloud Shell を使用することもできます。 
* [Azure Route Server のサービスの制限](route-server-faq.md#limitations)を確認します。 

##  <a name="create-a-route-server"></a>ルート サーバーを作成する 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択します。 

構成を始めるには、Azure アカウントにサインインします。 Cloud Shell の "試してみる" オプションを使用している場合は、自動的にサインインします。 接続については、次の例を参照してください。

```azurecli-interactive
az login
```

アカウントのサブスクリプションを確認します。

```azurecli-interactive
az account list
```

ExpressRoute 回線を作成するサブスクリプションを選択します。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>リソース グループと仮想ネットワークを作成する 

Azure Route Server を作成するには、デプロイをホストする仮想ネットワークが必要です。 次のコマンドを使用して、リソース グループと仮想ネットワークを作成します。 既に仮想ネットワークがある場合は、次のセクションに進むことができます。

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>サブネットの追加 

1. *RouteServerSubnet* という名前のサブネットを追加して、Azure Route Server の情報をデプロイします。 このサブネットは、Azure Route Server 専用のサブネットです。 RouteServerSubnet は /27 またはそれより短いプレフィックス (/26、/25 など) でなければなりません。そうでないと、Azure Route Server を追加するときにエラー メッセージが表示されます。

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. RouteServerSubnet ID を取得します。 仮想ネットワーク内のすべてのサブネットのリソース ID を表示するには、このコマンドを使用します。 

    ```azurecli-interactive 
    $subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

RouteServerSubnet ID は次のようになります。 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>ルート サーバーを作成する 

このコマンドを使用して、ルート サーバーを作成します。 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

この場所は、仮想ネットワークの場所と一致している必要があります。 HostedSubnet は、前のセクションで取得した RouteServerSubnet ID です。 

## <a name="create-peering-with-an-nva"></a>NVA とのピアリングを作成する 

次のコマンドを使用して、ルート サーバーから NVA へのピアリングを確立します。 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

"nva_ip" は、NVA に割り当てられた仮想ネットワーク IP です。 "nva_asn" は、NVA で構成された自律システム番号 (ASN) です。 ASN は、65515 から 65520 の範囲に含まれているもの以外の 16 ビットの任意の数値にすることができます。 この ASN の範囲は Microsoft によって予約されています。 

冗長性を確保するために、異なる NVA、または同じ NVA の別のインスタンスとのピアリングを設定するには、このコマンドを使用します。

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>NVA の構成を完了する 

NVA の構成を完了して BGP セッションを有効にするには、Azure Route Server の IP と ASN が必要です。 この情報は、このコマンドを使用して取得できます。 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

出力には次の情報が含まれます。 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>ルート交換を構成する 

同じ VNet に ExpressRoute ゲートウェイと Azure VPN ゲートウェイがあり、それらでルートを交換する場合は、Azure Route Server でルート交換を有効にすることができます。

> [!IMPORTANT]
> グリーンフィールド デプロイの場合は、Azure Route Server を作成する前に必ず Azure VPN ゲートウェイを作成してください。そうしないと、Azure VPN Gateway のデプロイが失敗します。
> 

1. Azure Route Server とゲートウェイ間のルート交換を有効にするには、このコマンドを使用します。

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Azure Route Server とゲートウェイ間のルート交換を無効にするには、このコマンドを使用します。

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>トラブルシューティング 

このコマンドを使用して、Azure Route Server によってアドバタイズおよび受信されたルートを表示できます。

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Route Server が不要になった場合は、これらのコマンドを使用して BGP ピアリングを削除してから、ルート サーバーを削除します。 

1. このコマンドを使用して、Azure Route Server と NVA の間の BGP ピアリングを削除します。

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. このコマンドを使用して、Azure Route Server を削除します。 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>次のステップ

Azure Route Server を作成した後、Azure Route Server が ExpressRoute および VPN Gateway とどのように連携するかについては、以下を参照してください。 

> [!div class="nextstepaction"]
> [Azure ExpressRoute と Azure VPN のサポート](expressroute-vpn-support.md)
 
