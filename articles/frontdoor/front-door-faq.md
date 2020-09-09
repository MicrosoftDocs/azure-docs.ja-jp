---
title: Azure Front Door - よく寄せられる質問
description: このページでは、Azure Front Door に関してよく寄せられる質問の回答を示します
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2020
ms.author: duau
ms.openlocfilehash: 995b8ab77779f0d3b9e2260ea18aa13aa242db36
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399737"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Azure Front Door についてよく寄せられる質問

この記事では、Azure Front Door の機能に関する一般的な質問を回答します。 ご質問に対する回答がここで見つからない場合は、次のチャネルでお問い合わせください (上から順に)。

1. この記事のコメント セクション。
2. [Azure Front Door UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)。
3. **Microsoft のサポート:** 新しいサポート要求を作成するには、Azure Portal の **[ヘルプ]** タブで、 **[ヘルプとサポート]** ボタンを選択し、 **[新しいサポート要求]** を選択します。

## <a name="general"></a>全般

### <a name="what-is-azure-front-door"></a>Azure Front Door とは

Azure Front Door は、Application Delivery Network (ADN) をサービスとして提供することで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 これは動的サイト アクセラレーション (DSA) とグローバル負荷分散に、ほぼリアルタイムのフェールオーバーを提供します。 これは Azure で完全に管理できる、高可用でスケーラブルなサービスを提供します。

### <a name="what-features-does-azure-front-door-support"></a>Azure Front Door ではどのような機能がサポートされますか?

Azure Front Door では、動的サイト アクセラレーション (DSA)、TLS/SSL オフロードとエンド ツー エンド TLS、Web アプリケーション ファイアウォール、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、無料の証明書、複数ドメインの管理などがサポートされます。 サポートされる機能の一覧については、[Azure Front Door の概要](front-door-overview.md)に関する記事をご覧ください。

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure Front Door と Azure Application Gateway の違いは何ですか?

Front Door と Application Gateway は両方ともレイヤー 7 (HTTP/HTTPS) のロード バランサーであり、主な違いは、Front Door がグローバル サービスであるのに対し、Application Gateway はリージョン サービスであるということです。 Front Door はリージョン全体のさまざまなスケール ユニット/クラスター/スタンプのユニット間で負荷分散ができますが、Application Gateway は、スケール ユニット内にある、仮想マシン/コンテナーなどの間で負荷分散ができます。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Front Door の背後に Application Gateway をデプロイする必要はありますか?

Front Door の背後で Application Gateway を使用するべき主要なシナリオは次のとおりです。

- Front Door は、パス ベースの負荷分散をグローバル レベルでしか実行できません。仮想ネットワーク (VNET) 内でトラフィックをさらに負荷分散したい場合は、Application Gateway を使用する必要があります。
- Front Door は VM/コンテナー レベルで機能しないので、接続のドレインは実行できません。 ただし、Application Gateway を使用すると、接続のドレインを実行できます。 
- Front Door の内側で Application Gateway を使用すると、100% の TLS/SSL オフロードを実現し、その仮想ネットワーク (VNET) 内で HTTP 要求のみをルーティングできます。
- Front Door と Application Gateway は両方ともセッション アフィニティをサポートします。 Front Door は、後続のトラフィックをユーザー セッションから特定のリージョン内の同じクラスターまたはバックエンドに転送できますが、Application Gateway は、トラフィックをクラスター内の同じサーバーにアフィニタイズ (affinitize) できます。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Front Door の背後に Azure Load Balancer をデプロイできますか?

Azure Front Door では、トラフィックをルーティングする先のパブリック VIP または公開されている DNS 名が必要です。 Front Door の背後に Azure Load Balancer をデプロイすることは、一般的なユース ケースです。

### <a name="what-protocols-does-azure-front-door-support"></a>Azure Front Door ではどのようなプロトコルがサポートされますか?

Azure Front Door では、HTTP、HTTPS、HTTP/2 がサポートされます。

### <a name="how-does-azure-front-door-support-http2"></a>Azure Front Door では HTTP/2 がどのようにサポートされますか?

HTTP/2 プロトコルのサポートを利用できるのは、Azure Front Door に接続しているクライアントだけです。 バックエンド プール内のバックエンドへの通信は、HTTP/1.1 を介して実行されます。 HTTP/2 のサポートは既定で有効です。

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>現在、バックエンド プールの一部としてどのようなリソースがサポートされますか?

バックエンド プールは、Storage、Web アプリ、Kubernetes インスタンス、またはパブリック接続があるその他の任意のカスタム ホスト名で構成できます。 Azure Front Door では、バックエンドがパブリック IP またはパブリックに解決可能な DNS ホスト名のいずれかで定義されている必要があります。 バックエンド プールのメンバーは、パブリック接続がある限り、複数のゾーンやリージョンにまたがって、または Azure の外部に存在することができます。

### <a name="what-regions-is-the-service-available-in"></a>このサービスは、どのリージョンで利用できますか?

Azure Front Door はグローバル サービスであり、特定の Azure リージョンに関連付けられていません。 Front Door を作成するときに指定する必要がある唯一の場所はリソース グループです。これは基本的にはリソース グループのメタデータを格納する場所を指定します。 Front Door リソース自体はグローバル リソースとして作成され、構成はすべて POP (ポイント オブ プレゼンス) にグローバルにデプロイされます。 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Azure Front Door 用の POP の場所とは何ですか?

Azure Front Door には、Microsoft からの Azure CDN と同じ POP (ポイント オブ プレゼンス) の場所のリストがあります。 POP の完全な一覧については、「[Azure CDN POP locations from Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)」(Microsoft 提供の Azure CDN の POP の場所) を参照してください。

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door はアプリケーション専用のデプロイですか、それとも複数の顧客と共有されますか?

Azure Front Door は、グローバルに分散されたマルチ テナント サービスです。 そのため、Front Door のインフラストラクチャは、すべての顧客間で共有されます。 ただし、ユーザーは Front Door のプロファイルを作成することで、アプリケーションに必要な特定の構成を定義します。Front Door に加えられた変更は、他の Front Door 構成に影響を与えません。

### <a name="is-http-https-redirection-supported"></a>HTTP から HTTPS へのリダイレクトはサポートされていますか?

はい。 実際には、Azure Front Door では、ホスト、パス、およびクエリ文字列のリダイレクトに加えて、URL リダイレクトの一部がサポートされています。 [URL リダイレクト](front-door-url-redirect.md)の詳細を確認してください。 

### <a name="in-what-order-are-routing-rules-processed"></a>どのような順序でルーティング ルールは処理されますか?

Front Door のルートには順序はなく、特定のルートは最適な一致に基づいて選択されます。 詳細については、「[Front Door が要求をルーティング規則と照合する方法](front-door-route-matching.md)」を参照してください。

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>バックエンドへのアクセスを Azure Front Door のみにロックダウンするにはどうしたらよいですか?

特定の Front Door からのみトラフィックを受け入れるようにアプリケーションをロックダウンするには、バックエンドの IP ACL を設定してから、Front Door によって送信されるヘッダー "X-Azure-FDID" の特定の値に、バックエンド上のトラフィックを制限する必要があります。 以下に、これらの手順の詳細を示します。

- 使用するバックエンドが、Azure Front Door のバックエンド IP アドレス空間と Azure のインフラストラクチャ サービスからのトラフィックのみを受け入れるように IP ACL 処理を構成します。 バックエンドに対する ACL については、以下の IP の詳細を参照してください。
 
    - Front Door の IPv4 バックエンド IP アドレスの範囲については、「[Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)」の「*AzureFrontDoor.Backend*」セクションを参照してください。または、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)でサービス タグ *AzureFrontDoor.Backend* を使用することもできます。
    - サービス タグでカバーされている Front Door の **IPv6** バックエンド IP 空間は、Azure IP 範囲の JSON ファイルには記載されていません。 明示的な IPv6 アドレス範囲を探している場合、現在は `2a01:111:2050::/44` に制限されています
    - 仮想化されたホスト IP アドレスを通した Azure の[基本的なインフラストラクチャ サービス](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations): `168.63.129.16` および `169.254.169.254`

    > [!WARNING]
    > Front Door のバックエンド IP 空間は後で変更される可能性があります。ただし Microsoft はその前に、[Azure IP 範囲およびサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)との統合が確実に行われるようにします。 変更または更新について知るために、[Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をサブスクライブすることをお勧めします。

-    API バージョン `2020-01-01` 以降を使用して、Front Door で GET 操作を実行します。 API 呼び出しで、`frontdoorID` フィールドを探します。 Front Door からバックエンドに送信された受信ヘッダー "**X-Azure-FDID**" を、フィールド `frontdoorID` の値でフィルター処理します。 `Front Door ID` の値は、Front Door ポータル ページの [概要] セクションでも確認できます。 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>エニーキャスト IP は、Front Door の有効期間を通じて変更できますか?

Front Door のフロントエンド エニーキャスト IP は、通常は変更すべきでなく、Front Door の有効期間を通じて静的のままにしておくことができます。 ただし、同じであることの**保証はありません**。 IP アドレスへの直接的な依存関係は存在しないようにしてください。

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure Front Door では静的または専用 IP アドレスがサポートされていますか?

いいえ、Azure Front Door では静的または専用のフロントエンド エニーキャスト IP アドレスは現在サポートされていません。 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front Door では x-forwarded-for ヘッダーはサポートされていますか?

はい、Azure Front Door では X-Forwarded-For、X-Forwarded-Host、および X-Forwarded-Proto ヘッダーがサポートされています。 X-Forwarded-For の場合、ヘッダーが既に存在していれば、Front Door はクライアント ソケット IP をそれに追加します。 そうでない場合、これは値としてクライアント ソケット IP アドレスを持つヘッダーを追加します。 X-Forwarded-Host と X-Forwarded-Proto の場合、値は上書きされます。

詳細については、「[Front Door がサポートする HTTP ヘッダー](front-door-http-headers-protocol.md)」を参照してください。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Azure Front Door のデプロイにはどのくらい時間がかかりますか? Front Door は更新中に動作しますか?

新しい Front Door の作成または既存の Front Door の更新は、グローバル デプロイの場合には約 3 ~ 5 分かかります。 つまり、約 3 ~ 5 分間で、Front Door の構成はすべての POP 間でグローバルにデプロイされます。

注 - カスタム TLS/SSL 証明書の更新は、グローバルにデプロイされるまでに約 30 分かかります。

ルートやバックエンド プールなどに対する更新はシームレスであり、ダウンタイムは発生しません (新しい構成が正しい場合)。 また、"AFD マネージド" から "独自の証明書の使用" に、またはその逆に切り替える場合を除き、証明書の更新もアトミックであり、停止が発生することはありません。


## <a name="configuration"></a>構成

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front Door は仮想ネットワーク内で負荷分散またはトラフィックのルーティングができますか?

Azure Front Door (AFD) には、トラフィックをルーティングするために、パブリック IP またはパブリックに解決可能な DNS 名が必要です。 そのため、答えは、AFD は仮想ネットワーク内でルーティングできないということになりますが、途中で Application Gateway または Azure Load Balancer を使用するとこのシナリオは解決されます。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure Front Door でのさまざまなタイムアウトおよび制限とは何ですか?

[Azure Front Door のさまざまなタイムアウトおよび制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)についてのすべてのドキュメントを参照してください。

## <a name="performance"></a>パフォーマンス

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure Front Door では高可用性とスケーラビリティはどのようにサポートされていますか?

Azure Front Door は、アプリケーションのスケーラビリティのニーズに対応できる大容量を利用した、グローバルに分散されたマルチ テナント プラットフォームです。 Microsoft のグローバル ネットワークのエッジから提供される Front Door は、リージョン間またはさまざまなクラウド間でのアプリケーション全体または個別のマイクロサービスのフェールオーバーを可能にする、グローバル負荷分散機能を提供します。

## <a name="tls-configuration"></a>TLS の構成

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure Front Door ではどの TLS バージョンがサポートされますか?

2019 年 9 月以降に作成されたすべての Front Door プロファイルでは、既定の最小値として TLS 1.2 が使用されます。

Front Door では、TLS バージョン 1.0、1.1、1.2 がサポートされます。 TLS 1.3 はまだサポートされていません。

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure Front Door ではどの証明書がサポートされますか?

Front Door のカスタム ドメインでコンテンツを安全に配信するために HTTPS プロトコルを有効にするには、Azure Front Door によって管理されている証明書を使用するか、または独自の証明書を使用することを選択できます。
Front Door のマネージド オプションは、Digicert 経由で、かつ Front Door の Key Vault に格納されている標準の TLS/SSL 証明書をプロビジョニングします。 独自の証明書を使用することを選択した場合は、サポートされている CA から証明書をオンボードできます。これは標準の TLS 証明書、Extended Validation 証明書、ワイルドカード証明書のいずれであってもかまいません。 自己署名証明書はサポートされていません。 [カスタム ドメインに対する HTTPS の有効化の方法](https://aka.ms/FrontDoorCustomDomainHTTPS)を説明します。

### <a name="does-front-door-support-autorotation-of-certificates"></a>Front Door は証明書のオートローテーションをサポートしていますか?

Front Door が管理する証明書オプションの場合、証明書は Front Door によってオートローテーションされます。 Front Door が管理する証明書を使用していて、証明書の有効期限日が 60 日未満であることが確認された場合は、サポート チケットを送信します。
</br>独自のカスタム TLS/SSL 証明書の場合、オートローテーションはサポートされません。 特定のカスタム ドメインに対して初めて設定が行われる方法と同様に、Front Door に Key Vault 内の適切な証明書のバージョンを参照させて、Front Door のサービス プリンシパルが引き続き Key Vault にアクセスできることを確認する必要があります。 Front Door による、この更新された証明書ロールアウト処理はアトミックであり、証明書のサブジェクト名や SAN が変更されない限り、運用環境への影響は一切発生しません。

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure Front Door でサポートされている最新の暗号スイートはどれですか?

TLS1.2 では、次の暗号スイートがサポートされています。 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

TLS1.0 または TLS1.1 が有効になっているカスタム ドメインを使用している場合は、次の暗号スイートがサポートされます。

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
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>TLS プロトコルのバージョンを制御するように TLS ポリシーを構成できますか?

Azure portal または [Azure REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) を使用して、カスタム ドメインの HTTPS の設定で、Azure Front Door での最低の TLS バージョンを構成できます。 現時点では、1.0 と 1.2 のどちらかを選択できます。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Front Door は特定の暗号スイートのみをサポートするように構成できますか?

いいえ、Front Door を特定の暗号スイートに対して構成することはサポートされていません。 ただし、証明機関 (Verisign、Entrust、Digicert など) から独自のカスタム TLS/SSL 証明書を取得し、生成される証明書に対して特定の暗号スイートがマークされるようにすることができます。 

### <a name="does-front-door-support-ocsp-stapling"></a>Front Door では OCSP スタップリングはサポートされていますか?

はい。Front Door では OCSP スタップリングが既定でサポートされており、構成は必要ありません。

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front Door ではバックエンドへのトラフィックの再暗号化もサポートされていますか?

はい。Azure Front Door では、バックエンドへのトラフィックを再暗号化する TLS/SSL オフロードやエンド ツー エンド TLS がサポートされています。 実際には、バックエンドへの接続はそのパブリック IP を介して行われるので、Front Door 転送プロトコルとして HTTPS を使用するように構成することをお勧めします。

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Front Door では、HTTPS 接続のバックエンドでの自己署名証明書はサポートされていますか?

いいえ。Front Door では自己署名証明書はサポートされておらず、両方に制限が適用されます。

1. **バックエンド**: HTTPS または HTTPS 正常性プローブとしてトラフィックを転送するとき、またはキャッシュが有効になっているルーティング規則で配信元からキャッシュに格納するときは、自己署名証明書を使用できません。
2. **フロントエンド**: カスタム ドメインで HTTPS を有効にするために独自のカスタム TLS/SSL 証明書を使用している場合は、自己署名証明書を使用できません。

### <a name="why-is-https-traffic-to-my-backend-failing"></a>バックエンドへの HTTPS トラフィックが失敗するのはなぜですか?

正常性プローブか転送要求かに関係なく、バックエンドに正常に HTTPS 接続した場合、HTTPS トラフィックが失敗する原因は 2 つあります。

1. **証明書のサブジェクト名の不一致**: HTTPS 接続の場合、Front Door では、バックエンドからバックエンドのホスト名と一致するサブジェクト名を持つ有効な CA からの証明書が提示されることを前提としています。 例として、バックエンドのホスト名が `myapp-centralus.contosonews.net` に設定されており、TLS ハンドシェイク中にバックエンドが提供する証明書のサブジェクト名に `myapp-centralus.contosonews.net` も `*myapp-centralus*.contosonews.net` も含まれていない場合、Front Door はその接続を拒否してエラーを生成します。 
    1. **解決策**:コンプライアンスの観点からは推奨されませんが、Front Door で証明書のサブジェクト名のチェックを無効にすることで、このエラーを回避できます。 これは、Azure portal の [設定]、および API の BackendPoolsSettings で設定できます。
2. **無効な CA からのバックエンド ホスティング証明書**: Front Door のバックエンドでは、[有効な CA](/azure/frontdoor/front-door-troubleshoot-allowed-ca) からの証明書のみを使用できます。 内部 CA からの証明書または自己署名証明書は許可されません。

## <a name="diagnostics-and-logging"></a>診断とログ記録

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>どのような種類のメトリックとログを Azure Front Door で使用できますか?

ログとその他の診断機能については、「[Monitoring metrics and logs for Front Door](front-door-diagnostics.md)」(Front Door のメトリックとログの監視) を参照してください。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診断ログにおける保持ポリシーとは何ですか?

診断ログはお客様のストレージ アカウントに送信され、お客様はログの優先順位に基づいて保持ポリシーを設定できます。 診断ログをイベント ハブまたは Azure Monitor ログに送信することもできます。 詳細については、[Azure Front Door の診断](front-door-diagnostics.md)に関するページを参照してください。

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Azure Front Door の監査ログはどのように取得できますか?

Azure Front Door では監査ログを使用できます。 ポータルで、Front Door のメニュー ブレードの **[アクティビティ ログ]** をクリックして監査ログにアクセスします。 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Azure Front Door でアラートを設定できますか?

はい、Azure Front Door ではアラートがサポートされています。 メトリックに関するアラートが構成されます。 

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
