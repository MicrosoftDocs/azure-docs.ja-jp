---
title: Azure Application Gateway に関してよく寄せられる質問
description: Azure Application Gateway に関してよく寄せられる質問の回答をご覧ください。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 83779dcd319614ae15de6b7e3e4e3abfd9599089
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619156"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Application Gateway に関してよく寄せられる質問

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Application Gateway に関して寄せられた一般的な質問を以下に示します。

## <a name="general"></a>全般

### <a name="what-is-application-gateway"></a>Application Gateway とは

Azure Application Gateway は、アプリケーション デリバリー コントローラー (ADC) をサービスとして提供します。 お客様のアプリケーションで、さまざまなレイヤー 7 負荷分散機能を利用できます。 このサービスは、Azure により提供されるフル マネージド サービスであり、高可用性とスケーラビリティを備えています。

### <a name="what-features-does-application-gateway-support"></a>Application Gateway はどのような機能をサポートしますか?

Application Gateway は、自動スケーリング、TLS オフロードとエンド ツー エンド TLS、Web アプリケーション ファイアウォール (WAF)、Cookie ベースのセッション アフィニティ、URL パスベースのルーティング、マルチサイト ホスティングなどの機能をサポートしています。 サポートされている機能の完全な一覧については、「[Application Gateway の概要](./overview.md)」をご覧ください。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Application Gateway と Azure Load Balancer の違いは何ですか?

Application Gateway はレイヤー 7 のロード バランサーです。つまり、Web トラフィックのみ (HTTP、HTTPS、WebSocket、HTTP/2) に対して機能します。 また、TLS 終端、Cookie ベースのセッション アフィニティ、ラウンド ロビンによるトラフィックの負荷分散などの機能をサポートします。 Load Balancer は、レイヤー 4 (TCP または UDP) でトラフィックを負荷分散するものです。

### <a name="what-protocols-does-application-gateway-support"></a>Application Gateway はどのようなプロトコルをサポートしますか?

Application Gateway は、HTTP、HTTPS、HTTP/2、WebSocket をサポートします。

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway は HTTP/2 をどのようにサポートしますか?

「[HTTP/2 のサポート](./configuration-listeners.md#http2-support)」を参照してください。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>バックエンド プールの一部としてはどのようなリソースがサポートされていますか?

[サポート対象のバックエンド リソース](./application-gateway-components.md#backend-pools)に関するセクションを参照してください。

### <a name="in-what-regions-is-application-gateway-available"></a>Application Gateway はどのリージョンで利用できますか?

Application Gateway v1 (Standard および WAF) は、グローバル Azure のすべてのリージョンで利用できます。 また、[Azure China 21Vianet](https://www.azure.cn/) と [Azure Government](https://azure.microsoft.com/overview/clouds/government/) でもご利用いただけます。

Application Gateway v2 (Standard_v2 と WAF_v2) の可用性については、[Application Gateway v2 でサポートされているリージョン](./application-gateway-autoscaling-zone-redundant.md#supported-regions)に関するページを参照してください。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>このデプロイは私のサブスクリプション専用ですか? それとも、複数の顧客と共有されますか?

Application Gateway は、お客様の仮想ネットワーク専用のデプロイメントです。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Application Gateway は HTTP から HTTPS へのリダイレクトをサポートしていますか?

リダイレクトはサポートされます。 「[Application Gateway のリダイレクトの概要](./redirect-overview.md)」を参照してください。

### <a name="in-what-order-are-listeners-processed"></a>リスナーはどのような順序で処理されますか?

「[リスナーを処理する順序](./configuration-listeners.md#order-of-processing-listeners)」を参照してください。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Application Gateway の IP と DNS はどこで確認できますか?

エンドポイントとしてパブリック IP アドレスを使用している場合には、パブリック IP アドレス リソースで IP と DNS の情報を確認できます。 このほか、ポータル内にあるアプリケーション ゲートウェイの概要ページで確認することもできます。 内部 IP アドレスを使用している場合は、概要ページで情報を確認できます。

v2 SKU の場合は、パブリック IP リソースを開き、 **[構成]** を選択します。 DNS 名を構成するには、 **[DNS 名ラベル (オプション)]** フィールドを使用できます。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>キープアライブ タイムアウトと TCP アイドル タイムアウトの設定はどのようになっていますか?

*キープアライブ タイムアウト* では、固定接続が再利用されるか、または閉じられる前に、クライアントによってその接続上で別の HTTP 要求が送信されるまでの Application Gateway の待機時間が制御されます。 *TCP アイドル タイムアウト* では、アクティビティがない場合に TCP 接続が開いた状態になる時間の長さが制御されます。 

Application Gateway v1 SKU での *キープアライブ タイムアウト* は 120 秒であり、v2 SKU では 75 秒です。 *TCP アイドル タイムアウト* は、Application Gateway の v1 および v2 SKU 両方のフロントエンド仮想 IP (VIP) 上で既定値の 4 分となっています。 v1 および v2 Application Gateway での TCP アイドル タイムアウト値は、4 分から 30 分までの範囲で構成できます。 v1 と v2 どちらの Application Gateway でも、ポータルで Application Gateway のパブリック IP に移動し、パブリック IP の [構成] ブレードで TCP アイドル タイムアウトを変更する必要があります。 次のコマンドを実行して、PowerShell からパブリック IP の TCP アイドル タイムアウト値を設定できます。 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>アプリケーション ゲートウェイの有効期間内に IP や DNS 名が変わることはありますか?

Application Gateway V1 SKU では、アプリケーション ゲートウェイを停止して起動すると、VIP が変わることがあります。 これに対して、アプリケーション ゲートウェイに関連付けられている DNS 名は、そのゲートウェイの有効期間全体を通して変わりません。 DNS 名が変わることはないので、CNAME エイリアスを使用し、その参照先をアプリケーション ゲートウェイの DNS アドレスにするようにしてください。 Application Gateway V2 SKU では、IP アドレスを静的として設定できるため、アプリケーション ゲートウェイの有効期間中、IP および DNS 名が変更されることはありません。 

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway は静的 IP をサポートしますか?

はい、Application Gateway v2 SKU は、静的パブリック IP アドレスをサポートしています。 v1 SKU では、静的内部 IP アドレスがサポートされます。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway は複数のパブリック IP をサポートしますか?

1 つのアプリケーション ゲートウェイでサポートされるパブリック IP アドレスは 1 つだけです。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Application Gateway のサブネットはどのくらい大きくする必要がありますか?

[Application Gateway のサブネットのサイズに関する考慮事項](./configuration-infrastructure.md#size-of-the-subnet)のセクションを参照してください。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>1 つのサブネットに複数の Application Gateway リソースをデプロイできますか?

はい。 特定の Application Gateway のデプロイの複数のインスタンスに加え、別の Application Gateway リソースを含む既存のサブネットに別の一意の Application Gateway リソースをプロビジョニングできます。

1 つのサブネットで v2 と v1 両方の Application Gateway SKU をサポートすることはできません。

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Application Gateway v2 はユーザー定義ルート (UDR) をサポートしていますか?

はい。ただし、特定のシナリオのみです。 詳細については、「[Application Gateway インフラストラクチャの構成](configuration-infrastructure.md#supported-user-defined-routes)」を参照してください。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway は x-forwarded-for ヘッダーをサポートしますか?

はい。 「[要求への変更](./how-application-gateway-works.md#modifications-to-the-request)」を参照してください。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>アプリケーション ゲートウェイのデプロイにはどのくらい時間がかかりますか? アプリケーション ゲートウェイは更新中にも動作しますか?

新しい Application Gateway v1 SKU のデプロイでは、プロビジョニングに最大 20 分かかります。 インスタンスのサイズまたは数を変更しても中断が発生することはありません。ゲートウェイはその間もアクティブな状態が続きます。

v2 SKU を使用するデプロイのほとんどは、プロビジョニングに 6 分ほどかかります。 ただし、デプロイの種類によっては、それよりも時間がかかることがあります。 たとえば、多数のインスタンスを持つ複数の可用性ゾーン全体にわたるデプロイには、6 分以上かかることがあります。 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Application Gateway で Exchange Server をバックエンドとして使用することはできますか?

いいえ。 Application Gateway は SMTP、IMAP、POP3 などの電子メール プロトコルをサポートしていません。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>v1 SKU から v2 SKU への移行に使用できるガイダンスはありますか。

はい。 詳しくは、「[Migrate Azure Application Gateway and Web Application Firewall from v1 to v2 (Azure Application Gateway と Web アプリケーション ファイアウォールを v1 から v2 に移行する)](migrate-v1-v2.md)」をご覧ください。

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Application Gateway v1 SKU は引き続きサポートされますか?

はい。 Application Gateway v1 SKU は引き続きサポートされます。 ただし、その SKU で行われた機能の更新を利用するために、v2 に移行することを強くお勧めします。 詳細については、「[自動スケーリングとゾーン冗長 Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)」を参照してください。

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Application Gateway v2 では、NTLM 認証を使用したプロキシ要求をサポートしていますか?

いいえ。 Application Gateway v2 では、NTLM 認証を使用したプロキシ要求をサポートしていません。

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Application Gateway アフィニティ Cookie は SameSite 属性をサポートしていますか?
はい。[Chromium ブラウザー](https://www.chromium.org/Home) [v80 の更新](https://chromiumdash.appspot.com/schedule) で、SameSite 属性のない HTTP Cookie を SameSite=Lax として扱うことが必須になりました。 これは、サードパーティのコンテキストでは、Application Gateway アフィニティ Cookie がブラウザーによって送信されないことを意味します。 

このシナリオをサポートするために、Application Gateway では、既存の *ApplicationGatewayAffinity* Cookie に加えて、*ApplicationGatewayAffinityCORS* という別の同一の Cookie が挿入されます。  これらの Cookie は似ていますが、*ApplicationGatewayAffinityCORS* Cookie には、次の 2 つの属性が追加されています。*SameSite=None; Secure* です。 これらの属性は、クロスオリジン要求でも固定セッションを維持します。 詳細については、「[Cookie ベースのアフィニティ](configuration-http-settings.md#cookie-based-affinity)」セクションを参照してください。

## <a name="performance"></a>パフォーマンス

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Application Gateway は高可用性とスケーラビリティをどのようにサポートしますか?

2 つ以上のインスタンスをデプロイすると、Application Gateway v1 SKU が高可用性のシナリオをサポートします。 Azure は、これらのインスタンスを更新ドメインと障害ドメインに分散させ、全インスタンスで同時に障害が発生しないようにします。 v1 SKU では、同じゲートウェイの複数のインスタンスを追加して負荷を共有することによってスケーラビリティをサポートします。

v2 SKU では、新しいインスタンスが障害ドメインと更新ドメインに自動的に分散されるようにします。 ゾーン冗長を選択した場合は、ゾーン障害回復性を提供するために、最新のインスタンスが可用性ゾーンにも分散されます。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Application Gateway を使用してデータ センターの垣根を越えた DR のシナリオを実現するにはどうすればよいですか?

Traffic Manager を使用すると、異なるデータ センターにある複数のアプリケーション ゲートウェイにトラフィックを分散できます。

### <a name="does-application-gateway-support-autoscaling"></a>Application Gateway は自動スケーリングをサポートしていますか?

はい、Application Gateway v2 SKU では、自動スケールをサポートします。 詳しくは、「[自動スケーリングとゾーン冗長 Application Gateway](application-gateway-autoscaling-zone-redundant.md)」を参照してください。

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>手動または自動のスケールアップまたはスケールダウンによってダウンタイムは発生しますか?

いいえ。 インスタンスはアップグレード ドメインと障害ドメインに分散されます。

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway は接続のドレインに対応していますか?

はい。 中断を発生させることなくバックエンド プール内のメンバーを変更するように接続のドレインを設定できます。 詳細については、[Application Gateway の「接続のドレイン」セクション](features.md#connection-draining)を参照してください。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>インスタンスを中断せずにサイズを中から大に変更できますか?

はい。

## <a name="configuration"></a>構成

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway は常に仮想ネットワークにデプロイされますか?

はい。 Application Gateway は、必ず仮想ネットワーク サブネットにデプロイされます。 このサブネットにはアプリケーション ゲートウェイのみを含めることができます。 詳細については、[仮想ネットワークとサブネットの要件](./configuration-infrastructure.md#virtual-network-and-dedicated-subnet)に関するセクションを参照してください。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Application Gateway は、所属している仮想ネットワークやサブスクリプションの外部にあるインスタンスと通信できますか?

Application Gateway は、IP 接続がある限り、所属している仮想ネットワークの外部にあるインスタンスと通信できます。 Application Gateway は、所属しているサブスクリプションの外部にあるインスタンスとも通信できます。 バックエンド プール メンバーとして内部 IP を使用する予定の場合には、[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)または [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用してください。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネット内に何か他にデプロイできるものはありますか?

いいえ。 もっとも、サブネット内に他のアプリケーション ゲートウェイをデプロイすることはできます。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネットでネットワーク セキュリティ グループはサポートされていますか?

[アプリケーション ゲートウェイ サブネット内のネットワーク セキュリティ グループ](./configuration-infrastructure.md#network-security-groups)に関するセクションを参照してください。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>アプリケーション ゲートウェイ サブネットはユーザー定義ルートをサポートしていますか?

「[アプリケーション ゲートウェイ サブネットでサポートされるユーザー定義ルート](./configuration-infrastructure.md#supported-user-defined-routes)」を参照してください。

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>サービス エンドポイント ポリシーは Application Gateway サブネットでサポートされていますか。

いいえ。 ストレージ アカウントの[サービス エンドポイント ポリシー](../virtual-network/virtual-network-service-endpoint-policies-overview.md)は Application Gateway サブネットではサポートされていません。構成すると、Azure インフラストラクチャのトラフィックがブロックされます。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Application Gateway にはどのような制限がありますか? これらの制限値を引き上げることはできますか?

「[Application Gateway の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)」を参照してください。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Application Gateway を外部と内部の両方のトラフィックに同時に使用することはできますか?

はい。 Application Gateway では、アプリケーション ゲートウェイごとに内部 IP と外部 IP を 1 つずつサポートできます。

### <a name="does-application-gateway-support-virtual-network-peering"></a>Application Gateway は仮想ネットワークのピアリングをサポートしていますか?

はい。 仮想ネットワークのピアリングは、他の仮想ネットワークにおけるトラフィックの負荷分散に役立ちます。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>ExpressRoute または VPN トンネルにより接続されているオンプレミスのサーバーと通信することはできますか?

はい。トラフィックが許可されている限り、通信可能です。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>1 つのバックエンド プールで異なるポートを使用し、多数のアプリケーションにサービスを提供することはできますか?

マイクロサービス アーキテクチャがサポートされています。 異なる複数のポートを対象にプローブを実行するには、複数の HTTP 設定を構成する必要があります。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>カスタム プローブは応答データ上のワイルドカードや正規表現をサポートしていますか?

いいえ。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Application Gateway ではルーティング規則がどのように処理されるのでしょうか?

「[規則を処理する順序](./configuration-request-routing-rules.md#order-of-processing-rules)」を参照してください。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>カスタム プローブの [ホスト] フィールドは何を表しているのでしょうか?

Application Gateway 上でマルチサイトを構成した場合には、[ホスト] フィールドにプローブの送信先の名前を指定します。 それ以外の場合には、"127.0.0.1" を使用します。 この値は、仮想マシンのホスト名とは異なります。 形式は \<protocol\>://\<host\>:\<port\>\<path\>です。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Application Gateway に対するアクセスを少数のソース IP アドレスだけに限定することはできますか?

はい。 [アクセスを特定のソース IP だけに限定する方法](./configuration-infrastructure.md#allow-access-to-a-few-source-ips)に関するセクションを参照してください。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>パブリック側のリスナーとプライベート側のリスナーの両方に同じポートを使用することはできますか?

いいえ。

### <a name="does-application-gateway-support-ipv6"></a>Application Gateway は IPv6 をサポートしていますか?

現在、Application Gateway v2 は IPv6 をサポートしていません。 IPv4 のみを使用してデュアル スタック VNet で動作できますが、ゲートウェイ サブネットは IPv4 のみである必要があります。 Application Gateway v1 は、デュアル スタック VNet をサポートしていません。 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>プライベート フロントエンド IP アドレスのみで Application Gateway V2 を使用するにはどうすればよいですか?

現在、Application Gateway V2 はプライベート IP モードのみをサポートしていません。 次の組み合わせをサポートしています。
* プライベート IP とパブリック IP
* パブリック IP のみ

ただし、プライベート IP のみで Application Gateway V2 を使用する場合は、次の手順に従うことができます。
1. パブリックとプライベートの両方のフロントエンド IP アドレスを使用して Application Gateway を作成する
2. パブリック フロントエンド IP アドレスのリスナーは作成しないでください。 リスナーが作成されていない場合、Application Gateway ではパブリック IP アドレスのトラフィックがリッスンされません。
3. Application Gateway サブネットの[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)を作成し、次の構成を使用して優先度順にアタッチします。
    
    a. [ソース] には **GatewayManager** サービス タグ、[宛先] には **[すべて]** 、[宛先のポート] には **65200-65535** を指定してトラフィックを許可します。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、証明書の認証によって保護 (ロック ダウン) されます。 ゲートウェイ ユーザー管理者を含む外部エンティティは、適切な証明書が配置されていないと、このようなエンドポイントに対する変更を開始できません。
    
    b. [ソース] には **AzureLoadBalancer** サービスタグ、[宛先] と [宛先のポート] には **[すべて]** を指定してトラフィックを許可します。
    
    c. [ソース] には **Internet** サービス タグ、[宛先] と [宛先のポート] には **[すべて]** を指定して受信トラフィックを拒否します。 この規則には、受信規則で *最小の優先順位* を指定します。
    
    d. プライベート IP アドレスへのアクセスがブロックされないように、VirtualNetwork の受信を許可するなどの既定の規則をそのまま使用します。
    
    e. 送信インターネット接続はブロックできません。 そうしないと、ログ記録やメトリックなどの問題が発生します。

プライベート IP のみのアクセスの NSG 構成の例:![プライベート IP アクセスのみの Application Gateway V2 NSG 構成](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>構成 - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Application Gateway はどのような証明書をサポートしていますか?

Application Gateway は、自己署名証明書、証明機関 (CA) の証明書、Extended Validation (EV) 証明書、複数ドメイン (SAN) 証明書、ワイルドカード証明書をサポートしています。

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

TLS オプションのカスタマイズ方法については、「[Application Gateway で SSL ポリシーのバージョンと暗号スイートを構成する](application-gateway-configure-ssl-policy-powershell.md)」を参照してください。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Application Gateway はバックエンドへのトラフィックの再暗号化をサポートしていますか?

はい。 Application Gateway は TLS オフロードとエンド ツー エンド TLS をサポートしており、バックエンドへのトラフィックが再暗号化されます。

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>TLS プロトコルのバージョンを制御するように TLS ポリシーを構成できますか?

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

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Application Gateway は、TLS または SSL 証明書をいくつサポートしていますか?

Application Gateway は、TLS または SSL 証明書を 100 件までサポートしています。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Application Gateway は、バックエンドの再暗号化用の認証証明書をいくつサポートしていますか?

Application Gateway は、認証証明書を 100 件までサポートしています。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway は Azure Key Vault とネイティブに統合されていますか?

はい、Application Gateway v2 SKU では、Key Vault をサポートします。 詳細については、「[Key Vault 証明書での SSL 終了](key-vault-certs.md)」を参照してください。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>.com と .net のサイトの HTTPS リスナーはどのように構成するのでしょうか? 

複数ドメインベース (ホストベース) のルーティングでは、マルチサイト リスナーを作成し、プロトコルとして HTTPS を使用するリスナーを設定してから、リスナーとルーティング規則を関連付けます。 詳細については、[Application Gateway を使用した複数サイトのホスティング](./multiple-site-overview.md)に関するページを参照してください。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>.pfx ファイルのパスワードに特殊文字を使用できますか?

いいえ、.pfx ファイルのパスワードには英数字のみを使用してください。

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>EV 証明書は DigiCert によって発行され、中間証明書は失効しています。 Application Gateway で証明書を更新するには、どうすればよいですか?

証明機関 (CA) ブラウザーのメンバーが最近公開したレポートでは、お客様、Microsoft、大規模のテクノロジ コミュニティが使用する CA ベンダーが発行している、公的に信頼されている CA の業界標準に準拠していない複数の証明書について詳しく説明しています。 準拠していない CA に関するレポートは、こちらで確認できます。  

* [バグ 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [バグ 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

業界のコンプライアンス要件に従って、CA ベンダーは非準拠の CA の失効と準拠 CA の発行を開始しました。お客様の証明書を再発行する必要があります。 Microsoft は、Azure サービスへの潜在的な影響を最小限に抑えるために、これらのベンダーと密接に連携しています。**ただし、BYOC ("Bring Your Own Certificate") シナリオで使用される証明書は、今もなお予期せずに失効するリスクがあります。**

アプリケーションによって使用されている証明書が失効しているかどうかを確認するには、[DigiCert の発表](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement)と [Certificate Revocation Tracker](https://misissued.com/#revoked) を参照してください。 証明書が失効している場合、または失効する場合は、アプリケーションで使用されている CA ベンダーから新しい証明書を要求する必要があります。 証明書が予期せず失効していることが原因でアプリケーションの可用性が中断されないようにする場合、または失効した証明書を更新する場合は、BYOC をサポートするさまざまな Azure サービスの修復リンクについて、Azure の最新情報に関する投稿を参照してください。 https://azure.microsoft.com/updates/certificateauthorityrevocation/

Application Gateway 固有の情報については、以下を参照してください。

失効した ICA のいずれかによって発行された証明書を使用している場合、アプリケーションの可用性が中断し、アプリケーションによっては、次のようなさまざまなエラーメッセージが表示されることがあります。 

1.  無効な証明書/失効した証明書
2.  接続がタイムアウトしました
3.  HTTP 502

この問題によってアプリケーションが中断されないようにする、または失効した CA を再発行するには、次の操作を行う必要があります。 

1.  証明書を再発行する方法については、証明書プロバイダーに問い合わせてください
2.  再発行後、完全な[信頼チェーン](/windows/win32/seccrypto/certificate-chains) (リーフ、中間、ルート証明書) を使用して、Azure Application Gateway/WAF で証明書を更新します。 証明書を使用している場所に基づいて、Application Gateway のリスナーまたは HTTP 設定で、次の手順に従って証明書を更新します。詳細については、記載されているドキュメントのリンクを確認してください。
3.  再発行された証明書を使用するようにバックエンド アプリケーション サーバーを更新します。 使用しているバックエンド サーバーによっては、証明書の更新手順が異なる場合があります。 ベンダーからのドキュメントを確認してください。

リスナーで証明書を更新するには、次の操作を行います。

1.  [Azure Portal](https://portal.azure.com/) で、Application Gateway リソースを開きます。
2.  証明書に関連付けられているリスナー設定を開きます。
3.  [選択した証明書の更新または編集] をクリックします。
4.  新しい PFX 証明書をパスワード付きでアップロードし、[保存] をクリックします。
5.  Web サイトにアクセスして、サイトが想定どおりに動作しているかどうかを確認します。詳細については、[こちらのドキュメント](./renew-certificates.md)を参照してください。

Application Gateway リスナーで Azure KeyVault からの証明書を参照している場合は、次の手順に従って簡単な変更を行うことをお勧めします。

1.  [Azure portal](https://portal.azure.com/) で、Application Gateway に関連付けられている Azure KeyVault の設定に移動します。
2.  再発行された証明書をストアに追加またはインポートします。 詳しくは、[こちらのドキュメントをご覧ください](../key-vault/certificates/quick-create-portal.md)。
3.  証明書がインポートされたら、Application Gateway リスナー設定に移動し、[キー コンテナーから証明書を選択する] で [証明書] ドロップダウンをクリックし、最近追加した証明書を選択します。
4.  [保存] をクリックします。キー コンテナー証明書による Application Gateway での TLS 終端の詳細については、[こちらのドキュメント](./key-vault-certs.md)を参照してください。


HTTP 設定で証明書を更新するには、次の操作を行います。

Application Gateway/WAF サービスの V1 SKU を使用している場合は、バックエンド認証証明書として新しい証明書をアップロードする必要があります。
1.  [Azure Portal](https://portal.azure.com/) で、Application Gateway リソースを開きます。
2.  証明書に関連付けられている HTTP 設定を開きます。
3.  [証明書の追加] をクリックして、再発行された証明書をアップロードし、[保存] をクリックします。
4.  後で古い証明書を削除できます。[...] [オプション] ボタン (古い証明書の横にある) をクリックし、[削除] を選択して [保存] をクリックします。
詳細については、[こちらのドキュメント](./end-to-end-ssl-portal.md#add-authenticationtrusted-root-certificates-of-back-end-servers)を参照してください。

Application Gateway/WAF サービスの V2 SKU を使用している場合、HTTP 設定で新しい証明書をアップロードする必要はありません。V2 SKU では "信頼されたルート証明書" が使用され、ここでアクションを実行する必要がないためです。

## <a name="configuration---ingress-controller-for-aks"></a>構成 - AKS のイングレス コントローラー

### <a name="what-is-an-ingress-controller"></a>イングレス コントローラーとは何ですか?

Kubernetes を使用すると、`deployment` リソースおよび `service` リソースを作成して、クラスター内のポッドのグループを内部で公開できます。 同じサービスを外部で公開するには、[`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) リソースを定義します。これは、負荷分散、TLS 終端、および名前ベースの仮想ホスティングを提供します。
この `Ingress` リソースを満たすには、`Ingress` リソースの変更をリスンし、ロード バランサー ポリシーを構成するイングレス コントローラーが必要です。

Application Gateway イングレス コントローラー (AGIC) を使用すると、AKS クラスターとも呼ばれる [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) に対するイングレスとして [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) を使用できます。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>イングレス コントローラーの単一のインスタンスで複数の Application Gateway を管理できますか?

現在、イングレス コントローラーの 1 つのインスタンスは、1 つの Application Gateway にのみ関連付けることができます。

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>AKS クラスターと kubenet が AGIC で機能しないのはなぜですか?

AGIC はルート テーブル リソースを Application Gateway サブネットに自動的に関連付けようとしますが、AGIC からのアクセス許可がないため、関連付けに失敗する場合があります。 AGIC がルート テーブルを Application Gateway サブネットに関連付けることができない場合、そのことを示すエラーが AGIC ログに記録されます。その場合、AKS クラスターによって作成されたルート テーブルを Application Gateway のサブネットに手動で関連付ける必要があります。 詳細については、「[サポートされているユーザー定義のルート](configuration-infrastructure.md#supported-user-defined-routes)」を参照してください。

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>AKS クラスターと Application Gateway を別々の仮想ネットワークに接続できますか? 

はい。ただし、仮想ネットワークがピアリングされていて、アドレス空間が重複していないことが条件です。 kubenet と共に AKS を実行している場合は必ず、AKS によって生成されたルート テーブルを Application Gateway サブネットに関連付けてください。 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>AGIC アドオンでサポートされていない機能は何ですか? 

Helm 経由でデプロイされる AGIC と、AKS アドオンとしてデプロイされる AGIC の違いについて、[こちら](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)を参照してください。

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>アドオンと Helm デプロイのどちらを使用すればよいですか? 

Helm 経由でデプロイされる AGIC と、AKS アドオンとしてデプロイされる AGIC の違いについて、[こちら](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)を参照してください。特に、AKS アドオンではなく Helm 経由でデプロイされる AGIC でサポートされているシナリオについて説明した表を参照してください。 一般に、Helm 経由でデプロイすると、正式リリースよりも前にベータ機能とリリース候補をテストできます。 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>どのバージョンの AGIC がアドオンでデプロイされるかを制御できますか?

いいえ。AGIC アドオンはマネージド サービスであり、アドオンは Microsoft によって自動的に最新の安定バージョンに更新されます。 

## <a name="diagnostics-and-logging"></a>診断とログ記録

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Application Gateway ではどのような種類のログを利用できますか?

Application Gateway では、次の 3 つのログを利用できます。 

* **ApplicationGatewayAccessLog**:このアクセス ログには、アプリケーション ゲートウェイ フロントエンドに送信された要求がそれぞれ記録されます。 データには、呼び出し元の IP、要求された URL、応答の待ち時間、リターン コード、入出力バイトが含まれます。アプリケーション ゲートウェイ 1 つごとに 1 つのレコードが含まれます。
* **ApplicationGatewayPerformanceLog**:このパフォーマンス ログには、各アプリケーション ゲートウェイのパフォーマンスが記録されます。 情報には、バイト単位のスループット、処理された要求の総数、失敗した要求の数、正常および異常なバックエンド インスタンスの数などが含まれます。
* **ApplicationGatewayFirewallLog**:このファイアウォール ログには、WAF を構成してあるアプリケーション ゲートウェイについて、検出モードまたは防止モードを通じてログが作成された要求が記録されます。

すべてのログが 60 秒ごとに収集されます。 詳細については、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」を参照してください。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>バックエンド プールのメンバーが正常かどうかを確認するにはどうすればよいですか?

PowerShell コマンドレット `Get-AzApplicationGatewayBackendHealth` とポータルのいずれかを使用することによって、正常性を確認できます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md)に関するトピックを参照してください。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>診断ログのアイテム保持ポリシーとは何ですか?

お客様のストレージ アカウントには、診断ログが送られます。 お客様は、自身の好みに応じてアイテム保持ポリシーを設定できます。 診断ログは、イベント ハブまたは Azure Monitor ログに送信することもできます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md)に関するトピックを参照してください。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Application Gateway の監査ログを取得するにはどうすればよいですか?

ポータル内で、アプリケーション ゲートウェイのメニュー ブレードにある **[アクティビティ ログ]** を選択すると、監査ログにアクセスできます。 

### <a name="can-i-set-alerts-with-application-gateway"></a>Application Gateway でアラートを設定できますか?

はい。 Application Gateway では、メトリックに基づいてアラートを構成します。 詳細については、[Application Gateway のメトリック](./application-gateway-metrics.md)に関するセクションと、[アラート通知の受信](../azure-monitor/alerts/alerts-overview.md)に関するページを参照してください。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Application Gateway のトラフィック統計情報を分析するにはどうすればよいですか?

アクセス ログを確認および分析する方法はいくつかあります。 Azure Monitor ログ、Excel、Power BI などを使用してください。

このほか、Application Gateway のアクセス ログに対応した人気のログ アナライザー [GoAccess](https://goaccess.io/) をインストールして実行する Resource Manager テンプレートも利用できます。 GoAccess では、ユニーク ビジター、要求されたファイル、ホスト、オペレーティング システム、ブラウザー、HTTP 状態コードなど、有益な HTTP トラフィック統計情報を確認できます。 詳細については、GitHub の [Resource Manager テンプレート フォルダーにある Readme ファイル](https://aka.ms/appgwgoaccessreadme)を参照してください。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>バックエンドの正常性として不明な状態が返されるのですが、どのような原因が考えられますか?

不明な状態が返されるのは通常、アプリケーション ゲートウェイ サブネット上でネットワーク セキュリティ グループ (NSG)、カスタム DNS、またはユーザー定義ルーティング (UDR) により、バックエンドに対するアクセスがブロックされているときです。 詳細については、[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)に関するページを参照してください。

### <a name="are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet"></a>NSG フローログは Application Gateway v2 サブネットに関連付けられている NSG でサポートされていますか?

現在のプラットフォームの制限により、Application Gateway v2 (Standard_v2、WAF_v2) サブネットに NSG があり、NSG フロー ログが有効になっている場合は、非決定的な動作が発生するため、このシナリオは現在サポートされていません。

### <a name="where-does-application-gateway-store-customer-data"></a>Application Gateway によって顧客データはどこに保存されますか?

Application Gateway によって、顧客データがデプロイされているリージョン外に移動または格納されることはありません。

## <a name="next-steps"></a>次のステップ

Application Gateway の詳細については、「[Azure Application Gateway とは](overview.md)」を参照してください。
