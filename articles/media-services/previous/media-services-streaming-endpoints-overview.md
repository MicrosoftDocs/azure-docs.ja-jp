---
title: Azure Media Services ストリーミング エンドポイントの概要 | Microsoft Docs
description: この記事では、Azure Media Services ストリーミング エンドポイントの概要を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0961b52ebc7271fabf4cc05ed99eea23d911a2d4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009096"
---
# <a name="streaming-endpoints-overview"></a>ストリーミング エンドポイントの概要  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Microsoft Azure Media Services (AMS) では、**ストリーミング エンドポイント** は、コンテンツをクライアント プレーヤー アプリケーションや、再配布のための Content Delivery Network (CDN) に直接配信するストリーミング サービスを表します。 Media Services は、シームレスな Azure CDN 統合もサポートしています。 StreamingEndpoint サービスからの送信ストリームには、ライブ ストリーム、ビデオ オンデマンド、または Media Services アカウントの資産のプログレッシブ ダウンロードを使用します。 各 Azure Media Services アカウントには、既定の StreamingEndpoint が含まれています。 追加の StreamingEndpoint をアカウントで作成できます。 StreamingEndpoint には、1.0 および 2.0 の 2 つのバージョンがあります。 2017 年 1 月 10 日から、新しく作成された AMS アカウントには、バージョン 2.0 が **既定** の StreamingEndpoint として含まれます。 このアカウントに追加する追加のストリーミング エンドポイントも、バージョン 2.0 になります。 この変更は、既存のアカウントに影響しません。既存の Streamingendpoint はバージョン 1.0 になり、バージョン 2.0 にアップグレードすることができます。 この変更により、動作、課金および機能が変更されます (詳細については、以下で説明する「**ストリーミングのタイプとバージョン**」のセクションを参照してください)。

Azure Media Services では、ストリーミング エンドポイントのエンティティに次のプロパティが追加されました。**CdnProvider**、**CdnProfile**、**StreamingEndpointVersion**。 これらのプロパティについて詳しくは、[こちら](/rest/api/media/operations/streamingendpoint)をご覧ください。 

Azure Media Services アカウントを作成すると、既定の標準ストリーミング エンドポイントが **停止** 状態で作成されます。 既定のストリーミング エンドポイントは削除できません。 対象のリージョンで使用できる Azure CDN によっては、新しく作成された既定のストリーミング エンドポイントに "StandardVerizon" CDN プロバイダー統合も既定で含まれます。 
                
> [!NOTE]
> Azure CDN 統合は、ストリーミング エンドポイントを開始する前に無効にすることができます。 CDN を有効にするかどうかを問わず、`hostname` とストリーミング URL は同じままにします。

このトピックでは、ストリーミング エンドポイントで利用できる主な機能の概要について説明します。

## <a name="naming-conventions"></a>名前付け規則

既定のエンドポイントの場合: `{AccountName}.streaming.mediaservices.windows.net`

追加エンドポイントの場合: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>ストリーミングのタイプとバージョン

### <a name="standardpremium-types-version-20"></a>Standard/Premium タイプ (バージョン 2.0)

2017 年 1 月リリース以降の Media Services には、ストリーミング タイプ **Standard** (プレビュー) と **Premium** の 2 つの型があります。 これらのタイプは、ストリーミング エンドポイントのバージョン「2.0」の一部です。


|Type|説明|
|--------|--------|  
|**Standard**|既定のストリーミング エンドポイントは **Standard** 型ですが、ストリーミング ユニットを調整することで Premium 型に変更できます。|
|**Premium** |このオプションは、より高度なスケールやコントロールを必要とするプロフェッショナルなシナリオに適しています。 **Premium** 型には、ストリーミング ユニットを調整することで移行します。<br/>専用のストリーミング エンドポイントは、分離環境で存在し、リソースの競合はありません。|

多数のインターネットの対象ユーザーにコンテンツを配信することを検討しているお客様には、ストリーミング エンドポイントで CDN を有効にすることをお勧めします。

詳細については、以下の「[ストリーミング タイプの比較](#comparing-streaming-types)」セクションを参照してください。

### <a name="classic-type-version-10"></a>クラシック タイプ (バージョン 1.0)

2017 年 1 月 10 日より前のリリースで AMS アカウントを作成したユーザーのために、**クラシック** タイプのストリーミング エンドポイントがあります。 このタイプは、ストリーミング エンドポイントのバージョン「1.0」の一部です。

**バージョン「1.0」** のストリーミング エンドポイントに 1 つ以上の Premium ストリーミング ユニット (SU) が含まれる場合は、Premium ストリーミング エンドポイントになり、AMS のすべての機能が提供されます (**Standard/Premium** タイプと同様)。追加の構成手順はありません。

>[!NOTE]
>**クラシック** ストリーミング エンドポイント (バージョン「1.0」、0 SU) は、機能に制限があり、SLA は含まれていません。 エクスペリエンスを改善し、**Standard** タイプに備わっているダイナミック パッケージや暗号化などの機能を使用できるように、**Standard** タイプに移行することをお勧めします。 **Standatrd** タイプに移行するには、[Azure Portal](https://portal.azure.com/) にアクセスして **[Standard にオプトイン]** を選択します。 移行の詳細については、[移行](#migration-between-types)に関するセクションを参照してください。
>
>この操作はロールバックできず、料金に影響を与えることに注意してください。
>
 
## <a name="comparing-streaming-types"></a>ストリーミング タイプの比較

### <a name="versions"></a>バージョン

|Type|StreamingEndpointVersion|ScaleUnits|CDN|課金|
|--------------|----------|-----------------|-----------------|-----------------|
|クラシック|1.0|0|NA|Free|
|Standard ストリーミング エンドポイント (プレビュー)|2.0|0|はい|有料|
|Premium ストリーミング ユニット|1.0|>0|はい|有料|
|Premium ストリーミング ユニット|2.0|>0|はい|有料|

### <a name="features"></a>特徴

機能|Standard|Premium
---|---|---
スループット |最大 600 Mbps であり、CDN を使用した場合に実効スループットが大幅に向上します。|ストリーミング ユニット (SU) あたり 200 Mbps。 CDN を使用した場合に実効スループットが大幅に向上します。
CDN|Azure CDN、サード パーティ製 CDN、または CDN なし。|Azure CDN、サード パーティ製 CDN、または CDN なし。
課金は日割り計算| 毎日|毎日
動的な暗号化|はい|はい
動的パッケージ|はい|はい
スケール|対象スループットまで自動スケールアップ。|追加のストリーミング ユニット
IP フィルタリング/G20/カスタム ホスト <sup>1</sup>|はい|はい
プログレッシブ ダウンロード|はい|はい
推奨使用量 |大半のストリーミング シナリオに推奨。|プロフェッショナルな使用量。 

<sup>1</sup> エンドポイントで CDN が有効でない場合にのみ、ストリーミング エンドポイントで直接使用します。<br/>

SLA については、[価格と SLA](https://azure.microsoft.com/pricing/details/media-services/) に関する記事をご覧ください。

## <a name="migration-between-types"></a>タイプの移行

ソース | ターゲット | アクション
---|---|---
クラシック|Standard|オプトインが必要
クラシック|Premium| スケール (追加のストリーミング ユニット)
Standard/Premium|クラシック|利用不可 (ストリーミング エンドポイントのバージョンが 1.0 の場合。 scaleunits を「0」に設定してクラシックに変更することは可能)
Standard (CDN あり/なし)|同じ構成の Premium|**開始済み** 状態で可能。 (Azure Portal 使用)
Premium (CDN あり/なし)|同じ構成の Standard|**開始済み** 状態で可能 (Azure Portal 使用)
Standard (CDN あり/なし)|別の構成の Premium|**停止** 状態で可能 (Azure Portal 使用)。 実行中の状態では不可。
Premium (CDN あり/なし)|別の構成の Standard|**停止** 状態で可能 (Azure Portal 使用)。 実行中の状態では不可。
バージョン 1.0、SU > = 1、CDN あり|CDN なしの Standard/Premium|**停止** 状態で可能。 **開始済み** 状態では不可。
バージョン 1.0、SU > = 1、CDN あり|Standard、CDN あり/なし|**停止** 状態で可能。 **開始済み** 状態では不可。 バージョン 1.0 の CDN は削除し、新しい CDN を作成して起動。
バージョン 1.0、SU > = 1、CDN あり|Premium、CDN あり/なし|**停止** 状態で可能。 **開始済み** 状態では不可。 クラシック CDN は削除し、新しい CDN を作成して起動。

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
