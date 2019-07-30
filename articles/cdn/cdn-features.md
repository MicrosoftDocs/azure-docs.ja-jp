---
title: Azure Content Delivery Network (CDN) 製品の機能の比較 | Microsoft Docs
description: 各 Azure Content Delivery Network (CDN) 製品がサポートする機能について説明します。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: e34fbe200420fef1653e21bb44ac2f7e0de90fb7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593862"
---
# <a name="compare-azure-cdn-product-features"></a>Azure CDN 製品の機能を比較する

Azure Content Delivery Network (CDN) には、**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Akamai**、**Azure CDN Standard from Verizon**、**Azure CDN Premium from Verizon** の 4 つの製品が含まれています。 **Azure CDN Standard from Verizon** プロファイルの **Azure CDN Premium from Verizon** への移行については、「[Standard Verizon から Premium Verizon に Azure CDN プロファイルを移行する](cdn-migrate.md)」を参照してください。

次の表では、各製品で使用できる機能を比較しています。

| **パフォーマンス機能と最適化** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [動的サイト アクセラレーション](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) により提供 | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動的サイトの高速化 - アダプティブ画像圧縮](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動的サイト アクセラレーション - オブジェクトのプリフェッチ](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [一般的な Web 配信の最適化](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** (平均ファイル サイズが 10 MB より小さい場合には、この最適化の種類を選択します)  | **&#x2713;** |  **&#x2713;** |
| [ビデオ ストリーミングの最適化](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | 一般的な Web 配信経由 | **&#x2713;**  | 一般的な Web 配信経由 |  一般的な Web 配信経由 |
| [大きなファイルの最適化](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | 一般的な Web 配信経由 | **&#x2713;** (平均ファイル サイズが 10 MB より大きい場合には、この最適化の種類を選択します)   | 一般的な Web 配信経由 |  一般的な Web 配信経由 |
| 最適化の種類を変更する | |**&#x2713;** | | |
| 配信元のポート |すべての TCP ポート |[使用できる配信元ポート](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |すべての TCP ポート |すべての TCP ポート |
| [グローバル サーバー負荷分散 (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [高速消去](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** (すべての消去およびワイルドカードによる消去は、現在 Azure CDN from Akamai ではサポートされていません) |**&#x2713;** |**&#x2713;** |
| [資産の事前読み込み](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| キャッシュ/ヘッダーの設定 ( [キャッシュ規則](cdn-caching-rules.md)を使用)  |  |**&#x2713;** |**&#x2713;** | |
| カスタマイズ可能なルール ベースのコンテンツ配信エンジン ([ルール エンジン](cdn-rules-engine.md)を使用)  |  | | |**&#x2713;** |
| キャッシュ/ヘッダーの設定 ( [ルール エンジン](cdn-rules-engine.md)を使用)  |  | | |**&#x2713;** |
| URL のリダイレクト/書き換え ( [ルール エンジン](cdn-rules-engine.md)を使用)  |  | | |**&#x2713;** |
| モバイル デバイスのルール ( [ルール エンジン](cdn-rules-engine.md)を使用)  |  | | |**&#x2713;** |
| [クエリ文字列のキャッシュ](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 デュアルスタック | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 のサポート](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **セキュリティ** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| CDN エンドポイントでの HTTPS のサポート | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [カスタム ドメイン HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** 、有効にするには直接 CNAME が必要です |**&#x2713;** |**&#x2713;** |
| [カスタム ドメイン名のサポート](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtering](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [認証トークン](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS 保護](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [独自の証明書の持ち込み](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
||||
| **分析とレポート** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure 診断ログ](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon からのコア レポート](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Verizon からのカスタム レポート](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [詳細な HTTP レポート](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [リアルタイム統計](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [エッジ ノードのパフォーマンス](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [リアルタイム アラート](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **使いやすさ** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Storage](cdn-create-a-storage-account-with-cdn.md)、 [Web Apps](cdn-add-to-web-app.md)、および [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) などの Azure サービスと簡単に統合できる  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [REST API](/rest/api/cdn/)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md)、または [PowerShell](cdn-manage-powershell.md) を介した管理  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [圧縮の MIME の種類](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |既定値のみ |構成可能 |構成可能  |構成可能  |
| 圧縮のエンコード  |gzip、brotli |gzip |gzip、deflate、bzip2、brotili  |gzip、deflate、bzip2、brotili  |






