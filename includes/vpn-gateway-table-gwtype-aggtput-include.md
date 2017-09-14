Azure では、次の VPN ゲートウェイ SKU を提供しています。

|**SKU**   | **S2S/VNet 間<br>トンネル** | **P2S<br>接続** | **合計<br>スループット ベンチマーク** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| 最大 30                         | 最大 128               | 650 Mbps                    |
|**VpnGw2**| 最大 30                         | 最大 128               | 1 Gbps                      |
|**VpnGw3**| 最大 30                         | 最大 128               | 1.25 Gbps                   |
|**Basic** | 最大 10                         | 最大 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- 合計スループット ベンチマークは、1 つのゲートウェイから集計された複数のトンネルの測定値に基づいています。 インターネットのトラフィックの状況とアプリケーションの動作に依存するため、これは保証されたスループットではありません。

- 料金情報については、[価格](https://azure.microsoft.com/pricing/details/vpn-gateway)に関するページをご覧ください。

- SLA (サービス レベル アグリーメント) 情報は [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) のページで確認できます。