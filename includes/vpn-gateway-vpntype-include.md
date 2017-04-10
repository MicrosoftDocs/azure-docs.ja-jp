* **PolicyBased:** PolicyBased VPN は、以前は (クラシック デプロイ モデルでは) 静的ルーティング ゲートウェイと呼ばれていました。 PolicyBased VPN では、パケットを暗号化し、オンプレミス ネットワークと Azure VNet の間でアドレスのプレフィックスの組み合わせで構成された IPsec ポリシーに基づいて、IPsec トンネル経由でそのパケットを送信します。 ポリシー (またはトラフィック セレクター) は、通常、VPN デバイスの構成でアクセス リストとして定義されます。 PolicyBased VPN の種類の値は *PolicyBased*です。 PolicyBased VPN を使用する場合は、次の制限事項に留意してください。
  
  * PolicyBased VPN は、Basic ゲートウェイ SKU **でのみ** 使用できます。 この種類の VPN には、その他のゲートウェイの SKU との互換性はありません。
  * PolicyBased VPN を使用する場合、設定できるトンネルは 1 つ だけです。
  * PolicyBased VPN は S2S 接続でのみ使用でき、また使用できる構成も特定のものに限られています。 ほとんどの VPN Gateway 構成では、RouteBased VPN が必要です。
* **RouteBased**: RouteBased VPN は、以前は (クラシック デプロイ モデルでは) 動的ルーティング ゲートウェイと呼ばれていました。 RouteBased VPN は、IP 転送やルーティング テーブルの "ルート" を使用して、対応するトンネル インターフェイスにパケットを直接送信します。 その後、トンネル インターフェイスではトンネルの内部または外部でパケットを暗号化または復号します。 RouteBased VPN のポリシーまたはトラフィック セレクターは、任意の環境間 (またはワイルドカード) として構成できます。 RouteBased VPN の種類の値は *RouteBased*です。



<!--HONumber=Nov16_HO3-->


