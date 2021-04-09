---
title: ExpressRoute プライベート ピアリング経由のサイト間 VPN 接続
description: この記事では、トラフィックを暗号化するために、ExpressRoute プライベート ピアリング経由でサイト間 VPN を有効にする方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 01d87bcb5697326fa87b25b20354897049900d9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880527"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>ExpressRoute プライベート ピアリング経由のサイト間 VPN 接続を構成する

仮想ネットワーク ゲートウェイへのサイト間 VPN を、ExpressRoute プライベート ピアリングを経由し、RFC 1918 IP アドレスを使用して構成できます。 この構成には次のような利点があります。

* プライベート ピアリング経由のトラフィックは暗号化されます。

* 仮想ネットワーク ゲートウェイに接続するポイント対サイト ユーザーは、(サイト間トンネルを介して) ExpressRoute を使用してオンプレミス リソースにアクセスできます。

>[!NOTE]
>この機能は、ゾーン冗長ゲートウェイでのみサポートされています。 たとえは、VpnGw1AZ、VpnGw2AZ などです。
>

この構成を完了するには、次の前提条件を満たしていることを確認してください。

* VPN ゲートウェイが作成される (予定の) VNet にリンクされた ExpressRoute 回線が正常に機能している。

* VNet 内の RFC1918 (プライベート) IP を使用して、ExpressRoute 回線経由でリソースに到達できる。

## <a name="routing"></a><a name="routing"></a>ルーティング

**図 1** は、ExpressRoute プライベート ピアリングを経由した VPN 接続の例を示しています。 この例では、ExpressRoute プライベート ピアリング経由で Azure ハブ VPN ゲートウェイに接続されたオンプレミス ネットワーク内のネットワークを確認できます。 この構成の重要な側面は、オンプレミス ネットワークと Azure の間で、ExpressRoute パスと VPN パスの両方を経由してルーティングを行うことです。

図 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="図 1":::

接続の確立は簡単です。

1. ExpressRoute 回線とプライベート ピアリングを使用して ExpressRoute 接続を確立します。

1. この記事の手順を使用して VPN 接続を確立します。

### <a name="traffic-from-on-premises-networks-to-azure"></a>オンプレミス ネットワークから Azure へのトラフィック

オンプレミス ネットワークから Azure へのトラフィックに関しては、Azure プレフィックスは、ExpressRoute プライベート ピアリング BGP と VPN BGP の両方を経由してアドバタイズされます。 結果として、オンプレミス ネットワークから Azure への 2 つのネットワーク ルート (パス) が生成されます。

• IPsec で保護されたパスを経由する 1 つのネットワーク ルート。

• IPsec で保護されていない ExpressRoute を直接経由する 1 つのネットワーク ルート。

通信に暗号化を適用するには、**図 1** の VPN 接続ネットワークに関して、オンプレミス VPN ゲートウェイ経由の Azure ルートが直接 ExpressRoute パスよりも優先されることを確認する必要があります。

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure からオンプレミス ネットワークへのトラフィック

Azure からオンプレミス ネットワークへのトラフィックにも同じ要件が適用されます。 IPsec パスを直接 ExpressRoute パス (IPsec なし) よりも確実に優先させるには、2 つのオプションがあります。

• **VPN 接続ネットワークの VPN BGP セッションで、より具体的なプレフィックスをアドバタイズします**。 VPN 接続ネットワークを包含する広い範囲を ExpressRoute プライベート ピアリング経由でアドバタイズし、次に VPN BGP セッションでより具体的な範囲をアドバタイズすることができます。 たとえば、ExpressRoute 経由では 10.0.0.0/16 を、VPN 経由では 10.0.1.0/24 をアドバタイズします。

• **VPN と ExpressRoute で、互いに切り離されたプレフィックスをアドバタイズします**。 VPN 接続ネットワーク範囲が他の ExpressRoute 接続ネットワークから切り離されている場合、VPN と ExpressRoute の各 BGP セッションでそれぞれプレフィックスをアドバタイズできます。 たとえば、ExpressRoute 経由では 10.0.0.0/24 を、VPN 経由では 10.0.1.0/24 をアドバタイズします。

どちらの例でも Azure は、ExpressRoute 経由で直接、VPN 保護なしで送信するのではなく、VPN 接続経由で 10.0.1.0/24 にトラフィックを送信します。

>[!Warning]
>ExpressRoute 接続と VPN 接続の両方を経由して同じプレフィックスをアドバタイズした場合、Azure は ExpressRoute パスを直接、VPN 保護なしで使用します。
>

## <a name="portal-steps"></a><a name="portal"></a>ポータルの手順

1. サイト間接続を構成します。 手順については、[サイト間接続](./tutorial-site-to-site-portal.md)に関する記事を参照してください。 必ず、ゾーン冗長ゲートウェイ SKU をゲートウェイに対して選択してください。 

   ゾーン冗長 SKU は、SKU の末尾に "AZ" が付いています。 たとえば、**VpnGw1AZ** のようになります。 ゾーン冗長ゲートウェイは、可用性ゾーン サービスが使用可能なリージョンでのみ利用できます。 可用性ゾーンがサポートされているリージョンの詳細については、[可用性ゾーンがサポートされているリージョン](../availability-zones/az-region.md)に関する記事を参照してください。

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="ゲートウェイ プライベート IP":::
1. ゲートウェイでプライベート IP を有効にします。 **[構成]** を選択し、 **[ゲートウェイ プライベート IP]** を **[有効]** に設定します。 **[保存]** を選択して変更を保存します。
1. **[概要]** ページで、 **[詳細表示]** を選択してプライベート IP アドレスを表示します。 後で構成手順で使用するために、この情報を書き留めておきます。

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="[概要] ページ" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. 接続で **[Azure プライベート IP アドレスを使用する]** を有効にするには、 **[構成]** を選択します。 **[Azure プライベート IP アドレスを使用する]** を **[有効]** に設定してから、 **[保存]** を選択します。

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="ゲートウェイ プライベート IP - 有効":::
1. ファイアウォールから、手順 3 で書き留めたプライベート IP に ping を実行します。 ExpressRoute プライベート ピアリング経由でプライベート IP に到達できるはずです。
1. このプライベート IP をオンプレミス ファイアウォール上のリモート IP として使用して、ExpressRoute プライベート ピアリング経由のサイト間トンネルを確立します。

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell ステップ

1. サイト間接続を構成します。 手順については、「[サイト間 VPN の構成](./tutorial-site-to-site-portal.md)」の記事を参照してください。 必ず、ゾーン冗長ゲートウェイ SKU をゲートウェイに対して選択してください。 ゾーン冗長 SKU は、SKU の末尾に "AZ" が付いています。 たとえば、VpnGw1AZ のようになります。
1. 次の PowerShell コマンドを使用して、ゲートウェイ上でプライベート IP を使用するようにフラグを設定します。

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   パブリックとプライベートの IP アドレスが表示されます。 出力の "TunnelIpAddresses" セクションの IP アドレスを書き留めます。 この情報は後の手順で使用します。
1. 次の PowerShell コマンドを使用して、プライベート IP アドレスを使用するように接続を設定します。

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. ファイアウォールから、手順 2 で書き留めたプライベート IP に ping を実行します。 ExpressRoute プライベート ピアリング経由で到達できるはずです。
1. このプライベート IP をオンプレミス ファイアウォール上のリモート IP として使用して、ExpressRoute プライベート ピアリング経由のサイト間トンネルを確立します。

## <a name="next-steps"></a>次のステップ

VPN Gateway の詳細については、「[VPN ゲートウェイとは](vpn-gateway-about-vpngateways.md)」をご覧ください。