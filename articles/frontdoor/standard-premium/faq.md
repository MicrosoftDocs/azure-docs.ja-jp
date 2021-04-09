---
title: 'Azure Front Door: よく寄せられる質問'
description: このページでは、Azure Front Door Standard/Premium に関してよく寄せられる質問の回答をご紹介します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 6f6d71dec9726f009ab9a56e0a49ba21f5d218fd
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181025"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) についてよく寄せられる質問

この記事では、Azure Front Door の機能に関する一般的な質問を回答します。

## <a name="general"></a>全般

### <a name="what-is-azure-front-door"></a>Azure Front Door とは

Azure Front Door は、インテリジェントな脅威保護が備わっている、セキュリティで保護された高速で信頼性が高い最新のクラウド CDN です。 インテリジェントな脅威保護により、グローバルなハイパースケール アプリケーション、API、Web サイト、クラウド サービスに対し、静的および動的なコンテンツ アクセラレーション、グローバル負荷分散、強化されたセキュリティを提供します。

### <a name="what-features-does-azure-front-door-support"></a>Azure Front Door ではどのような機能がサポートされますか?

Azure Front Door では以下がサポートされています。

* 静的コンテンツと動的アプリケーション アクセラレーションの両方
* TLS/SSL オフロードとエンド ツー エンド TLS
* Web アプリケーション ファイアウォール
* Cookie ベースのセッション アフィニティ
* URL パスベースのルーティング
* 無料の証明書と複数のドメイン管理

サポートされる機能の一覧については、[Azure Front Door の概要](overview.md)に関する記事をご覧ください。

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure Front Door と Azure Application Gateway の違いは何ですか?

Front Door と Application Gateway は両方ともレイヤー 7 (HTTP/HTTPS) のロード バランサーであり、主な違いは、Front Door がグローバル サービスである点です。 Application Gateway はリージョン サービスです。 Front Door はリージョン全体のさまざまなスケール ユニット、クラスター、スタンプのユニット間で負荷分散ができますが、Application Gateway は、スケール ユニット内にある、仮想マシンやコンテナーの間で負荷分散ができます。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Front Door の背後に Application Gateway をデプロイする必要はありますか?

Front Door の背後で Application Gateway を使用するべき主要なシナリオは次のとおりです。

* Front Door は、パスベースの負荷分散をグローバル レベルでしか実行できません。仮想ネットワーク (VNET) 内でトラフィックをさらに負荷分散したい場合は、Application Gateway を使用する必要があります。
* Front Door は VM やコンテナー レベルで機能しないので、接続のドレインは実行できません。 ただし、Application Gateway を使用すると、接続のドレインを実行できます。 
* Front Door の内側で Application Gateway を使用すると、100% の TLS/SSL オフロードを実現し、その仮想ネットワーク (VNET) 内で HTTP 要求のみをルーティングできます。
* Front Door と Application Gateway は両方ともセッション アフィニティをサポートします。 Front Door は、後続のトラフィックをユーザー セッションから特定のリージョン内の同じクラスターまたはバックエンドに転送できます。 Application Gateway は、トラフィックをクラスター内の同じサーバーに関連付けることができます。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Front Door の背後に Azure Load Balancer をデプロイできますか?

Azure Front Door では、トラフィックをルーティングする先のパブリック VIP または公開されている DNS 名が必要です。 Front Door の背後に Azure Load Balancer をデプロイすることは、一般的なユース ケースです。

### <a name="what-protocols-does-azure-front-door-support"></a>Azure Front Door ではどのようなプロトコルがサポートされますか?

Azure Front Door では、HTTP、HTTPS、HTTP/2 がサポートされます。

### <a name="how-does-azure-front-door-support-http2"></a>Azure Front Door では HTTP/2 がどのようにサポートされますか?

HTTP/2 プロトコルのサポートを利用できるのは、Azure Front Door に接続しているクライアントだけです。 バックエンド プール内のバックエンドへの通信は、HTTP/1.1 を介して実行されます。 HTTP/2 のサポートは既定で有効です。

### <a name="what-resources-are-supported-today-as-part-of-origin-group"></a>現在、配信元グループの一部としてどのようなリソースがサポートされていますか?

配信元グループは、Storage、Web アプリ、Kubernetes インスタンス、またはパブリック接続があるその他の任意のカスタム ホスト名で構成できます。 Azure Front Door では、配信元がパブリック IP またはパブリックに解決可能な DNS ホスト名のいずれかで定義されている必要があります。 配信元グループのメンバーは、パブリック接続がある限り、複数のゾーンやリージョンにまたがって、または Azure の外部に存在することができます。

### <a name="what-regions-is-the-service-available-in"></a>このサービスは、どのリージョンで利用できますか?

Azure Front Door はグローバル サービスであり、特定の Azure リージョンには関連付けられていません。 Front Door を作成するときに指定する必要がある唯一の場所はリソース グループです。 この場所は基本的にはリソース グループのメタデータを格納する場所を指定しています。 Front Door リソース自体はグローバル リソースとして作成され、構成はすべて POP (ポイント オブ プレゼンス) にグローバルにデプロイされます。 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Azure Front Door 用の POP の場所とは何ですか?

Azure Front Door には、Microsoft からの Azure CDN と同じ POP (ポイント オブ プレゼンス) の場所のリストがあります。 POP の完全な一覧については、「[Azure CDN POP locations from Microsoft](../../cdn/cdn-pop-locations.md)」(Microsoft 提供の Azure CDN の POP の場所) を参照してください。

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door はアプリケーション専用のデプロイですか、それとも複数の顧客と共有されますか?

Azure Front Door は、グローバルに分散されたマルチ テナント サービスです。 Front Door のインフラストラクチャは、すべての顧客間で共有されます。 Front Door プロファイルを作成することで、アプリケーションに必要な特定の構成が定義されます。 Front Door に加えられた変更は、他の Front Door の構成には影響しません。

### <a name="is-http-https-redirection-supported"></a>HTTP から HTTPS へのリダイレクトはサポートされていますか?

はい。 実際には、Azure Front Door では、ホスト、パス、クエリ文字列のリダイレクトに加えて、URL リダイレクトの一部がサポートされています。 [URL リダイレクト](concept-rule-set-url-redirect-and-rewrite.md)の詳細を確認してください。 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>バックエンドへのアクセスを Azure Front Door のみにロックダウンするにはどうしたらよいですか?

特定の Front Door インスタンスからのトラフィックのみを受け入れるようにアプリケーションを制限する最善の方法は、プライベート エンドポイント経由でアプリケーションを公開することです。 Front Door とアプリケーション間のネットワーク トラフィックは、Vnet および Microsoft バックボーン ネットワーク上の Private Link を経由することで、パブリック インターネットに晒されないようになっています。

詳細については、[Private Link を使用して Front Door の配信元をセキュリティで保護する](concept-private-link.md)方法に関するページを参照してください。  

別の方法で特定の Front Door からのトラフィックのみを受け入れるようにアプリケーションを制限するには、バックエンド用の IP ACL を設定する必要があります。 その後、バックエンドのトラフィックを、Front Door によって送信されたヘッダー 'X-Azure-FDID' の特定の値に制限します。 以下に、これらの手順の詳細を示します。

* 使用するバックエンドが、Azure Front Door のバックエンド IP アドレス空間と Azure のインフラストラクチャ サービスからのトラフィックのみを受け入れるように IP ACL 処理を構成します。 バックエンドに対する ACL については、以下の IP の詳細を参照してください。
 
    * Front Door の IPv4 バックエンド IP アドレスの範囲については、[Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)に関するページの「*AzureFrontDoor.Backend*」セクションを参照してください。 または、[ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md#security-rules)でサービス タグ *AzureFrontDoor.Backend* を使用することもできます。
    * 仮想化されたホスト IP アドレスを通した Azure の[基本的なインフラストラクチャ サービス](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations): `168.63.129.16` および `169.254.169.254`。

    > [!WARNING]
    > Front Door のバックエンド IP 空間は後で変更される可能性があります。ただし Microsoft はその前に、[Azure IP 範囲およびサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)との統合が確実に行われるようにします。 変更または更新について知るために、[Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をサブスクライブすることをお勧めします。

* API バージョン `2020-01-01` 以降を使用して、Front Door で GET 操作を実行します。 API 呼び出しで、`frontdoorID` フィールドを探します。 Front Door からバックエンドに送信された受信ヘッダー "**X-Azure-FDID**" を、フィールド `frontdoorID` の値でフィルター処理します。 `Front Door ID` の値は、Front Door ポータル ページの [概要] セクションでも確認できます。 

* バックエンド Web サーバーでルール フィルターを適用して、結果の "X-Azure-FDID" ヘッダー値に基づいてトラフィックを制限します。

  [Microsoft インターネット インフォメーション サービス (IIS)](https://www.iis.net/) の例を次に示します。

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>エニーキャスト IP は、Front Door の有効期間を通じて変更できますか?

Front Door のフロントエンド エニーキャスト IP は、通常は変更すべきでなく、Front Door の有効期間を通じて静的のままにしておくことができます。 ただし、同じであることの **保証はありません**。 IP アドレスへの直接的な依存関係は存在しないようにしてください。

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure Front Door では静的または専用 IP アドレスがサポートされていますか?

いいえ、Azure Front Door では静的または専用のフロントエンド エニーキャスト IP アドレスは現在サポートされていません。 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front Door では x-forwarded-for ヘッダーはサポートされていますか?

はい、Azure Front Door では X-Forwarded-For、X-Forwarded-Host、および X-Forwarded-Proto ヘッダーがサポートされています。 X-Forwarded-For の場合、ヘッダーが既に存在していれば、Front Door はクライアント ソケット IP をそれに追加します。 そうでない場合、これは値としてクライアント ソケット IP アドレスを持つヘッダーを追加します。 X-Forwarded-Host と X-Forwarded-Proto の場合、値は上書きされます。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Azure Front Door のデプロイにはどのくらい時間がかかりますか? Front Door は更新中に動作しますか?

新しい Front Door の作成または既存の Front Door の更新は、グローバル デプロイの場合には約 3 ~ 5 分かかります。 つまり、約 3 ~ 5 分間で、Front Door の構成はすべての POP 間でグローバルにデプロイされます。

注 - カスタム TLS/SSL 証明書の更新は、グローバルにデプロイされるまでに約 30 分かかります。

ルートやバックエンド プールに対する更新はシームレスであり、ダウンタイムは発生しません (新しい構成が正しい場合)。 また、"Azure Front Door マネージド" から "独自の証明書の使用" に、またはその逆に切り替える場合を除き、証明書の停止が発生することはありません。


## <a name="configuration"></a>構成

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front Door は仮想ネットワーク内で負荷分散またはトラフィックのルーティングができますか?

Azure Front Door (AFD) には、トラフィックをルーティングするために、パブリック IP またはパブリックに解決可能な DNS 名が必要です。 Azure Front Door は、仮想ネットワーク内のリソースに直接ルーティングすることはできません。 この問題は、パブリック IP を持つ Application Gateway または Azure Load Balancer を使用することで解決できます。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure Front Door でのさまざまなタイムアウトおよび制限とは何ですか?

[Azure Front Door のさまざまなタイムアウトおよび制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)についてのすべてのドキュメントを参照してください。

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Front Door ルール エンジンに追加された後、ルールが有効になるまでにどれくらいの時間がかかりますか?

ルール エンジンの構成は、更新が完了するまでに約 10 分から 15 分かかります。 更新が完了するとすぐにルールが有効になります。 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Azure CDN を Front Door プロファイルの背後に構成したり、Front Door を Azure CDN の背後に構成したりすることはできますか?

Azure Front Door と Azure CDN を同時に構成することはできません。これは、どちらのサービスでも、要求に応答するときに同じ Azure エッジ サイトが使用されているためです。 

## <a name="performance"></a>パフォーマンス

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure Front Door では高可用性とスケーラビリティはどのようにサポートされていますか?

Azure Front Door は、アプリケーションのスケーラビリティのニーズに対応できる膨大な容量を持つ、グローバルに分散されたマルチ テナント プラットフォームです。 Microsoft のグローバル ネットワークのエッジから提供される Front Door は、リージョン間またはさまざまなクラウド間でのアプリケーション全体または個別のマイクロサービスのフェールオーバーを可能にする、グローバル負荷分散機能を提供します。

## <a name="tls-configuration"></a>TLS の構成

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure Front Door ではどの TLS バージョンがサポートされますか?

2019 年 9 月以降に作成されたすべての Front Door プロファイルでは、既定の最小値として TLS 1.2 が使用されます。

Front Door では、TLS バージョン 1.0、1.1、1.2 がサポートされます。 TLS 1.3 はまだサポートされていません。

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure Front Door ではどの証明書がサポートされますか?

Front Door のカスタム ドメインで HTTPS プロトコルを有効にするには、Azure Front Door によって管理される証明書を使用するか、または独自の証明書を使用できます。
Front Door のマネージド オプションは、Digicert 経由で、かつ Front Door の Key Vault に格納されている標準の TLS/SSL 証明書をプロビジョニングします。 独自の証明書を使用することを選択した場合は、サポートされている CA から証明書をオンボードできます。これは標準の TLS 証明書、Extended Validation 証明書、ワイルドカード証明書のいずれであってもかまいません。 自己署名証明書はサポートされていません。

### <a name="does-front-door-support-autorotation-of-certificates"></a>Front Door は証明書のオートローテーションをサポートしていますか?

Front Door が管理する証明書オプションの場合、証明書は Front Door によってオートローテーションされます。 Front Door によって管理される証明書を使用していて、証明書の有効期限日が 60 日未満である場合は、サポート チケットを送信します。

独自のカスタム TLS/SSL 証明書の場合、オートローテーションはサポートされません。 特定のカスタム ドメインに対して初めて設定が行われる方法と同様に、Front Door に Key Vault 内の適切な証明書のバージョンを参照させる必要があります。 この際、Front Door のサービス プリンシパルが Key Vault にアクセスできていることを確認してください。 証明書のサブジェクト名や SAN が変更されていない限り、Front Door による、この更新された証明書ロールアウト処理によって運用環境のダウンタイムが引き起されることはありません。

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

Azure portal または [Azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) を使用して、カスタム ドメインの HTTPS の設定から Azure Front Door に TLS の最小バージョンを構成できます。 現時点では、1.0 と 1.2 のどちらかを選択できます。

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Front Door は特定の暗号スイートのみをサポートするように構成できますか?

いいえ、Front Door を特定の暗号スイートに対して構成することはサポートされていません。 証明機関 (Verisign、Entrust、Digicert など) から独自のカスタム TLS/SSL 証明書を取得することはできます。 その後、生成する証明書に対して特定の暗号スイートがマークされるようにすることができます。 

### <a name="does-front-door-support-ocsp-stapling"></a>Front Door では OCSP スタップリングはサポートされていますか?

はい。Front Door では OCSP スタップリングが既定でサポートされており、構成は必要ありません。

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front Door ではバックエンドへのトラフィックの再暗号化もサポートされていますか?

はい。Azure Front Door では、バックエンドへのトラフィックを再暗号化する TLS/SSL オフロードやエンド ツー エンド TLS がサポートされています。 バックエンドへの接続はそのパブリック IP を介して行われるので、転送プロトコルとして HTTPS を使用するように Front Door を構成することをお勧めします。

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Front Door では、HTTPS 接続のバックエンドでの自己署名証明書はサポートされていますか?

いいえ。Front Door では自己署名証明書はサポートされておらず、両方に制限が適用されます。

* **バックエンド**: HTTPS または HTTPS 正常性プローブとしてトラフィックを転送するとき、またはキャッシュが有効になっているルーティング規則で配信元からキャッシュに格納するときは、自己署名証明書を使用できません。
* **フロントエンド**: カスタム ドメインで HTTPS を有効にするために独自のカスタム TLS/SSL 証明書を使用している場合は、自己署名証明書を使用できません。

### <a name="why-is-https-traffic-to-my-backend-failing"></a>バックエンドへの HTTPS トラフィックが失敗するのはなぜですか?

正常性プローブか転送要求かに関係なく、バックエンドに正常に HTTPS 接続した場合、HTTPS トラフィックが失敗する原因は 2 つあります。

* **証明書のサブジェクト名の不一致**: HTTPS 接続の場合、Front Door では、バックエンドからバックエンドのホスト名と一致するサブジェクト名を持つ有効な CA からの証明書が提示されることを前提としています。 例えば、バックエンドのホスト名が `myapp-centralus.contosonews.net` に設定されており、TLS ハンドシェイク中にバックエンドが提供する証明書のサブジェクト名に `myapp-centralus.contosonews.net` や `*myapp-centralus*.contosonews.net` が含まれていないなどです。 Front Door によって接続が拒否され、エラーが生成されます。 
    * **解決策**: コンプライアンスの観点から推奨はされませんが、Front Door で証明書のサブジェクト名のチェックを無効にすることで、このエラーを回避できます。 このオプションは、Azure portal の [設定]、および API の BackendPoolsSettings にあります。
* **無効な CA からのバックエンド ホスティング証明書**: Front Door のバックエンドでは、[有効な証明機関](troubleshoot-allowed-certificate-authority.md)からの証明書のみを使用できます。 内部 CA からの証明書または自己署名証明書は許可されません。

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Azyre Front Door でクライアント/相互認証を使用できますか?

いいえ。 TLS 1.2 をサポートしている Azure Front Door は [RFC 5246](https://tools.ietf.org/html/rfc5246) でクライアント/相互認証を導入していますが、現時点で Azure Front Door はクライアント/相互認証をサポートしていません。

## <a name="diagnostics-and-logging"></a>診断とログ記録

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>どのような種類のメトリックとログを Azure Front Door で使用できますか?

ログとその他の診断機能については、「Monitoring metrics and logs for Front Door」(Front Door のメトリックとログの監視) を参照してください。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診断ログにおける保持ポリシーとは何ですか?

診断ログはお客様のストレージ アカウントに送信され、お客様はログの優先順位に基づいて保持ポリシーを設定できます。 診断ログをイベント ハブまたは Azure Monitor ログに送信することもできます。 詳細については、[Azure Front Door のログ](how-to-logs.md)に関するページを参照してください。

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Azure Front Door の監査ログはどのように取得できますか?

Azure Front Door では監査ログを使用できます。 ポータルで、Front Door のメニュー ページの **[アクティビティ ログ]** を選択して監査ログにアクセスします。 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Azure Front Door でアラートを設定できますか?

はい、Azure Front Door ではアラートがサポートされています。 メトリックに関するアラートが構成されます。 

## <a name="next-steps"></a>次のステップ

[Front Door Standard/Premium を作成する](create-front-door-portal.md)方法について学習します。
