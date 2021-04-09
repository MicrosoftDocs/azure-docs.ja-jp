---
title: Azure Traffic Manager のしくみ | Microsoft Docs
description: この記事は、Traffic Manager でトラフィックをルーティングし、Web アプリケーションの高いパフォーマンスと可用性を実現するしくみを理解するために役立ちます
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: a1e1bd107e8b3b9209f99d1abfc4d7e391c3c4a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184341"
---
# <a name="how-traffic-manager-works"></a>Traffic Manager のしくみ

Azure Traffic Manager を使用すると、アプリケーション エンドポイント全体でトラフィックの分散を制御できます。 エンドポイントは、Azure の内部または外部でホストされている、インターネットに公開されたサービスです。

Traffic Manager の 2 つのメリットを次に示します。

- 複数の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)
- [エンドポイントの正常性を継続的に監視](traffic-manager-monitoring.md) し、エンドポイントで障害が発生したときに、自動フェールオーバーが行われます

クライアントからサービスへの接続を試みようとするときは、最初にサービスの DNS 名を IP アドレスに解決する必要があります。 その後、クライアントは、その IP アドレスに接続してサービスにアクセスします。

**ここで把握しておかなければならない重要なポイントは、Traffic Manager が DNS レベルで動作するということです。**  Traffic Manager は、DNS を使用して、トラフィックのルーティング メソッドのルールに基づいてクライアントを特定のサービス エンドポイントに転送します。 選択されたエンドポイントに、クライアントが **直接** 接続します。 Traffic Manager は、プロキシでもゲートウェイでもありません。 Traffic Manager は、クライアントとサービスの間を通過するトラフィックを認識することはありません。

## <a name="traffic-manager-example"></a>Traffic Manager の例

Contoso Corp が、新しいパートナー ポータルを開発しました。 このポータルの URL は `https://partners.contoso.com/login.aspx` です。 アプリケーションは、Azure の 3 つのリージョンにホストされます。 可用性を向上させ、グローバル パフォーマンスを最大化するには、Traffic Manager を使用して、利用可能な最も近いエンドポイントにクライアント トラフィックを分散します。

この構成を実現するために、次の手順を実行します。

1. 3 つのサービス インスタンスをデプロイします。 各デプロイの DNS 名は "contoso-us.cloudapp.net"、"contoso-eu.cloudapp.net"、および "contoso-asia.cloudapp.net" です。
1. "contoso.trafficmanager.net" という名前の Traffic Manager プロファイルを作成し、3 つのエンドポイントで "パフォーマンス" トラフィック ルーティング方法を使用するように構成します。
1. DNS CNAME レコードを使用して、"contoso.trafficmanager.net" を参照するようにバニティ ドメイン名 "partners.contoso.com" を構成します。

![Traffic Manager の DNS 構成][1]

> [!NOTE]
> Azure Traffic Manager でバニティ ドメインを使用する場合は、CNAME を使用して、バニティ ドメイン名が、Traffic Manager ドメイン名を指すようにする必要があります。 DNS の標準では、ドメインの "頂点" (またはルート) で CNAME を作成できません。 したがって "contoso.com" ("ネイキッド" ドメインと呼ばれることがあります) の CNAME を作成することはできません。 CNAME は、"contoso.com" ("www.contoso.com" など) の下にあるドメインについてのみ作成できます。 この制限を回避するために、DNS ドメインを [Azure DNS](../dns/dns-overview.md) 上にホストし、[エイリアス レコード](../dns/tutorial-alias-tm.md)を使用して Traffic Manager プロファイルを指すことをお勧めします。 または、シンプルな HTTP リダイレクトを使用して、'contoso.com' に対する要求を、'www.contoso.com' などの代替名に転送することもできます。

### <a name="how-clients-connect-using-traffic-manager"></a>Traffic Manager でのクライアントの接続方法

前の例に続いて、クライアントがページ `https://partners.contoso.com/login.aspx` を要求すると、クライアントが次の手順を実行して DNS 名を解決して接続を確立します。

![Traffic Manager を使用した接続の確立][2]

1. クライアントは、構成されている再帰 DNS サービスに対して DNS クエリを送信し、名前 "partners.contoso.com" を解決します 再帰 DNS サービス ("ローカル DNS" サービスと呼ばれることもあります) は、DNS ドメインを直接ホストしません。 代わりに、クライアントは、DNS 名を解決するために必要となる、信頼できるさまざまな DNS サービスにインターネット経由で接続する作業をオフロードします。
2. DNS 名を解決するために、再帰 DNS サービスは、"contoso.com" ドメインのネーム サーバーを見つけます。 次に、そのネーム サーバーに接続して、"partners.contoso.com" DNS レコードを要求します。 "contoso.com" DNS サーバーは、"contoso.trafficmanager.net" を指す CNAME レコードを返します。
3. 次に、再帰 DNS サービスは、Azure Traffic Manager サービスで提供される、"trafficmanager.net" ドメインのネーム サーバーを見つけます。 見つけた DNS サーバーに "contoso.trafficmanager.net" の DNS レコードの要求を送信します。
4. Traffic Manager ネーム サーバーは要求を受け取り、 次の内容に基づいてエンドポイントを選択します。

    - 各エンドポイントの構成済みの状態 (無効なエンドポイントは返されません)
    - 各エンドポイントの現在の正常性。Traffic Manager の正常性チェックによって確認されます。 詳細については、 [Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md)に関する記事をご覧ください。
    - 選択されているトラフィック ルーティング方法。 詳細については、「[Traffic Manager のルーティング方法](traffic-manager-routing-methods.md)」を参照してください。

5. 選択されたエンドポイントは、別の DNS の CNAME レコードとして返されます。 この場合は、"contoso-us.cloudapp.net" が返されるものとします。
6. 次に、再帰 DNS サービスが、"cloudapp.net" ドメインのネーム サーバーを見つけます。 これらのネーム サーバーに接続して、"contoso-eu.cloudapp.net" DNS レコードを要求します。 EU を拠点とするサービス エンドポイントの IP アドレスが含まれる DNS の "A" レコードが返されます。
7. 再帰 DNS サービスは、結果を統合し、クライアントに単一の DNS 応答を返します。
8. クライアントは、DNS 結果を受け取り、指定された IP アドレスに接続します。 クライアントは、アプリケーション サービス エンドポイントに Traffic Manager 経由ではなく直接接続します。 これは HTTPS エンドポイントであるため、クライアントは必要な SSL/TLS ハンドシェイクを実行し、"/login.aspx" ページに対して HTTP GET 要求を実行します。

再帰 DNS サービスでは、受信した DNS 応答をキャッシュします。 クライアント デバイス上の DNS リゾルバーも、結果をキャッシュします。 キャッシュのデータを使用することで、後続の DNS クエリに対する応答が迅速になります。他のネーム サーバーにクエリを実行する必要はありません。 キャッシュの期間は、各 DNS レコードの "Time-to-Live" (TTL) プロパティによって決まります。 値を小さくすると、キャッシュの有効期限が短くなるため、Traffic Manager ネーム サーバーとのラウンドトリップが増加します。 値を大きくすると、失敗したエンドポイントからトラフィックを転送するときにより時間がかかる可能性があります。 Traffic Manager では、Traffic Manager の DNS 応答で使用する TTL を 0 ～ 2,147,483,647 秒に構成できるため (最大範囲は [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt) に準拠している)、アプリケーションのニーズの最適なバランスを実現する値を選択できます。

## <a name="faqs"></a>FAQ

* [Traffic Manager ではどの IP アドレスが使用されますか。](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Traffic Manager を使用して、どのような種類のトラフィックをルーティングできますか。](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Traffic Manager では "スティッキー" セッションはサポートされていますか。](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Traffic Manager を使用していると HTTP エラーが表示されたのはなぜですか。](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Traffic Manager を使用すると、パフォーマンスにどのような影響がありますか。](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Traffic Manager ではどのようなアプリケーション プロトコルを使用できますか](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* ["ネイキッド" ドメイン名で Traffic Manager を使用することはできますか。](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Traffic Manager では、DNS クエリを処理するときにクライアントのサブネット アドレスは考慮されますか。](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [DNS TTL とは何ですか。DNS TTL はユーザーにどのような影響を及ぼしますか。](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Traffic Manager の応答に設定できる TTL の値を教えてください。](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [マイ プロファイルへのクエリの量を把握する方法を教えてください。](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>次のステップ

Traffic Manager の [エンドポイントの監視と自動フェールオーバー](traffic-manager-monitoring.md)の詳細を確認する。

Traffic Manager の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)の詳細を確認する。

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png