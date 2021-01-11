---
title: Azure Media Services のレガシ コンポーネント | Microsoft Docs
description: このトピックでは、Azure Media Services のレガシ コンポーネントについて説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 1c3e50fd64c113aafd2d70cc76c7f789e080ad23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269524"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services のレガシ コンポーネント

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Media Services のコンポーネントは、時間の経過と共に着実に改善され機能が強化されてきました。 その結果、いくつかのレガシ コンポーネントは廃止されています。 次の記事では、レガシ コンポーネントから最新のコンポーネントにアプリケーションを移行する手順を確認できます。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>レガシ コンポーネントと移行のガイダンスの提供終了予定

お知らせしているように *Windows Azure Media Encoder* (WAME) と *Azure Media Encoder* (AME) のメディア プロセッサは非推奨となっており、 これらのプロセッサは、2020 年 3 月 31 日に廃止される予定です。

* [Windows Azure Media Encoder から Media Encoder Standard に移行する](migrate-windows-azure-media-encoder.md)
* [Azure Media Encoder から Media Encoder Standard に移行する](migrate-azure-media-encoder.md)

また、以下の Media Analytics メディア プロセッサの提供終了もお知らせします。 
 
|メディア プロセッサ名|提供終了日|その他のメモ|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|2020 年 1 月 1 日|このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられています。 詳細については、「[Media Indexer と Media Indexer 2 から Video Indexer に移行する](migrate-indexer-v1-v2.md)」をご覧ください。|
|[Azure Media Indexer](media-services-index-content.md)|2023 年 3 月 1 日|このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられています。 詳細については、「[Media Indexer と Media Indexer 2 から Video Indexer に移行する](migrate-indexer-v1-v2.md)」をご覧ください|
|[モーション検出](media-services-motion-detection.md)|2020 年 6 月 1 日|現時点では置換する計画はありません。|
|[ビデオ要約](media-services-video-summarization.md)|2020 年 6 月 1 日|現時点では置換する計画はありません。|
|[ビデオ光学式文字認識](media-services-video-optical-character-recognition.md)|2020 年 6 月 1 日|このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられています。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください|
|[Face Detector](media-services-face-and-emotion-detection.md)|2020 年 6 月 1 日|このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられています。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください|
|[Content Moderator](media-services-content-moderation.md)|2020 年 6 月 1 日|このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられています。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください|

## <a name="next-steps"></a>次のステップ

[Media Services v2 から v3 への移行のガイダンス](../latest/migrate-from-v2-to-v3.md)
