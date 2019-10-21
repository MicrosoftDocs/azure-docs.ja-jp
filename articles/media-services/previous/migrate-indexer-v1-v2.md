---
title: Indexer v1 と v2 から Azure Media Services Video Indexer への移行| Microsoft Docs
description: このトピックでは、Azure Media Indexer v1 と v2 から Azure Media Services Video Indexer に移行する方法を説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 329c7191482787f25cfddfd8c4d70074df9a7c1f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719963"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Media Indexer と Media Indexer 2 から Video Indexer に移行する

[Azure Media Indexer](media-services-index-content.md) メディア プロセッサは、2020 年 10 月 1 日に廃止されます。 [Azure Media Indexer 2 プレビュー](media-services-process-content-with-indexer2.md) メディア プロセッサは、2020 年 1 月 1 日に廃止されます。  [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) が、これらの従来のメディア プロセッサに取って代わります。

Azure Media Services Video Indexer は、Azure Media Analytics、Azure Search、Cognitive Services (Face API、Microsoft Translator、Computer Vision API、Custom Speech Service など) を基盤として構築されました。 Video Indexer のビデオとオーディオのモデルを使用して、ビデオから分析情報を抽出することができます。 Video Indexer はどのようなシナリオで使用できるか、どのような機能を提供するか、どのように使用を開始するかを確認するには、[Video Indexer のビデオとオーディオのモデル](../video-indexer/video-indexer-overview.md)に関するページを参照してください。 

[Azure Media Services v3 アナライザー プリセット](../latest/analyzing-video-audio-files-concept.md)を使用するか、直接 [Video Indexer API](https://api-portal.videoindexer.ai/) を使用して、ビデオ ファイルとオーディオ ファイルから分析情報を抽出できます。 現在、Video Indexer API と Media Services v3 API によって提供される機能には重複があります。

> [!NOTE]
> どのような場合に Video Indexer やMedia Services アナライザー プリセットを使用するかについて詳しくは、[比較のドキュメント](../video-indexer/compare-video-indexer-with-media-services-presets.md)をご覧ください。 

この記事では、Azure Media Indexer と Azure Media Indexer 2 から Azure Media Services Video Indexer に移行する手順について説明します。  

## <a name="migration-options"></a>移行オプション 

|以下が必要な場合  |と |
|---|---|
|字幕ファイル形式(VTT、SRT、または TTML)<br/>のメディア ファイル形式に対する音声からテキストへの文字起こしと、追加のオーディオ分析情報 (キーワード、トピック推論、音響イベント、話者のダイアライゼーション、エンティティの抽出、翻訳など) を提供するソリューション| Video Indexer v2 REST API または Azure Media Services v3 オーディオ アナライザー プリセットを通じて Azure Video Indexer の機能を使用するように、お使いのアプリケーションを更新します。|
|音声テキスト変換機能| Cognitive Services Speech API を直接使用します。|  

## <a name="getting-started-with-video-indexer"></a>Video Indexer の使用を開始する

次のセクションでは、関連するリンクを示します。[Video Indexer を使い始めるにはどうすればよいですか?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Media Services v3 API の使用を開始する

Azure Media Services v3 API では、[Azure Media Services v3 アナライザー プリセット](../latest/analyzing-video-audio-files-concept.md)を使用して、ビデオ ファイルとオーディオ ファイルから分析情報を抽出できます。 

**AudioAnalyzerPreset** を使用して、音声または画像ファイルから複数の音声分析情報を抽出できます。 出力には、音声トランスクリプト用の VTT または TTML ファイルと、JSON ファイル (すべての追加の音声分析情報が格納される) が含まれます。 音声分析情報には、キーワード、話者インデックス作成、音声のセンチメント分析が含まれます。 AudioAnalyzerPreset では、特定の言語の言語検出もサポートされます。 詳細については、[変換](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)に関するページを参照してください。

### <a name="get-started"></a>作業開始

開始するには、以下をご覧ください。

* [チュートリアル](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset の例:[Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) または [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset の例:[Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) または [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Cognitive Services Speech Services の使用を開始する

[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) では、オーディオ ストリームからテキストへの文字起こしがリアルタイムで行われる音声テキスト変換サービスが提供されます。結果のテキストを、お使いのアプリケーション、ツール、またはデバイスで使用したり表示したりできます。 音声テキスト変換を使用して、[独自の音響モデル、言語モデル、または発音モデルをカスタマイズ](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)できます。 詳細については、[Cognitive Services の音声テキスト変換](../../cognitive-services/speech-service/speech-to-text.md)に関するページを参照してください。 

> [!NOTE] 
> 音声テキスト変換サービスでは、ビデオ ファイル形式は使用されず、[特定のオーディオ形式](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats)だけが使用されます。 

音声テキスト変換サービスの詳細と開始方法については、「[音声変換の概要](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)」を参照してください。

## <a name="known-differences-from-deprecated-services"></a>非推奨のサービスとの既知の違い 

Video Indexer、Azure Media Services v3 AudioAnalyzerPreset、および Cognitive Services Speech Services サービスは、廃止になった Azure Media Indexer 1 と Azure Media Indexer 2 プロセッサよりも信頼性が高く、より高品質な出力が生成されることがわかります。  

既知の相違点をいくつか次に示します。 

* SAMI 形式の字幕ファイルはサポートされなくなります。 これは、一般に使用されなくなった古い字幕形式です。 TTML、WebVTT、SRT に置き換えられています。  
* オーディオ インデックス BLOB (AIB) ファイルはサポートされなくなります。 この機能は、Indexer 1 テクノロジに固有のものであり、提供されなくなりました。  
* Cognitive Services Speech Services では、キーワード抽出はサポートされません。 ただし、Video Indexer と Media Services v3 AudioAnalyzerPreset はどちらも、JSON ファイル形式のより堅牢なキーワード セットを提供します。 

## <a name="contact-us"></a>お問い合わせ 

Azure Media Indexer 1 と Azure Media Indexer 2 の非推奨や、他のサービスへの移行についてご質問がある場合は、amshelp@microsoft.com までお問い合わせください。 

## <a name="next-steps"></a>次の手順

* [レガシ コンポーネント](legacy-components.md)
* [価格ページ](https://azure.microsoft.com/pricing/details/media-services/#encoding)


