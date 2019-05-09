---
title: 目視レビューによるビデオのモデレート - Content Moderator
titlesuffix: Azure Cognitive Services
description: マシンによるビデオのモデレートと目視レビュー ツールを使用して、不適切なコンテンツをモデレートします
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: 43a43ddcbfc656a3eb5a274e1bb63a473b7c89a2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867717"
---
# <a name="video-moderation-with-human-review"></a>目視レビューによるビデオのモデレート

Content Moderator のマシンによる[ビデオのモデレート](video-moderation-api.md)と[目視レビュー ツール](Review-Tool-User-Guide/human-in-the-loop.md)を使用して、成人向けコンテンツ (明示的) やわいせつなコンテンツ (推奨) のビデオとトランスクリプトをモデレートし、ビジネスに最適な結果を取得します。

## <a name="video-trained-classifier-preview"></a>ビデオ トレーニング分類子 (プレビュー)

マシンによるビデオの分類は、画像トレーニング モデルまたはビデオ トレーニング モデルで実現されます。 画像トレーニング ビデオ分類子とは異なり、Microsoft の成人向けおよびわいせつビデオ分類子はビデオでトレーニングされます。 この方法により、一致品質が向上します。

## <a name="shot-detection"></a>ショット検出

分類の詳細を出力する際に、追加のビデオ インテリジェンスがビデオの分析における柔軟性の向上に役立ちます。 フレームだけを出力する代わりに、Microsoft のビデオのモデレート サービスは、ショット レベルの情報も提供します。 ビデオをショット レベルとフレーム レベルで分析するオプションがあります。
 
## <a name="key-frame-detection"></a>キー フレームの検出

フレームを一定の間隔で出力する代わりに、ビデオのモデレート サービスは、完全 (良好) なフレームのみを識別し、出力します。 この機能により、フレーム レベルでの成人向けおよびわいせつ性の分析のためにフレームを効率的に生成できます。

次の抽出は、潜在的なショット、キー フレーム、成人向けとわいせつ性のスコアを含む応答の一部を示しています。

    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]


## <a name="visualization-for-human-reviews"></a>目視レビューのための視覚化

より微妙なケースについては、ビデオ、そのフレーム、マシンが割り当てたタグのレンダリングに対する目視レビュー ソリューションが必要です。 ビデオとフレームをレビューするモデレート担当者は、分析情報の完全なビューを取得し、タグを変更し、自身の判断を送信します。

![ビデオ レビュー ツールの既定のビュー](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>ビデオ レベル レビュー用のプレーヤー ビュー

ビデオ レベルのバイナリ決定は、潜在的な成人向けフレームとわいせつなフレームを表示するビデオ プレーヤー ビューを使用して行うことができます。 レビュー担当者は、さまざまな速度オプションでビデオをナビゲートし、シーンを調べます。 タグを切り替えることにより、自身の判断を確定します。

![ビデオ レビュー ツールのプレーヤー ビュー](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>詳細なレビューのためのフレーム ビュー

フレームごとの分析のための詳細なビデオ レビューは、フレーム ベースのビューを使用して行うことができます。 レビュー担当者は、1 つまたは複数のフレームをレビューして選択し、タグを切り替えて自身の判断を確定します。 省略可能な次の手順は、不快感を与えるフレームやコンテンツの校正です。

![ビデオ レビュー ツールのフレーム ビュー](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>トランスクリプトのモデレート

通常、ビデオにはボイスオーバーがあり、これも不快感を与える音声に対するモデレートが必要です。 Azure Media Indexer サービスを使用して、音声をテキストに変換し、Content Moderator のレビュー API を使用して、レビュー ツール内でテキストのモデレートを行うためにトランスクリプトを送信します。

![ビデオ レビュー ツールのトランスクリプト ビュー](images/video-review-transcript-view.png)

## <a name="next-steps"></a>次の手順

[ビデオのモデレートのクイック スタート](video-moderation-api.md)を開始する。 

モデレート済みの出力からレビュー担当者のために[ビデオのレビュー](video-reviews-quickstart-dotnet.md)を生成する方法を学ぶ。

[ビデオ トランスクリプト レビュー](video-transcript-reviews-quickstart-dotnet.md)をビデオのレビューに追加する。

[完全なビデオ モデレーション ソリューション](video-transcript-moderation-review-tutorial-dotnet.md)を開発する方法に関する詳細なチュートリアルを確認する。 
