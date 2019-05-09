---
title: Azure Media Services ストリーミング エンドポイントの概要 | Microsoft Docs
description: このトピックでは、Azure Media Services ストリーミング エンドポイントの概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c5979fa7ff67c5acda9ab653bc4ee52d8b5129a5
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293806"
---
# <a name="streaming-endpoints-overview"></a>ストリーミング エンドポイントの概要  

## <a name="overview"></a>概要

Microsoft Azure Media Services (AMS) では、**ストリーミング エンドポイント**は、コンテンツをクライアント プレーヤー アプリケーションや、再配布のための Content Delivery Network (CDN) に直接配信するストリーミング サービスを表します。 Media Services は、シームレスな Azure CDN 統合もサポートしています。 StreamingEndpoint サービスからの送信ストリームには、ライブ ストリーム、ビデオ オンデマンド、または Media Services アカウントの資産のプログレッシブ ダウンロードを使用します。 各 Azure Media Services アカウントには、既定の StreamingEndpoint が含まれています。 追加の StreamingEndpoint をアカウントで作成できます。 StreamingEndpoint には、1.0 および 2.0 の 2 つのバージョンがあります。 2017 年 1 月 10 日から、新しく作成された AMS アカウントには、バージョン 2.0 が**既定**の StreamingEndpoint として含まれます。 このアカウントに追加する追加のストリーミング エンドポイントも、バージョン 2.0 になります。 この変更は、既存のアカウントに影響しません。既存の StreamingEndpoint はバージョン 1.0 になり、バージョン 2.0 にアップグレードすることができます。 この変更により、動作、課金および機能が変更されます (詳細については、以下で説明する「**ストリーミングのタイプとバージョン**」のセクションを参照してください)。

Azure Media Services では、ストリーミング エンドポイントのエンティティに次のプロパティが追加されました。**CdnProvider**、**CdnProfile**、**FreeTrialEndTime**、**StreamingEndpointVersion**。 これらのプロパティについて詳しくは、[こちら](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)をご覧ください。 

Azure Media Services アカウントを作成すると、既定の標準ストリーミング エンドポイントが**停止**状態で作成されます。 既定のストリーミング エンドポイントは削除できません。 対象のリージョンで使用できる Azure CDN によっては、新しく作成された既定のストリーミング エンドポイントに "StandardVerizon" CDN プロバイダー統合も既定で含まれます。 
                
> [!NOTE]
> Azure CDN 統合は、ストリーミング エンドポイントを開始する前に無効にすることができます。 CDN を有効にするかどうかを問わず、`hostname` とストリーミング URL は同じままにします。

このトピックでは、ストリーミング エンドポイントで利用できる主な機能の概要について説明します。

## <a name="naming-conventions"></a>名前付け規則

既定のエンドポイントの場合: `{AccountName}.streaming.mediaservices.windows.net`

追加エンドポイントの場合: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>ストリーミングのタイプとバージョン

### <a name="standardpremium-types-version-20"></a>Standard/Premium タイプ (バージョン 2.0)

2017 年 1 月リリース以降の Media Services には、ストリーミング タイプ **Standard** と **Premium** の 2 つの型があります。 これらのタイプは、ストリーミング エンドポイントのバージョン「2.0」の一部です。

type|説明
---|---
**Standard** |ほとんどのシナリオで使用する既定のオプションです。<br/>このオプションでは、固定/制限付きの SLA を取得でき、開始後の最初の 15 日間ストリーミング エンドポイントは無料です。<br/>複数のストリーミング エンドポイントを作成する場合は、最初の 1 つだけが最初の 15 日間無料で、他は開始するとすぐに課金されます。 <br/>無料評価版は新しく作成したメディア サービス アカウントと既定のストリーミング エンドポイントにのみ適用されることに注意してください。 既存のストリーミング エンドポイントと追加で作成されたストリーミング エンドポイントは、バージョン 2.0 にアップグレードした場合やバージョン 2.0 で作成した場合にも、無料の試用期間に含まれません。
**Premium** |このオプションは、より高度なスケールやコントロールを必要とするプロフェッショナルなシナリオに適しています。<br/>購入済みの Premium ストリーミング ユニット (SU) の容量をベースとする可変の SLA、専用のストリーミング エンドポイントは分離された環境に存在し、リソースの競合は起こりません。

詳細については、以下の「**ストリーミング タイプの比較**」セクションを参照してください。

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

|type|StreamingEndpointVersion|ScaleUnits|CDN|課金|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|クラシック|1.0|0|NA|無料|NA|
|Standard ストリーミング エンドポイント|2.0|0|はい|有料|はい|
|Premium ストリーミング ユニット|1.0|>0|はい|有料|はい|
|Premium ストリーミング ユニット|2.0|>0|はい|有料|はい|

### <a name="features"></a>機能

機能|Standard|Premium
---|---|---
最初の 15 日間無料| はい |いいえ 
スループット |Azure CDN を使用しない場合は、最大 600 Mbps。 CDN に合わせて拡大縮小。|ストリーミング ユニット (SU) あたり 200 Mbps。 CDN に合わせて拡大縮小。
SLA | 99.9|99.9 (SU あたり 200 Mbps)。
CDN|Azure CDN、サード パーティ製 CDN、または CDN なし。|Azure CDN、サード パーティ製 CDN、または CDN なし。
課金は日割り計算| 毎日|毎日
動的な暗号化|はい|はい
動的パッケージ|はい|はい
スケール|対象スループットまで自動スケールアップ。|追加のストリーミング ユニット
IP フィルタリング/G20/カスタム ホスト|はい|はい
プログレッシブ ダウンロード|はい|はい
推奨使用量 |大半のストリーミング シナリオに推奨。|プロフェッショナルな使用量。<br/>Standard 以上必要と考えられる場合。 同時実行の対象ユーザー数が 50,000 ビューアーを超えると予想される場合は、お問い合せ (amsstreaming@microsoft.com) ください。


## <a name="migration-between-types"></a>タイプの移行

ソース | ターゲット | Action
---|---|---
クラシック|Standard|オプトインが必要
クラシック|Premium| スケール (追加のストリーミング ユニット)
Standard/Premium|クラシック|利用不可 (ストリーミング エンドポイントのバージョンが 1.0 の場合。 scaleunits を「0」に設定してクラシックに変更することは可能)
Standard (CDN あり/なし)|同じ構成の Premium|**開始済み**状態で可能。 (Azure Portal 使用)
Premium (CDN あり/なし)|同じ構成の Standard|**開始済み**状態で可能 (Azure Portal 使用)
Standard (CDN あり/なし)|別の構成の Premium|**停止**状態で可能 (Azure Portal 使用)。 実行中の状態では不可。
Premium (CDN あり/なし)|別の構成の Standard|**停止**状態で可能 (Azure Portal 使用)。 実行中の状態では不可。
バージョン 1.0、SU > = 1、CDN あり|CDN なしの Standard/Premium|**停止**状態で可能。 **開始済み**状態では不可。
バージョン 1.0、SU > = 1、CDN あり|Standard、CDN あり/なし|**停止**状態で可能。 **開始済み**状態では不可。 バージョン 1.0 の CDN は削除し、新しい CDN を作成して起動。
バージョン 1.0、SU > = 1、CDN あり|Premium、CDN あり/なし|**停止**状態で可能。 **開始済み**状態では不可。 クラシック CDN は削除し、新しい CDN を作成して起動。

## <a name="next-steps"></a>次の手順
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

