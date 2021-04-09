---
title: FAQ - Azure DNS
description: この記事では、Azure DNS に関してよく寄せられる質問について説明します
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/11/2021
ms.author: rohink
ms.openlocfilehash: cb14cb95ec1362782a634b0e62cfa2f8237a5852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246384"
---
# <a name="azure-dns-faq"></a>Azure DNS に関する FAQ

## <a name="about-azure-dns"></a>Azure DNS について

### <a name="what-is-azure-dns"></a>Azure DNS とは

ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換 (解決) します。 Azure DNS は DNS ドメイン用のホスティング サービスです。 それは Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure DNS 内の DNS ドメインは、DNS ネーム サーバーから成る Azure のグローバル ネットワーク上でホストされます。 このシステムではエニーキャスト ネットワークが使用されるため、各 DNS クエリには、使用できる最も近い DNS サーバーが応答します。 Azure DNS によって、ドメインに高速なパフォーマンスと高可用性が提供されます。

Azure DNS は、Azure Resource Manager に基づいています。 Azure DNS では、Azure ロールベースのアクセス制御、監査ログ、リソース ロックなどの Resource Manager 機能を利用できます。 ドメインとレコードは、Azure portal、Azure PowerShell コマンドレット、およびクロス プラットフォームの Azure CLI を使用して管理できます。 DNS の自動管理を必要とするアプリケーションは、REST API と SDK を使用してサービスを統合できます。

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS の料金はいくらですか。

Azure DNS の課金モデルは、Azure DNS でホストされる DNS ゾーンの数に基づいています。 受信する DNS クエリの数にも基づいています。 使用量に基づいて割引が提供されます。

詳細については、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」ページをご覧ください。

### <a name="what-is-the-sla-for-azure-dns"></a>Azure DNS の SLA はどうなっていますか。

DNS 要求が有効な場合、Azure では 100% の時間において Azure DNS ネーム サーバーの少なくとも 1 つから応答を受信することが保証されます。

詳細については、「[Azure DNS のSLA](https://azure.microsoft.com/support/legal/sla/dns)」ページをご覧ください。

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>DNS ゾーンとは何ですか。 DNS ドメインと同じですか。 

ドメインとは、ドメイン ネーム システム内の一意の名前です。 一例として contoso.com があります。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 たとえば、ドメイン contoso.com に、複数の DNS レコードが含まれることがあります。 これらのレコードに、メール サーバー用の mail.contoso.com と Web サイト用の www\.contoso.com が含まれることがあります。 これらのレコードは DNS ゾーン contoso.com でホストされます。

ドメイン名は *単なる名前* です。 DNS ゾーンは、ドメイン名用の DNS レコードを含むデータ リソースです。 Azure DNS を使用すると、DNS ゾーンをホストし、Azure のドメインの DNS レコードを管理できます。 また、インターネットからの DNS クエリに応答する DNS ネーム サーバーも提供します。

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Azure DNS を使用するために DNS ドメイン名を購入する必要がありますか。 

必ずしもその必要はありません。

Azure DNS で DNS ゾーンをホストするためにドメインを購入する必要はありません。 ドメイン名を所有することなく、いつでも DNS ゾーンを作成することができます。 このゾーンに対する DNS クエリが解決されるのは、ゾーンに割り当てられた Azure DNS ネーム サーバーに送られた場合のみです。

DNS ゾーンをグローバル DNS 階層にリンクするには、ドメイン名を購入する必要があります。 そうすると、世界中のどこからでも、DNS クエリで DNS ゾーンを検出して、DNS レコードの回答を得ることができます。

## <a name="azure-dns-features"></a>Azure DNS の機能

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Traffic Manager でドメイン名の apex に対してエイリアス レコードを使用するときに、何か制限はありますか。

はい。 Azure Traffic Manager では静的パブリック IP アドレスを使用する必要があります。 静的 IP アドレスを使用して **外部エンドポイント** ターゲットを構成してください。 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS では DNS ベースのトラフィック ルーティングまたはエンドポイント フェールオーバーがサポートされますか。

DNS ベースのトラフィック ルーティングとエンドポイント フェールオーバーは Traffic Manager によって提供されます。 Traffic Manager は、Azure DNS と共に使用できる別の Azure サービスです。 詳細については、[Traffic Manager の概要](../traffic-manager/traffic-manager-overview.md)を参照してください。

Azure DNS では、静的な DNS ドメインのみサポートされます。つまり、特定の DNS レコードに対する DNS クエリでは、常に同じ DNS 応答が返されます。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS ではドメイン名の登録がサポートされますか。

いいえ。 現在、Azure DNS では、ドメイン名を購入するというオプションはサポートされていません。 ドメインを購入するには、サード パーティのドメイン名レジストラーを使用する必要があります。 レジストラーは、通常、少額の年間料金がかかります。 購入後、ドメインを Azure DNS でホストして DNS レコードを管理できます。 詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

ドメインの購入は Azure バックログで追跡される機能です。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)してください。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS では DNSSEC はサポートされますか。

いいえ。 現在、Azure DNS では、ドメイン ネーム システム セキュリティ拡張機能 (DNSSEC) はサポートされていません。

DNSSEC 機能は、Azure DNS バックログで追跡されます。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)してください。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS ではゾーン転送 (IXFR AXFR/) はサポートされますか。

いいえ。 現在、Azure DNS でゾーン転送はサポートされていません。 DNS ゾーンは [Azure CLI を使用して Azure DNS にインポート](dns-import-export.md)できます。 DNS レコードは、[Microsoft Azure DNS 管理ポータル](dns-operations-recordsets-portal.md)、[REST API](/powershell/module/az.dns)、[SDK](dns-sdk.md)、[PowerShell コマンドレット](dns-operations-recordsets.md)、または [CLI ツール](dns-operations-recordsets-cli.md)で管理できます。

ゾーン転送機能は、Azure DNS バックログで追跡されます。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)してください。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS では URL リダイレクトはサポートされますか。

いいえ。 URL リダイレクト サービスは DNS サービスではありません。 それらは、DNS レベルではなく、HTTP レベルで機能します。 DNS プロバイダーによっては、総合的なオファーの一部として、URL リダイレクト サービスをバンドルしています。 現在、このサービスは、Azure DNS ではサポートされていません。

URL リダイレクト機能は Azure DNS バックログで追跡されます。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)してください。

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Azure DNS では、TXT レコードセットの拡張 ASCII エンコード (8 ビット) セットはサポートされますか。

はい。 Azure DNS では、TXT レコードセットの拡張 ASCII エンコード セットがサポートされています。 ただし、最新バージョンの Azure REST API、SDK、PowerShell、CLI を使用する必要があります。 2017 年 10 月 1 日より前のバージョンまたは SDK 2.1 では、拡張 ASCII セットはサポートされません。 

たとえば、TXT レコードの値として、拡張 ASCII 文字 \128 を含む文字列が指定されたとします。 たとえば "abcd \128efgh" です。 Azure DNS では、この文字のバイト値 (128) を内部表現で使用します。 DNS 解決時には、応答でこのバイト値が返されます。 また、解決に関する限り、"abc" と "\097\098\099" は代替可能です。 

TXT レコードについては、[RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) のゾーン ファイルのマスター形式のエスケープ規則に従います。 たとえば、RFC に従い、`\` は実際にはあらゆるものをエスケープします。 TXT レコード値として `A\B` を指定した場合、単に `AB` と表現され、解決されます。 解決時に TXT レコードに `A\B` を含める必要がある場合は、`\` を再度エスケープする必要があります。 たとえば、`A\\B` と指定します。

現在、このサポートは、Azure portal から作成された TXT レコードでは利用できません。

## <a name="alias-records"></a>エイリアス レコード

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>エイリアス レコードが役に立つのはどのようなシナリオですか。

「[Azure DNS エイリアス レコードの概要](dns-alias.md)」の「シナリオ」セクションをご覧ください。

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>エイリアス レコード セットにはどのようなレコードの種類がサポートされていますか。

Azure DNS ゾーンでは、エイリアス レコード セットとして、次の種類のレコードがサポートされています。
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>エイリアス レコード セットのターゲットとしては、どのようなリソースがサポートされていますか。

- **DNS の A または AAAA レコード セットからパブリック IP リソースにポイントする**。 A または AAAA レコード セットを作成し、パブリック IP リソースをポイントするエイリアス レコード セットにすることができます。
- **DNS の A、AAAA または CNAME レコード セットから Traffic Manager プロファイルをポイントする**。 DNS の CNAME レコード セットから Traffic Manager プロファイルの CNAME をポイントできます。 一例は contoso.trafficmanager.net です。 DNS ゾーン内の A または AAAA レコード セットから、外部エンドポイントがある Traffic Manager プロファイルをポイントすることもできます。
- **Azure Content Delivery Network (CDN) エンドポイントをポイントする**。 これは、Azure Storage と Azure CDN を使って静的な Web サイトを作成する場合に便利です。
- **同じゾーン内の別の DNS レコード セットをポイントする**。 エイリアス レコードでは、同じ種類の別のレコード セットを参照することができます。 たとえば、DNS の CNAME レコード セットを同じ種類の別の CNAME レコード セットのエイリアスにできます。 この配置は、一部のレコード セットをエイリアスにしたり、一部をエイリアスにしたくない場合に便利です。

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Azure portal からエイリアス レコードを作成および更新できますか。

はい。 Azure portal、Azure REST API、PowerShell、CLI、および SDK で、エイリアス レコードを作成して管理できます。

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>エイリアス レコードは、基になるパブリック IP が削除されたときに DNS レコード セットを確実に削除するのに役立ちますか。

はい。 この機能は、エイリアス レコードの主要な機能の 1 つです。 それは、アプリケーションのユーザーに対する障害の可能性を回避するのに役立ちます。

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>エイリアス レコードは、基になるパブリック IP アドレスが変更されたときに、DNS レコード セットを正しい IP アドレスに確実に更新するのに役立ちますか。

はい。 この機能は、エイリアス レコードの主要な機能の 1 つです。 それは、アプリケーションの障害の可能性やセキュリティ リスクを回避するのに役立ちます。

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Traffic Manager をポイントする A または AAAA レコードに対してエイリアス レコード セットを使用するときに、何か制限はありますか。

はい。 A または AAAA レコード セットからエイリアスとして Traffic Manager プロファイルをポイントする場合は、Traffic Manager プロファイルで外部エンドポイントのみを使用する必要があります。 Traffic Manager で外部エンドポイントを作成するときに、エンドポイントの実際の IP アドレスを指定します。

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>エイリアス レコードを使用すると追加料金が発生しますか。

エイリアス レコードは、有効な DNS レコード セットを修飾するものです。 エイリアス レコードに対する追加料金はありません。

## <a name="use-azure-dns"></a>Azure DNS の使用

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Azure DNS と別の DNS プロバイダーを使用して 1 つのドメインを共同ホスティングできますか。

はい。 Azure DNS では、他の DNS サービスとのドメインの共同ホスティングがサポートされます。

共同ホスティングを設定するには、両方のプロバイダーのネーム サーバーをポイントするようにドメインの NS レコードを変更します。 ドメインに対する DNS クエリを受信するプロバイダーは、ネーム サーバー (NS) レコードによって制御されます。 Azure DNS、他のプロバイダー、および親ゾーン内のこれらの NS レコードを変更できます。 通常、親ゾーンは、ドメイン名レジストラーによって構成されます。 DNS 委任の詳細については、[DNS へのドメインの委任](dns-domain-delegation.md)に関する記事を参照してください。

また、ドメイン用の DNS レコードが両方の DNS プロバイダー間で同期していることを確認してください。 現在、Azure DNS では DNS ゾーン転送はサポートされていません。 DNS レコードは、[Azure DNS 管理ポータル](dns-operations-recordsets-portal.md)、[REST API](/rest/api/dns/)、[SDK](dns-sdk.md)、[PowerShell コマンドレット](dns-operations-recordsets.md)、[CLI ツール](dns-operations-recordsets-cli.md)のいずれかを使用して同期する必要があります。

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>自分のドメインを 4 個の Azure DNS ネーム サーバーすべてに委任する必要がありますか。

はい。 Azure DNS は、各 DNS ゾーンに 4 個のネーム サーバーを割り当てます。 この配置は、障害を分離し、復元性を向上させるためのものです。 Azure DNS の SLA を満たすために、ドメインを 4 個すべてのネーム サーバーに委任してください。

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS の使用制限とは何ですか。

Azure DNS を使用する際は、次の制限が既定で適用されます。

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Azure DNS ゾーンをリソース グループ間またはサブスクリプション間で移動できますか。

はい。 DNS ゾーンは、リソース グループ間またはサブスクリプション間で移動できます。

DNS ゾーンを移動しても、DNS クエリへの影響はありません。 ゾーンに割り当てられたネーム サーバーは変わりません。 DNS クエリは、全体が通常どおりに処理されます。

DNS ゾーンの移動に関する詳細と手順については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS の変更が有効になるまでどれくらいかかりますか。

通常、新しい DNS ゾーンと DNS レコードは、Azure DNS ネーム サーバー内にすぐに表示されます。 タイミングはほんの数秒です。

既存の DNS レコードの変更は、少し長くかかる可能性があります。 通常は、60 秒以内に Azure DNS ネーム サーバーに表示されます。 DNS クライアントと Azure DNS を除く DNS 再帰リゾルバーによる DNS キャッシュも、タイミングに影響を与える可能性があります。 このキャッシュ期間を制御するには、各レコード セットの Time-To-Live (TTL) プロパティを使用します。

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>誤って削除されないように DNS ゾーンを保護するにはどうすればよいですか。

Azure DNS の管理は、Azure Resource Manager を使用して行われます。 Azure DNS では、Azure Resource Manager が提供するアクセス制御機能を利用しています。 Azure ロールベースのアクセス制御を使用すると、DNS ゾーンとレコード セットへの読み取りアクセスまたは書き込みアクセスを持つユーザーを制御できます。 リソース ロックによって、DNS ゾーンとレコード セットを誤って変更または削除することを回避します。

詳細については、「[DNS ゾーンとレコードを保護する方法](dns-protect-zones-recordsets.md)」を参照してください。

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Azure DNS 内で SPF レコードを設定するにはどうすればよいですか。

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Azure DNS ネーム サーバーは IPv6 で解決しますか。 

はい。 Azure DNS ネーム サーバーは、デュアル スタックです。 デュアル スタックとは、IPv4 アドレスと IPv6 アドレスがあることを意味します。 DNS ゾーンに割り当てられている Azure DNS ネーム サーバーの IPv6 アドレスを調べるには、nslookup などのツールを使用します。 たとえば `nslookup -q=aaaa <Azure DNS Nameserver>` です。

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Azure DNS に IDN を設定するにはどうすればよいですか。

国際化ドメイン名 (IDN) は、[punycode](https://en.wikipedia.org/wiki/Punycode) を使用して各 DNS 名をエンコードします。 このような punycode エンコード名を使用して、DNS クエリが作成されます。

Azure DNS に IDN を構成するには、ゾーン名またはレコード セット名を punycode に変換します。 現在、Azure DNS では punycode に対する組み込みの変換機能はサポートされていません。

## <a name="next-steps"></a>次のステップ

- [Azure DNS の詳細を確認する](dns-overview.md)

- [プライベート ドメインで Azure DNS を使用する方法を確認する](private-dns-overview.md)

- [DNS ゾーンとレコードの詳細を確認する](dns-zones-records.md)

- [Azure DNS の使用を開始する](dns-getstarted-portal.md)
