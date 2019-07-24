---
title: 'ExpressRoute Global Reach を構成する: Azure CLI | Microsoft Docs'
description: この記事では、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築し、Global Reach を有効にする方法について説明します。
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 28df8f59944ccac9d731e15a558d864beed2f3ae
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817696"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Azure CLI を使用して ExpressRoute Global Reach を構成する

この記事は、Azure CLI を使用して Azure ExpressRoute Global Reach を構成するのに役立ちます。 詳細については、[ExpressRoute Global Reach](expressroute-global-reach.md) に関するページを参照してください。
 
構成を開始する前に、次の要件を完了します。

* 最新バージョンの Azure CLI をインストールします。 [Azure CLI のインストール](/cli/azure/install-azure-cli)と [Azure CLI の使用開始](/cli/azure/get-started-with-azure-cli)に関する各ページを参照してください。
* ExpressRoute 回線のプロビジョニング [ワークフロー](expressroute-workflows.md)を理解します。
* ExpressRoute 回線がプロビジョニング済み状態であることを確認します。
* Azure プライベート ピアリングが ExpressRoute 回線に構成されていることを確認します。  

### <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン

構成を開始するには、Azure アカウントにサインインします。 次のコマンドによって既定のブラウザーが開かれ、Azure アカウントのサインイン資格情報が求められます。  

```azurecli
az login
```

複数の Azure サブスクリプションを所有している場合は、アカウントのサブスクリプションをすべて確認します。

```azurecli
az account list
```

使用するサブスクリプションを指定します。

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>構成のために ExpressRoute 回線を特定する

任意の 2 つの ExpressRoute 回線間で ExpressRoute Global Reach を有効にすることができるのは、それらがサポートされている国に配置され、かつ異なるピアリングの場所で作成されている場合だけです。 サブスクリプションで両方の回線を所有している場合は、この記事の後半で説明されているように、どちらかの回線を選択して構成を実行できます。 2 つの回線が異なる Azure サブスクリプションにある場合、一方の Azure サブスクリプションで構成コマンドを実行するときに、もう一方の Azure サブスクリプションからの承認が必要であり、承認キーを渡す必要があります。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を有効にする

接続を有効にするためにコマンドを実行する場合、パラメーターの値に次の要件があることに注意してください。

* *peer-circuit* は、完全なリソース ID にする必要があります。 例: 

  > /subscriptions/{自分のサブスクリプション ID}/resourceGroups/{自分のリソース グループ}/providers/Microsoft.Network/expressRouteCircuits/{自分の回線名}

* *address-prefix* は、"/29" IPv4 サブネット ("10.0.0.0/29" など) である必要があります。 このサブネット内の IP アドレスを使用して、2 つの ExpressRoute 回線間の接続を確立します。 Azure 仮想ネットワークまたはオンプレミスのネットワークでは、このサブネット内のアドレスを使用しないでください。

次の CLI コマンドを実行して、2 つの ExpressRoute 回線を接続します。

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI 出力は次のようになります。

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

この操作が完了したら、2 つの ExpressRoute 回線を介して、両側にオンプレミスのネットワーク間の接続があります。

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>異なる Azure サブスクリプションで ExpressRoute 回線間の接続を有効にする

2 つの回線が同じ Azure サブスクリプション内にない場合は、承認が必要です。 次の構成では、回線 2 のサブスクリプションで承認を生成し、承認キーを回線 1 に渡します。

1. 承認キーを生成します。

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLI 出力は次のようになります。

   ```azurecli
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. 回線 2 のリソース ID と承認キーの両方をメモします。

1. 回線 2 のリソース ID と承認キーを渡して、回線 1 に対して次のコマンドを実行します。

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

この操作が完了したら、2 つの ExpressRoute 回線を介して、両側にオンプレミスのネットワーク間の接続があります。

## <a name="get-and-verify-the-configuration"></a>構成を取得して確認する

構成が行われた回線 (上記の例の回線 1) で構成を確認するには、次のコマンドを使用します。

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

CLI の出力では、*CircuitConnectionStatus* が表示されます。 2 つの回線の間で接続が確立されたか ("Connected")、確立されていないか ("Disconnected") が表示されます。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を無効にする

接続を無効にするには、構成が作成された回線 (前の例の回線 1) に対して次のコマンドを実行します。

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

```show``` コマンドを使用して状態を確認します。

この操作が完了すると、ExpressRoute 回線を経由したオンプレミスのネットワーク間の接続は存在しなくなります。

## <a name="next-steps"></a>次の手順

* [ExpressRoute Global Reach について詳しく学習する](expressroute-global-reach.md)
* [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
* [ExpressRoute 回線を仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)
