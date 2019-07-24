---
title: 既存のビデオからのビデオの分析情報の作成 - Azure
titlesuffix: Azure Media Services
description: このトピックでは、既存のビデオ ファイルに基づいてビデオの分析情報を作成し、公開する方法を示します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 4a65e88e3f94f64a56bde882b535030968ae354d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991278"
---
# <a name="create-highlights-from-existing-videos"></a>既存のビデオからハイライトを作成する

このトピックでは、他のビデオに基づいてビデオの分析情報を作成し、公開する方法を示します。

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
2. ビデオの分析情報を作成するビデオを見つけます。
3. **[再生]** を押します。

    ビデオの要約された分析情報がページに表示されます。 

    ![洞察](./media/video-indexer-create-new/video-indexer-summarized-insights.png)
3. **[編集]** ボタンを押します。

    このページには、ビデオの完全な内訳が表示されます。 内訳はブロックに分割されています。 ブロックは、データ内の移動を簡単にするために使用されています。 たとえば、話者が変わったり、長い休止があるときにブロックが分割されます。 必要な行のみを含む独自のプレイリストを作成できます。 ソース ビデオの特定の部分のみを表示するには、トピック/キーワード、センチメント、人物、話者でフィルター処理することができます。 ビデオのトランスクリプトや OCR のみを表示することもできます。    

    ![洞察](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)
4. プレイリストを作成します。

    プレイリストの行の追加/削除を行うには、**+**/**-** を押します。
5. プレイリストをプレビューします。

    プレイリストの作成が完了したら、**[プレビュー]** を押します。
6. プレイリストを公開します。

    プレイリストをプレビューしたら、公開できます。

    公開したプレイリストは、ビデオの分析情報のリストに追加されます。


## <a name="next-steps"></a>次の手順 

新しいプレイリストを作成したら、次のいずれかのトピックの説明に従ってプレイリストの処理を続行できます。 

- [Video Indexer REST API を使用してコンテンツを処理する](video-indexer-use-apis.md)
- [アプリケーションにビジュアル ウィジェットを埋め込む](video-indexer-embed-widgets.md)

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md) 
