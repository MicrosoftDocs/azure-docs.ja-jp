---
title: 'クイック スタート: Azure PowerShell を使用してルート サーバーを作成および構成する'
description: このクイック スタートでは、Azure PowerShell を使用して、ルート サーバーを作成して構成する方法について学習します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: a3ab3a801872cc20b4e41bbff02ad6474c3bab8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655208"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>クイック スタート: Azure PowerShell を使用してルート サーバーを作成および構成する

この記事は、PowerShell を使用して、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) とピアリングするように Azure Route Server を構成するのに役立ちます。 Azure Route Server では、NVA からルートを学習し、仮想ネットワーク内の仮想マシン上でそれらをプログラムします。 また、Azure Route Server は NVA に仮想ネットワーク ルートをアドバタイズします。 詳細については、[Azure Route Server](overview.md) に関するページを参照してください。

> [!IMPORTANT]
> Azure Route Server (プレビュー) は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 最新の Azure PowerShell モジュールがあることを確認してください。ポータルで Azure Cloud Shell を使用することもできます。
* [Azure Route Server のサービスの制限](route-server-faq.md#limitations)を確認します。

## <a name="create-a-route-server"></a>ルート サーバーを作成する

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択します。

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>リソース グループと仮想ネットワークを作成する

Azure Route Server を作成するには、デプロイをホストする仮想ネットワークが必要です。 次のコマンドを使用して、リソース グループと仮想ネットワークを作成します。 既に仮想ネットワークがある場合は、次のセクションに進むことができます。

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>サブネットの追加

1. *RouteServerSubnet* という名前のサブネットを追加して、Azure Route Server の情報をデプロイします。 このサブネットは、Azure Route Server 専用のサブネットです。 RouteServerSubnet は /27 またはそれより短いプレフィックス (/26、/25 など) でなければなりません。そうでないと、Azure Route Server を追加するときにエラー メッセージが表示されます。

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. RouteServerSubnet ID を取得します。 仮想ネットワーク内のすべてのサブネットのリソース ID を表示するには、このコマンドを使用します。

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

RouteServerSubnet ID は次のようになります。

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>ルート サーバーを作成する

このコマンドを使用して、ルート サーバーを作成します。

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

この場所は、仮想ネットワークの場所と一致している必要があります。 HostedSubnet は、前のセクションで取得した RouteServerSubnet ID です。

## <a name="create-peering-with-an-nva"></a>NVA とのピアリングを作成する

次のコマンドを使用して、ルート サーバーから NVA への BGP ピアリングを確立します。

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" は、NVA に割り当てられた仮想ネットワーク IP です。 "nva_asn" は、NVA で構成された自律システム番号 (ASN) です。 ASN は、65515 から 65520 の範囲に含まれているもの以外の 16 ビットの任意の数値にすることができます。 この ASN の範囲は Microsoft によって予約されています。

冗長性を確保するために、異なる NVA、または同じ NVA の別のインスタンスとのピアリングを設定するには、このコマンドを使用します。

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>NVA の構成を完了する

NVA の構成を完了して BGP セッションを有効にするには、Azure Route Server の IP と ASN が必要です。 この情報は、このコマンドを使用して取得できます。

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

出力には次の情報が含まれます。

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>ルート交換を構成する

同じ VNet に ExpressRoute ゲートウェイと Azure VPN ゲートウェイがあり、それらでルートを交換する場合は、Azure Route Server でルート交換を有効にすることができます。

1. Azure Route Server とゲートウェイ間のルート交換を有効にするには、このコマンドを使用します。

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Azure Route Server とゲートウェイ間のルート交換を無効にするには、このコマンドを使用します。

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>トラブルシューティング

このコマンドを使用して、Azure Route Server によってアドバタイズおよび受信されたルートを表示できます。

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Route Server が不要になった場合は、これらのコマンドを使用して BGP ピアリングを削除してから、ルート サーバーを削除します。 

1. このコマンドを使用して、Azure Route Server と NVA の間の BGP ピアリングを削除します。

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. このコマンドを使用して、Azure Route Server を削除します。

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>次のステップ

Azure Route Server を作成した後、Azure Route Server が ExpressRoute および VPN Gateway とどのように連携するかについては、以下を参照してください。 

> [!div class="nextstepaction"]
> [Azure ExpressRoute と Azure VPN のサポート](expressroute-vpn-support.md)
