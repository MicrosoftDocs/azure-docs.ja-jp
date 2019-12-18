---
title: Azure Application Gateway に関してよく寄せられる質問
description: Azure Application Gateway に関してよく寄せられる質問の回答をご覧ください。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: c93198848058bad8c9af6903cc68253e71e2d668
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996665"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Application Gateway に関してよく寄せられる質問

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Application Gateway に関して寄せられた一般的な質問を以下に示します。

## <a name="general"></a>全般

### <a name="what-is-application-gateway"></a>Application Gateway とは

Azure Application Gateway は、アプリケーション デリバリー コントローラー (ADC) をサービスとして提供します。 お客様のアプリケーションで、さまざまなレイヤー 7 負荷分散機能を利用できます。 このサービスは、Azure により提供されるフル マネージド サービスであり、高可用性とスケーラビリティを備えています。

### <a name="what-features-does-application-gateway-support"></a>Application Gateway はどのような機能をサポートしますか?

Application Gateway は、自動スケーリング、SSL オフロードとエンド ツー エンド SSL、Web アプリケーション ファイアウォール、Cookie ベースのセッション アフィニティ、URL パスベースのルーティング、マルチサイト ホスティングなどの機能をサポートしています。 サポートされている機能の完全な一覧については、「[Application Gateway の概要](application-gateway-introduction.md)」をご覧ください。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Application Gateway と Azure Load Balancer の違いは何ですか?

Application Gateway はレイヤー 7 のロード バランサーです。つまり、Web トラフィックのみ (HTTP、HTTPS、WebSocket、HTTP/2) に対して機能します。 また、SSL ターミネーション、Cookie ベースのセッション アフィニティ、ラウンド ロビンによるトラフィックの負荷分散などの機能をサポートしています。 Load Balancer は、レイヤー 4 (TCP または UDP) でトラフィックを負荷分散するものです。

### <a name="what-protocols-does-application-gateway-support"></a>Application Gateway はどのようなプロトコルをサポートしますか?

Application Gateway は、HTTP、HTTPS、HTTP/2、WebSocket をサポートします。

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway は HTTP/2 をどのようにサポートしますか?

「[HTTP/2 のサポート](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)」を参照してください。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>バックエンド プールの一部としてはどのようなリソースがサポートされていますか?

[サポート対象のバックエンド リソース](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)に関するセクションを参照してください。

### <a name="in-what-regions-is-application-gateway-available"></a>Application Gateway はどのリージョンで利用できますか?

Application Gateway は、グローバル Azure のすべてのリージョンで利用できます。 また、[Azure China 21Vianet](https://www.azure.cn/) と [Azure Government](https://azure.microsoft.com/overview/clouds/government/) でもご利用いただけます。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>このデプロイは私のサブスクリプション専用ですか? それとも、複数の顧客と共有されますか?

Application Gateway は、お客様の仮想ネットワーク専用のデプロイメントです。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Application Gateway は HTTP から HTTPS へのリダイレクトをサポートしていますか?

リダイレクトはサポートされます。 「[Application Gateway のリダイレクトの概要](application-gateway-redirect-overview.md)」を参照してください。

### <a name="in-what-order-are-listeners-processed"></a>リスナーはどのような順序で処理されますか?

「[リスナーを処理する順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)」を参照してください。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Application Gateway の IP と DNS はどこで確認できますか?

エンドポイントとしてパブリック IP アドレスを使用している場合には、パブリック IP アドレス リソースで IP と DNS の情報を確認できます。 このほか、ポータル内にあるアプリケーション ゲートウェイの概要ページで確認することもできます。 内部 IP アドレスを使用している場合は、概要ページで情報を確認できます。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>キープアライブ タイムアウトと TCP アイドル タイムアウトの設定はどのようになっていますか?

 Application Gateway v1 SKU では、キープアライブ タイムアウトは 120 秒です。 v2 SKU のキープアライブ タイムアウトは 75 秒です。 TCP アイドル タイムアウトは、Application Gateway のフロントエンド仮想 IP (VIP) では既定値の 4 分となっています。

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>アプリケーション ゲートウェイの有効期間内に IP や DNS 名が変わることはありますか?

Application Gateway V1 SKU では、アプリケーション ゲートウェイを停止して起動すると、VIP が変わることがあります。 これに対して、アプリケーション ゲートウェイに関連付けられている DNS 名は、そのゲートウェイの有効期間全体を通して変わりません。 DNS 名が変わることはないので、CNAME エイリアスを使用し、その参照先をアプリケーション ゲートウェイの DNS アドレスにするようにしてください。 Application Gateway V2 SKU では、IP アドレスを静的として設定できるため、アプリケーション ゲートウェイの有効期間中、IP および DNS 名が変更されることはありません。 

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway は静的 IP をサポートしますか?

はい、Application Gateway v2 SKU は、静的パブリック IP アドレスをサポートしています。 v1 SKU では、静的内部 IP アドレスがサポートされます。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway は複数のパブリック IP をサポートしますか?

1 つのアプリケーション ゲートウェイでサポートされるパブリック IP アドレスは 1 つだけです。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Application Gateway のサブネットはどのくらい大きくする必要がありますか?

[Application Gateway のサブネットのサイズに関する考慮事項](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)のセクションを参照してください。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>1 つのサブネットに複数の Application Gateway リソースをデプロイできますか?

はい。 特定の Application Gateway のデプロイの複数のインスタンスに加え、別の Application Gateway リソースを含む既存のサブネットに別の一意の Application Gateway リソースをプロビジョニングできます。

1 つのサブネットで Standard_v2 と Standard の両方の Application Gateway を混在させることはできません。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway は x-forwarded-for ヘッダーをサポートしますか?

はい。 「[要求への変更](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)」を参照してください。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>アプリケーション ゲートウェイのデプロイにはどのくらい時間がかかりますか? アプリケーション ゲートウェイは更新中にも動作しますか?

新しい Application Gateway v1 SKU のデプロイでは、プロビジョニングに最大 20 分かかります。 インスタンスのサイズまたは数を変更しても中断が発生することはありません。ゲートウェイはその間もアクティブな状態が続きます。

v2 SKU を使用するデプロイのほとんどは、プロビジョニングに 6 分ほどかかります。 ただし、デプロイの種類によっては、それよりも時間がかかることがあります。 たとえば、多数のインスタンスを持つ複数の可用性ゾーン全体にわたるデプロイには、6 分以上かかることがあります。 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Application Gateway で Exchange Server をバックエンドとして使用することはできますか?

No. Application Gateway は SMTP、IMAP、POP3 などの電子メール プロトコルをサポートしていません。 

## <a name="performance"></a>パフォーマンス

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Application Gateway は高可用性とスケーラビリティをどのようにサポートしますか?

2 つ以上のインスタンスをデプロイすると、Application Gateway v1 SKU が高可用性のシナリオをサポートします。 Azure は、これらのインスタンスを更新ドメインと障害ドメインに分散させ、全インスタンスで同時に障害が発生しないようにします。 v1 SKU では、同じゲートウェイの複数のインスタンスを追加して負荷を共有することによってスケーラビリティをサポートします。

v2 SKU では、新しいインスタンスが障害ドメインと更新ドメインに自動的に分散されるようにします。 ゾーン冗長を選択した場合は、ゾーン障害回復性を提供するために、最新のインスタンスが可用性ゾーンにも分散されます。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Application Gateway を使用してデータ センターの垣根を越えた DR のシナリオを実現するにはどうすればよいですか?

Traffic Manager を使用すると、異なるデータ センターにある複数のアプリケーション ゲートウェイにトラフィックを分散できます。

### <a name="does-application-gateway-support-autoscaling"></a>Application Gateway は自動スケーリングをサポートしていますか?

はい、Application Gateway v2 SKU では、自動スケールをサポートします。 詳しくは、「[自動スケーリングとゾーン冗長 Application Gateway](application-gateway-autoscaling-zone-redundant.md)」を参照してください。

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>手動でのスケールアップまたはスケールダウンによってダウンタイムが発生することはありますか?

No. インスタンスはアップグレード ドメインと障害ドメインに分散されます。

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway は接続のドレインに対応していますか?

はい。 中断を発生させることなくバックエンド プール内のメンバーを変更するように接続のドレインを設定できます。 詳細については、[Application Gateway の「接続のドレイン」セクション](overview.md#connection-draining)を参照してください。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>インスタンスを中断せずにサイズを中から大に変更できますか?

はい。 Azure では、インスタンスを更新ドメインと障害ドメインに分散させ、全インスタンスで同時に障害が発生することがないようにしています。 Application Gateway は、同じゲートウェイの複数のインスタンスを追加して負荷を共有することによるスケーリングをサポートします。

## <a name="configuration"></a>構成

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway は常に仮想ネットワークにデプロイされますか?

はい。 Application Gateway は、必ず仮想ネットワーク サブネットにデプロイされます。 このサブネットにはアプリケーション ゲートウェイのみを含めることができます。 詳細については、[仮想ネットワークとサブネットの要件](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)に関するセクションを参照してください。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Application Gateway は、所属している仮想ネットワークやサブスクリプションの外部にあるインスタンスと通信できますか?

Application Gateway は、IP 接続がある限り、所属している仮想ネットワークの外部にあるインスタンスと通信できます。 Application Gateway は、所属しているサブスクリプションの外部にあるインスタンスとも通信できます。 バックエンド プール メンバーとして内部 IP を使用する予定の場合には、[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)または [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用してください。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネット内に何か他にデプロイできるものはありますか?

No. もっとも、サブネット内に他のアプリケーション ゲートウェイをデプロイすることはできます。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネットでネットワーク セキュリティ グループはサポートされていますか?

[アプリケーション ゲートウェイ サブネット内のネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)に関するセクションを参照してください。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>アプリケーション ゲートウェイ サブネットはユーザー定義ルートをサポートしていますか?

「[アプリケーション ゲートウェイ サブネットでサポートされるユーザー定義ルート](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)」を参照してください。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Application Gateway にはどのような制限がありますか? これらの制限値を引き上げることはできますか?

「[Application Gateway の制限](../azure-subscription-service-limits.md#application-gateway-limits)」を参照してください。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Application Gateway を外部と内部の両方のトラフィックに同時に使用することはできますか?

はい。 Application Gateway では、アプリケーション ゲートウェイごとに内部 IP と外部 IP を 1 つずつサポートできます。

### <a name="does-application-gateway-support-virtual-network-peering"></a>Application Gateway は仮想ネットワークのピアリングをサポートしていますか?

はい。 仮想ネットワークのピアリングは、他の仮想ネットワークにおけるトラフィックの負荷分散に役立ちます。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>ExpressRoute または VPN トンネルにより接続されているオンプレミスのサーバーと通信することはできますか?

はい。トラフィックが許可されている限り、通信可能です。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>1 つのバックエンド プールで異なるポートを使用し、多数のアプリケーションにサービスを提供することはできますか?

マイクロサービス アーキテクチャがサポートされています。 異なる複数のポートを対象にプローブを実行するには、複数の HTTP 設定を構成する必要があります。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>カスタム プローブは応答データ上のワイルドカードや正規表現をサポートしていますか?

No. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Application Gateway ではルーティング規則がどのように処理されるのでしょうか?

「[規則を処理する順序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)」を参照してください。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>カスタム プローブの [ホスト] フィールドは何を表しているのでしょうか?

Application Gateway 上でマルチサイトを構成した場合には、[ホスト] フィールドにプローブの送信先の名前を指定します。 それ以外の場合には、"127.0.0.1" を使用します。 この値は、仮想マシンのホスト名とは異なります。 形式は、\<プロトコル\>://\<ホスト\>:\<ポート\>\<パス\> です。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Application Gateway に対するアクセスを少数のソース IP アドレスだけに限定することはできますか?

はい。 [アクセスを特定のソース IP だけに限定する方法](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)に関するセクションを参照してください。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>パブリック側のリスナーとプライベート側のリスナーの両方に同じポートを使用することはできますか?

No.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>v1 SKU から v2 SKU への移行に使用できるガイダンスはありますか。

はい。 詳しくは、「[Migrate Azure Application Gateway and Web Application Firewall from v1 to v2 (Azure Application Gateway と Web アプリケーション ファイアウォールを v1 から v2 に移行する)](migrate-v1-v2.md)」をご覧ください。


## <a name="configuration---ssl"></a>構成 - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Application Gateway はどのような証明書をサポートしていますか?

Application Gateway は、自己署名証明書、証明機関 (CA) の証明書、Extended Validation (EV) 証明書、ワイルドカード証明書をサポートしています。

### <a name="what-cipher-suites-does-application-gateway-support"></a>Application Gateway はどのような暗号スイートをサポートしていますか?

Application Gateway は、次の暗号スイートをサポートしています。 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

SSL オプションのカスタマイズ方法については、「[Application Gateway で SSL ポリシーのバージョンと暗号スイートを構成する](application-gateway-configure-ssl-policy-powershell.md)」を参照してください。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Application Gateway はバックエンドへのトラフィックの再暗号化をサポートしていますか?

はい。 Application Gateway は SSL オフロードとエンド ツー エンド SSL をサポートしており、バックエンドへのトラフィックが再暗号化されます。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>SSL ポリシーを構成して SSL プロトコルのバージョンを管理することはできますか?

はい。 Application Gateway を構成して、TLS1.0、TLS1.1、TLS1.2 を拒否することができます。 SSL 2.0 および 3.0 は既定で無効になっているため、構成できません。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>暗号スイートおよびポリシーの順序は構成できますか?

はい。 Application Gateway では、[暗号スイートを構成](application-gateway-ssl-policy-overview.md)できます。 カスタム ポリシーを定義するためには、次の暗号スイートのうち少なくとも 1 つを有効にする必要があります。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway では、バックエンドの管理に SHA256 を使用しています。

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Application Gateway は、SSL 証明書をいくつサポートしていますか?

Application Gateway は、SSL 証明書を 100 件までサポートしています。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Application Gateway は、バックエンドの再暗号化用の認証証明書をいくつサポートしていますか?

Application Gateway は、認証証明書を 100 件までサポートしています。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway は Azure Key Vault とネイティブに統合されていますか?

はい、Application Gateway v2 SKU では、Key Vault をサポートします。 詳細については、「[Key Vault 証明書での SSL 終了](key-vault-certs.md)」を参照してください。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>.com と .net のサイトの HTTPS リスナーはどのように構成するのでしょうか? 

複数ドメインベース (ホストベース) のルーティングでは、マルチサイト リスナーを作成し、プロトコルとして HTTPS を使用するリスナーを設定してから、リスナーとルーティング規則を関連付けます。 詳細については、[Application Gateway を使用した複数サイトのホスティング](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)に関するページを参照してください。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>.pfx ファイルのパスワードに特殊文字を使用できますか?

いいえ、.pfx ファイルのパスワードには英数字のみを使用してください。

## <a name="configuration---web-application-firewall-waf"></a>構成 - Web アプリケーション ファイアウォール (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU では、Standard SKU で利用可能な機能をすべて利用できますか?

はい。 WAF は Standard SKU に含まれる機能をすべてサポートしています。

### <a name="how-do-i-monitor-waf"></a>WAF を監視するにはどうすればよいですか?

WAF の監視には、診断ログを使用します。 詳細については、[Application Gateway の診断ログとメトリック](application-gateway-diagnostics.md)に関するページを参照してください。

### <a name="does-detection-mode-block-traffic"></a>検出モードではトラフィックがブロックされますか?

No. 検出モードでは、WAF 規則をトリガーするトラフィックをログに記録するにとどまります。

### <a name="can-i-customize-waf-rules"></a>WAF ルールはカスタマイズできますか?

はい。 詳細については、[WAF 規則グループと規則のカスタマイズ](application-gateway-customize-waf-rules-portal.md)に関するページを参照してください。

### <a name="what-rules-are-currently-available-for-waf"></a>WAF で現在利用できるのはどのような規則ですか?

WAF で現在サポートされているのは、CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229)、[3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)、および [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31) です。 これらの規則は、Open Web Application Security Project (OWASP) が特定した 10 の脆弱性のほとんどに対するベースライン セキュリティを提供するものです。 

* SQL インジェクションからの保護
* クロスサイト スクリプティングに対する保護
* 一般的な Web 攻撃 (コマンド インジェクション、HTTP 要求スマグリング、HTTP 応答スプリッティング、リモート ファイル インクルード攻撃など) に対する保護
* HTTP プロトコル違反に対する保護
* HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)
* ボット、クローラー、スキャナーの防止
* アプリケーションのよくある構成ミスの検出 (Apache、IIS など)

詳細については、[OWASP の 10 の脆弱性](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)に関するページを参照してください。

### <a name="does-waf-support-ddos-protection"></a>WAF は DDoS 保護をサポートしていますか?

はい。 アプリケーション ゲートウェイをデプロイしてある仮想ネットワーク上で、DDos 保護を有効にすることができます。 この設定によって、アプリケーション ゲートウェイの仮想 IP (VIP) も Azure DDoS Protection サービスによる保護の対象になります。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>v1 SKU から v2 SKU への移行に使用できるガイダンスはありますか。

はい。 詳しくは、「[Migrate Azure Application Gateway and Web Application Firewall from v1 to v2 (Azure Application Gateway と Web アプリケーション ファイアウォールを v1 から v2 に移行する)](migrate-v1-v2.md)」をご覧ください。

## <a name="configuration---ingress-controller-for-aks"></a>構成 - AKS のイングレス コントローラー

### <a name="what-is-an-ingress-controller"></a>イングレス コントローラーとは何ですか?

Kubernetes を使用すると、`deployment` リソースおよび `service` リソースを作成して、クラスター内のポッドのグループを内部で公開できます。 同じサービスを外部で公開するには、[`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) リソースを定義します。これは、負荷分散、SSL 終了、および名前ベースの仮想ホスティングを提供します。
この `Ingress` リソースを満たすには、`Ingress` リソースの変更をリスンし、ロード バランサー ポリシーを構成するイングレス コントローラーが必要です。

Application Gateway のイングレス コントローラーを使用すると、AKS クラスターとも呼ばれる [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) に対するイングレスとして [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) を使用できます。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>イングレス コントローラーの単一のインスタンスで複数の Application Gateway を管理できますか?

現在、イングレス コントローラーの 1 つのインスタンスは、1 つの Application Gateway にのみ関連付けることができます。

## <a name="diagnostics-and-logging"></a>診断とログ記録

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Application Gateway ではどのような種類のログを利用できますか?

Application Gateway では、次の 3 つのログを利用できます。 

* **ApplicationGatewayAccessLog**:このアクセス ログには、アプリケーション ゲートウェイ フロントエンドに送信された要求がそれぞれ記録されます。 データには、呼び出し元の IP、要求された URL、応答の待ち時間、リターン コード、入出力バイトが含まれます。アクセス ログの収集間隔は 300 秒ごとです。 アプリケーション ゲートウェイ 1 つごとに 1 つのレコードが含まれます。
* **ApplicationGatewayPerformanceLog**:このパフォーマンス ログには、各アプリケーション ゲートウェイのパフォーマンスが記録されます。 情報には、バイト単位のスループット、処理された要求の総数、失敗した要求の数、正常および異常なバックエンド インスタンスの数などが含まれます。
* **ApplicationGatewayFirewallLog**:このファイアウォール ログには、WAF を構成してあるアプリケーション ゲートウェイについて、検出モードまたは防止モードを通じてログが作成された要求が記録されます。

詳細については、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」を参照してください。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>バックエンド プールのメンバーが正常かどうかを確認するにはどうすればよいですか?

PowerShell コマンドレット `Get-AzApplicationGatewayBackendHealth` とポータルのいずれかを使用することによって、正常性を確認できます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md)に関するトピックを参照してください。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>診断ログのアイテム保持ポリシーとは何ですか?

お客様のストレージ アカウントには、診断ログが送られます。 お客様は、自身の好みに応じてアイテム保持ポリシーを設定できます。 診断ログは、イベント ハブまたは Azure Monitor ログに送信することもできます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md)に関するトピックを参照してください。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Application Gateway の監査ログを取得するにはどうすればよいですか?

ポータル内で、アプリケーション ゲートウェイのメニュー ブレードにある **[アクティビティ ログ]** を選択すると、監査ログにアクセスできます。 

### <a name="can-i-set-alerts-with-application-gateway"></a>Application Gateway でアラートを設定できますか?

はい。 Application Gateway では、メトリックに基づいてアラートを構成します。 詳細については、[Application Gateway のメトリック](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics)に関するセクションと、[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)に関するページを参照してください。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Application Gateway のトラフィック統計情報を分析するにはどうすればよいですか?

アクセス ログを確認および分析する方法はいくつかあります。 Azure Monitor ログ、Excel、Power BI などを使用してください。

このほか、Application Gateway のアクセス ログに対応した人気のログ アナライザー [GoAccess](https://goaccess.io/) をインストールして実行する Resource Manager テンプレートも利用できます。 GoAccess では、ユニーク ビジター、要求されたファイル、ホスト、オペレーティング システム、ブラウザー、HTTP 状態コードなど、有益な HTTP トラフィック統計情報を確認できます。 詳細については、GitHub の [Resource Manager テンプレート フォルダーにある Readme ファイル](https://aka.ms/appgwgoaccessreadme)を参照してください。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>バックエンドの正常性として不明な状態が返されるのですが、どのような原因が考えられますか?

不明な状態が返されるのは通常、アプリケーション ゲートウェイ サブネット上でネットワーク セキュリティ グループ (NSG)、カスタム DNS、またはユーザー定義ルーティング (UDR) により、バックエンドに対するアクセスがブロックされているときです。 詳細については、[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)に関するページを参照してください。

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>許可されているトラフィックが NSG フロー ログに表示されない場合がありますか?

はい。 構成が次のシナリオに一致する場合は、許可されているトラフィックが NSG フロー ログに表示されません。
- Application Gateway v2 をデプロイした
- アプリケーション ゲートウェイ サブネットに NSG がある
- その NSG 上で NSG フロー ログを有効にした

## <a name="next-steps"></a>次の手順

Application Gateway の詳細については、「[Azure Application Gateway とは](overview.md)」を参照してください。
