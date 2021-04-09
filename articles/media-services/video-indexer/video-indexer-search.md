---
title: Video Indexer を使用してビデオ内の正確な場面を検索する
titleSuffix: Azure Media Services
description: Video Indexer を使用してビデオ内の正確な場面を検索する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030532"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Video Indexer を使用してビデオ内の正確な場面を検索する

このトピックでは、Video Indexer Web サイトを使用して、ビデオ内の正確な場面を検索する方法について説明します。

1. [Video Indexer](https://www.videoindexer.ai/) の Web サイトに移動してサインインします。
1. 検索キーワードを指定すると、アカウントのライブラリ内のすべてのビデオに対して検索が実行されます。 

    **[フィルター]** を選択することで、検索をフィルター処理できます。 下の例では、画面上のテキストのみ (OCR) として表示される "Microsoft" を検索しています。

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="フィルター、テキストのみ":::
1. **[検索]** を押すと、結果が表示されます。

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="ビデオ検索の結果":::

    結果の 1 つを選択すると、プレーヤーに、ビデオ内のその正確な場面が表示されます。
1. ビデオの **[再生]** をクリックするか、元の検索結果の 1 つを選択することで、ビデオの要約された分析情報を表示および検索します。 

    **分析情報** を表示、検索、編集できます。 分析情報の 1 つを選択すると、プレーヤーに、ビデオ内のその正確な場面が表示されます。  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="ビデオの分析情報の表示、検索、編集":::

    Video Indexer ウィジェットを使用してビデオを埋め込むと、アプリでプレーヤーまたは分析情報の表示と同期を行うことができます。 詳細については、[アプリに Video Indexer ウィジェットを埋め込む](video-indexer-embed-widgets.md)ことに関するページを参照してください。
1. **[タイムライン]** タブをクリックすると、トランスクリプトを表示、検索、編集できます。 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="ビデオのトランスクリプトの表示、検索、編集":::

    テキストを編集するには、右上隅にある **[編集]** を選択し、テキストを必要に応じて変更します。 

    また、右上隅にある適切なオプションを選択して、トランスクリプトを翻訳およびダウンロードすることもできます。 

## <a name="embed-download-create-projects"></a>埋め込み、ダウンロード、プロジェクトの作成

ビデオの下の **[埋め込み]** を選択することで、ビデオを埋め込むことができます。 詳細については、[アプリケーションへのビジュアル ウィジェットの埋め込み](video-indexer-embed-widgets.md)に関する記事を参照してください。

ビデオの下の **[ダウンロード]** をクリックすることで、ソース ビデオ、ビデオの分析情報、トランスクリプトをダウンロードできます。

**[エディターで開く]** をクリックすることで、ビデオの特定の音声および場面に基づいてクリップを作成できます。 その後、ビデオを編集し、プロジェクトを保存します。 詳細については、[ビデオの詳細な分析情報の使用](use-editor-create-project.md)に関する記事を参照してください。

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="ビデオの埋め込み、ダウンロード、プロジェクトの作成":::

## <a name="next-steps"></a>次の手順

[Video Indexer REST API を使用してコンテンツを処理する](video-indexer-use-apis.md)
