---
title: Video Indexer の分析情報の表示と編集
titlesuffix: Azure Media Services
description: この記事では、Azure Media Services Video Indexer の分析情報を表示および編集する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: e9bb516d498d989b97f1069af9a9f4cb673ea96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74892670"
---
# <a name="view-and-edit-video-indexer-insights"></a>Video Indexer の分析情報の表示と編集

このトピックでは、ビデオの Video Indexer 分析情報を表示および編集する方法を示します。

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
2. Video Indexer の分析情報を作成するビデオを見つけます。 詳細については、「[ビデオ内の正確な場面を見つける](video-indexer-search.md)」を参照してください。
3. **[再生]** を押します。

    ビデオの要約された分析情報がページに表示されます。 

    ![洞察](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. ビデオの要約された分析情報を調べます。 

    要約された分析情報には、データ (顔、キーワード、センチメント) の集約されたビューが表示されます。 たとえば、人物の顔と、それぞれの顔が出現する時間範囲、その顔が表示される時間の割合 (%) を確認できます。

    プレーヤーと分析情報は同期されます。 たとえば、キーワードやトランスクリプトの行をクリックすると、ビデオのその場面がプレーヤーで表示されます。 アプリケーション内で、プレーヤー/分析情報の表示と同期を実現できます。 詳細については、[アプリケーションへの Azure Indexer ウィジェットの埋め込み](video-indexer-embed-widgets.md)に関する記事を参照してください。 

## <a name="next-steps"></a>次のステップ

[ビデオの詳細な分析情報を使用する](use-editor-create-project.md)

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)

