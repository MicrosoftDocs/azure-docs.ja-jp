---
title: Azure Media Services のレガシ コンポーネント | Microsoft Docs
description: このトピックでは、Azure Media Services のレガシ コンポーネントについて説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3487e735e48cb5067515762d6276429ca81e43fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008741"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services のレガシ コンポーネント

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

徐々に Media Service コンポーネントを機能強化し、レガシ コンポーネントを廃止しています。 この記事は、レガシ コンポーネントから現行コンポーネントにアプリケーションを移行する際に役立ちます。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>レガシ コンポーネントと移行のガイダンスの提供終了予定

*Windows Azure Media Encoder* (WAME) と *Azure Media Encoder* (AME) のメディア プロセッサは非推奨となっています。

* [Windows Azure Media Encoder から Media Encoder Standard に移行する](migrate-windows-azure-media-encoder.md)
* [Azure Media Encoder から Media Encoder Standard に移行する](migrate-azure-media-encoder.md)

次の Media Analytics メディア プロセッサは非推奨になっているか、間もなく非推奨になります。

  
 
| **メディア プロセッサ名** | **提供終了日** | **補足メモ** |
| --- | --- | ---|
| Azure Media Indexer 2 | 2020 年 1 月 1 日 | このメディア プロセッサは、[Media Services v3 AudioAnalyzerPreset Basic モード](../latest/analyzing-video-audio-files-concept.md)に置き換えられます。 詳細については、「[Media Indexer と Media Indexer 2 から Video Indexer に移行する](migrate-indexer-v1-v2.md)」をご覧ください。 |
| Azure Media Indexer | 2023 年 3 月 1 日 | このメディア プロセッサは、[Media Services v3 AudioAnalyzerPreset Basic モード](../latest/analyzing-video-audio-files-concept.md)に置き換えられます。 詳細については、「[Media Indexer と Media Indexer 2 から Video Indexer に移行する](migrate-indexer-v1-v2.md)」をご覧ください。 |
| モーション検出 | 2020 年 6 月 1 日|現時点では置換する計画はありません。 |
| ビデオ要約 |2020 年 6 月 1 日|現時点では置換する計画はありません。|
| ビデオ光学式文字認識 | 2020 年 6 月 1 日 |このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられます。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください。 |
| Face Detector | 2020 年 6 月 1 日 | このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられます。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください。 |
| Content Moderator | 2020 年 6 月 1 日 |このメディア プロセッサは、[Azure Media Services Video Indexer](../video-indexer/index.yml) に置き換えられます。 また、[Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md) の使用も検討してください。 <br/>「[Azure Media Services v3 プリセットと Video Indexer の比較](../video-indexer/compare-video-indexer-with-media-services-presets.md)」をご覧ください。 |

## <a name="next-steps"></a>次のステップ

[Media Services v2 から v3 への移行のガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)
