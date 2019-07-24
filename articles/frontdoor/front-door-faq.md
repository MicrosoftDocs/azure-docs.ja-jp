---
title: Azure Front Door Service - Front Door よく寄せられる質問 | Microsoft Docs
description: このページでは、Azure Front Door Service に関してよく寄せられる質問の回答を紹介します
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407737"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Azure Front Door Service についてよく寄せられる質問

この記事では、Azure Front Door Service の機能に関する一般的な質問を回答します。 ご質問に対する回答がここで見つからない場合は、次のチャネルでお問い合わせください (上から順に)。

1. この記事のコメント セクション。
2. [Azure Front Door Service の UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **Microsoft のサポート:** 新しいサポート要求を作成するには、Azure Portal の **[ヘルプ]** タブで、**[ヘルプとサポート]** ボタンを選択し、**[新しいサポート要求]** を選択します。

## <a name="general"></a>全般

### <a name="what-is-azure-front-door-service"></a>Azure Front Door Service とは

Azure Front Door Service は、Application Delivery Network (ADN) をサービスとして提供することで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 これは動的サイト アクセラレーション (DSA) とグローバル負荷分散に、ほぼリアルタイムのフェールオーバーを提供します。 これは Azure で完全に管理できる、高可用でスケーラブルなサービスを提供します。

### <a name="what-features-does-azure-front-door-service-support"></a>Azure Front Door Service はどのような機能をサポートしますか?

Azure Front Door Service は、動的サイト アクセラレーション (DSA)、SSL オフロードおよびエンド ツー エンド SSL、Web アプリケーション ファイアウォール、cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、無料の証明書と複数のドメイン管理などをサポートします。 サポートされる機能の一覧については、[Azure Front Door Service の概要](front-door-overview.md)を参照してください。

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Azure Front Door Service と Azure Application Gateway の違いは何ですか?

Front Door と Application Gateway は両方ともレイヤー 7 (HTTP/HTTPS) のロード バランサーであり、主な違いは、Front Door がグローバル サービスであるのに対し、Application Gateway はリージョン サービスであるということです。 Front Door はリージョン全体のさまざまなスケール ユニット/クラスター/スタンプのユニット間で負荷分散ができますが、Application Gateway は、スケール ユニット内にある、仮想マシン/コンテナーなどの間で負荷分散ができます。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Front Door の背後に Application Gateway をデプロイする必要はありますか?

Front Door の背後で Application Gateway を使用するべき主要なシナリオは次のとおりです。

- Front Door は、パス ベースの負荷分散をグローバル レベルでしか実行できません。仮想ネットワーク (VNET) 内でトラフィックをさらに負荷分散したい場合は、Application Gateway を使用する必要があります。
- Front Door は VM/コンテナー レベルで機能しないので、接続のドレインは実行できません。 ただし、Application Gateway を使用すると、接続のドレインを実行できます。 
- AFD の背後で Application Gateway を使用して、100% の SSL オフロードを実現し、その仮想ネットワーク (VNET) 内で HTTP 要求のみをルーティングすることができます。
- Front Door と Application Gateway は両方ともセッション アフィニティをサポートします。 Front Door は、後続のトラフィックをユーザー セッションから特定のリージョン内の同じクラスターまたはバックエンドに転送できますが、Application Gateway は、トラフィックをクラスター内の同じサーバーにアフィニタイズ (affinitize) できます。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Front Door の背後に Azure Load Balancer をデプロイできますか?

Azure Front Door Service は、トラフィックをルーティングする先のパブリック VIP または公開されている DNS 名が必要です。 Front Door の背後に Azure Load Balancer をデプロイすることは、一般的なユース ケースです。

### <a name="what-protocols-does-azure-front-door-service-support"></a>Azure Front Door Service はどのようなプロトコルをサポートしますか?

Azure Front Door Service は、HTTP、HTTPS、HTTP/2 をサポートします。

### <a name="how-does-azure-front-door-service-support-http2"></a>Azure Front Door Service は HTTP/2 をどのようにサポートしますか?

HTTP/2 プロトコルのサポートを利用できるのは、Azure Front Door Service に接続しているクライアントだけです。 バックエンド プール内のバックエンドへの通信は、HTTP/1.1 を介して実行されます。 HTTP/2 のサポートは既定で有効です。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>現在、バックエンド プールの一部としてどのようなリソースがサポートされますか?

バックエンド プールは、Storage、Web アプリ、Kubernetes インスタンス、またはパブリック接続があるその他の任意のカスタム ホスト名で構成できます。 Azure Front Door Service では、バックエンドがパブリック IP またはパブリックに解決可能な DNS ホスト名のいずれかで定義されている必要があります。 バックエンド プールのメンバーは、パブリック接続がある限り、複数のゾーンやリージョンにまたがって、または Azure の外部に存在することができます。

### <a name="what-regions-is-the-service-available-in"></a>このサービスは、どのリージョンで利用できますか?

Azure Front Door Service はグローバル サービスであり、特定の Azure リージョンに関連付けられていません。 Front Door を作成するときに指定する必要がある唯一の場所はリソース グループです。これは基本的にはリソース グループのメタデータを格納する場所を指定します。 Front Door リソース自体はグローバル リソースとして作成され、構成はすべて POP (ポイント オブ プレゼンス) にグローバルにデプロイされます。 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Azure Front Door Service 用の POP の場所とは何ですか?

Azure Front Door Service には、Microsoft からの Azure CDN と同じ POP (ポイント オブ プレゼンス) の場所のリストがあります。 POP の完全な一覧については、「[Azure CDN POP locations from Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)」(Microsoft 提供の Azure CDN の POP の場所) を参照してください。

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door Service はアプリケーション専用のデプロイメントですか? または、複数の顧客と共有されますか?

Azure Front Door Service は、グローバルに分散されたマルチ テナント サービスです。 そのため、Front Door のインフラストラクチャは、すべての顧客間で共有されます。 ただし、Front Door を作成することで、アプリケーションに必要な特定の構成は定義されます。 

### <a name="is-http-https-redirection-supported"></a>HTTP から HTTPS へのリダイレクトはサポートされていますか?

Front Door は現在、URL のリダイレクトをサポートしていません。

### <a name="in-what-order-are-routing-rules-processed"></a>どのような順序でルーティング ルールは処理されますか?

Front Door のルートには順序はなく、特定のルートは最適な一致に基づいて選択されます。 詳細については、「[Front Door が要求をルーティング規則と照合する方法](front-door-route-matching.md)」を参照してください。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>バックエンドへのアクセスを Azure Front Door Service のみにロックダウンするにはどうしたらよいですか?

Azure Front Door Service からのトラフィックのみを受け入れるように、バックエンドの IP ACL 化を構成することができます。 Azure Front Door Service のバックエンド IP 空間のみからの着信接続を受け入れるようにアプリケーションを制限できます。 [Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)との統合の方向で取り組んでいますが、今のところ IP 範囲は次のように表現しています。
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> バックエンド IP 空間は後で変更される可能性があります。ただし、変更を行う前には必ず [Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)との統合を行います。 変更または更新について知るために、[Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をサブスクライブすることをお勧めします。 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>エニーキャスト IP は、Front Door の有効期間を通じて変更できますか?

Front Door のフロントエンド エニーキャスト IP は、通常は変更すべきでなく、Front Door の有効期間を通じて静的のままにしておくことができます。 ただし、同じであることの**保証はありません**。 IP アドレスへの直接的な依存関係は存在しないようにしてください。  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure Front Door Service は静的または専用 IP アドレスをサポートしていますか?

いいえ、Azure Front Door Service は静的または専用のフロントエンド エニーキャスト IP アドレスを現在サポートしていません。 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure Front Door Service は x-forwarded-for ヘッダーをサポートしていますか?

はい、Azure Front Door Service は X-Forwarded-For、X-Forwarded-Host、および X-Forwarded-Proto ヘッダーをサポートしています。 X-Forwarded-For の場合、ヘッダーが既に存在していれば、Front Door はクライアント ソケット IP をそれに追加します。 そうでない場合、これは値としてクライアント ソケット IP アドレスを持つヘッダーを追加します。 X-Forwarded-Host と X-Forwarded-Proto の場合、値は上書きされます。

詳細については、「[Front Door がサポートする HTTP ヘッダー](front-door-http-headers-protocol.md)」を参照してください。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Azure Front Door Service のデプロイにはどのくらい時間がかかりますか? Front Door は更新中に動作しますか?

新しい Front Door の作成または既存の Front Door の更新は、グローバル デプロイの場合には約 3 ~ 5 分かかります。 つまり、約 3 ~ 5 分間で、Front Door の構成はすべての POP 間でグローバルにデプロイされます。

注 - カスタム SSL 証明書の更新は、グローバルにデプロイされるまで約 30 分かかります。

## <a name="configuration"></a>構成

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front Door は仮想ネットワーク内で負荷分散またはトラフィックのルーティングができますか?

Azure Front Door (AFD) には、トラフィックをルーティングするために、パブリック IP またはパブリックに解決可能な DNS 名が必要です。 そのため、答えは、AFD は仮想ネットワーク内でルーティングできないということになりますが、途中で Application Gateway または Azure Load Balancer を使用するとこのシナリオは解決されます。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Azure Front Door Service のさまざまなタイムアウトおよび制限とは何ですか?

[Azure Front Door Service のさまざまなタイムアウトおよび制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits)についてのすべてのドキュメントを参照してください。

## <a name="performance"></a>パフォーマンス

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Azure Front Door Service は高可用性とスケーラビリティをどのようにサポートしますか?

Azure Front Door Service は、アプリケーションのスケーラビリティのニーズに対応できる大容量を利用した、グローバルに分散されたマルチ テナント プラットフォームです。 Microsoft のグローバル ネットワークのエッジから提供される Front Door は、リージョン間またはさまざまなクラウド間でのアプリケーション全体または個別のマイクロサービスのフェールオーバーを可能にする、グローバル負荷分散機能を提供します。

## <a name="ssl-configuration"></a>SSL の構成

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Azure Front Door Service ではどの TLS バージョンがサポートされますか?

Front Door では、TLS バージョン 1.0、1.1、1.2 がサポートされます。 TLS 1.3 はまだサポートされていません。

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Azure Front Door Service ではどの証明書がサポートされますか?

Front Door のカスタム ドメインでコンテンツを安全に配信するために HTTPS プロトコルを有効にするには、Azure Front Door Service によって管理されている証明書を使用するか、または独自の証明書を使用することを選択できます。
Front Door マネージド オプションは、Digicert を介し、Front Door のキー コンテナーに格納される標準 SSL 証明書をプロビジョニングします。 独自の証明書を使用する場合、サポートされている CA から証明書をオンボードできます。証明書は、標準的な SSL、拡張検証証明書、またはワイルドカード証明書でも構いません。 自己署名証明書はサポートされていません。 [カスタム ドメインに対する HTTPS の有効化の方法](https://aka.ms/FrontDoorCustomDomainHTTPS)を説明します。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Azure Front Door Service でサポートされている最新の暗号スイートはどれですか?

Azure Front Door Service でサポートされている最新の暗号スイートは次のとおりです。

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front Door Service ではバックエンドへのトラフィックの再暗号化もサポートされていますか?

はい。Azure Front Door Service は SSL オフロードとエンド ツー エンド SSL をサポートしており、バックエンドへのトラフィックが再暗号化されます。 実際には、バックエンドへの接続はそのパブリック IP を介して行われるので、Front Door 転送プロトコルとして HTTPS を使用するように構成することをお勧めします。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>SSL プロトコルのバージョンを管理する SSL ポリシーを構成できますか?

いいえ、現在 Front Door は特定の TLS バージョンを拒否することをサポートしておらず、最小限の TLS バージョンを設定することもできません。 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Front Door は特定の暗号スイートのみをサポートするように構成できますか?

いいえ、Front Door を特定の暗号スイートに対して構成することはサポートされていません。 

## <a name="diagnostics-and-logging"></a>診断とログ記録

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>どのような種類のメトリックとログを Azure Front Door Service で使用できますか?

ログとその他の診断機能については、「[Monitoring metrics and logs for Front Door](front-door-diagnostics.md)」(Front Door のメトリックとログの監視) を参照してください。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診断ログにおける保持ポリシーとは何ですか?

診断ログはお客様のストレージ アカウントに送信され、お客様はログの優先順位に基づいて保持ポリシーを設定できます。 診断ログをイベント ハブまたは Azure Monitor ログに送信することもできます。 詳細については、「[Azure Front Door Service Diagnostics](front-door-diagnostics.md)」(Azure Front Door Service の診断) を参照してください。

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Azure Front Door Service の監査ログはどのように取得できますか?

Azure Front Door Service では監査ログを使用できます。 ポータルで、Front Door のメニュー ブレードの **[アクティビティ ログ]** をクリックして監査ログにアクセスします。 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Azure Front Door Service でアラートを設定できますか?

はい、Azure Front Door Service はアラートをサポートしません。 メトリックに関するアラートが構成されます。 

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。