### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>ポイント対サイト接続で使用できるオペレーティング システムを教えてください。
次のクライアント オペレーティング システムがサポートされています。

* Windows 7 (32 ビットと 64 ビット)
* Windows Server 2008 R2 (64 ビットのみ)
* Windows 8 (32 ビットと 64 ビット)
* Windows 8.1 (32 ビットと 64 ビット)
* Windows Server 2012 (64 ビットのみ)
* Windows Server 2012 R2 (64 ビットのみ)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>SSTP をサポートしているポイント対サイト接続では、ソフトウェア VPN クライアントを使用できますか。
いいえ。 上記のバージョンの Windows オペレーティング システムのみがサポートされています。

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>ポイント対サイト構成で保持できる VPN クライアント エンドポイントの最大数を教えてください。
仮想ネットワークに同時に接続できる VPN クライアント数は、最大で 128 個です。

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>ポイント対サイト接続で自社の内部 PKI ルート CA を使用できますか。
はい。 以前は、自己署名ルート証明書のみを使用できました。 現在も 20 ルート証明書までアップロードできます。

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>プロキシやファイアウォールを経由してポイント対サイト接続の機能を使用できますか。
はい。 SSTP (Secure Socket トンネリング プロトコル) を使用してファイアウォール経由のトンネルが確立されます。 このトンネルは HTTPS 接続として表示されます。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>ポイント対サイト接続用に構成されたクライアント コンピューターを再起動した場合、VPN は自動的に再接続されますか。
既定では、クライアント コンピューターの再起動時には VPN の自動再接続は行われません。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>ポイント対サイトの自動再接続と VPN クライアントの DDNS はサポートされていますか。
ポイント対サイト VPN では、自動再接続と DDNS は現時点ではサポートされていません。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>サイト間接続およびポイント対サイト接続の構成は、同一仮想ネットワークに共存させることはできますか。
はい。 ゲートウェイの VPN の種類が RouteBased の場合は、どちらのソリューションも機能します。 クラシック デプロイメント モデルの場合は、動的ゲートウェイが必要です。 静的ルーティング VPN ゲートウェイと、`-VpnType PolicyBased` コマンドレットを使用するゲートウェイでは、ポイント対サイト接続はサポートされません。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>ポイント対サイト接続のクライアントを同時に複数の仮想ネットワークに接続するように構成することはできますか。
はい、できます。 ただし、仮想ネットワーク内で重複する IP プレフィックスを使用することはできません。また、ポイント対サイト接続のアドレス空間は仮想ネットワーク内で重複しないようにする必要があります。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>サイト間接続またはポイント対サイト接続ではどの程度のスループットが得られますか。
VPN トンネルのスループットを正確に一定レベルに維持することは困難です。 IPsec と SSTP は、VPN プロトコルの中でも暗号化処理の負荷が高いものです。 また、スループットはオンプレミスとインターネットの間の待機時間や帯域幅によっても制限されます。

<!--HONumber=Feb17_HO3-->


