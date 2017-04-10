次の表は、ゲートウェイの種類と、ゲートウェイ SKU によって予測される合計スループットを示したものです。 この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。 料金はゲートウェイの SKU によって異なります。 詳細については、「[VPN Gateway の価格](https://azure.microsoft.com/pricing/details/vpn-gateway)」を参照してください。

UltraPerformance ゲートウェイ SKU はこの表には示されていません。 UltraPerformance SKU については、[ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) のドキュメントを参照してください。

|  | **VPN Gateway のスループット (1)** | **VPN Gateway の IPsec トンネルの最大数 (2)** | **ExpressRoute ゲートウェイのスループット** | **VPN Gateway と ExpressRoute の共存** |
| --- | --- | --- | --- | --- |
| **Basic SKU (3)(5)** |100 Mbps |10 |500 Mbps |なし |
| **Standard SKU (4)(5)** |100 Mbps |10 |1000 Mbps |はい |
| **High Performance SKU (4)** |200 Mbps |30 |2000 Mbps |はい |

* (1) VPN のスループットは、同一 Azure リージョン内の VNet 間での測定値に基づく大まかな推定値です。 インターネット経由でのクロスプレミス接続では、このスループットが保証されるわけではありません。 この値は、達成可能な最大スループットです。
* (2) トンネルの数とは、RouteBased VPN を指しています。 PolicyBased VPN がサポートできるサイト間 VPN トンネルは 1 つのみです。
* (3) BGP は、Basic SKU ではサポートされていません。
* (4) PolicyBased VPN は、この SKU ではサポートされていません。 Basic SKU でのみサポートされています。
* (5) アクティブ/アクティブ S2S VPN ゲートウェイ接続は、この SKU ではサポートされていません。 アクティブ/アクティブは、HighPerformance SKU のみでサポートされています。



<!--HONumber=Jan17_HO1-->


