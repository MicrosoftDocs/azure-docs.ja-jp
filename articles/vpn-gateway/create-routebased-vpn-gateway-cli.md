---
title: 'ルートベースの Azure VPN Gateway を作成する: CLI'
description: Azure CLI を使用して、オンプレミス ネットワークへの VPN 接続のため、または仮想ネットワークに接続するために、ルート ベースの Azure VPN ゲートウェイをすばやく作成します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2bbd7e39ee65ba304ec62697b6fcc77bea133b41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94737253"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>CLI を使用してルートベースの VPN ゲートウェイを作成する

この記事では、Azure CLI を使用して、ルートベースの Azure VPN ゲートウェイをすばやく作成する方法について説明します。 VPN ゲートウェイが使用されるのは、ご利用のオンプレミスのネットワークへの VPN 接続を作成するときです。 また、VNet への接続に VPN ゲートウェイを使用することもできます。

この記事の手順では、VNet、サブネット、ゲートウェイ サブネット、およびルートベースの VPN ゲートウェイ (仮想ネットワーク ゲートウェイ) を作成します。 仮想ネットワーク ゲートウェイの作成には 45 分以上かかる場合があります。 ゲートウェイの作成が完了すると、接続を作成できます。 これらの手順には、Azure サブスクリプションが必要です。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet) コマンドを使用して、仮想ネットワークを作成します。 次の例では、**EastUS** の場所に、**VNet1** という名前の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>ゲートウェイ サブネットの追加

ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイ サービスが使用する予約済み IP アドレスが含まれます。 次の例を使用してゲートウェイ サブネットを追加します。

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>パブリック IP アドレスの要求

VPN ゲートウェイには、動的に割り当てられるパブリック IP アドレスが必要です。 仮想ネットワーク用に作成した VPN ゲートウェイにパブリック IP アドレスが割り当てられます。 次の例を使用して、パブリック IP アドレスを要求します。

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>VPN ゲートウェイの作成

VPN ゲートウェイの作成には、[az network vnet-gateway create](/cli/azure/group) コマンドを使用します。

`--no-wait` パラメーターを使用してこのコマンドを実行した場合は、フィードバックや出力が表示されなくなります。 `--no-wait` パラメーターは、ゲートウェイをバックグラウンドで作成するためのものです。 これは、VPN ゲートウェイがすぐに作成されるという意味ではありません。

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

VPN ゲートウェイの作成には 45 分以上かかる場合があります。

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>VPN ゲートウェイの表示

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

応答は次のようになります。

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>パブリック IP アドレスの表示

ゲートウェイに割り当てられたパブリック IP アドレスを表示するには、次の例を使用します。

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

**ipAddress** フィールドに関連付けられている値が VPN ゲートウェイのパブリック IP アドレスです。

応答の例:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースが必要でなくなったら、[az group delete](/cli/azure/group) を使用してリソース グループを削除します。 これでリソース グループとそれに含まれるすべてのリソースが削除されます。

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>次のステップ

ゲートウェイの作成が完了したら、自分の仮想ネットワークと別の VNet の間の接続を作成できます。 または、自分の仮想ネットワークとオンプレミスの場所の間の接続を作成します。

> [!div class="nextstepaction"]
> [サイト間接続を作成する](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [ポイント対サイト接続を作成する](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [別の VNet への接続を作成する](vpn-gateway-vnet-vnet-rm-ps.md)
