---
title: Azure Traffic Manager - よくあるご質問
description: この記事では、Traffic Manager に関してよく寄せられる質問に対する回答を示します。
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.openlocfilehash: 640d36649f59842a740b4c12b4e3ab39a6d98c13
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050953"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager についてよく寄せられる質問 (FAQ)

## <a name="traffic-manager-basics"></a>Traffic Manager の基礎

### <a name="what-ip-address-does-traffic-manager-use"></a>Traffic Manager ではどの IP アドレスが使用されますか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-how-it-works.md)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を送信して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。

そのため、Traffic Manager では、クライアントが接続するエンドポイントまたは IP アドレスが提供されません。 サービスに静的 IP アドレスが必要な場合は、Traffic Manager ではなくサービス側で構成する必要があります。

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Traffic Manager を使用して、どのような種類のトラフィックをルーティングできますか。
「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-how-it-works.md)」で説明したとおり、Traffic Manager エンドポイントとして、Azure の内部または外部でホストされているインターネット接続サービスを指定することができます。 したがって、Traffic Manager は、インターネットに接続されている一連のエンドポイントにパブリック インターネットからのトラフィックをルーティングすることができます。 エンドポイントがプライベート ネットワーク内にある場合 (内部バージョンの [Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer) など)、またはユーザーが内部ネットワークから DNS 要求を行う場合、Traffic Manager をこのトラフックのルーティングに使用することはできません。

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager では "スティッキー" セッションはサポートされていますか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-how-it-works.md)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。 したがって、Traffic Manager は、クライアントとサーバーの間の HTTP トラフィックを認識することはありません。

また、Traffic Manager が受信する DNS クエリの発信元 IP アドレスは、クライアントではなく、再帰 DNS サービスのものであることに注意してください。 そのため、Traffic Manager は、個々のクライアントを追跡することはできず、"スティッキー" セッションを実装することはできません。 この制限は、すべての DNS ベースのトラフィック管理システムに共通であり、Traffic Manager 固有のものではありません。

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Traffic Manager を使用していると HTTP エラーが表示されたのはなぜですか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-how-it-works.md)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。 Traffic Manager では、クライアントとサーバーの間の HTTP トラフィックは把握されません。 そのため、表示される HTTP エラーはすべて、アプリケーションによって生成されたものと考えられます。 クライアントがアプリケーションに接続するための DNS 解決手順は、すべて完了しています。 これには、Traffic Manager がアプリケーション トラフィック フローにおいて行う対話がすべて含まれます。

そのため、詳細な調査はアプリケーションを対象とする必要があります。

クライアントのブラウザーから送信される HTTP ホスト ヘッダーが、問題の最もよくある原因です。 使用しているドメイン名の正しいホスト ヘッダーを受け入れるようにアプリケーションが構成されていることを確認します。 Azure App Service を使用しているエンドポイントの場合は、「[Traffic Manager を使用して Azure App Service Web アプリのカスタム ドメイン名を構成する](../app-service/web-sites-traffic-manager-custom-domain-name.md)」を参照してください。

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Traffic Manager を使用すると、パフォーマンスにどのような影響がありますか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-how-it-works.md)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 クライアントはサービス エンドポイントに直接接続するため、接続の確立後は、Traffic Manager の使用に伴うパフォーマンスへの影響は発生しません。

Traffic Manager は DNS レベルでアプリケーションと統合されるため、追加の DNS 参照を DNS 解決チェーンに挿入する必要はありません。 Traffic Manager が DNS 解決時間に与える影響は最小限です。 Traffic Manager では、ネーム サーバーのグローバル ネットワークが使用されます。また、[エニーキャスト](https://en.wikipedia.org/wiki/Anycast) ネットワーキングが使用されるため、DNS クエリは常に、使用可能な最も近いネーム サーバーにルーティングされます。 さらに、DNS 応答がキャッシュされるため、Traffic Manager の使用に伴って発生する DNS 待機時間の増加がごく一部のセッションにしか適用されなくなります。

パフォーマンス方式は、利用可能な最も近いエンドポイントにトラフィックをルーティングします。 最終的に、このメソッドに関連付けられているパフォーマンス全体の影響を最小限に抑える必要があります。 DNS 待機時間の増大は、エンドポイントへのネットワーク待ち時間を短縮することで相殺する必要があります。

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Traffic Manager ではどのようなアプリケーション プロトコルを使用できますか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-how-it-works.md)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 DNS 参照が完了すると、クライアントはアプリケーション エンドポイントに Traffic Manager 経由ではなく直接接続します。 そのため、この接続では、任意のアプリケーション プロトコルを使用できます。 監視プロトコルとして TCP を選択すると、アプリケーション プロトコルを使用せずに、Traffic Manager のエンドポイント正常性監視を実行できます。 アプリケーション プロトコルを使用して正常性を検証する場合、エンドポイントは HTTP または HTTPS の GET 要求に応答できる必要があります。

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>"ネイキッド" ドメイン名で Traffic Manager を使用することはできますか。

はい。 Azure Traffic Manager プロファイルを参照するためのドメイン名の頂点に対するエイリアス レコードを作成する方法を確認するには、「[Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する](../dns/tutorial-alias-tm.md)」を参照してください。

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Traffic Manager では、DNS クエリを処理するときにクライアントのサブネット アドレスは考慮されますか。 

はい。Traffic Manager は、受信する DNS クエリの送信元 IP アドレス (通常は DNS リゾルバーの IP アドレス) を考慮するだけでなく、地理的なルーティング方法、パフォーマンスによるルーティング方法、およびサブネット ルーティング方法で検索を実行するときに、エンド ユーザーの代わりに要求を行うリゾルバーによるクエリにクライアントのサブネット アドレスが含まれる場合は、そのアドレスも考慮します。  
具体的には、クライアントのサブネット アドレスをサポートするリゾルバーからそのアドレスを伝えることができる [Extension Mechanism for DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) を提供する [RFC 7871 – Client Subnet in DNS Queries](https://tools.ietf.org/html/rfc7871) です。

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>DNS TTL とは何ですか。DNS TTL はユーザーにどのような影響を及ぼしますか。

Traffic Manager が DNS クエリを受信すると、応答に Time to Live (TTL) と呼ばれる値が設定されます。 この値 (秒単位) は、ダウンストリームの DNS リゾルバーに、この応答のキャッシュ期間を示します。 DNS リゾルバーはこの結果をキャッシュするとは限りませんが、結果をキャッシュすることによって、Traffic Manager の DNS サーバーにアクセスするのではなく、キャッシュを使用して以降のクエリに応答できるようになります。 応答への影響は次のとおりです。

- TTL を大きくすると、Traffic Manager の DNS サーバーが受信するクエリの数が減ります。Traffic Manager では、提供されるクエリの数に基づいて課金されるため、お客様はコストを削減できます。
- TTL を大きくすると、DNS 参照の実行に要する時間が削減される可能性があります。
- TTL を大きくすると、Traffic Manager がプローブ エージェントを介して取得した最新の正常性情報がデータに反映されなくなります。

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Traffic Manager の応答に設定できる TTL の値を教えてください。

DNS TTL は、プロファイル レベルで 0 ～ 2,147,483,647 秒に設定できます (最大範囲は [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt ) に準拠しています)。 TTL を 0 にすると、ダウンストリームの DNS リゾルバーはクエリの応答をキャッシュしないので、すべてのクエリが解決のために Traffic Manager の DNS サーバーに送信されることになります。

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>マイ プロファイルへのクエリの量を把握する方法を教えてください。 

Traffic Manager が提供しているメトリックの 1 つに、プロファイルが応答するクエリ数があります。 プロファイル レベルの集計でこの情報を取得できます。または、これをさらに分割して、特定のエンドポイントが返されたクエリの量を確認できます。 また、アラートを設定して、クエリ応答量が設定した条件を超えた場合に通知できます。 詳細については、「[Traffic Manager のメトリックとアラート](traffic-manager-metrics-alerts.md)」を参照してください。

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager の地理的トラフィック ルーティング方法

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>地理的ルーティングが役立つ例を教えてください。

地理的ルーティング タイプは、Azure をご利用のお客様が地理的リージョンに基づいてユーザーを識別する必要がある場合に使用できます。 たとえば、地理的トラフィック ルーティング方法を使用して、特定のリージョンのユーザーに、他のリージョンとは異なるユーザー エクスペリエンスを提供できます。 また別の例として、ローカル データの主権性に関する (特定の地域のユーザーに対し、その地域のエンドポイントからのみサービスを提供する) 義務への準拠が挙げられます。

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>パフォーマンス ルーティング方法を使用するか、地理的ルーティング方法を使用するかを判断する方法を教えてください。

これら 2 つの一般的なルーティング方法の主な違いは、パフォーマンス ルーティング方法の主な目標が呼び出し元の待ち時間を最小にできるエンドポイントにトラフックを送信することであるのに対して、地理的ルーティング方法の主な目標が呼び出し元にジオ フェンスを適用して故意に呼び出し元を特定のエンドポイントにルーティングすることであることです。 地理的な近さと短い待ち時間には相関があるため重複が発生しますが、必ず重複するわけではありません。 異なる地域に呼び出し元の待ち時間を最小にできるエンドポイントがある場合があり、その場合パフォーマンス ルーティングはユーザーをそのエンドポイントにルーティングしますが、地理的ルーティングでは常にユーザーの地域にマップされたエンドポイントにユーザーをルーティングします。 わかりやすくするために、次の例について考えてみます。地理的ルーティングでは、アジアからのすべてのトラフィックを米国内のエンドポイントに送信し、米国のすべてのトラフィックをアジアのエンドポイントに送信するというような一般的ではないマッピングを作成できます。 この場合、地理的ルーティングは構成した内容を正確に実行し、パフォーマンスの最適化は考慮されません。 
>[!NOTE]
>パフォーマンス ルーティングと地理的ルーティングの両方の機能を必要とするシナリオがあるかもしれません。このようなシナリオの場合、入れ子になったプロファイルの選択が最適な場合があります。 たとえば、北米からのすべてのトラフィックを米国内にエンドポイントがある入れ子になったプロファイルに送信する地理的ルーティングを使用して親プロファイルを設定し、パフォーマンス ルーティングを使用してその設定内の最適なエンドポイントにこれらのトラフィックを送信できます。 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Traffic Manager の地理的ルーティングがサポートされる地域を教えてください。

Traffic Manager によって使用される国/地域階層は、[こちら](traffic-manager-geographic-regions.md)でご覧いただけます。 このページは常に最新の状態に保たれ、変更があれば反映されます。また、[Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) を使用して、同じ情報をプログラムで取得することもできます。 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>ユーザーがどこからクエリを実行しているのかを Traffic Manager はどのようにして判別しているのですか。

Traffic Manager は、クエリの送信元 IP (ほとんどの場合、ユーザーの代わりにクエリを実行するローカル DNS リゾルバーになります) を調べ、リージョンと IP を対応付ける内部マップを使って場所を特定します。 このマップは、インターネットにおける変化を考慮して絶えず更新されます。 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Traffic Manager では、どのような場合でもユーザーの正確な地理的場所を正しく特定できることが保証されていますか。

いいえ。次の理由から、Traffic Manager では、DNS クエリの送信元 IP アドレスから推測される地理的リージョンがユーザーの場所に常に対応していることを保証できるわけではありません。

- 第 1 に、前の FAQ で説明するように、送信元 IP アドレスはユーザーの代わりに検索を実行する DNS リゾルバーの IP アドレスです。 DNS リゾルバーの地理的な場所は、ユーザーの地理的な場所に適したプロキシですが、この DNS リゾルバー サービスとお客様が使用する DNS リゾルバー サービスのフットプリントによっては、DNS リゾルバーの地理的な場所が異なる場合もあります。 たとえば、マレーシアのお客様が、シンガポールの DNS サーバーを使用してユーザー/デバイスのクエリ解決を処理する DNS リゾルバー サービスを使用することをデバイスの設定で指定しているとします。 この場合、Traffic Manager は、シンガポールの場所に対応するリゾルバーの IP アドレスのみを認識します。 このページのクライアント サブネット アドレスのサポートに関する FAQ もご覧ください。

- 第 2 に、Traffic Manager は内部マップを使用して、IP アドレスの地理的リージョンへの変換を実行します。 このマップは、インターネットの進化し続ける特性を考慮し、精度を高めるために継続的に検証され、更新されていますが、その情報が一部の IP アドレスの地理的な場所を正確に表していない可能性もあります。

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>地理的ルーティングでは、エンドポイントが、その構成に使用された地域と物理的に同じ地域に存在する必要があるのですか。

いいえ。どの地域をエンドポイントにマッピングできるかが、エンドポイントの場所によって制限されることはありません。 たとえば米国中部の Azure リージョンのエンドポイントに、インドからのすべてのユーザーを誘導することもできます。

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>地理的ルーティングを行うための構成がなされていないプロファイルのエンドポイントにリージョンを割り当てることはできますか。

はい。プロファイルのルーティング方法が地理的ルーティングではない場合でも、[Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) を使用して、そのプロファイルのエンドポイントに地理的リージョンを割り当てることができます。 プロファイルのルーティング タイプが地理的ルーティングではない場合、この構成は無視されます。 そのようなプロファイルを後から地理的ルーティング タイプに変更した場合、Traffic Manager はそれらのマッピングを使用できます。

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>既存のプロファイルのルーティング方法を地理的ルーティングに変更しようとしたときにエラーが発生するのはなぜですか。

地理的ルーティングが適用されているプロファイルのすべてのエンドポイントには、少なくとも 1 つのリージョンが対応付けられている必要があります。 既にあるプロファイルを地理的ルーティング タイプに変換するにはまず、[Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) を使って、そのすべてのエンドポイントにリージョンを関連付ける必要があります。そのうえでルーティング タイプを地理的ルーティングに変更してください。 ポータルを使用する場合は、まずエンドポイントを削除し、プロファイルのルーティング方法を地理的ルーティングに変更してから、エンドポイントを地理的リージョン マッピングと共に追加します。

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>地理的ルーティングを有効にしたプロファイルには、エンドポイントではなく、入れ子にしたプロファイルを作成することが強く推奨されているのはなぜですか。

地理的ルーティング方法が使用されているプロファイル内のエンドポイントには、リージョンを一対一でしか割り当てることができません。 そのエンドポイントに子プロファイルが入れ子式に関連付けられていない場合、エンドポイントで異常が発生しても、トラフィックの送信先となる良好な状態の代替エンドポイントが存在しないため、Traffic Manager は引き続きそのエンドポイントにトラフィックを送信します。 別のエンドポイントに割り当てられているリージョンが、異常が発生したエンドポイントに割り当てられているリージョンの "親" であっても、その別のエンドポイントにはフェールオーバーされません (たとえば、スペイン リージョンが割り当てられているエンドポイントで異常が発生した場合、ヨーロッパ リージョンが割り当てられている別のエンドポイントにフェールオーバーされません)。 Traffic Manager は、お客様がそのプロファイルの中で設定した地理的境界を尊重するようになっているためです。 エンドポイントで異常が発生したときに別のエンドポイントへのフェールオーバーのメリットが得られるように、地理的リージョンは、個々のエンドポイントに割り当てるのではなく、複数のエンドポイントを含む入れ子になったプロファイルに割り当てることをお勧めします。 そうすることで、入れ子になった子プロファイルのいずれかのエンドポイントで異常が発生した場合、入れ子にされている同じ子プロファイル内の別のエンドポイントにトラフィックがフェールオーバーされます。

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>このルーティング タイプをサポートする API バージョンに制限はありますか。

はい。地理的ルーティング タイプのサポートは、API バージョン 2017-03-01 以降に限られます。 それより前の API バージョンを使用して、地理的ルーティング タイプのプロファイルを作成したり、エンドポイントに地理的リージョンを割り当てたりすることはできません。 以前の API バージョンを使用して Azure サブスクリプションからプロファイルを取得した場合、地理的ルーティング タイプのプロファイルは返されません。 また、以前の API バージョンを使用した場合、返されたプロファイルに地理的リージョンが割り当てられたエンドポイントが含まれていても、地理的リージョンの割り当ては表示されません。

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager のサブネット トラフィック ルーティング方法

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>サブネット ルーティングが役立つユース ケースを教えてください。

サブネット ルーティングを使用すると、DNS 要求 IP アドレスの送信元 IP によって識別される特定のユーザーのセットに対して提供するエクスペリエンスを区別することができます。 たとえば、ユーザーが企業の本社から Web サイトに接続している場合に別のコンテンツを表示することができます。 また、IPv6 の使用時に特定の ISP のパフォーマンスが平均を下回る場合は、その ISP のユーザーによるアクセスを、IPv4 接続だけがサポートされているエンドポイントのみに制限することもできます。
サブネット ルーティング方法を使用すべきもう 1 つの理由は、入れ子になったプロファイル セット内にある、他のプロファイルに関連しています。 たとえば、ユーザーのジオフェンスのために地理的なルーティング方法を使用する一方で、特定の ISP には別のルーティング方法を使用したい場合は、サブネット ルーティング方法を使用するプロファイルを親プロファイルにし、その ISP をオーバーライドして特定の子プロファイルが使用されるようにします。そうすることで、これ以外ではすべて標準の地理的プロファイルとなります。

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Traffic Manager はどのような方法でエンド ユーザーの IP アドレスを把握するのですか。

エンド ユーザーのデバイスでは、通常、DNS 参照が DNS リゾルバーによって代行されます。 このようなリゾルバーの発信 IP が、Traffic Manager によって送信元 IP と見なされます。 また、サブネット ルーティング方法でも、要求と共に渡された EDNS0 Extended Client Subnet (ECS) 情報があるかどうかが検索されます。 ECS 情報が存在する場合は、それがルーティングの決定に使用されるアドレスになります。 ECS 情報がない場合は、クエリの送信元 IP がルーティングの目的で使用されます。

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>サブネット ルーティングを使用する場合に IP アドレスを指定するにはどうすればよいですか。

エンドポイントに関連付ける IP アドレスは、2 とおりの方法で指定できます。 1 つ目は、範囲を指定するために、開始アドレスと終了アドレスを示す Quad Dot の 10 進数オクテット表記を使用する方法です (例: 1.2.3.4-5.6.7.8 または 3.4.5.6-3.4.5.6)。 2 つ目は、範囲を指定するために CIDR 表記を使用する方法です (例: 1.2.3.0/24)。 複数の範囲を指定することができ、範囲のセットの中で両方の種類の表記を使用することができます。 いくつかの制限が適用されます。

-   各 IP が 1 つのエンドポイントだけにマップされる必要があるため、アドレス範囲を重複させることはできません
-   開始アドレスを終了アドレスより後にすることはできません
-   CIDR 表記の場合、"/" の前の IP アドレスは、その範囲の開始アドレスである必要があります (たとえば、1.2.3.0/24 は有効ですが、1.2.3.4.4/24 は有効ではありません)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>サブネット ルーティングを使用する場合にフォールバック エンドポイントを指定するにはどうすればよいですか。

サブネット ルーティングのプロファイルで、サブネットがマップされていないエンドポイントがある場合、他のエンドポイントと一致しないすべての要求は、このエンドポイントに送られます。 プロファイルにそのようなフォールバック エンドポイントを指定することを強くお勧めします。要求が到着し、どのエンドポイントにもマップされない場合、またはエンドポイントにマップされてもそのエンドポイントに異常がある場合、Traffic Manager からは NXDOMAIN 応答が返されるためです。

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>サブネット ルーティング型のプロファイルでエンドポイントが無効になっている場合はどうなりますか。

サブネット ルーティングを使用するプロファイルで、そのエンドポイントが無効になっている場合、Traffic Manager はそのエンドポイントとそれのサブネット マッピングが存在しないかのように動作します。 IP アドレス マッピングと一致するクエリが受信され、エンドポイントが無効になっている場合、Traffic Manager はフォールバック エンドポイント (マッピングを持たないエンドポイント) を返します。そのようなエンドポイントがない場合は、NXDOMAIN 応答を返します

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager の複数値トラフィック ルーティング方法

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>複数値ルーティングが役立つユース ケースを教えてください。

複数値ルーティングでは、単一のクエリ応答で複数の正常なエンドポイントが返されます。 この主な利点は、エンドポイントに異常がある場合、クライアントには別の DNS 呼び出し (アップストリーム キャッシュから同じ値が返される可能性があります) を行わずに再試行できるオプションがほかにあることです。 この方法は、ダウンタイムを最小限に抑える必要がある、可用性が重要なアプリケーションに適しています。
複数値ルーティング方法のもう 1 つの用途は、エンドポイントが IPv4 アドレスと IPv6 アドレスの両方の "デュアル ホーム" になっていて、呼び出し元がエンドポイントへの接続を開始するときにどちらのオプションも選択できるようにする場合です。

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>複数値ルーティングを使用する場合、何個のエンドポイントが返されますか。

返されるエンドポイントの最大数を指定することができ、複数値方式でクエリを受信したときに、その数より多い正常なエンドポイントが返されることはありません。 この構成に設定できる最大値は 10 です。

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>複数値ルーティングを使用する場合、同じエンドポイントのセットが取得されますか。

各クエリで同じエンドポイントのセットが返されるとは限りません。 これは、一部のエンドポイントが正常でなくなる場合もあるという事実にも影響を受けます。その場合、異常のあるエンドポイントは応答に含まれません

## <a name="real-user-measurements"></a>Real User Measurements

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Real User Measurements のメリットは何ですか。

パフォーマンスによるルーティング方法を使用する場合、Traffic Manager は、送信元 IP および EDNS クライアントのサブネットを確認し (渡された場合)、そのサブネットを、サービスによって保持されているネットワーク待機時間インテリジェンスと照合することで、エンドユーザーにとって最適な接続先 Azure リージョンを選択します。 Real User Measurements により、エンド ユーザー ベースのこの動作が強化されます。エンド ユーザーの動作がこの待機時間テーブルに反映され、このテーブルは、Azure に接続するエンド ユーザーの接続元ネットワークを確実に網羅するようになるためです。 これにより、エンドユーザーのルーティングの精度が向上します。

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Azure 以外のリージョンで Real User Measurements を使用できますか。

Real User Measurements では、Azure リージョンにアクセスするときの待機時間のみが測定およびレポートされます。 Azure 以外のリージョンでホストされているエンドポイントでパフォーマンス ベースのルーティングを使用している場合は、選択した代表的な Azure リージョンの待機時間増加に関する情報を、このエンドポイントに関連付けることで、引き続きこの機能のメリットを得られます。

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>どのルーティング方法が Real User Measurements のメリットを得られますか。

Real User Measurements から取得された追加情報は、パフォーマンスによるルーティング方法を使用するプロファイルにのみ適用されます。 Real User Measurements リンクは、Azure portal で表示すると、すべてのプロファイルから利用できます。

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Real User Measurements は、プロファイルごとに個別に有効にする必要がありますか。

いいえ。サブスクリプションごとに 1 回有効にすれば、測定およびレポートされたすべての待機時間情報を、すべてのプロファイルで利用できます。

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>サブスクリプションの Real User Measurements をオフにするには、どうすればよいですか。

クライアント アプリケーションからの待機時間の測定値の収集と送信を停止すると、Real User Measurements 関連の課金を停止できます。 たとえば、測定 JavaScript が Web ページに埋め込まれている場合は、JavaScript を削除するか、ページがレンダリングされるときに、その呼び出しをオフにすることで、この機能の使用を停止できます。

キーを削除して、Real User Measurements をオフにすることもできます。 キーを削除すると、そのキーで Traffic Manager に送信されるすべての測定値が破棄されます。

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Web ページ以外のクライアント アプリケーションで Real User Measurements を使用できますか。

はい。Real User Measurements は、さまざまな種類のエンド ユーザー クライアントによって収集されたデータを取り込むように設計されています。 この FAQ は、新しい種類のクライアント アプリケーションがサポートされた時点で更新されます。

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Real User Measurements が有効な Web ページのレンダリング 1 回あたりの測定数はどのくらいですか。

測定 JavaScript を指定して Real User Measurements が使用されている場合、ページ レンダリングあたり 6 つの測定値が取得されます。 この測定値は Traffic Manager サービスにレポートされます。 この機能の料金は、Traffic Manager サービスにレポートされた測定数に基づいて発生します。 たとえば、測定が行われていても、レポートされる前に、ユーザーが自分の Web ページから移動すると、その測定は課金の対象とはなりません。

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Web ページで Real User Measurements スクリプトが実行されるまでの間に、遅延が発生しますか。

いいえ。スクリプトは、プログラムによる遅延なしで呼びされます。

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Real User Measurements を使用できるのは、測定対象の Azure リージョンでだけですか。

いいえ。Real User Measurements スクリプトは、呼び出されるたびに、サービスで指定されているように 6 つの Azure リージョン セットを測定します。 このセットは呼び出しによって異なり、大量の呼び出しが発生すると、測定範囲はさまざまな Azure リージョンにまたがります。

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>測定数を特定の数値に制限できますか。

測定 JavaScript は Web ページに埋め込まれており、使用の開始と停止のタイミングは完全にご自身で制御できます。 Traffic Manager サービスが、測定対象の Azure リージョン一覧に対する要求を受け取った場合に、リージョン セットが返されます。

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Real User Measurements の一環としてクライアント アプリケーションによって取得される測定を表示できますか。

クライアント アプリケーションから測定ロジックが実行されるため、待機時間の測定値の表示など、動作を完全に制御できます。 Traffic Manager では、サブスクリプションにリンクされているキーで受信した測定値の集計ビューはレポートされません。

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Traffic Manager によって提供される測定スクリプトを変更できますか。

Web ページに埋め込まれた内容を制御しているときは、待機時間が正しく測定およびレポートされるように、測定スクリプトは変更しないようにすることを強くお勧めします。

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Real User Measurements で使用しているキーを、他のユーザーが見ることはできますか。

測定スクリプトを Web ページに埋め込むと、他のユーザーが、そのスクリプトと Real User Measurements (RUM) キーを表示できるようになります。 ただし、このキーは、サブスクリプション ID とは異なり、Traffic Manager が、この目的のためだけに生成するものです。 RUM キーが知られても、Azure アカウントの安全性に問題が発生することはありません。

### <a name="can-others-abuse-my-rum-key"></a>自分の RUM キーを、他のユーザーが悪用することはできますか。

他のユーザーが、あなたのキーを使用して、誤った情報を Azure に送信することはできますが、誤った測定値が 2 ～ 3 個あっても、その測定値は、Azure に送信された他のすべての測定値と共に考慮されるため、ルーティングが変更されることはありません。 キーを変更する必要がある場合は、古いキーが破棄された時点で、キーを再生成できます。

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>すべての Web ページに測定 JavaScript を配置する必要がありますか。

測定数が多くなると、Real User Measurements によって提供される値も増えます。 しかし、測定 JavaScript をすべての Web ページに配置するか、一部にだけ配置するかは、ご自身の自由です。 最初は、ユーザーのアクセスが多く、アクセスしたユーザーが 5 秒以上留まっているページに配置することをお勧めします。

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Real User Measurements を使用している場合、Traffic Manager によってエンド ユーザーの情報を識別することは可能ですか。

指定された測定 JavaScript が使用されている場合は、Traffic Manager で、エンドユーザーのクライアントの IP アドレスと、使用されているローカル DNS リゾルバーの発信元 IP アドレスを確認できます。 Traffic Manager がクライアント IP アドレスを使用できるのは、測定値を送信したエンド ユーザーを特定できないように、クライアント IP アドレスが切り詰められた後だけです。

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Real User Measurements を測定している Web ページは、ルーティング用に Traffic Manager を使用する必要がありますか。

いいえ。Traffic Manager を使用する必要はありません。 Traffic Manager のルーティング側は、Real User Measurements の部分とは別に動作します。同じ Web プロパティで両方を使用するのはすばらしいアイデアですが、必須ではありません。

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Real User Measurements と共に使用するために、Azure リージョンでホストしなければならないサービスはありますか。

いいえ。Real User Measurements を動作させるために、Azure でホストする必要があるサーバー側コンポーネントはありません。 測定 JavaScript によってダウンロードされた 1 つのピクセル イメージと、そのイメージをさまざまな Azure リージョンで実行するサービスは、Azure でホストおよび管理されます。 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Real User Measurements を使用しているとき、Azure の帯域幅の使用量は増えますか。

前の回答で説明したように、Real User Measurements のサーバー側コンポーネントは、Azure によって所有および管理されます。 つまり、Real User Measurements を使用していることが原因で、Azure の帯域幅の使用量が増加することはありません。 これは、帯域幅の使用量が Azure の課金対象にならない、という意味ではありません。 Microsoft では、Azure リージョンへの待機時間の測定のためにダウンロードするピクセル イメージを 1 つだけにして、帯域幅の使用量を最小限に抑えています。 

## <a name="traffic-view"></a>Traffic View

### <a name="what-does-traffic-view-do"></a>Traffic View は何をしますか。

Traffic View は Traffic Manager の機能で、ユーザーとユーザー エクスペリエンスを詳しく理解するうえで役立ちます。 この Traffic View は、Traffic Manager で受信したクエリと、サービスによって保持されるネットワーク待機時間インテリジェンス テーブルを使用します。このテーブルには、次の情報が示されています。

- Azure のエンドポイントに接続しているユーザーのリージョン。
- そのリージョンから接続しているユーザーの数。
- ユーザーのルーティング先の Azure リージョン。
- その Azure リージョンに対する待機時間。

この情報は、生データとしてダウンロードして使用することに加えて、ポータルで地理的な地図のオーバーレイと表形式ビューで使用することもできます。

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Traffic View を使用することには、どのようなメリットがありますか。

Traffic View には、Traffic Manager プロファイルが受信するトラフィックの全体的なビューが表示されます。 具体的には、これを使用することで、ユーザー ベースの接続元を把握できます。また、これと同様に重要なのが、その待機時間の平均を知ることができる点です。 この情報を使うと、焦点を当てる必要がある領域を見つけることができます。たとえば、ユーザーの待機時間を短くできるリージョンに Azure フットプリントを拡大することができます。 また、さまざまなリージョンへのトラフィック パターンも Traffic View から得られる洞察の 1 つで、これは、こうしたリージョンにおける増減に関する決定を行ううえで役立ちます。

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Traffic View は、Azure Monitor で使用できる Traffic Manager メトリックとは、どのように異なりますか。

Azure Monitor を使用すると、使用しているプロファイルとそのエンドポイントで受信したトラフィックを集計レベルで把握できます。 また、正常性チェックの結果を公開することで、エンドポイントの正常性の状態を追跡することもできます。 Traffic View は、さらに詳しい情報が必要で、Azure に接続しているエンド ユーザーのエクスペリエンスをリージョン レベルで理解したいときにご利用いただけます。

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Traffic View は EDNS クライアント サブネット情報を使用しますか。

Azure Traffic Manager によって処理される DNS クエリでは、ECS 情報を考慮に入れて、ルーティングの精度を高めています。 しかし、ユーザーがどこから接続しているかを示すデータ セットの作成時には、Traffic View は DNS リゾルバーの IP アドレスのみを使用しています。

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Traffic View は何日分のデータを使用しますか。

Traffic View は、ユーザーが表示した日の前日から遡って 7 日間のデータを処理して、出力を作成します。 これは移動ウィンドウであり、アクセスするたびに最新データが使用されます。

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Traffic View は外部エンドポイントをどのように処理しますか。

Traffic Manager プロファイルで Azure リージョン外でホストされる外部エンドポイントを使用する場合は、その待機時間特性に対するプロキシである Azure リージョンにマップされるよう指定できます (実際、これはパフォーマンスによるルーティング方法を使用する場合に必要)。 これに Azure リージョン マッピングが含まれていると、トラフィック ビュー出力の作成時に、Azure リージョンの待機時間メトリックが使用されます。 Azure リージョンが指定されていない場合、待機時間情報は、こうした外部エンドポイントのデータでは空になります。

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>サブスクリプションのプロファイルごとに Traffic View を有効にする必要がありますか。

プレビューの期間中、Traffic View はサブスクリプション レベルで有効にされました。 一般公開の前に加えた強化の一部として、Traffic View はプロファイル レベルで有効にできるようになっています。これにより、この機能をよりきめ細かに有効化できます。 既定では、プロファイルでの Traffic View は無効になります。

>[!NOTE]
>プレビュー期間中にサブスクリプション レベルで Traffic View を有効にした場合、そのサブスクリプションのプロファイルごとに Traffic View を再度有効にする必要があります。
 
### <a name="how-can-i-turn-off-traffic-view"></a>Traffic View をオフにするには、どうすればよいですか。

ポータルまたは REST API を使用して、任意のプロファイルの Traffic View をオフにできます。 

### <a name="how-does-traffic-view-billing-work"></a>Traffic View はどのように課金されますか。

Traffic View の価格は、出力の作成に使用されたデータ ポイントの数に基づきます。 現時点でサポートされている唯一のデータ型は、プロファイルが受け取るクエリです。 また、Traffic View が有効になっているときに実行された処理についてのみ、課金されます。 つまり、1 か月の間で Traffic View が有効になっている期間と、無効になっている期間がある場合、この機能を有効にしていた期間に処理されたデータ ポイントだけが課金対象としてカウントされます。

## <a name="traffic-manager-endpoints"></a>Traffic Manager エンドポイント

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>複数のサブスクリプションのエンドポイントで Traffic Manager を使用できますか。

Azure Web Apps では、複数のサブスクリプションからのエンドポイントを使用できません。 Azure Web Apps の要件により、Web Apps で使用するカスタム ドメイン名を使用できるのは 1 つのサブスクリプション内に限定されます。 複数のサブスクリプション内で同一のドメイン名を持つ Web Apps を使用することはできません。

他の種類のエンドポイントの場合は、複数のサブスクリプションのエンドポイントで Traffic Manager を使用できます。 Resource Manager では、任意のサブスクリプションのエンドポイントを Traffic Manager に追加できますが、Traffic Manager プロファイルを構成するユーザーにそのエンドポイント対する読み取りアクセス権が必要となります。 こうしたアクセス許可を付与するには、 [Azure Resource Manager のロール ベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md)を使用します。

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>クラウド サービス 'Staging' スロットで Traffic Manager を使用できますか。

はい。 クラウド サービス 'Staging' スロットは、Traffic Manager で外部エンドポイントとして構成できます。 正常性チェックには、Azure エンドポイントの料金が課金されます。

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager では IPv6 エンドポイントはサポートされていますか。

現在、Traffic Manager では、IPv6 アドレスに対応するネーム サーバーを提供していません。 ただし、Traffic Manager は、IPv6 エンドポイントに接続する IPv6 クライアントでも使用できます。 クライアントが Traffic Manager に対して DNS 要求を直接行うことはなく、 代わりに、再帰的な DNS サービスを使用します。 IPv6 専用のクライアントは、IPv6 経由で再帰的な DNS サービスに要求を送信します。 その後、再帰サービスは、IPv4 を使用して Traffic Manager ネーム サーバーに接続します。

Traffic Manager からの応答には、エンドポイントの DNS 名または IP アドレスが含まれます。 IPv6 エンドポイントをサポートする場合、2 つのオプションがあります。 関連付けられている AAAA レコードが含まれる DNS 名としてエンドポイントを追加すると、そのエンドポイントは、Traffic Manager によって正常性がチェックされ、クエリ応答内の CNAME レコード タイプとして返されます。 そのエンドポイントを、IPv6 アドレスを使用して直接追加することもできます。その場合、Traffic Manager によって、AAAA タイプのレコードがクエリ応答で返されます。

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>同じリージョン内の複数の Web アプリで Traffic Manager を使用できますか。

通常、Traffic Manager は、さまざまなリージョンにデプロイされたアプリケーションにトラフィックを送信するときに使用されます。 ただし、この Traffic Manager は、同じリージョンに複数のアプリケーションがデプロイされている場合も使用できます。 Traffic Manager Azure エンドポイントでは、同じ Azure リージョンの複数の Web アプリケーション エンドポイントを、同じ Traffic Manager プロファイルに追加することはできません。

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Traffic Manager プロファイルの Azure エンドポイントを別のリソース グループに移動する操作方法を教えてください。

Traffic Manager プロファイルに関連付けられている Azure エンドポイントは、それぞれのリソース ID を使用して追跡されます。 エンドポイントとして使用している Azure リソース (パブリック IP、クラシック クラウド サービス、Web アプリ、入れ子にして使用されている別の Traffic Manager プロファイルなど) を別のリソース グループに移動すると、リソース ID が変更されます。 このシナリオの場合、現時点では、まずエンドポイントを削除してから Traffic Manager プロファイルに追加することにより、プロファイルを更新する必要があります。

## <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager エンドポイントの監視

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Traffic Manager には Azure リージョンの障害に対する回復性がありますか。

トラフィック マネージャーは、Azure での高可用性アプリケーションの配信の重要なコンポーネントです。
高可用性を実現するには、Traffic Manager は非常に高いレベルの可用性を実現し、地域の障害に対応できる必要があります。

仕様では、Traffic Manager のコンポーネントは、どの Azure リージョン全体の障害に対しても耐障害性を持っています。 この回復性は Traffic Manager のすべての構成要素、つまり、DNS ネーム サーバー、API、ストレージ層、エンドポイント監視サービスに適用されます。

万一 Azure リージョン全体が停止した場合で、Traffic Manager は引き続き正常に機能すると予想されます。 複数の Azure リージョンにデプロイされたアプリケーションは、Traffic Manager を利用して、そのアプリケーションの利用可能なインスタンスにトラフィックを送ることができます。

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>リソース グループの場所の選択は Traffic Manager にどのような影響を与えますか。

Traffic Manager は、1 つのグローバル サービスです。 いずれかのリージョンに限定されたものではありません。 リソース グループの場所をどこに選択しても、そのリソース グループにデプロイされる Traffic Manager プロファイルに違いはありません。

Azure Resource Manager では、すべてのリソース グループに対して場所を指定することが求められます。これに基づいて、リソース グループにデプロイされたリソースの既定の場所が決定されます。 Traffic Manager プロファイルを作成するときには、プロファイルはリソース グループ内に作成されます。 Traffic Manager プロファイル自体の場所には、常に **グローバル** が使用され、リソース グループの既定値はオーバーライドされます。

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>各エンドポイントの現在の正常性を確認するには、どうすればよいですか。

各エンドポイントとプロファイル全体の現在の監視状態は、Azure ポータルに表示されます。 この情報は、Traffic Manager の [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx)、[PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/az.trafficmanager)、および [クロスプラットフォームの Azure CLI](../cli-install-nodejs.md) を使用して取得することもできます。

Azure Monitor を使用すると、エンドポイントの正常性を追跡して、視覚的に表現することもできます。 Azure Monitor の使用方法について詳しくは、[Azure Monitoring のドキュメント](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)をご覧ください。

### <a name="can-i-monitor-https-endpoints"></a>HTTPS エンドポイントを監視できますか。

はい。 Traffic Manager は、HTTPS 経由のプローブをサポートしています。 監視構成でプロトコルとして **HTTPS** を構成します。

Traffic Manager は、次のように証明書の検証を提供できません。

* サーバー側証明書は検証されません。
* SNI サーバー側証明書は検証されません。
* クライアント証明書はサポートされていません。

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>エンドポイントを追加する際には、IP アドレスと DNS 名のどちらを使用しますか。

Traffic Manager では、追加するエンドポイントの参照方法として、DNS 名、IPv4 アドレス、IPv6 アドレスという 3 つの方法がサポートされています。 エンドポイントが IPv4 アドレスまたは IPv6 アドレスとして追加された場合、クエリ応答のレコード タイプはそれぞれ A と AAAA になります。 エンドポイントが DNS 名として追加された場合、クエリ応答のレコード タイプは CNAME になります。 IPv4 アドレスまたは IPv6 アドレスとして追加できるエンドポイントは、種類が**外部**のエンドポイントだけです。
この 3 種類のエンドポイント アドレス指定方法では、すべてのルーティング方法と監視設定がサポートされています。

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>エンドポイントを追加するときに、どの種類の IP アドレスを使用できますか。

Traffic Manager では、エンドポイントの指定に IPv4 アドレスまたは IPv6 アドレスを使用することができます。 以下に挙げるような、いくつかの制限があります。

- 予約済みのプライベート IP アドレス空間に対応するアドレスは使用できません。 これらのアドレスには、RFC 1918、RFC 6890、RFC 5737、RFC 3068、RFC 2544、および RFC 5771 で示されているアドレスが含まれます
- アドレスにポート番号を含めることはできません (使用するポートは、プロファイルの構成設定で指定することができます)
- 同じプロファイル内の 2 つのエンドポイントに同じターゲット IP アドレスを指定することはできません

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>1 つのプロファイル内で異なる種類のエンドポイント アドレス指定方法を使用することはできますか。

いいえ。Traffic Manager では、プロファイル内でエンドポイント アドレス指定方法を混在させることはできません。例外は複数値ルーティング タイプのプロファイルであり、その場合は IPv4 アドレス指定方法と IPv6 アドレス指定方法を混在させることができます

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>受信クエリのレコード タイプが、エンドポイントのアドレス指定方法に関連付けられているレコード タイプと異なる場合はどうなりますか。

Traffic Manager は、プロファイルに対するクエリを受信すると、まず、指定されたルーティング方法およびエンドポイントの正常性状態に基づいて返される必要があるエンドポイントを特定します。 次に、受信クエリで要求されたレコード タイプと、エンドポイントに関連付けられているレコード タイプを調べたうえで、以下の表に基づいて応答を返します。

複数値以外のルーティング方法のプロファイルの場合:

|受信クエリ要求|    エンドポイントの種類|  提供される応答|
|--|--|--|
|ANY |  A/AAAA/CNAME |  ターゲット エンドポイント| 
|A |    A/CNAME | ターゲット エンドポイント|
|A |    AAAA |  NODATA |
|AAAA | AAAA/CNAME |  ターゲット エンドポイント|
|AAAA | A | NODATA |
|CNAME |    CNAME | ターゲット エンドポイント|
|CNAME  |A/AAAA | NODATA |
|

ルーティング方法が複数値に設定されているプロファイルの場合:

|受信クエリ要求|    エンドポイントの種類 | 提供される応答|
|--|--|--|
|ANY |  A と AAAA の混在 | ターゲット エンドポイント|
|A |    A と AAAA の混在 | タイプ A のターゲット エンドポイントのみ|
|AAAA   |A と AAAA の混在|     タイプ AAAA のターゲット エンドポイントのみ|
|CNAME |    A と AAAA の混在 | NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>入れ子になったプロファイル内で、IPv4/IPv6 アドレスでエンドポイントが指定されているプロファイルを使用することはできますか。

はい。ただし、種類が複数値のプロファイルは、入れ子になったプロファイル セット内の親プロファイルにすることはできないという例外があります。

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Web アプリケーションのエンドポイントを Traffic Manager プロファイルで停止しましたが、それを再起動した後でもトラフィックを受信していません。 どうしたらいいですか。

Azure Web アプリケーションのエンドポイントが停止されると、Traffic Manager は正常性のチェックを停止し、エンドポイントが再起動されていることを検出した後にのみ正常性チェックを再開します。 この遅延を防ぐには、エンドポイントを再起動した後で、Traffic Manager プロファイルでエンドポイントを無効にしてから再び有効にします。

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>アプリケーションが HTTP または HTTPS をサポートしていなくても Traffic Manager を使用できますか。

はい。 監視プロトコルとして TCP を指定すると、Traffic Manager は TCP 接続を開始し、エンドポイントからの応答を待機します。 接続を確立するために、エンドポイントがタイムアウト期間内に接続要求に応答すると、そのエンドポイントは正常とマークされます。

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>TCP 監視を使用する場合、エンドポイントからどのような応答が必要ですか。

TCP 監視を使用すると、Traffic Manager は指定されたポートでエンドポイントに SYN 要求を送信して 3 方向の TCP ハンドシェイクを開始します。 Traffic Manager は、エンドポイントからの応答を一定期間 (タイムアウト設定で指定) 待機します。 エンドポイントが、監視設定で指定されたタイムアウト期間内に SYN-ACK 応答で SYN 要求に応答すると、そのエンドポイントは正常と見なされます。 SYN-ACK 応答を受信すると、Traffic Manager は RST で応答して接続をリセットします。

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Traffic Manager は、どの程度迅速に異常なエンドポイントからユーザーを移動させますか。

Traffic Manager には、Traffic Manager プロファイルのフェールオーバーの動作を制御できる、次のような複数の設定が用意されています。

- プローブ間隔を 10 秒に設定することで、Traffic Manager がエンドポイントをプローブする頻度を増やすことができます。 これにより、異常が発生したエンドポイントを可能な限り速やかに検出できます。 
- 正常性チェック要求がタイムアウトになるまでの待機時間を指定できます (最小タイムアウト値は 5 秒です)。
- エンドポイントが異常としてマークされるまでに許容されるエラーの数を指定できます。 この値を 0 にすることもできます。その場合、エンドポイントは、最初の正常性チェックに失敗するとすぐに異常とマークされます。 ただし、エラーの許容数に最小値の 0 を使用すると、プローブ時に発生する可能性のある一時的な問題によって、エンドポイントがローテーションから除外される可能性があります。
- DNS 応答の Time to Live (TTL) に 0 を指定できます。 この場合、DNS リゾルバーは応答をキャッシュできないので、新しい各クエリでは Traffic Manager が持つ最新の正常性情報が組み込まれた応答を取得します。

これらの設定を使用することで、Traffic Manager はエンドポイントで異常が発生してから 10 秒以内にフェールオーバーを実行できるようになり、対応するプロファイルに対して DNS クエリが作成されます。

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>プロファイル内のエンドポイントごとに異なる監視設定を指定するにはどうすればよいですか。

Traffic Manager の監視設定は、プロファイル レベルで行われます。 1 つのエンドポイントにのみ別の監視設定を使用する必要がある場合、これを実現するには、監視設定が親プロファイルと異なる[入れ子になったプロファイル](traffic-manager-nested-profiles.md)としてそのエンドポイントを使用します。

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>エンドポイントに対する Traffic Manager の正常性チェックに HTTP ヘッダーを割り当てるにはどうすればよいですか。

Traffic Manager では、エンドポイントに対して開始される HTTP(S) 正常性チェックにカスタム ヘッダーを指定することができます。 カスタム ヘッダーを指定する場合は、プロファイル レベルで指定する (すべてのエンドポイントに適用する) か、エンドポイント レベルで指定することができます。 ヘッダーが両方のレベルで定義されている場合は、エンドポイント レベルで指定されている方が、プロファイル レベルで指定されている方をオーバーライドします。
この一般的なユース ケースの 1 つは、マルチテナント環境でホストされているエンドポイントに Traffic Manager の要求が正しくルーティングされるように、ホスト ヘッダーを指定することです。 もう 1 つのユース ケースは、エンドポイントの HTTP (S) 要求ログからの Traffic Manager の要求を識別することです

## <a name="what-host-header-do-endpoint-health-checks-use"></a>エンドポイントの正常性チェックには、どのようなホストヘッダーが使用されますか。

カスタム ホスト ヘッダー設定が指定されていない場合、Traffic Manager によって使用されるホスト ヘッダーは、プロファイルで構成されているエンドポイント ターゲットの DNS 名です (それが利用可能な場合)。

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>正常性チェックはどの IP アドレスから発信されますか。

[ここ](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)をクリックして表示される JSON ファイルに、Traffic Manager の正常性チェックの実行元になる IP アドレスが列挙されています。 JSON ファイルに列挙されている IP を確認し、正常性状態をチェックするためにこれらの IP アドレスからの受信接続がエンドポイントで確実に許可されるように設定してください。

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Traffic Manager では、エンドポイントに対して正常性チェックが何回実行されるのですか。

エンドポイントに対して実行される Traffic Manager の正常性チェックの回数は以下によって異なります。

- 監視間隔に設定した値 (間隔が短いほど、特定の期間にエンドポイントに到達する要求の数が増えます)。
- 正常性チェックの実行元である場所の数 (正常性チェックの実行元になる IP アドレスは、前の FAQ に記載されています)。

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>いずれかのエンドポイントがダウンした場合に通知を受ける方法を教えてください。

Traffic Manager が提供しているメトリックの 1 つにプロファイルのエンドポイントの正常性状態があります。 プロファイル内のすべてのエンドポイントの集計として (たとえば、お使いのエンドポイントの 75% が正常) またはエンドポイントごとに正常性を確認できます。 Traffic Manager のメトリックは Azure Monitor で公開されていて、[アラート機能](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)を使用して、お使いのエンドポイントの正常性状態に変化があった場合に通知を受けることができます。 詳細については、「[Traffic Manager のメトリックとアラート](traffic-manager-metrics-alerts.md)」を参照してください。  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager の入れ子になったプロファイル

### <a name="how-do-i-configure-nested-profiles"></a>入れ子になったプロファイルを構成するにはどうすればよいですか。

入れ子になった Traffic Manager プロファイルは、Azure Resource Manager と従来の Azure REST API (Azure PowerShell コマンドレットとクロスプラットフォームの Azure CLI コマンド) を使用して構成できます。 新しい Azure Portal を使用する場合もサポートされます。

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Traffic Manager では、何層の入れ子がサポートされますか。

プロファイルは最大 10 レベルまで入れ子にすることができます。 "ループ" は使用できません。

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>同じ Traffic Manager プロファイルに、入れ子になった子プロファイルと他の種類のエンドポイントを混在させることはできますか。

はい。 プロファイル内での異なる種類のエンドポイントの組み合わせには制限はありません。

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>入れ子になったプロファイルに対して課金モデルはどのように適用されますか。

入れ子になったプロファイルの使用が料金に悪影響を及ぼすことはありません。

Traffic Manager の課金には、エンドポイントの正常性チェックと数百万の DNS クエリの 2 つの構成要素があります。

* エンドポイントの正常性チェック: 親プロファイルのエンドポイントとして構成されている子プロファイルには課金されません。 子プロファイル内のエンドポイントの監視は、通常の方法で課金されます。
* DNS クエリ: 各クエリは 1 回だけカウントされます。 子プロファイルからエンドポイントを返す親プロファイルに対するクエリは、親プロファイルにのみカウントされます。

詳細については、「[Traffic Manager の価格](https://azure.microsoft.com/pricing/details/traffic-manager/)」のページを参照してください。

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>入れ子になったプロファイルでは、パフォーマンスへの影響はありますか。

いいえ。 入れ子になったプロファイルを使用しても、パフォーマンスに影響はありません。

Traffic Manager のネーム サーバーは、各 DNS クエリを処理するときにプロファイル階層の内部をスキャンします。 親プロファイルに対する DNS クエリは、子プロファイルのエンドポイントを含む DNS 応答を受信できます。 使用するのが単一のプロファイルか入れ子になったプロファイルかに関係なく、単一の CNAME レコードが使用されます。 階層内のプロファイルごとに CNAME レコードを作成する必要はありません。

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Traffic Manager では、親プロファイルの入れ子になったエンドポイントの正常性をどのように計算するのですか。

親プロファイルでは、子の正常性チェックは直接には実行されません。 代わりに、子プロファイルのエンドポイントの正常性を使用して、子プロファイルの全体的な正常性が計算されます。 この情報は、入れ子になったプロファイルの階層の上位に伝達され、入れ子になったエンドポイントの状態が判断されます。 親プロファイルでは、この正常性の集計結果を使用して、トラフィックを子プロファイルに送信できるかどうかを決定します。

次の表に、入れ子になったエンドポイントに対する Traffic Manager 正常性チェックの動作を示します。

| 子プロファイル モニターの状態 | 親エンドポイント監視の状態 | メモ |
| --- | --- | --- |
| 無効。 子プロファイルは無効化されています。 |停止済み |親エンドポイントの状態は停止で、無効ではありません。 無効な状態は、親プロファイルでエンドポイントを無効にしたことを示すために予約されています。 |
| 低下。 1 つ以上の子プロファイル エンドポイントが "低下" 状態です。 |オンライン: 子プロファイルの "オンライン" 状態のエンドポイントの数が MinChildEndpoints の値以上です。<BR>エンドポイントの確認: 子プロファイルの "オンライン" 状態および "エンドポイントの確認" 状態のエンドポイントの数が MinChildEndpoints の値以上です。<BR>低下: それ以外の場合。 |トラフィックは、"エンドポイントの確認" 状態のエンドポイントにルーティングされます。 MinChildEndpoints の設定値が大きすぎると、エンドポイントは常に "低下" 状態になります。 |
| オンライン。 1 つ以上の子プロファイル エンドポイントが "オンライン" 状態です。 "低下" 状態のエンドポイントはありません。 |上記を参照してください。 | |
| エンドポイントの確認。 1 つ以上の子プロファイル エンドポイントが "エンドポイント チェック中" 状態です。 "オンライン" または "低下" 状態のエンドポイントはありません。 |上記と同じです。 | |
| 非アクティブ。 すべての子プロファイル エンドポイントが "無効" 状態または "停止済み" 状態であるか、このプロファイルにエンドポイントがありません。 |停止済み | |

## <a name="next-steps"></a>次のステップ:

- Traffic Manager の [エンドポイントの監視と自動フェールオーバー](../traffic-manager/traffic-manager-monitoring.md)の詳細を確認する。
- Traffic Manager の [トラフィック ルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)の詳細を確認する。
