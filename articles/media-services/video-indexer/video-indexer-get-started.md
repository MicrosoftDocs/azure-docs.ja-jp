---
title: Video Indexer にサインアップして最初のビデオをアップロードする - Azure
titleSuffix: Azure Media Services
description: Video Indexer ポータルを使用して、サインアップと最初のビデオのアップロードを行う方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: 5b38c731db141052e6700472020cd60b6a4d13a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98797798"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>クイック スタート:サインアップして最初のビデオをアップロードする方法

この入門クイック スタートでは、Video Indexer Web サイトにサインインする方法と、最初のビデオをアップロードする方法を示します。

Video Indexer アカウントを作成する場合、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限がありません) を選択できます。 無料試用アカウントで Video Indexer 使用すると、Web サイト ユーザーは最大 600 分間の無料インデックス作成、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、[ご使用の Azure サブスクリプションと Azure Media Services アカウントに接続される](connect-to-azure.md) Video Indexer アカウントを作成します。 インデックス作成にかかった時間 (分) に対して支払います。詳細については、「[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)」を参照してください。 

## <a name="sign-up-for-video-indexer"></a>Video Indexer にサインアップする

Video Indexer での開発を始めるには、[Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。

Video Indexer の使用を開始すると、保存されているデータおよびアップロードされたコンテンツはすべて、Microsoft で管理されるキーを使用して保存時に暗号化されます。

> [!NOTE]
> [Video Indexer Web サイトに予定されている認証の変更](release-notes.md#planned-video-indexer-website-authenticatication-changes)に関するセクションを参照してください。

## <a name="upload-a-video-using-the-video-indexer-website"></a>Video Indexer Web サイトを使用してビデオをアップロードする

### <a name="supported-file-formats-for-video-indexer"></a>Video Indexer でサポートされているファイル形式

Video Indexer で使用できるファイル形式の一覧については、「[入力コンテナー/ファイル形式](../latest/media-encoder-standard-formats.md#input-containerfile-formats)」を参照してください。

### <a name="upload-a-video"></a>ビデオをアップロードする

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトにサインインします。
1. ビデオをアップロードするには、 **[アップロード]** ボタンまたはリンクを押します。

    > [!NOTE]
    > ビデオの名前は、80 文字以下にする必要があります。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="アップロード":::
1. ビデオがアップロードされると、Video Indexer がビデオのインデックス作成と分析を開始します。 進行状況が表示されます。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="アップロードの進行状況":::
1. Video Indexer で分析が完了すると、ビデオへのリンクとビデオの内容の簡単な説明が記載されたメールが届きます。 人、話されたり書かれたりしている単語、トピック、名前付きエンティティなどです。
1. 後でライブラリの一覧でビデオを検索し、さまざまな操作を実行できます。 検索、インデックスの再作成、編集などです。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="アップロードの完了":::

## <a name="supported-browsers"></a>サポートされているブラウザー

詳細については、「[サポートされているブラウザー](video-indexer-overview.md#supported-browsers)」を参照してください。

## <a name="see-also"></a>関連項目

詳細については、[ビデオのアップロードとインデックス作成](upload-index-videos.md)に関するページを参照してください。

ビデオをアップロードしてインデックスを付けたら、[Video Indexer](video-indexer-view-edit.md) Web サイトまたは [Video Indexer 開発者ポータル](video-indexer-use-apis.md)を使用して、ビデオの分析情報を表示できます。 

[API の使用開始](video-indexer-use-apis.md)

## <a name="next-steps"></a>次の手順

詳しい紹介記事については、[概要ラボ](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)を参照してください。 

ワークショップを終えるころには、ビデオやオーディオ コンテンツから抽出できる情報の種類についての理解が深まります。また、コンテンツ インテリジェンスに関連した好機を発見したり、Azure 上のビデオ AI を売り込んだり、Video Indexer に関するさまざまなシナリオをデモンストレーションしたりするための気運も高まることでしょう。

