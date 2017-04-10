---
title: Azure Traffic Manager - FAQ | Microsoft Docs
description: "この記事では、Traffic Manager に関してよく寄せられる質問に対する回答を示します。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8c4c8db3cf57537dd77d33b3ded2dc24167f511f
ms.lasthandoff: 03/25/2017

---

# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager についてよく寄せられる質問 (FAQ)

## <a name="traffic-manager-basics"></a>Traffic Manager の基礎

### <a name="what-ip-address-does-traffic-manager-use"></a>Traffic Manager ではどの IP アドレスが使用されますか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を送信して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。

そのため、Traffic Manager では、クライアントが接続するエンドポイントまたは IP アドレスが提供されません。 したがって、サービスに静的 IP アドレスが必要な場合は、Traffic Manager ではなくサービス側で構成する必要があります。

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager では "スティッキー" セッションはサポートされていますか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。 したがって、Traffic Manager は、クライアントとサーバーの間の HTTP トラフィックを認識することはありません。

また、Traffic Manager が受信する DNS クエリの発信元 IP アドレスは、クライアントではなく、再帰 DNS サービスのものであることに注意してください。 そのため、Traffic Manager は、個々のクライアントを追跡することはできず、"スティッキー" セッションを実装することはできません。 この制限は、すべての DNS ベースのトラフィック管理システムに共通であり、Traffic Manager 固有のものではありません。

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Traffic Manager を使用していると HTTP エラーが表示されたのはなぜですか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。 Traffic Manager では、クライアントとサーバーの間の HTTP トラフィックは把握されません。 そのため、表示される HTTP エラーはすべて、アプリケーションによって生成されたものと考えられます。 クライアントがアプリケーションに接続するための DNS 解決手順は、すべて完了しています。 これには、Traffic Manager がアプリケーション トラフィック フローにおいて行う対話がすべて含まれます。

そのため、詳細な調査はアプリケーションを対象とする必要があります。

クライアントのブラウザーから送信される HTTP ホスト ヘッダーが、問題の最もよくある原因です。 使用しているドメイン名の正しいホスト ヘッダーを受け入れるようにアプリケーションが構成されていることを確認します。 Azure App Service を使用しているエンドポイントの場合は、「[Traffic Manager を使用して Azure App Service Web アプリのカスタム ドメイン名を構成する](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)」を参照してください。

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Traffic Manager を使用すると、パフォーマンスにどのような影響がありますか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 クライアントはサービス エンドポイントに直接接続するため、接続の確立後は、Traffic Manager の使用に伴うパフォーマンスへの影響は発生しません。

Traffic Manager は DNS レベルでアプリケーションと統合されるため、追加の DNS 参照を DNS 解決チェーンに挿入する必要はありません。 Traffic Manager が DNS 解決時間に与える影響は最小限です。 Traffic Manager では、ネーム サーバーのグローバル ネットワークが使用されます。また、[エニーキャスト](https://en.wikipedia.org/wiki/Anycast) ネットワーキングが使用されるため、DNS クエリは常に、使用可能な最も近いネーム サーバーにルーティングされます。 さらに、DNS 応答がキャッシュされるため、Traffic Manager の使用に伴って発生する DNS 待機時間の増加がごく一部のセッションにしか適用されなくなります。

パフォーマンス方式は、利用可能な最も近いエンドポイントにトラフィックをルーティングします。 最終的に、このメソッドに関連付けられているパフォーマンス全体の影響を最小限に抑える必要があります。 DNS 待機時間の増大は、エンドポイントへのネットワーク待ち時間を短縮することで相殺する必要があります。

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Traffic Manager ではどのようなアプリケーション プロトコルを使用できますか。

「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 DNS 参照が完了すると、クライアントはアプリケーション エンドポイントに Traffic Manager 経由ではなく直接接続します。 そのため、この接続では、任意のアプリケーション プロトコルを使用できます。 ただし、Traffic Manager のエンドポイント正常性チェックには、HTTP エンドポイントか HTTPS エンドポイントが必要です。 正常性チェックのためのエンドポイントは、クライアントが接続しているアプリケーション エンドポイントと異なる場合があります。

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>"ネイキッド" ドメイン名で Traffic Manager を使用することはできますか。

いいえ。 DNS 標準では、同じ名前を持つ他の DNS レコードと CNAME が共存することは許可されていません。 DNS ゾーンの頂点 (またはルート) には、SOA と権限のある NS レコードという、既存の 2 つの DNS レコードが常に含まれます。 これは、ゾーンの頂点で CNAME レコードを作成すると DNS 標準に違反してしまうことを意味します。

Traffic Manager では、バニティ DNS 名をマッピングするために DNS CNAME レコードが必要です。 たとえば、www.contoso.com を Traffic Manager プロファイルの DNS 名 contoso.trafficmanager.net にマッピングします。 また、Traffic Manager プロファイルも、クライアントが接続するエンドポイントを示すために、別の DNS CNAME を返します。

この問題を回避するために、HTTP リダイレクトを使用してトラフィックをネイキッド ドメイン名から別の URL に転送することをお勧めします。これにより、Traffic Manager を使用できるようになります。 たとえば、ネイキッド ドメイン "contoso.com" を使用すると、Traffic Manager の DNS 名を指す CNAME "www.contoso.com" にユーザーをリダイレクトできます。

Traffic Manager におけるネイキッド ドメインの完全サポートは、開発待ちの機能として登録されています。 この機能を要求するためにサポートを登録するには、[コミュニティ フィードバック サイトの投票](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)で、ぜひ支持を表明してください。

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Traffic Manager では、DNS クエリを処理するときにクライアントのサブネット アドレスは考慮されますか。 
いいえ。現時点では、Traffic Manager は地理的なルーティング方法とパフォーマンスによるルーティング方法で検索を実行するときに、受信する DNS クエリの送信元 IP アドレス (ほとんどの場合、DNS リゾルバーの IP アドレス) のみを考慮します。  
具体的には、クライアントのサブネット アドレスをサポートするリゾルバーから DNS サーバーにクライアントのサブネット アドレスを伝えることができる [Extension Mechanism for DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) を提供する [RFC 7871 – Client Subnet in DNS Queries](https://tools.ietf.org/html/rfc7871) は、Traffic Manager では現在サポートされていません。 この機能を要求する場合は、[コミュニティ フィードバック サイト](https://feedback.azure.com/forums/217313-networking)で支持を表明してください。


## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager の地理的トラフィック ルーティング方法

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>地理的ルーティングが役立つ例を教えてください。 
地理的ルーティング タイプは、Azure をご利用のお客様が地理的リージョンに基づいてユーザーを識別する必要がある場合に使用できます。 たとえば、地理的トラフィック ルーティング方法を使用して、特定のリージョンのユーザーに、他のリージョンとは異なるユーザー エクスペリエンスを提供できます。 また別の例として、ローカル データの主権性に関する (特定の地域のユーザーに対し、その地域のエンドポイントからのみサービスを提供する) 義務への準拠が挙げられます。

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Traffic Manager の地理的ルーティングがサポートされる地域を教えてください。 
Traffic Manager によって使用される国/地域階層は、[こちら](traffic-manager-geographic-regions.md)でご覧いただけます。 このページは常に最新の状態に保たれ、変更があれば反映されます。また、[Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) を使用して、同じ情報をプログラムで取得することもできます。 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>ユーザーがどこからクエリを実行しているのかを Traffic Manager はどのようにして判別しているのですか。 
Traffic Manager は、クエリの送信元 IP (これは通常、ユーザーの代わりにクエリを実行するローカル DNS リゾルバになります) を調べ、地域と IP とを対応付ける内部マップを使って場所を特定します。 このマップは、インターネットにおける変化を考慮して絶えず更新されます。 

### <a name="is-it-guaranteed-that-traffic-manager-will-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Traffic Manager では、どのような場合でもユーザーの正確な地理的場所を正しく特定することが保証されていますか。
いいえ。次の理由から、Traffic Manager では、DNS クエリの送信元 IP アドレスから推測される地理的リージョンがユーザーの場所に常に対応していることを保証できるわけではありません。 

- 第 1 に、前の FAQ で説明するように、送信元 IP アドレスはユーザーの代わりに検索を実行する DNS リゾルバーの IP アドレスです。 DNS リゾルバーの地理的な場所は、ユーザーの地理的な場所に適したプロキシですが、この DNS リゾルバー サービスとお客様が使用する DNS リゾルバー サービスのフットプリントによっては、DNS リゾルバーの地理的な場所が異なる場合もあります。 たとえば、マレーシアのお客様が、シンガポールの DNS サーバーを使用してユーザー/デバイスのクエリ解決を処理する DNS リゾルバー サービスを使用することをデバイスの設定で指定しているとします。 この場合、Traffic Manager は、シンガポールの場所に対応するリゾルバーの IP アドレスのみを認識します。 このページのクライアント サブネット アドレスのサポートに関する FAQ もご覧ください。

- 第 2 に、Traffic Manager は内部マップを使用して、IP アドレスの地理的リージョンへの変換を実行します。 このマップは、インターネットの進化し続ける特性を考慮し、精度を高めるために継続的に検証され、更新されていますが、その情報が一部の IP アドレスの地理的な場所を正確に表していない可能性もあります。


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>地理的ルーティングでは、エンドポイントが、その構成に使用された地域と物理的に同じ地域に存在する必要があるのですか。 
いいえ。どの地域をエンドポイントにマッピングできるかが、エンドポイントの場所によって制限されることはありません。 たとえば米国中部の Azure リージョンのエンドポイントに、インドからのすべてのユーザーを誘導することもできます。

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>地理的ルーティングを行うための構成がなされていないプロファイルのエンドポイントにリージョンを割り当てることはできますか。 
はい。プロファイルのルーティング方法が地理的ルーティングではない場合でも、[Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) を使用して、そのプロファイルのエンドポイントに地理的リージョンを割り当てることができます。 プロファイルのルーティング タイプが地理的ルーティングではない場合、この構成は無視されます。 そのようなプロファイルを後から地理的ルーティング タイプに変更した場合、それらのマッピングが Traffic Manager によって使われるようになります。


### <a name="when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic-i-am-getting-an-error"></a>既存のプロファイルのルーティング方法を地理的ルーティングに変更しようとした場合、エラーは発生しますか。
地理的ルーティングが適用されているプロファイルのすべてのエンドポイントには、少なくとも 1 つのリージョンが対応付けられている必要があります。 既にあるプロファイルを地理的ルーティング タイプに変換するにはまず、[Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) を使って、そのすべてのエンドポイントにリージョンを関連付ける必要があります。そのうえでルーティング タイプを地理的ルーティングに変更してください。 ポータルを使用する場合は、まずエンドポイントを削除し、プロファイルのルーティング方法を地理的ルーティングに変更してから、エンドポイントをその地理的リージョン マッピングと共に追加します。 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>地理的ルーティングを有効にしたプロファイルには、エンドポイントではなく、入れ子にしたプロファイルを作成することが強く推奨されているのはなぜですか。 
地理的ルーティング タイプが使用されているプロファイル内のエンドポイントには、リージョンを一対一でしか割り当てることができません。 万一エンドポイントが異常な状態に陥っても、そこに子プロファイルが入れ子式に関連付けられていなければ、Traffic Manager は、そのエンドポイントにトラフィックを誘導し続けます。トラフィックの誘導先としてそれよりも状態の良いエンドポイントが存在しないからです。 Traffic Manager が別のエンドポイントにトラフィックをフェールオーバーすることはできません。異常状態のエンドポイントに割り当てられているリージョンの "親" がそこに割り当てられていたとしても同様です (例: スペイン リージョンが割り当てられているエンドポイントに異常が発生した場合に、ヨーロッパ リージョンが割り当てられている別のエンドポイントにフェールオーバーされることはありません)。 Traffic Manager は、お客様がそのプロファイルの中で設定した地理的境界を尊重するようになっているためです。 エンドポイントで異常が生じた場合に別のエンドポイントにフェールオーバーできるようにするために、プロファイルにはエンドポイントを直接割り当てるのではなく、プロファイルを入れ子にして複数のエンドポイントを持たせ、それらのエンドポイントに対して各リージョンを割り当てることが推奨されています。 そうすることで、入れ子になった子プロファイルのいずれかのエンドポイントで異常が発生した場合、入れ子にされている同じ子プロファイル内の別のエンドポイントにトラフィックがフェールオーバーされます。

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>このルーティング タイプをサポートする API バージョンに制限はありますか。

はい。地理的ルーティング タイプのサポートは、API バージョン 2017-03-01 以降に限られます。 それより前の API バージョンを使用して、地理的ルーティング タイプのプロファイルを作成したり、エンドポイントに地理的リージョンを割り当てたりすることはできません。 以前の API バージョンを使用して Azure サブスクリプションからプロファイルを取得した場合、地理的ルーティング タイプのプロファイルは返されません。 加えて、以前のバージョンの API を使用して取得したプロファイルは、そのエンドポイントにリージョンが割り当てられていても、リージョンの割り当ては表示されません。



## <a name="traffic-manager-endpoints"></a>Traffic Manager エンドポイント

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>複数のサブスクリプションのエンドポイントで Traffic Manager を使用できますか。

Azure Web Apps では、複数のサブスクリプションからのエンドポイントを使用できません。 Azure Web Apps の要件により、Web Apps で使用するカスタム ドメイン名を使用できるのは 1 つのサブスクリプション内に限定されます。 複数のサブスクリプション内で同一のドメイン名を持つ Web Apps を使用することはできません。

他の種類のエンドポイントの場合は、複数のサブスクリプションのエンドポイントで Traffic Manager を使用できます。 Traffic Manager の構成方法は、クラシック デプロイ モデルまたはリソース マネージャーのエクスペリエンスのどちらを使用しているかによって異なります。

* Resource Manager では、すべてのサブスクリプションのエンドポイントを Traffic Manager に追加できますが、それには、Traffic Manager プロファイルを構成しているユーザーに、エンドポイントへの読み取りアクセス権が付与されている必要があります。 こうしたアクセス許可を付与するには、 [Azure Resource Manager のロール ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md)を使用します。
* クラシック デプロイ モデル インターフェイスでは、Azure エンドポイントとして構成された Cloud Services や Web Apps が、Traffic Manager プロファイルと同じサブスクリプションに存在していることが、Traffic Manager によって求められます。 その他のサブスクリプションにあるクラウド サービスのエンドポイントは、'外部' エンドポイントとして Traffic Manager に追加できます。 これらの外部エンドポイントは、外部レートではなく Azure エンドポイントとして課金されます。

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>クラウド サービス 'Staging' スロットで Traffic Manager を使用できますか。

はい。 クラウド サービス 'Staging' スロットは、Traffic Manager で外部エンドポイントとして構成できます。 正常性チェックには、Azure エンドポイントの料金が課金されます。 使用されているエンドポイントの種類は外部エンドポイントです。したがって、基になるサービスへの変更は自動的には反映されません。 外部エンドポイントでは、Traffic Manager はクラウド サービスが停止または削除されたタイミングを検出できません。 そのため、Traffic Manager では、エンドポイントが無効になるか、削除されるまで、正常性チェックへの課金を続けます。

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager では IPv6 エンドポイントはサポートされていますか。

現在、Traffic Manager では、IPv6 アドレスに対応するネーム サーバーを提供していません。 ただし、Traffic Manager は、IPv6 エンドポイントに接続する IPv6 クライアントでも使用できます。 クライアントが Traffic Manager に対して DNS 要求を直接行うことはなく、 代わりに、再帰的な DNS サービスを使用します。 IPv6 専用のクライアントは、IPv6 経由で再帰的な DNS サービスに要求を送信します。 その後、再帰サービスは、IPv4 を使用して Traffic Manager ネーム サーバーに接続します。

Traffic Manager はエンドポイントの DNS 名を返します。 IPv6 エンドポイントをサポートするには、エンドポイント DNS 名が IPv6 アドレスを指す DNS AAAA レコードが必要です。 Traffic Manager の正常性チェックは、IPv4 アドレスのみをサポートしています。 サービスは、同じ DNS 名で IPv4 エンドポイントを公開する必要があります。

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>同じリージョン内の複数の Web アプリで Traffic Manager を使用できますか。

通常、Traffic Manager は、さまざまなリージョンにデプロイされたアプリケーションにトラフィックを送信するときに使用されます。 ただし、この Traffic Manager は、同じリージョンに複数のアプリケーションがデプロイされている場合も使用できます。 Traffic Manager Azure エンドポイントでは、同じ Azure リージョンの複数の Web アプリケーション エンドポイントを、同じ Traffic Manager プロファイルに追加することはできません。

次の手順により、この制約を回避することができます。

1. エンドポイントが別の Web アプリケーション 'スケール ユニット' に存在することを確認します。 ドメイン名は特定のスケール ユニット内の 1 つのサイトにマップする必要があります。 したがって、同じスケール ユニット内の 2 つの Web アプリケーションは、Traffic Manager プロファイルを共有できません。
2. 各 Web アプリケーションにカスタム ホスト名としてバニティ ドメイン名を追加します。 各 Web アプリケーションは、別のスケール ユニット内に存在する必要があります。 Web アプリケーションは、すべて同じサブスクリプションに属する必要があります。
3. Traffic Manager プロファイルに Web アプリケーション エンドポイントを Azure エンドポイントとして 1 つだけ追加します。
4. 追加の各 Web アプリ エンドポイントを、外部エンドポイントとして Traffic Manager プロファイルに追加します。 外部エンドポイントを追加する場合は、Resource Manager デプロイ モデルを使用する必要があります。
5. Traffic Manager プロファイルの DNS 名 (<…>.trafficmanager.net) をポイントするバニティ ドメインで、DNS CNAME レコードを作成します。
6. Traffic Manager プロファイルの DNS 名ではなく、バニティ ドメイン名を使用してサイトにアクセスします。

##  <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager エンドポイントの監視

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Traffic Manager には Azure リージョンの障害に対する回復性がありますか。

トラフィック マネージャーは、Azure での高可用性アプリケーションの配信の重要なコンポーネントです。
高可用性を実現するには、Traffic Manager は非常に高いレベルの可用性を実現し、地域の障害に対応できる必要があります。

仕様では、Traffic Manager のコンポーネントは、どの Azure リージョン全体の障害に対しても耐障害性を持っています。 この回復性は Traffic Manager のすべての構成要素、つまり、DNS ネーム サーバー、API、ストレージ層、エンドポイント監視サービスに適用されます。

万一 Azure リージョン全体が停止した場合で、Traffic Manager は引き続き正常に機能すると予想されます。 複数の Azure リージョンにデプロイされたアプリケーションは、Traffic Manager を利用して、そのアプリケーションの利用可能なインスタンスにトラフィックを送ることができます。

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>リソース グループの場所の選択は Traffic Manager にどのような影響を与えますか。

Traffic Manager は、1 つのグローバル サービスです。 いずれかのリージョンに限定されたものではありません。 リソース グループの場所をどこに選択しても、そのリソース グループにデプロイされる Traffic Manager プロファイルに違いはありません。

Azure Resource Manager では、すべてのリソース グループに対して場所を指定することが求められます。これに基づいて、リソース グループにデプロイされたリソースの既定の場所が決定されます。 Traffic Manager プロファイルを作成するときには、プロファイルはリソース グループ内に作成されます。 Traffic Manager プロファイル自体の場所には、常に **グローバル** が使用され、リソース グループの既定値はオーバーライドされます。

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>各エンドポイントの現在の正常性を確認するには、どうすればよいですか。

各エンドポイントとプロファイル全体の現在の監視状態は、Azure ポータルに表示されます。 この情報は、Traffic Manager の [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx)、[PowerShell コマンドレット](https://msdn.microsoft.com/library/mt125941.aspx)、および [クロスプラットフォームの Azure CLI](../cli-install-nodejs.md) を使用して取得することもできます。

Azure では、過去のエンドポイントの正常性に関する履歴情報や、エンドポイントの正常性の変更に関してアラートを生成する機能は提供されません。

### <a name="can-i-monitor-https-endpoints"></a>HTTPS エンドポイントを監視できますか。

はい。 Traffic Manager は、HTTPS 経由のプローブをサポートしています。 監視構成でプロトコルとして **HTTPS** を構成します。

Traffic Manager は、次のように証明書の検証を提供できません。

* サーバー側証明書は検証されません。
* SNI サーバー側証明書はサポートされていません。
* クライアント証明書はサポートされていません。

### <a name="what-host-header-do-endpoint-health-checks-use"></a>エンドポイントの正常性チェックには、どのようなホストヘッダーが使用されますか。

トラフィック マネージャーは、HTTP および HTTPS の正常性チェックにホスト ヘッダーを使用します。 Traffic Manager で使用されるホスト ヘッダーは、プロファイルで構成されているエンドポイントのターゲットの名前です。 ホスト ヘッダーで使用される値を、ターゲットプロパティとは別に指定することはできません。

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>正常性チェックはどの IP アドレスから発信されますか。

次の一覧には、Traffic Manager の正常性チェックの実行元になる IP アドレスが含まれています。 この一覧を使用して、正常性状態をチェックするためにこれらの IP アドレスからの受信接続がエンドポイントで許可されるように設定できます。

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager の入れ子になったプロファイル

### <a name="how-do-i-configure-nested-profiles"></a>入れ子になったプロファイルを構成するにはどうすればよいですか。

入れ子になった Traffic Manager プロファイルは、Azure Resource Manager と従来の Azure REST API (Azure PowerShell コマンドレットとクロスプラットフォームの Azure CLI コマンド) を使用して構成できます。 新しい Azure Portal を使用する場合もサポートされます。 クラシック ポータルではサポートされません。

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
