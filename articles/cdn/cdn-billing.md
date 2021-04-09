---
title: Azure CDN での課金について | Microsoft Docs
description: 課金リージョン、配信料金、コストの管理方法など、Azure Content Delivery Network によってホストされているコンテンツの課金構造について説明します。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: aa2f00a732a3978524fc017481285859c9535387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018683"
---
# <a name="understanding-azure-cdn-billing"></a>Azure CDN での課金について

このよくあるご質問では、Azure Content Delivery Network (CDN) でホストされているコンテンツの課金の体系について説明します。

## <a name="what-is-a-billing-region"></a>請求先リージョンとは何ですか。
請求先リージョンとは、Azure CDN からのオブジェクトの配信に対して課金される料金を決定するための地理的領域です。 現在の請求先ゾーンとそれらのリージョンは以下のとおりです。

- ゾーン 1:北米、ヨーロッパ、中東、およびアフリカ

- ゾーン 2:アジア太平洋 (日本を含む)

- ゾーン 3:南アメリカ

- ゾーン 4:オーストラリアとニュージーランド

- ゾーン 5:インド

ポイント オブ プレゼンス (POP) のリージョンについては、[リージョン別の Azure CDN POP の場所](./cdn-pop-locations.md)についてのページを参照してください。 たとえば、メキシコに置かれた POP は北米リージョン内にあり、そのためゾーン 1 に含まれます。 

Azure CDN の価格については、「[Content Delivery Network の価格](https://azure.microsoft.com/pricing/details/cdn/)」をご覧ください。

## <a name="how-are-delivery-charges-calculated-by-region"></a>リージョンごとの配信料金はどのように計算されますか。
Azure CDN の請求先リージョンは、エンド ユーザーにコンテンツを配信するソース サーバーの場所に基づいています。 クライアントの宛先 (物理的な場所) は請求先リージョンとはみなされません。

たとえば、所在地がメキシコのユーザーが要求を発行し、この要求が、ピアリングまたはトラフィックの条件のために米国の POP にあるサーバーによって処理される場合、請求先リージョンは米国となります。

## <a name="what-is-a-billable-azure-cdn-transaction"></a>課金対象の Azure CDN トランザクションとは何ですか。
CDN で終了するすべての HTTP(S) 要求は課金対象イベントで、それには応答のすべての種類 (成功、失敗、またはその他) が含まれます。 ただし、応答が異なれば、生成されるトラフィック量が異なる可能性があります。 たとえば、*304 Not Modified* やその他のヘッダーのみの応答では、それらは小さなヘッダー応答であるため、生成されるトラフィックは少量です。同様に、エラー応答 (たとえば *404 Not Found*) は課金対象ですが、応答のペイロードがとても小さいため、かかるコストはわずかです。

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Azure CDN の使用には、その他にどのような Azure コストが関連付けられていますか。
Azure CDN の使用により、オブジェクトの配信元として使用されるサービスでもいくつかの使用料金が発生します。 これらのコストは通常、CDN 全体の使用コストのうち、ほんのわずかです。

コンテンツの配信元として Azure BLOB ストレージを使用している場合は、キャッシュ フィルに対して以下のストレージ料金も発生します。

- 使用された実際の GB:ソース オブジェクトの実際のストレージ。

- トランザクション:キャッシュに入力するための必要に応じて。

- GB 単位の転送:CDN キャッシュに入力するために転送されたデータの量。

> [!NOTE]
> 2019 年 10 月以降、Microsoft の Azure CDN を使用している場合、Azure でホストされている配信元から CDN Pop へのデータ転送のコストは無料です。 Verizon から Azure CDN および Akamai から Azure CDN には、以下で説明する料金が適用されます。

Azure Storage の課金の詳細については、「[Azure Storage の課金について - 帯域幅、トランザクション、容量](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)」を参照してください。

*ホステッド サービス配信* を使用している場合は、次のように料金が発生します。

- Azure のコンピューティング時間:配信元として動作するコンピューティング インスタンス。

- Azure のコンピューティング転送:Azure CDN のキャッシュに入力するためのコンピューティング インスタンスからのデータ転送。

クライアントが (配信元サービスとは無関係に) バイト範囲要求を使用している場合は、次の考慮事項が適用されます。

- *バイト範囲要求* は、CDN での課金対象トランザクションです。 クライアントがバイト範囲要求を発行する場合、この要求は、オブジェクトのサブセット (範囲) 用です。 CDN は、要求されたコンテンツの一部分のみで応答します。 この部分的応答は課金対象トランザクションで、転送量は範囲応答 (とヘッダー) のサイズに限られています。

- オブジェクトの一部のみに対する要求が (バイト範囲ヘッダーの指定によって) 到着すると、CDN はオブジェクト全体をキャッシュにフェッチする可能性があります。 結果として、CDN からの課金対象トランザクションが部分的応答に対するものであっても、配信元からの課金対象トランザクションにはフル サイズのオブジェクトが含まれることがあります。

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>キャッシュをサポートするために、どれほどの転送アクティビティが発生しますか。
CDN POP は、キャッシュへの入力を行う必要があるたびに、キャッシュされるオブジェクトの配信元に要求を発行します。 その結果、配信元ではキャッシュ ミスがあるたびに課金対象トランザクションが発生します。 キャッシュ ミスの数は、さまざまな要因に左右されます。

- コンテンツをどのようにキャッシュできるか:コンテンツの TTL (Time to Live)/有効期限の値が大きく、頻繁にアクセスされるためコンテンツがキャッシュ内でよく使われるままになる場合、負荷の大部分が CDN によって処理されます。 キャッシュ ヒット率は、良好な場合は一般に 90% を大幅に上回ります。これは、キャッシュ ミスまたはオブジェクトの更新のいずれかのために、配信元に返す必要があるクライアント要求は 10% 未満であることを意味します。

- オブジェクトを読み込む必要があるノードの数:ノードが配信元からオブジェクトを読み込むたびに、課金対象トランザクションが発生します。 その結果、(より多くのノードからアクセスされる) グローバルなコンテンツは、より多くの課金対象トランザクションになります。

- TTL の影響:オブジェクトの TTL が長いと、配信元からフェッチする必要がある頻度は低くなります。 これは、ブラウザーなどのクライアントは、オブジェクトをより長時間キャッシュできることも意味し、CDN に対するトランザクションを減らすことができます。

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Microsoft の Azure CDN を使用した無料のデータ転送の対象となるのは、どの配信元サービスですか。 
CDN の配信元として次のいずれかの Azure サービスを使用した場合、配信元から CDN Pop へのデータ転送は課金されません。 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Load Balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Azure App Service
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL データベース
- Azure SQL Managed Instance
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>最も効果的にコストを管理する方法を教えてください。
コンテンツにはできるだけ長い TTL を設定します。