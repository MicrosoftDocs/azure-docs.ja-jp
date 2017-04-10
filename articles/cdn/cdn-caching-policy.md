---
title: "Azure Media Services での Azure CDN キャッシュ ポリシーの管理 | Microsoft Docs"
description: "Azure Media Services で Azure CDN キャッシュ ポリシーを管理する方法について説明します。"
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: 
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: fba5384acae53ac416d91ae30840c6cfa04e7c68
ms.openlocfilehash: 0c479a58f4158bb1a72dc43432507160f65d2791


---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure Media Services での Azure CDN キャッシュ ポリシーの管理
Azure Media Services では、HTTP ベースのアダプティブ ストリーミングとプログレッシブ ダウンロードが提供されます。 HTTP ベースのストリーミングは、プロキシと CDN レイヤーにおけるキャッシュだけでなくクライアント側のキャッシュの利点もあり、高い拡張性を備えています。 ストリーミング エンドポイントは、一般的なストリーミング機能と、HTTP キャッシュ ヘッダーの構成も提供します。 ストリーミング エンドポイントは、HTTP Cache-Control: max-age および Expires ヘッダーを設定します。 HTTP キャッシュ ヘッダーの詳細については、 [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)を参照してください。

## <a name="default-caching-headers"></a>既定のキャッシュ ヘッダー
既定では、ストリーミング エンドポイントは、オンデマンド ストリーミング データ (実際のメディア フラグメント/チャンク) および マニフェスト (プレイリスト) に対して 3 日間有効なキャッシュ ヘッダーを適用します。 ライブ ストリーミングの場合は、ストリーミング エンドポイントは、データ (実際のメディア フラグメント/チャンク) に対しては 3 日間有効なキャッシュ ヘッダーを、マニフェスト (プレイリスト) 要求には 2 秒間有効なキャッシュ ヘッダーを適用します。 ライブ プログラムがオンデマンド (ライブ アーカイブ) に変わると、オンデマンド ストリーミング キャッシュ ヘッダーが適用されます。

## <a name="azure-cdn-integration"></a>Azure CDN 統合
Azure Media services は、ストリーミング エンドポイントに対して [統合 CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) を提供します。 キャッシュ制御ヘッダーは、ストリーミング エンドポイントと同じ方法で CDN が有効なストリーミング エンドポイントに適用されます。 Azure CDN は、ストリーミング エンドポイントで構成されたキャッシュ値を使用して内部キャッシュされるオブジェクトの有効期間を定義し、配信キャッシュ ヘッダーもこの値を使用して設定します。 CDN 対応のストリーミング エンドポイントを使用するときは、小さいキャッシュ値を設定することは推奨されません。 小さい値を設定すると、パフォーマンスが低下し、CDN のメリットが小さくなります。 CDN が有効なストリーミング エンドポイントの場合、600 秒未満にキャッシュ ヘッダーを設定することは許可されません。

> [!IMPORTANT]
>Azure Media Services では Azure CDN との統合が完了しました。 シングル クリックで、利用可能なすべての Azure CDN プロバイダー (Akamai および Verizon) をCDN Standard および Premium の製品も含めたストリーミング エンドポイントに統合できます。 詳細については、こちらおの[お知らせ](https://azure.microsoft.com/blog/standardstreamingendpoint/)をご覧ください。
> 
> CDN がストリーミング エンドポイント API 経由または Microsoft Azure 管理ポータルのストリーミング エンドポイント セクションを使用して有効にされている場合のみ、ストリーミング エンドポイントから CDN へのデータ料金が無効になります。 手動で統合している場合や CDN API またはポータルのセクションを使用して CDN エンドポイントを直接作成した場合は、データ料金は無効になりません。

## <a name="configuring-cache-headers-with-azure-media-services"></a>Azure Media Services でのキャッシュ ヘッダーの構成
Azure 管理ポータルまたは Azure Media Services API を使用して、キャッシュ ヘッダーの値を構成できます。

1. 管理ポータルを使用してキャッシュ ヘッダーを構成する方法については、「 [Media Services アカウントでストリーミング エンドポイントを管理する方法](../media-services/media-services-portal-manage-streaming-endpoints.md) 」の「ストリーミング エンドポイントの構成」セクションを参照してください。
2. Azure Media Services REST API の「 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)」。
3. Azure Media Services .NET SDK の「 [StreamingEndpointCacheControl プロパティ](http://go.microsoft.com/fwlink/?LinkId=615302)」。

## <a name="cache-configuration-precedence-order"></a>キャッシュの構成の優先順位
1. Azure Media Services で構成されたキャッシュ値は、既定値を上書きします。
2. 手動による構成がない場合は既定値が適用されます。
3. 既定では、Azure Media または Azure Storage の構成に関係なく 2 秒のキャッシュ ヘッダーがライブ ストリーミング マニフェスト (プレイリスト) に適用され、この値の上書きは使用できません。




<!--HONumber=Feb17_HO1-->


