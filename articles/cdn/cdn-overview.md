---
title: "Azure CDN の概要 | Microsoft Docs"
description: "Azure Content Delivery Network (CDN) の概要と、CDN を使用して BLOB と静的コンテンツをキャッシュして高帯域幅コンテンツを配信する方法について説明します。"
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/08/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: 6c8c04e0237e2168df60ecc597754c38af167e14
ms.openlocfilehash: 0aed3d224199b17bac748b14416a738eac342103


---
# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Azure Content Delivery Network (CDN) の概要
> [!NOTE]
> このドキュメントでは、Azure Content Delivery Network (CDN) の概要、しくみ、各 Azure CDN 製品の機能について説明します。  この情報をスキップし、CDN エンドポイントの作成方法に関するチュートリアルに直接進む場合は、「 [Azure CDN の使用](cdn-create-new-endpoint.md)」を参照してください。  現在の CDN ノードの場所の一覧については、「 [Azure CDN POP Locations (Azure CDN の POP の場所)](cdn-pop-locations.md)」を参照してください。
> 
> 

Azure Content Delivery Network (CDN) では、戦略的に配置された場所に静的 Web コンテンツをキャッシュし、ユーザーへのコンテンツ配信に最大スループットを使用できます。  CDN では、世界各地の物理ノードにコンテンツをキャッシュすることによって、高帯域幅コンテンツを配信するためのグローバル ソリューションを開発者に提供します。 

CDN を使用して Web サイト資産をキャッシュすると、次のような利点があります。

* エンド ユーザーのパフォーマンスとユーザー エクスペリエンスの向上。コンテンツの読み込みに複数のラウンドトリップか必要なアプリケーションを使用する場合は特にそうです。
* 製品発表イベントの開始時のような、瞬間的高負荷を処理しやすくする大型のスケーリング。
* ユーザー要求を分散させ、コンテンツをエッジ サーバーから配信することによる、配信元へのトラフィックの削減。

## <a name="how-it-works"></a>動作のしくみ
![CDN の概要](./media/cdn-overview/cdn-overview.png)

1. ユーザー (Alice) は、特殊なドメイン名 ( `<endpointname>.azureedge.net`など) の URL を使用して、ファイル (資産とも呼ばれます) を要求します。  DNS は、パフォーマンスが最高の Point-of-Presence (POP) の場所に要求をルーティングします。  通常は、ユーザーに地理的に最も近い位置にある POP です。
2. POP のエッジ サーバーのキャッシュにファイルがない場合、エッジ サーバーは配信元にあるファイルを要求します。  配信元は、Azure Web App、Azure Cloud Service、Azure ストレージ アカウント、またはパブリックにアクセスできる Web サーバーです。
3. 配信元からエッジ サーバーに対して、ファイルの有効期間 (TTL) を記述したオプションの HTTP ヘッダーなどのファイルが戻されます。
4. エッジ サーバーはファイルをキャッシュし、ファイルを要求元 (Alice) に返します。  TTL が期限切れになるまで、ファイルはエッジ サーバーにキャッシュされた状態になります。  配信元で TTL を指定していなかった場合、既定の TTL は&7; 日間です。
5. その他のユーザーが同じ URL を使用して同じファイルを要求し、同じ POP に転送することもできます。
6. ファイルの TTL が期限切れになっていない場合、エッジ サーバーはキャッシュのファイルを返します。  その結果、応答時間が短縮されます。

## <a name="azure-cdn-features"></a>Azure CDN の機能
Azure CDN 製品には、**Azure CDN Standard from Akamai**、**Azure CDN Standard from Verizon**、**Azure CDN Premium from Verizon** の&3; つがあります。  次の表に、各製品で使用できる機能を示します。

|  | Standard Akamai | Standard Verizon | Premium Verizon |
| --- | --- | --- | --- |
| [Storage](cdn-create-a-storage-account-with-cdn.md)、[Cloud Services](cdn-cloud-service-with-cdn.md)、[Web Apps](../app-service-web/cdn-websites-with-cdn.md)、[Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) などの Azure サービスと簡単に統合 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [REST API](https://msdn.microsoft.com/library/mt634456.aspx)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md)、[PowerShell](cdn-manage-powershell.md) を介した管理。 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| CDN エンドポイントでの HTTPS のサポート |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| カスタム ドメイン HTTPS | |**&#x2713;** |**&#x2713;** |
| 負荷分散 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) 保護 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 デュアルスタック |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [カスタム ドメイン名のサポート](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [クエリ文字列のキャッシュ](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtering](cdn-restrict-access-by-country.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [高速消去](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [資産の事前読み込み](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| [コア分析](cdn-analyze-usage-patterns.md) | |**&#x2713;** |**&#x2713;** |
| [HTTP/2 のサポート](https://msdn.microsoft.com/library/mt762901.aspx) |**&#x2713;** | | |
| [詳細な HTTP レポート](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [リアルタイム統計](cdn-real-time-stats.md) | | |**&#x2713;** |
| [リアルタイム アラート](cdn-real-time-alerts.md) | | |**&#x2713;** |
| [カスタマイズ可能なルール ベースのコンテンツ配信エンジン](cdn-rules-engine.md) | | |**&#x2713;** |
| キャッシュ/ヘッダーの設定 ( [ルール エンジン](cdn-rules-engine.md)を使用) | | |**&#x2713;** |
| URL のリダイレクト/書き換え ([ルール エンジン](cdn-rules-engine.md)を使用) | | |**&#x2713;** |
| モバイル デバイスのルール ( [ルール エンジン](cdn-rules-engine.md)を使用) | | |**&#x2713;** |
| [認証トークン](cdn-token-auth.md)|  |  |**&#x2713;**| 


> [!TIP]
> Azure CDN に搭載してほしい機能がありましたら、  [ぜひご意見をお聞かせください](https://feedback.azure.com/forums/169397-cdn)。 
> 
> 

## <a name="next-steps"></a>次のステップ
CDN の概要については、「 [Azure CDN の使用](cdn-create-new-endpoint.md)」を参照してください。

既存の CDN 顧客は、[Microsoft Azure Portal](https://portal.azure.com) から、または [PowerShell](cdn-manage-powershell.md) を使用して、CDN エンドポイントを管理できるようになりました。

実際の CDN の効果については、 [Build 2016 セッションのビデオ](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/)をご覧ください。

[.NET](cdn-app-dev-net.md) または [Node.js](cdn-app-dev-node.md) を使用して Azure CDN を自動化する方法について学習します。

料金情報については、 [CDN の価格](https://azure.microsoft.com/pricing/details/cdn/)に関するページを参照してください。




<!--HONumber=Feb17_HO2-->


