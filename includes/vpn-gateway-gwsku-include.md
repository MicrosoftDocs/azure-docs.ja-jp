仮想ネットワーク ゲートウェイを作成する場合、使用するゲートウェイの SKU を指定する必要があります。 ワークロード、スループット、機能、および SLA の種類に基づく要件を満たす SKU を選択します。

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>運用環境のワークロード*と*開発テスト環境のワークロード

SLA と機能セットに違いがあるため、"*運用環境と開発テスト環境*" では、次の SKU をお勧めします。

| **ワークロード**                       | **SKU**               |
| ---                                | ---                    |
| **運用環境での重要なワークロード** | VpnGw1、VpnGw2、VpnGw3 |
| **開発テストまたは概念実証**   | Basic                  |
|                                    |                        |

古い SKU を使用している場合、運用環境で推奨される SKU は、Standard SKU と HighPerformance SKU です。 古い SKU については、[ゲートウェイ SKU (レガシ SKU)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md) に関するページを参照してください。

###  <a name="feature"></a>ゲートウェイ SKU の機能セット

新しいゲートウェイ SKU では、ゲートウェイで提供される機能セットが効率化されています。

| **SKU**| **機能**|
| ---    | ---         |
|**Basic**   | **ルート ベースの VPN**: 10 個のトンネルと P2S、RADIUS 認証なし、IKEv2 なし<br>**ポリシーベース VPN**: (IKEv1): 1 トンネル。P2S なし|
| **VpnGw1、VpnGw2、および VpnGw3** | **ルートベース VPN**: 最大 30 トンネル (*)、P2S、BGP、アクティブ/アクティブ、カスタム IPsec/IKE ポリシー、ExpressRoute/VPN 共存 |
|        |             |

(*) "PolicyBasedTrafficSelectors" を構成することによって、ルートベースの VPN ゲートウェイ (VpnGw1、VpnGw2、VpnGw3) を、オンプレミスにある複数のポリシーベース ファイアウォール デバイスに接続することができます。 詳細については、[PowerShell を使って複数のオンプレミス ポリシーベース VPN デバイスに VPN ゲートウェイを接続する方法](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)に関するページを参照してください。

###  <a name="resize"></a>ゲートウェイ SKU のサイズ変更

1. VpnGw1、VpnGw2、VpnGw3 SKU の間でサイズ変更できます。
2. 古いゲートウェイ SKU では、Basic、Standard、HighPerformance SKU の間でサイズ変更できます。
2. Basic/Standard/HighPerformance SKU から新しい VpnGw1/VpnGw2/VpnGw3 SKU にサイズ変更することは**できません**。 この場合は、新しい SKU に[移行](#migrate)する必要があります。

###  <a name="migrate"></a>古い SKU から新しい SKU への移行

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
