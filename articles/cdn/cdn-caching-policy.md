---
title: Azure Media Services での Azure CDN キャッシュ ポリシーの管理 | Microsoft Docs
description: この記事では、Azure Media Services で Azure CDN キャッシュ ポリシーを管理する方法について説明します。
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 5807d38e22d8cecf40b5ad4262f9e4662b77ec4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779137"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure Media Services での Azure CDN キャッシュ ポリシーの管理
Azure Media Services では、HTTP ベースのアダプティブ ストリーミングとプログレッシブ ダウンロードが提供されます。 HTTP ベースのストリーミングは、プロキシと CDN レイヤーにおけるキャッシュだけでなくクライアント側のキャッシュの利点もあり、高い拡張性を備えています。 ストリーミング エンドポイントは、一般的なストリーミング機能と、HTTP キャッシュ ヘッダーの構成も提供します。 ストリーミング エンドポイントは、HTTP Cache-Control: max-age および Expires ヘッダーを設定します。 HTTP キャッシュ ヘッダーの詳細については、 [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)を参照してください。

## <a name="default-caching-headers"></a>既定のキャッシュ ヘッダー
既定では、ストリーミング エンドポイントは、オンデマンド ストリーミング データ (実際のメディア フラグメント/チャンク) および マニフェスト (プレイリスト) に対して 3 日間有効なキャッシュ ヘッダーを適用します。 ライブ ストリーミングの場合は、ストリーミング エンドポイントは、データ (実際のメディア フラグメント/チャンク) に対しては 3 日間有効なキャッシュ ヘッダーを、マニフェスト (プレイリスト) 要求には 2 秒間有効なキャッシュ ヘッダーを適用します。 ライブ プログラムがオンデマンド (ライブ アーカイブ) に変わると、オンデマンド ストリーミング キャッシュ ヘッダーが適用されます。

## <a name="azure-cdn-integration"></a>Azure CDN 統合
Azure Media services は、ストリーミング エンドポイントに対して [統合 CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) を提供します。 キャッシュ制御ヘッダーは、ストリーミング エンドポイントと同じ方法で CDN が有効なストリーミング エンドポイントに適用されます。 Azure CDN は、ストリーミング エンドポイントで構成されたキャッシュ値を使用して内部キャッシュされるオブジェクトの有効期間を定義し、配信キャッシュ ヘッダーもこの値を使用して設定します。 CDN 対応のストリーミング エンドポイントを使用するときは、小さいキャッシュ値を設定することは推奨されません。 小さい値を設定すると、パフォーマンスが低下し、CDN のメリットが小さくなります。 CDN が有効なストリーミング エンドポイントの場合、600 秒未満にキャッシュ ヘッダーを設定することは許可されません。

> [!IMPORTANT]
>Azure Media Services では Azure CDN との統合が完了しました。 シングル クリックで、利用可能なすべての Azure CDN プロバイダーを Standard および Premium の製品も含めたストリーミング エンドポイントに統合できます。 詳細については、こちらおの[お知らせ](https://azure.microsoft.com/blog/standardstreamingendpoint/)をご覧ください。
> 
> CDN がストリーミング エンドポイント API 経由または Azure Portal のストリーミング エンドポイント セクションを使用して有効にされている場合にのみ、ストリーミング エンドポイントから CDN へのデータ料金が無効になります。 手動で統合している場合や CDN API またはポータルのセクションを使用して CDN エンドポイントを直接作成した場合は、データ料金は無効になりません。

## <a name="configuring-cache-headers-with-azure-media-services"></a>Azure Media Services でのキャッシュ ヘッダーの構成
Azure Portal または Azure Media Services API を使用して、キャッシュ ヘッダーの値を構成できます。

1. Azure Portal を使用してキャッシュ ヘッダーを構成する方法については、[ストリーミング エンドポイントの管理方法](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)に関するページの「ストリーミング エンドポイントの構成」セクションをご覧ください。
2. Azure Media Services REST API の「 [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl)」。
3. Azure Media Services .NET SDK の「 [StreamingEndpointCacheControl プロパティ](/dotnet/api/microsoft.windowsazure.mediaservices.client.streamingendpointcachecontrol)」。

## <a name="cache-configuration-precedence-order"></a>キャッシュの構成の優先順位
1. Azure Media Services で構成されたキャッシュ値は、既定値をオーバーライドします。
2. 手動による構成がない場合は既定値が適用されます。
3. 既定では、Azure Media または Azure Storage の構成に関係なく 2 秒のキャッシュ ヘッダーがライブ ストリーミング マニフェスト (プレイリスト) に適用され、この値のオーバーライドは使用できません。