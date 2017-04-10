次の表に、PolicyBased および RouteBased の VPN Gateway の要件を示します。 この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。 クラシック モデルでは、PolicyBased VPN Gateway は静的ゲートウェイと同じです。また、RouteBased のゲートウェイは動的ゲートウェイと同じです。

|  | **PolicyBased の Basic VPN Gateway** | **RouteBased の Basic VPN Gateway** | **RouteBased の Standard VPN Gateway** | **RouteBased の HighPerformance VPN Gateway** |
| --- | --- | --- | --- | --- |
| **サイト間接続 (S2S)** |PolicyBased VPN の構成 |RouteBased VPN の構成 |RouteBased VPN の構成 |RouteBased VPN の構成 |
| **ポイント対サイト接続 (P2S**) |サポートされていません |サポートされています (S2S と共存可能) |サポートされています (S2S と共存可能) |サポートされています (S2S と共存可能) |
| **認証方法** |事前共有キー |S2S 接続用の事前共有キー、P2S 接続用の証明書 |S2S 接続用の事前共有キー、P2S 接続用の証明書 |S2S 接続用の事前共有キー、P2S 接続用の証明書 |
| **S2S 接続の最大数** |1 |10 |10 |30 |
| **P2S 接続の最大数** |サポートされていません |128 |128 |128 |
| **アクティブ ルーティングのサポート (BGP)** |サポートされていません |サポートされていません |サポートされています |サポートされています |



<!--HONumber=Nov16_HO3-->


