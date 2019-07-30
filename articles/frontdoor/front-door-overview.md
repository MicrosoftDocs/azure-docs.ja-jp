---
title: Azure Front Door Service | Microsoft Docs
description: この記事では、Azure Front Door の概要を示します。 実際のアプリケーションのユーザー トラフィックを負荷分散するための選択肢として適切かどうかを見極めましょう。
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 20cfcea4a8b58c1c01a7c710163b7320ff96d65c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330828"
---
# <a name="what-is-azure-front-door-service"></a>Azure Front Door Service とは
Azure Front Door Service では、高可用性のために最大限のパフォーマンスと即時グローバル フェイルオーバーを最適化することで、Web トラフィックのグローバル ルーティングを定義、管理、および監視することができます。 Front Door を使用すると、グローバル (複数リージョン) のコンシューマーや企業のアプリケーションを、Azure を使用して世界中のユーザーに発信する、堅牢で高性能なパーソナライズされた最新のアプリケーション、API、およびコンテンツに変換することができます。

Front Door は、レイヤー 7 または HTTP/HTTPS 層で動作し、スプリット TCP と Microsoft のグローバル ネットワークでエニーキャスト プロトコルを使用してグローバル接続を向上させます。 そのため、構成でルーティング方法を選択することで、Front Door がクライアント要求を最も高速で最も使用可能なアプリケーション バックエンドにルーティングすることを保証できます。 アプリケーション バックエンドは、Azure の内部または外部でホストされている、インターネットに公開されたサービスです。 Front Door には、さまざまなアプリケーション ニーズと自動フェールオーバー モデルに対応する、さまざまな[トラフィック ルーティング方法](front-door-routing-methods.md)と[バックエンドの正常性監視オプション](front-door-health-probes.md)が用意されています。 [Traffic Manager](../traffic-manager/traffic-manager-overview.md) と同様、Front Door は Azure リージョン全体の障害などの障害に対応します。

>[!NOTE]
> Azure では、ユーザーのシナリオのためにフル マネージドの負荷分散ソリューションのスイートが提供されます。 DNS ベースのグローバルなルーティングが必要で、トランスポート層セキュリティ (TLS) プロトコル終端 ("SSL オフロード") または HTTP/HTTPS 要求によるアプリケーション層の処理に対する要件が**ない**場合は、「[Traffic Manager](../traffic-manager/traffic-manager-overview.md)」をご覧ください。 リージョン内のサーバー間で負荷分散が必要な場合は、アプリケーション層については [Application Gateway](../application-gateway/application-gateway-introduction.md) に関するページ、ネットワーク層については [Load Balancer](../load-balancer/load-balancer-overview.md) を確認してください。 実際のエンド ツー エンドのシナリオでは、必要に応じてこれらのソリューションを組み合わせると役に立つことがあります。

Front Door には、次の機能が含まれています。

## <a name="accelerate-application-performance"></a>アプリケーションのパフォーマンスの高速化
Front Door は、スプリット TCP ベースのエニーキャスト プロトコルを使用して、エンド ユーザーが最も近い Front Door POP (point of presence) にすぐに接続されるようにします。 Front Door POP からアプリケーション バックエンドに接続するために Microsoft のグローバル ネットワークを使用すると、パフォーマンスを維持しながら高可用性と信頼性を確保できます。 バックエンドへのこの接続は、最も短いネットワーク待機時間にも基づいています。 [スプリット TCP](front-door-routing-architecture.md#splittcp) や[エニーキャスト プロトコル](front-door-routing-architecture.md#anycast)のような Front Door ルーティング手法についての詳細を確認してください。

## <a name="increase-application-availability-with-smart-health-probes"></a>スマート正常性プローブによるアプリケーションの可用性の向上

Front Door は、そのスマート正常性プローブを使用し、バックエンドの待機時間と可用性の両方を監視し、バックエンドがダウンしたときに即時自動フェールオーバーを実現して、重要なアプリケーションの高可用性を実現します。 そのため、ダウンタイムを発生させることなく、アプリケーションで定期的なメンテナンスを実行することができます。 メンテナンス中は、Front Door がトラフィックを別のバックエンドに送信します。

## <a name="url-based-routing"></a>URL ベースのルーティング
URL パス ベースのルーティングを使用すると、要求の URL パスに基づいてバックエンド プールにトラフィックをルーティングできます。 1 つのシナリオとして、異なる種類のコンテンツの要求を、異なるバックエンド プールにルーティングします。

たとえば、`http://www.contoso.com/users/*` の要求は UserProfilePool にルーティングされ、`http://www.contoso.com/products/*` は ProductInventoryPool にルーティングされます。  Front Door では、最も一致するアルゴリズムを使用してさらに複雑なルート照合のシナリオも可能なため、一致するパスのパターンがない場合は `http://www.contoso.com/*` の既定のルーティング規則が選択され、トラフィックは既定の包括的なルーティング規則に送信されます。 詳細は、[ルートの照合](front-door-route-matching.md)に関するページを参照してください。

## <a name="multiple-site-hosting"></a>複数サイトのホスティング
複数サイトのホストにより、同じ Front Door 構成に複数の Web サイトを構成することができます。 この機能を使用すると、さまざまな Web サイトを 1 つの Front Door 構成に追加することによって、デプロイのための効率的なトポロジを構成できます。 Azure Front Door Service は、アプリケーションのアーキテクチャに基づいて、各 web サイトをその独自のバックエンド プールに送信するか、さまざまな Web サイトが同じバックエンド プールに送信されるように構成することができます。 たとえば、Front Door では、ImagePool と VideoPool という 2 つのバックエンド プールから、`images.contoso.com` と `videos.contoso.com` のトラフィックを処理することができます。 あるいは、両方のフロント エンド ホストを、MediaPool と呼ばれる 1 つのバックエンド プールにトラフィックを送信するように構成できます。

同様に、`www.contoso.com` と `www.fabrikam.com` という 2 つの異なるドメインを、同じ Front Door 上に構成することができます。

## <a name="session-affinity"></a>セッション アフィニティ
Cookie ベースのセッション アフィニティ機能は、同じアプリケーション バックエンド上にユーザー セッションを保持する場合に便利です。 Front Door で管理される Cookie を使用することで、ユーザー セッションからの後続のトラフィックは、処理のために同じアプリケーション バックエンドに送信されます。 この機能は、ユーザー セッションのためにセッションの状態をバックエンド上でローカルに保存する場合に重要です。

## <a name="secure-sockets-layer-ssl-termination"></a>Secure Sockets Layer (SSL) ターミネーション
Front Door は、エッジでの SSL 終了をサポートています。つまり、個々のユーザーは、アプリケーション バックエンドとの長距離接続を介して確立するのではなく、Front Door 環境との SSL 接続を設定することができます。 さらに、Front Door は、Front Door 環境とバックエンドの間の HTTP 接続と HTTPS 接続の両方をサポートしています。 そのため、エンド ツー エンドの SSL 暗号化を設定することもできますも。 たとえば、アプリケーションのワークロードのための Front Door が、ウォーム接続の再利用が原因で、アクティブなサービスに対する要求を 1 分間で 5000 以上受信する場合は、アプリケーション バックエンドとの接続は約 500 しか確立されないため、バックエンドからの大きな負荷が削減されます。

## <a name="custom-domains-and-certificate-management"></a>カスタム ドメインと証明書の管理
Front Door を使用してコンテンツを配信するときに独自のドメイン名を Front Door URL に表示するには、カスタム ドメインが必要です。 見てわかるドメイン名を使用することは、顧客にとって便利であり、ブランド化の目的にも役立ちます。
Front Door はカスタム ドメイン名でも HTTPS をサポートしています。 この機能は、Front Door で管理されるトラフィックの証明書を選択するか、独自のカスタム SSL 証明書をアップロードすることで使用します。

## <a name="application-layer-security"></a>アプリケーション層のセキュリティ
Azure Front Door を使用すると、アクセス制御のためのカスタムの Web アプリケーション ファイアウォール (WAF) 規則を作成して、クライアント IP アドレス、国番号、および http パラメーターに基づいて HTTP/HTTPS ワークロードを不正使用から保護することができます。 さらに、Front Door では、悪意のあるボット トラフィックと戦うためにレート制限ルールを作成することもできます。 

Front Door プラットフォーム自体は、[Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) Basic によって保護されます。 さらに保護するために、Azure DDoS Protection Standard を VNET で有効にし、自動チューニングとリスク軽減によってネットワーク層 (TCP または UDP) 攻撃からリソースを保護することもできます。 Front Door はレイヤー 7 のリバース プロキシであり、既定では Web トラフィックにバックエンドへのパススルーと他のタイプのトラフィックのブロックしか許可しません。

## <a name="url-redirection"></a>URL リダイレクト
セキュリティで保護された通信のみをサポートすることが業界で強力に推進されているため、Web アプリケーションは HTTP トラフィックを自動的に HTTPS にリダイレクトすることが求められています。 これにより、ユーザーとアプリケーション間のすべての通信が、暗号化されたパスを経由して行われるようになります。 

これまでは、アプリケーションの所有者は、HTTP で受信した要求を HTTPS にリダイレクトすることが唯一の目的である専用のサービスを作成することによって、この要件に対処してきました。 Azure Front Door Service では、トラフィックを HTTP から HTTPS にリダイレクトする機能がサポートされています。 これにより、アプリケーションの構成が簡単になり、リソースの使用が最適化され、グローバルなリダイレクトやパスに基づくリダイレクトなどの新しいリダイレクト シナリオがサポートされるようになります。 Azure Front Door Service からの URL リダイレクトは、HTTP から HTTPS へのリダイレクトだけでなく、別のホスト名へのリダイレクト、別のパスへのリダイレクト、または URL 内の新しいクエリ文字列へのリダイレクトにも対応しています。

詳細については、Azure Front Door Service による[トラフィックのリダイレクト](front-door-url-redirect.md)に関するページを参照してください。

## <a name="url-rewrite"></a>URL 書き換え
Front Door では、バックエンドに転送する要求の作成時に使用する、省略可能なカスタム転送パスの構成を許可することで、[URL 書き換え](front-door-url-rewrite.md)がサポートされています。 さらに、Front Door では、バックエンドに要求を転送するときに送信されるようにホスト ヘッダーを構成することができます。

## <a name="protocol-support---ipv6-and-http2-traffic"></a>プロトコルのサポート - IPv6 および HTTP/2 トラフィック
Azure Front Door は、エンド ツー エンドの IPv6 接続と、HTTP/2 プロトコルも、ネイティブでサポートします。 

HTTP/2 プロトコルによって、長時間実行されている TCP 接続上でアプリケーション バックエンドとクライアント間の全二重通信が可能になります。 HTTP/2 により、HTTP ベースの実装では必須だったポーリングを使用することなく、バックエンドとクライアントの間により対話的な双方向通信が可能になります。 HTTP/2 プロトコルは、HTTP とは異なってオーバーヘッドが少なく、複数の要求や応答で同じ TCP 接続を再利用できるため、リソースをより効率的に使用できます。 詳細については、[Azure Front Door Service の HTTP/2 サポート](front-door-http2.md)に関するページを参照してください。

## <a name="pricing"></a>価格

料金情報については、[Front Door の価格](https://azure.microsoft.com/pricing/details/frontdoor/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
