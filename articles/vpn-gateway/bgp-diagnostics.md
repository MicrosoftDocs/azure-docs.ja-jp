---
title: BGP の状態とメトリックの表示
titleSuffix: Azure VPN Gateway
description: トラブルシューティングのために、BGP 関連の重要な情報を表示できます。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103148848"
---
# <a name="view-bgp-metrics-and-status"></a>BGP のメトリックと状態の表示

Azure portal または Azure PowerShell を使用して、BGP のメトリックと状態を表示できます。

## <a name="azure-portal"></a>Azure portal

Azure portal では、BGP ピア、学習したルート、およびアドバタイズされたルートを表示できます。 このデータを含む .csv ファイルをダウンロードすることもできます。

1. [Azure portal](https://portal.azure.com)で、お使いの仮想ネットワーク ゲートウェイに移動します。
1. **[監視]** の下にある **[BGP ピア]** を選択し、[BGP ピア] ページを開きます。

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Azure portal のメトリックのスクリーンショット":::

### <a name="learned-routes"></a>学習したルート

1. 最大 50 個の学習されたルートをポータルで表示できます。

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="学習したルートのスクリーンショット。":::

1. また、学習したルートのファイルをダウンロードすることもできます。 学習したルートが 50 を超えている場合、そのすべてを表示する唯一の方法は、.csv ファイルをダウンロードして表示することです。 ダウンロードするには、 **[Download learned routes]\(学習したルートのダウンロード\)** を選択します。

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="学習したルートのダウンロードのスクリーンショット。":::
1. その後、そのファイルを表示します。

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="ダウンロードした、学習したルートのスクリーンショット。":::

### <a name="advertised-routes"></a>アドバタイズされたルート

1. アドバタイズされたルートを表示するには、表示するネットワークの最後にある **[...]** を選択し、 **[View advertised routes]\(アドバタイズされたルートの表示\)** をクリックします。

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="アドバタイズされたルートを表示する方法を示すスクリーンショット。" lightbox="./media/bgp-diagnostics/route-expand.png":::
1. **[Routes advertised to peer]\(ピアにアドバタイズされたルート\)** ページには、最大 50 個のアドバタイズされたルートが表示されます。

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="アドバタイズされたルートのスクリーンショット。":::
1. また、アドバタイズされたルートのファイルをダウンロードすることもできます。 アドバタイズされたルートが 50 を超えている場合、そのすべてを表示する唯一の方法は、.csv ファイルをダウンロードして表示することです。 ダウンロードするには、 **[Download advertised routes]\(アドバタイズされたルートのダウンロード\)** を選択します。

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="ダウンロード対象のアドバタイズされたルートを選択したスクリーンショット。":::
1. その後、そのファイルを表示します。

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="ダウンロードしたアドバタイズされたルートのスクリーンショット。":::

### <a name="bgp-peers"></a>BGP ピア

1. 最大 50 個の BGP ピアをポータルで表示できます。

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="BGP ピアのスクリーンショット。":::
1. BGP ピアのファイルをダウンロードすることもできます。 BGP ピアが 50 を超えている場合、そのすべてを表示する唯一の方法は、.csv ファイルをダウンロードして表示することです。 ダウンロードするには、ポータル ページで **[Download BGP peers]\(BGP ピアのダウンロード\)** を選択します。

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="BGP ピアのダウンロードのスクリーンショット。":::
1. その後、そのファイルを表示します。

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="ダウンロードした BGP ピアのスクリーンショット。":::

## <a name="powershell"></a>PowerShell

すべての BGP ピアとその状態を表示するには、**Get-AzVirtualNetworkGatewayBGPPeerStatus** を使用します。

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

BGP を通じてゲートウェイで学習されたすべてのルートを表示するには、**Get-AzVirtualNetworkGatewayLearnedRoute** を使用します。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

BGP を通じてゲートウェイからピアにアドバタイズされているすべてのルートを表示するには、**Get-AzVirtualNetworkGatewayAdvertisedRoute** を使用します。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>次のステップ

BGP についての詳細は、[VPN Gatewayの BGP の構成](bgp-howto.md)に関する記事を参照してください。
