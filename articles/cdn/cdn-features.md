---
title: Azure Content Delivery Network (CDN) 製品の機能の比較
description: 各 Azure Content Delivery Network (CDN) 製品がサポートする機能について説明します。
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3a794d8915c820abecd530911f05ac649e5e538c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379915"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Azure CDN 製品の機能比較

Azure Content Delivery Network (CDN) には、 

* **Azure CDN Standard from Microsoft**
* **Azure CDN Standard from Akamai**
* **Azure CDN Standard from Verizon**
* **Azure CDN Premium from Verizon**. 

次の表では、各製品で使用できる機能を比較しています。

| **パフォーマンス機能と最適化** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [動的サイト アクセラレーション](./cdn-dynamic-site-acceleration.md)  | [Azure Front Door Service](../frontdoor/front-door-overview.md) により提供 | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動的サイトの高速化 - アダプティブ画像圧縮](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[動的サイト アクセラレーション - オブジェクトのプリフェッチ](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [一般的な Web 配信の最適化](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;** (平均ファイル サイズが 10 MB より小さい場合には、この最適化の種類を選択します)  | **&#x2713;** |  **&#x2713;** |
| [ビデオ ストリーミングの最適化](./cdn-media-streaming-optimization.md)  | 一般的な Web 配信経由 | **&#x2713;**  | 一般的な Web 配信経由 |  一般的な Web 配信経由 |
| [大きなファイルの最適化](./cdn-large-file-optimization.md)  | 一般的な Web 配信経由 | **&#x2713;** (平均ファイル サイズが 10 MB より大きい場合には、この最適化の種類を選択します)   | 一般的な Web 配信経由 |  一般的な Web 配信経由 |
| 最適化の種類を変更する | |**&#x2713;** | | |
| 配信元のポート |すべての TCP ポート |[使用できる配信元ポート](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |すべての TCP ポート |すべての TCP ポート |
| [グローバル サーバー負荷分散 (GSLB)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [高速消去](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** (すべての消去およびワイルドカードによる消去は、現在 Azure CDN from Akamai ではサポートされていません) |**&#x2713;** |**&#x2713;** |
| [アセットの事前読み込み](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| キャッシュ/ヘッダーの設定 ( [キャッシュ規則](cdn-caching-rules.md)を使用)  |**&#x2713;** ([Standard ルール エンジン](cdn-standard-rules-engine.md)を使用)  |**&#x2713;** |**&#x2713;** | |
| カスタマイズ可能なルール ベースのコンテンツ配信エンジン |**&#x2713;** ([Standard ルール エンジン](cdn-standard-rules-engine.md)を使用)  | | |**&#x2713;** ([ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用) |
| キャッシュ/ヘッダーの設定  |**&#x2713;** ([Standard ルール エンジン](cdn-standard-rules-engine.md)を使用) | | |**&#x2713;** ([Premium ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用) |
| URL のリダイレクト/書き換え |**&#x2713;** ([Standard ルール エンジン](cdn-standard-rules-engine.md)を使用)  | | |**&#x2713;** ([Premium ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用) |
| モバイル デバイスのルール  |**&#x2713;** ([Standard ルール エンジン](cdn-standard-rules-engine.md)を使用) | | |**&#x2713;** ([Premium ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用) |
| [クエリ文字列のキャッシュ](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 デュアルスタック | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 のサポート](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Security** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| CDN エンドポイントでの HTTPS のサポート | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [カスタム ドメイン HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** 、有効にするには直接 CNAME が必要です |**&#x2713;** |**&#x2713;** |
| [カスタム ドメイン名のサポート](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtering](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [認証トークン](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS 保護](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [独自の証明書の持ち込み](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| サポートされている TLS バージョン | TLS 1.2、TLS 1.0 または 1.1 - [構成可能](/rest/api/cdn/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
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
| [Storage](cdn-create-a-storage-account-with-cdn.md)、 [Web Apps](cdn-add-to-web-app.md)、および [Media Services](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) などの Azure サービスと簡単に統合できる  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [REST API](/rest/api/cdn/)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md)、または [PowerShell](cdn-manage-powershell.md) を介した管理  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [圧縮の MIME の種類](./cdn-improve-performance.md)  |既定値のみ |構成可能 |構成可能  |構成可能  |
| 圧縮のエンコード  |gzip、brotli |gzip |gzip、deflate、bzip2、brotli  |gzip、deflate、bzip2、brotli  |

## <a name="migration"></a>移行

**Azure CDN Standard from Verizon** プロファイルの **Azure CDN Premium from Verizon** への移行については、「[Standard Verizon から Premium Verizon に Azure CDN プロファイルを移行する](cdn-migrate.md)」を参照してください。 

> [!NOTE]
> Standard Verizon から Premium Verizon へのアップグレード パスはありますが、現時点では他の製品間の変換メカニズムはありません。

## <a name="next-steps"></a>次の手順

* [Azure CDN](cdn-overview.md) についての詳しい情報をご覧ください。
