---
title: Indexer v1 と v2 から Azure Media Services Video Indexer への移行| Microsoft Docs
description: このトピックでは、Azure Media Indexer v1 と v2 から Azure Media Services Video Indexer に移行する方法を説明します。
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: eb4f662a4b34d748da0e97da9b64a386be3f5c49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016425"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Media Indexer と Media Indexer 2 から Video Indexer に移行する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> 顧客は、[Media Services v3 AudioAnalyzerPreset Basic モード](../latest/analyzing-video-audio-files-concept.md)を使用して、Indexer v1 および Indexer v2 からに移行することをお勧めします。 [Azure Media Indexer](media-services-index-content.md) メディア プロセッサと [Azure Media Indexer 2 プレビュー](./legacy-components.md) メディア プロセッサのインベントリは廃止される予定です。 提供終了日については、この[レガシ コンポーネント](legacy-components.md)に関するトピックを参照してください。

Azure Media Services Video Indexer は、Azure Media Analytics、Azure Cognitive Search、Cognitive Services (Face API、Microsoft Translator、Computer Vision API、Custom Speech Service など) を基盤として構築されています。 Video Indexer のビデオとオーディオのモデルを使用して、ビデオから分析情報を抽出することができます。 Video Indexer はどのようなシナリオで使用できるか、どのような機能を提供するか、どのように使用を開始するかを確認するには、[Video Indexer のビデオとオーディオのモデル](../video-indexer/video-indexer-overview.md)に関するページを参照してください。 

[Azure Media Services v3 アナライザー プリセット](../latest/analyzing-video-audio-files-concept.md)を使用するか、直接 [Video Indexer API](https://api-portal.videoindexer.ai/) を使用して、ビデオ ファイルとオーディオ ファイルから分析情報を抽出できます。 現在、Video Indexer API と Media Services v3 API によって提供される機能には重複があります。

> [!NOTE]
> Video Indexer と Media Services の違いを理解する方法について詳しくは、[比較のドキュメント](../video-indexer/compare-video-indexer-with-media-services-presets.md)を参照してください。

この記事では、Azure Media Indexer と Azure Media Indexer 2 から Azure Media Services Video Indexer に移行する手順について説明します。  

## <a name="migration-options"></a>移行オプション

|以下が必要な場合  |と |
|---|---|
|次の字幕形式のメディア ファイル形式での音声からテキストへの文字起こしを提供するソリューション。VTT、SRT、または TTML<br/>さらに、キーワード、トピック推論、音響イベント、話者の分離 (ダイアライゼーション)、エンティティの抽出、翻訳など、その他のオーディオ分析情報を提供するソリューション| Video Indexer v2 REST API または Azure Media Services v3 オーディオ アナライザー プリセットを通じて Azure Video Indexer の機能を使用するように、お使いのアプリケーションを更新します。|
|音声テキスト変換機能| Cognitive Services Speech API を直接使用します。|  

## <a name="getting-started-with-video-indexer"></a>Video Indexer の使用を開始する

次のセクションでは、関連するリンクを示します。[Video Indexer を使い始めるにはどうすればよいですか?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Media Services v3 API の使用を開始する

Azure Media Services v3 API では、[Azure Media Services v3 アナライザー プリセット](../latest/analyzing-video-audio-files-concept.md)を使用して、ビデオ ファイルとオーディオ ファイルから分析情報を抽出できます。

**AudioAnalyzerPreset** を使用して、音声または画像ファイルから複数の音声分析情報を抽出できます。 出力には、音声トランスクリプト用の VTT または TTML ファイルと、JSON ファイル (すべての追加の音声分析情報が格納される) が含まれます。 音声分析情報には、キーワード、話者インデックス作成、音声のセンチメント分析が含まれます。 AudioAnalyzerPreset では、特定の言語の言語検出もサポートされます。 詳細については、[変換](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)に関するページを参照してください。

### <a name="get-started"></a>はじめに

開始するには、以下をご覧ください。

* [チュートリアル](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset の例:[Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) または [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset の例:[Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) または [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Cognitive Services Speech Services の使用を開始する

[Azure Cognitive Services](../../cognitive-services/index.yml) では、オーディオ ストリームからテキストへの文字起こしがリアルタイムで行われる音声テキスト変換サービスが提供されます。結果のテキストを、お使いのアプリケーション、ツール、またはデバイスで使用したり表示したりできます。 音声テキスト変換を使用して、[独自の音響モデル、言語モデル、または発音モデルをカスタマイズ](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)できます。 詳細については、[Cognitive Services の音声テキスト変換](../../cognitive-services/speech-service/speech-to-text.md)に関するページを参照してください。 

> [!NOTE] 
> 音声テキスト変換サービスでは、ビデオ ファイル形式は使用されず、[特定のオーディオ形式](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats)だけが使用されます。 

音声テキスト変換サービスの詳細と開始方法については、「[音声変換の概要](../../cognitive-services/speech-service/speech-to-text.md)」を参照してください。

## <a name="known-differences-from-deprecated-services"></a>非推奨のサービスとの既知の違い

Video Indexer、Azure Media Services v3 AudioAnalyzerPreset、および Cognitive Services Speech Services サービスは、廃止になった Azure Media Indexer 1 と Azure Media Indexer 2 プロセッサよりも信頼性が高く、より高品質な出力が生成されることがわかります。  

既知の相違点をいくつか次に示します。

* Cognitive Services Speech Services では、キーワード抽出はサポートされません。 ただし、Video Indexer と Media Services v3 AudioAnalyzerPreset はどちらも、JSON ファイル形式のより堅牢なキーワード セットを提供します。

## <a name="support"></a>サポート

[[新しいサポート リクエスト]](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) に移動してサポート チケットを開くことができます

## <a name="next-steps"></a>次のステップ

* [レガシ コンポーネント](legacy-components.md)
* [価格ページ](https://azure.microsoft.com/pricing/details/media-services/#encoding)