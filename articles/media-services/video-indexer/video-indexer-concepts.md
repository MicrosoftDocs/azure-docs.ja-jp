---
title: Video Indexer の概念
titlesuffix: Azure Media Services
description: このトピックでは、Video Indexer サービスのいくつかの概念について説明します。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 156eceba856bf159d4821360639a0641d3ed02be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799067"
---
# <a name="video-indexer-concepts"></a>Video Indexer の概念
 
この記事では、Video Indexer サービスのいくつかの概念について説明します。
    
## <a name="summarized-insights"></a>要約された分析情報

要約された分析情報には、データ (顔、トピック、感情) の集約されたビューが含まれます。 たとえば、数千もの時間範囲を一つ一つ確認してどの顔が出現するかを調べなくても、要約された分析情報に、すべての顔と、それぞれの顔が出現する時間範囲、その顔が表示される時間の割合 (%) が表示されます。

## <a name="time-range-vs-adjusted-time-range"></a>時間範囲と調整された時間範囲

TimeRange は、元のビデオでの時間範囲です。 AdjustedTimeRange は、現在のプレイリストを基準とした時間範囲です。 複数のビデオの複数の行からプレイリストを作成できるため、1 時間のビデオのうち 1 行だけ (たとえば 10:00-10:15) を使用する場合もあります。 その場合、時間の範囲は 10:00-10:15 であっても、adjustedTimeRange が 00:00-00:15 である 1 行を含むプレイリストが作成されます。
 
## <a name="blocks"></a>Blocks

ブロックは、データ内の移動を簡単にするために使用されます。 たとえば、話者が変わったり、長い休止があるときにブロックが分割されます。

## <a name="next-steps"></a>次の手順

作業の開始方法の詳細については、「[サインアップして最初のビデオをアップロードする方法](video-indexer-get-started.md)」を参照してください。

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)
