---
title: ビデオ内の正確な場面の検出 - Video Indexer
titlesuffix: Azure Media Services
description: このトピックでは、Video Indexer を使用して、ビデオ内の正確な場面を見つける方法を示します。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 93376059dac2a8336e581269d8289d893779b291
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799088"
---
# <a name="find-exact-moments-within-videos"></a>ビデオ内の正確な場面の検出

このトピックでは、ビデオ内の正確な場面を見つけるための検索オプションを示します。

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
2. アカウントのすべてのビデオから検索します。

    次の例では、セキュリティについて説明した、Satya が出演しているビデオをすべて検索します。

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
3. ビデオの要約された分析情報を検索します。

    ビデオの **[再生]** をクリックして、ビデオ内を検索します。 次に、 **[検索]** タブを選択して、ビデオ内を検索します。 

    次の例では、選択したビデオ内の「セキュア」を検索します。

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    結果の 1 つをクリックすると、ビデオのその場面がプレーヤーで表示されます。 アプリケーション内で、プレーヤー/分析情報の表示と同期を実現できます。 詳細については、「[アプリケーションに Video Indexer ウィジェットを埋め込む](video-indexer-embed-widgets.md)」を参照してください。 
4. ビデオの詳細な内訳を検索します。
    
    見つけたビデオに基づいて独自のクリップを作成する場合は、 **[編集]** ボタンを押します。 このページには、フィルターとしてのその分析情報と共にビデオが表示されます。 詳細については、「[Video Indexer の分析情報を表示および編集する](video-indexer-view-edit.md)」を参照してください。 

    ビデオ内を検索して、関心のある行のみを表示することができます。また、サイドの分析情報を使用して特定の部分だけにフィルターできます。 完了したら、クリップをプレビューでき、 **[発行]** を押してギャラリーに表示される新しいクリップを作成できます。
    
    次の例では、「mixed reality」テキストを検索します。 また、下の画面に示すように追加のフィルターも適用しました。
    
    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>次の手順 

処理したいビデオを見つけたら、次のいずれかのトピックの説明に従ってビデオの処理を続行できます。 

- [ビデオの詳細な分析情報を使用する](use-editor-create-project.md)
- [Video Indexer REST API を使用してコンテンツを処理する](video-indexer-use-apis.md)
- [アプリケーションにビジュアル ウィジェットを埋め込む](video-indexer-embed-widgets.md)

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)
