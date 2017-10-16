[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>RADIUS 認証はすべての Azure VPN Gateway SKU でサポートされていますか。

RADIUS 認証は VpnGw1、VpnGw2、VpnGw3 の 3 つの SKU でサポートされています。 従来の SKU を使用している場合は、Standard と High Performance の SKU に限り RADIUS 認証をサポートしています。 Basic Gateway SKU ではサポートされていません。 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>クラシック デプロイメント モデルでは RADIUS 認証がサポートされていますか。
 
いいえ。 クラシック デプロイメント モデルでは、RADIUS 認証がサポートされていません。
 
### <a name="are-3rd-party-radius-servers-supported"></a>サードパーティ製の RADIUS サーバーはサポートされていますか。

はい。サードパーティ製の RADIUS サーバーはサポートされています。
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Azure ゲートウェイがオンプレミスの RADIUS サーバーに到達するための接続の要件は何ですか。

オンプレミスのサイトに対する VPN サイト対サイト接続が必要です (適切なルートが構成されている必要があります)。  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>ExpressRoute 接続を使って (Azure VPN ゲートウェイから) オンプレミスの RADIUS サーバーに対するトラフィックをルーティングすることはできますか。

いいえ。 そのようなトラフィックは、サイト対サイト接続でのみルーティングできます。
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>RADIUS 認証でサポートされる SSTP 接続の数に変更はありますか。 サポートされる SSTP 接続と IKEv2 接続の最大数はそれぞれどのくらいですか。

RADIUS 認証を使用するゲートウェイでサポートされる SSTP 接続の最大数には変更ありません。 以前と変わらず 128 です。 ゲートウェイに構成されているのが SSTP、IKEv2、その両方のいずれであっても、サポートされる接続の最大数は 128 です。
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trusted-certificate-to-azure"></a>RADIUS サーバーを使った証明書認証と Azure ネイティブ証明書認証を使った証明書認証 (信頼された証明書を Azure にアップロードする) の違いは何ですか。

RADIUS による証明書認証では、認証要求が実際に証明書の検証処理を担当する RADIUS サーバーに転送されます。 このオプションは、RADIUS を使用する証明書認証インフラストラクチャが既にあり、それを統合したい場合に有用です。
  
証明書認証に Azure を使用する場合には、Azure VPN ゲートウェイが証明書の検証を担当します。 ユーザーの側では、ゲートウェイに証明書の公開キーをアップロードする必要があります。 このほか、接続を許可してはならない失効した証明書の一覧を指定することもできます。

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>RADIUS 認証は、IKEv2 と SSTP VPN の両方で機能しますか。

はい、RADIUS 認証は、IKEv2 と SSTP VPN の両方でサポートされています。  