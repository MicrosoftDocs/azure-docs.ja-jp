---
title: Video Indexer の概念 - Azure
titleSuffix: Azure Media Services Video Indexer
description: この記事では、Azure Media Services Video Indexer の用語と概念について概要を説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633577"
---
# <a name="video-indexer-concepts"></a>Video Indexer の概念

この記事では、Azure Media Services Video Indexer の用語と概念について概要を説明します。

## <a name="audiovideocombined-insights"></a>オーディオ、ビデオ、結合された分析情報

Video Indexer にビデオをアップロードすると、さまざまな AI モデルを実行することで、ビジュアルとオーディオの両方が分析されます。 Video Indexer でビデオを分析すると、分析情報が AI モデルによって抽出されます。 詳細については、[概要](video-indexer-overview.md)に関するページを参照してください。

## <a name="confidence-scores"></a>信頼度スコア 

信頼度スコアは、分析情報の信頼性を示します。 これは 0.0 から 1.0 までの数値です。 スコアが高得点であるほど、回答の信頼度は高くなります。 たとえば、次のように入力します。 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>コンテンツ モデレーション

テキストとビジュアルのコンテンツ モデレーション モデルを使用して、不適切なコンテンツからユーザーの安全を維持し、公開したコンテンツが組織の値と一致することを検証します。 コンテンツに関して、特定のビデオを自動的にブロックしたり、ユーザーに通知したりすることができます。 詳細については、[分析情報: ビジュアルとテキストのコンテンツ モデレーション](video-indexer-output-json-v2.md#visualcontentmoderation)に関するページを参照してください。 

## <a name="blocks"></a>Blocks   

ブロックは、データ内の移動を簡単にするために使用されます。 たとえば、話者が変わったり、長い休止があるときにブロックが分割されます。  

## <a name="project-and-editor"></a>プロジェクトとエディター

[Video Indexer](https://www.videoindexer.ai/) Web サイトでは、ビデオの詳細な分析情報を使用して、適切なメディア コンテンツを検索したり、関心がある部分の位置を特定したり、結果を使用してまったく新しいプロジェクトを作成したりできます。 作成したプロジェクトは、Video Indexer からレンダリングおよびダウンロードしたり、独自の編集アプリケーションまたは下流のワークフローで使用したりできます。

この機能が役立つかもしれないシナリオの例を次に示します。 

* 予告編のために映画のハイライトを作成する。
* ニュース放送で古いビデオ クリップを使用する。
* ソーシャル メディア用の短いコンテンツを作成する。

詳細については、[エディターを使用したプロジェクトの作成](use-editor-create-project.md)に関するページを参照してください。

## <a name="keyframes"></a>キーフレーム

Video Indexer により、各ショットを最適に表すフレームが選択されます。 キーフレームは、審美的プロパティ (たとえば、コントラストや安定性) に基づいてビデオ全体から選択された代表的なフレームです。 詳細については、「[Scenes, shots, and keyframes](scenes-shots-keyframes.md)」(シーン、ショット、キーフレーム) を参照してください。

## <a name="time-range-vs-adjusted-time-range"></a>時間範囲と調整された時間範囲   

TimeRange は、元のビデオでの時間範囲です。 AdjustedTimeRange は、現在のプレイリストを基準とした時間の範囲です。 複数のビデオの複数の行からプレイリストを作成できるため、1 時間のビデオのうち 1 行だけ (たとえば 10:00-10:15) を使用する場合もあります。 その場合、時間の範囲は 10:00-10:15 であっても、adjustedTimeRange が 00:00-00:15 である 1 行を含むプレイリストが作成されます。 

## <a name="widgets"></a>Widgets (ウィジェット)

Video Indexer は、アプリへのウィジェットの埋め込みをサポートしています。 詳細については、「[お使いのアプリに Video Indexer ウィジェットを埋め込む](video-indexer-embed-widgets.md)」を参照してください。

## <a name="summarized-insights"></a>要約された分析情報  

要約された分析情報には、データ (顔、トピック、感情) の集約されたビューが含まれます。 たとえば、数千もの時間範囲を一つ一つ確認してどの顔が出現するかを調べなくても、要約された分析情報に、すべての顔と、それぞれの顔が出現する時間範囲、その顔が表示される時間の割合 (%) が表示されます。  

## <a name="next-steps"></a>次のステップ

- [概要](video-indexer-overview.md)
- [分析情報](video-indexer-output-json-v2.md)
