---
title: 言語のサポート
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services では、見る、聞く、話す、そして、ユーザーを理解するアプリケーションを構築することができます。 これらのサービス間では、3 ダース以上の言語がサポートされており、ユーザーがアプリと自然にやりとりすることができます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/16/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: cd31215f578062548c67f8d14e724dc49f73b21c
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775002"
---
# <a name="natural-language-support-for-azure-cognitive-services"></a>Azure Cognitive Services の自然言語のサポート

Azure Cognitive Services では、見る、聞く、話す、そして、ユーザーを理解するアプリケーションを構築することができます。 これらのサービス間では、3 ダース以上の言語がサポートされており、ユーザーがアプリと自然にやりとりすることができます。

この記事では、2 つのセクションに分割されます。 1 つ目は、Azure Cognitive Services で一般的にサポートされているコア言語を一覧表示します。 2 番目のセクションでは、各サービスの追加の言語サポートについて説明します。 国/地域および市場で利用可能な言語が異なる場合があります。

## <a name="core-languages"></a>コア言語

これらのコア言語がAzure Cognitive Servicesでサポートされています:

* 中国語
* 英語
* フランス語
* ドイツ語
* イタリア語
* 日本語
* 韓国語¹
* ポルトガル語
* スペイン語

> [!NOTE]
> ¹ LUIS および Video Indexer: 音声テキスト変換はサポートされていません。

## <a name="additional-language-availability-by-service"></a>サービスによって利用可能なその他の言語

これらのテーブルには、コア言語を除く利用可能な言語をサービス カテゴリごとにハイライトしています。 以下のリンク先で、追加の言語サポート、および国/地域と市場の可用性について説明します。

### <a name="vision"></a>視覚

| | アラビア語 | ブルガリア語 | カタルニア語 | クロアチア語 | チェコ語 | デンマーク語 | オランダ語 | エストニア語 | フィンランド語 | ギリシャ語 | ヒンディー語 | ハンガリー語 | アイスランド語 | インドネシア語 | ラトビア語 | リトアニア語 | マレー語 | ノルウェー語 | ポーランド語 | ルーマニア語 | ロシア語 | セルビア語 | スロバキア語 | スロベニア語 | スウェーデン語 | タミル語 | タイ語 | トルコ語 | ウクライナ語 | ベトナム語 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Computer Vision:OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/faq) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Video Indexer:音声テキスト変換](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Video Indexer:音声翻訳](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

### <a name="speech"></a>音声

| | アラビア語 | ブルガリア語 | カタルニア語 | クロアチア語 | チェコ語 | デンマーク語 | オランダ語 | エストニア語 | フィンランド語 | ギリシャ語 | ヒンディー語 | ハンガリー語 | アイスランド語 | インドネシア語 | ラトビア語 | リトアニア語 | マレー語 | ノルウェー語 | ポーランド語 | ルーマニア語 | ロシア語 | セルビア語 | スロバキア語 | スロベニア語 | スウェーデン語 | タミル語 | タイ語 | トルコ語 | ウクライナ語 | ベトナム語 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [カスタム音声](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/customspeech-how-to-topics/cognitive-services-custom-speech-change-locale) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Speech Service:音声テキスト変換](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#speech-to-text) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: |
| [音声サービス: Text-to-Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: |
| [Speech Service:音声翻訳](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#speech-translation) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

### <a name="language"></a>言語

| | アラビア語 | ブルガリア語 | カタルニア語 | クロアチア語 | チェコ語 | デンマーク語 | オランダ語 | エストニア語 | フィンランド語 | ギリシャ語 | ヒンディー語 | ハンガリー語 | アイスランド語 | インドネシア語 | ラトビア語 | リトアニア語 | マレー語 | ノルウェー語 | ポーランド語 | ルーマニア語 | ロシア語 | セルビア語 | スロバキア語 | スロベニア語 | スウェーデン語 | タミル語 | タイ語 | トルコ語 | ウクライナ語 | ベトナム語 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Bing Spell Check](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-supported-languages) | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Translator Text](https://docs.microsoft.com/azure/cognitive-services/translator/languages) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |

### <a name="search"></a>Search

| | アラビア語 | ブルガリア語 | カタルニア語 | クロアチア語 | チェコ語 | デンマーク語 | オランダ語 | エストニア語 | フィンランド語 | ギリシャ語 | ヒンディー語 | ハンガリー語 | アイスランド語 | インドネシア語 | ラトビア語 | リトアニア語 | マレー語 | ノルウェー語 | ポーランド語 | ルーマニア語 | ロシア語 | セルビア語 | スロバキア語 | スロベニア語 | スウェーデン語 | タミル語 | タイ語 | トルコ語 | ウクライナ語 | ベトナム語 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/supported-countries-markets) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/supported-countries-markets) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing News Search](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/supported-countries-markets) | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing Autosuggest](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest/bing-autosuggest-supported-languages) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing Visual Search](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/supported-countries-markets) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |
| [Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/supported-countries-markets) | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign: |

### <a name="decision"></a>決定

| | アラビア語 | ブルガリア語 | カタルニア語 | クロアチア語 | チェコ語 | デンマーク語 | オランダ語 | エストニア語 | フィンランド語 | ギリシャ語 | ヒンディー語 | ハンガリー語 | アイスランド語 | インドネシア語 | ラトビア語 | リトアニア語 | マレー語 | ノルウェー語 | ポーランド語 | ルーマニア語 | ロシア語 | セルビア語 | スロバキア語 | スロベニア語 | スウェーデン語 | タミル語 | タイ語 | トルコ語 | ウクライナ語 | ベトナム語 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [Content Moderator:テキストのフィルタリング](https://docs.microsoft.com/azure/cognitive-services/content-moderator/text-moderation-api-languages) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

次の Cognitive Services は言語に依存せず、言語に基づく制限はありません。

* [Personalizer (プレビュー)](https://docs.microsoft.com/azure/cognitive-services/personalizer/)
* [Anomaly Detector (プレビュー)](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/overview)


## <a name="see-also"></a>関連項目

* [Cognitive Services とは](welcome.md)
* [アカウントの作成](cognitive-services-apis-create-account.md)
