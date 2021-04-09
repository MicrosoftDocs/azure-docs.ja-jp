---
title: 言語サポート - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声サービスでは、音声翻訳に加え、音声からテキストへの変換とテキストから音声への変換のためのさまざまな言語がサポートされます。 この記事では、サービス機能によってサポートされている言語の包括的な一覧を示します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 17511778f63a2d7270178042c4bb414cdd6630c3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955467"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>音声サービスの言語と音声のサポート

言語サポートは、音声サービスの機能によって異なります。 次の表は、[音声テキスト変換](#speech-to-text)、[テキスト読み上げ](#text-to-speech)、および [音声翻訳](#speech-translation)サービスの言語サポートをまとめたものです。

## <a name="speech-to-text"></a>音声テキスト変換

Microsoft Speech SDK と REST API は、どちらも以下の言語 (ロケール) をサポートしています。 

精度を高めるために、 **"オーディオ + 人間" というラベルが付いたトランスクリプト** または **関連テキスト (文)** をアップロードすることにより、言語のサブセットに対してカスタマイズが提供されます。 **オーディオ + 人間というラベルが付いたトランスクリプト** を使用した音響モデルのカスタマイズのサポートは、下に示す特定の基本モデルに限定されます。 その他の基本モデルおよび言語では、以下と同様に、カスタム モデルをトレーニングするために、トランスクリプトのテキストのみが使用されます: **関連するテキスト: (文)** をアップロードすることにより、言語のサブセットに対してカスタマイズが提供されます。 カスタマイズの詳細については、[Custom Speech の利用の開始](./custom-speech-overview.md)に関するページを参照してください。

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Language                 | ロケール (BCP-47) | カスタマイズ  | [言語検出](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| アラビア語 (バーレーン)、現代標準  | `ar-BH` | Text                                   | はい                           | 
| アラビア語 (エジプト)                     | `ar-EG` | Text                                   | はい                          |
| アラビア語 (イラク)                      | `ar-IQ` | テキスト                                   |                           |
| アラビア語 (イスラエル)                    | `ar-IL` | テキスト                                   |                           |
| アラビア語 (ヨルダン)                    | `ar-JO` | テキスト                                   |                           |
| アラビア語 (クウェート)                    | `ar-KW` | テキスト                                   |                           |
| アラビア語 (レバノン)                   | `ar-LB` | テキスト                                   |                           |
| アラビア語 (オマーン)                      | `ar-OM` | テキスト                                   |                           |
| アラビア語 (カタール)                     | `ar-QA` | テキスト                                   |                           |
| アラビア語 (サウジアラビア)              | `ar-SA` | Text                                   | はい                          |
| アラビア語 (パレスチナ自治政府)        | `ar-PS` | テキスト                                   |                           |
| アラビア語 (シリア)                     | `ar-SY` | Text                                   | はい                          |
| アラビア語 (アラブ首長国連邦)      | `ar-AE` | テキスト                                   |                           |
| ブルガリア語 (ブルガリア)               | `bg-BG` | テキスト                                   |                           |
| カタルニア語 (スペイン)                    | `ca-ES` | Text                                   | はい                          |
| 中国語 (繁体字)   | `zh-HK` | オーディオ (20201015)<br>Text                 |        はい                   |
| 中国語 (標準、簡体字)     | `zh-CN` | オーディオ (20200910)<br>Text                 |     はい                      |
| 中国語 (台湾標準中国語)       | `zh-TW` | オーディオ (20190701、20201015)<br>Text                 |           はい                |
| クロアチア語 (クロアチア)                 | `hr-HR` | テキスト                                   |                           |
| チェコ語 (チェコ共和国)             | `cs-CZ` | テキスト                                   |                           |
| デンマーク語 (デンマーク)                   | `da-DK` | Text                                   | はい                          |
| オランダ語 (オランダ)                | `nl-NL` | オーディオ (20201015)<br>Text                                   |    はい                       |
| 英語 (オーストラリア)                | `en-AU` | オーディオ (20201019)<br>Text                 | はい                          |
| 英語 (カナダ)                   | `en-CA` | オーディオ (20201019)<br>Text                 | はい                          |
| 英語 (ガーナ)                    | `en-GH` | テキスト                                   |                           |
| 英語 (香港)                | `en-HK` | テキスト                                   |                           |
| 英語 (インド)                    | `en-IN` | オーディオ (20200923)<br>Text                 | はい                          |
| 英語 (アイルランド)                  | `en-IE` | テキスト                                   |                           |
| 英語 (ケニア)                    | `en-KE` | テキスト                                   |                           |
| 英語 (ニュージーランド)              | `en-NZ` | オーディオ (20201019)<br>Text                 |  はい                         |
| 英語 (ナイジェリア)                  | `en-NG` | テキスト                                   |                           |
| 英語 (フィリピン)              | `en-PH` | テキスト                                   |                           |
| 英語 (シンガポール)                | `en-SG` | テキスト                                   |                           |
| 英語 (南アフリカ)             | `en-ZA` | テキスト                                   |                           |
| 英語 (タンザニア)                 | `en-TZ` | テキスト                                   |                           |
| 英語 (イギリス)           | `en-GB` | オーディオ (20201019)<br>テキスト<br>発音| はい                          |
| 英語 (米国)            | `en-US` | Audio (20201019、20210223)<br>テキスト<br>発音| はい                          |
| エストニア語 (エストニア)                  | `et-EE` | テキスト                                   |                           |
| フィリピノ語 (フィリピン)             | `fil-PH`| テキスト                                   |                           |
| フィンランド語 (フィンランド)                  | `fi-FI` | Text                                   |     はい                      |
| フランス語 (カナダ)                    | `fr-CA` | オーディオ (20201015)<br>Text                 |     はい                      |
| フランス語 (フランス)                    | `fr-FR` | オーディオ (20201015)<br>テキスト<br>発音|      はい                     |
| フランス語 (スイス)               | `fr-CH` | テキスト                                   |                           |
| ドイツ語 (オーストリア)                   | `de-AT` | テキスト                                   |                           |
| ドイツ語 (ドイツ)                   | `de-DE` | オーディオ (20190701、20200619、20201127)<br>テキスト<br>発音|  はい                         |
| ギリシャ語 (ギリシャ)                     | `el-GR` | テキスト                                   |                           |
| グジャラート語 (インド)                  | `gu-IN` | テキスト                                   |                           |
| ヒンディー語 (インド)                      | `hi-IN` | オーディオ (20200701)<br>Text                 |     はい                      |
| ハンガリー語 (ハンガリー)                | `hu-HU` | テキスト                                   |                           |
| インドネシア語 (インドネシア)             | `id-ID` | テキスト                                   |                           |
| アイルランド語 (アイルランド)                     | `ga-IE` | テキスト                                   |                           |
| イタリア語 (イタリア)                    | `it-IT` | オーディオ (20201016)<br>テキスト<br>発音|      はい                     |
| 日本語 (日本)                   | `ja-JP` | Text                                   |      はい                     |
| 韓国語 (韓国)                     | `ko-KR` | オーディオ (20201015)<br>Text                 |      はい                     |
| ラトビア語 (ラトビア)                   | `lv-LV` | テキスト                                   |                           |
| リトアニア語 (リトアニア)             | `lt-LT` | テキスト                                   |                           |
| マレー語 (マレーシア)                    | `ms-MY` | テキスト                                   |                           |
| マルタ語 (マルタ)                     | `mt-MT` | テキスト                                   |                           |
| マラーティー語 (インド)                    | `mr-IN` | テキスト                                   |                           |
| ノルウェー語 (ブークモール、ノルウェー)         | `nb-NO` | Text                                   |     はい                      |
| ポーランド語 (ポーランド)                    | `pl-PL` | Text                                   |       はい                    |
| ポルトガル語 (ブラジル)                | `pt-BR` | オーディオ (20190620、20201015)<br>テキスト<br>発音|          はい                 |
| ポルトガル語 (ポルトガル)              | `pt-PT` | Text                                   |             はい              |
| ルーマニア語 (ルーマニア)                 | `ro-RO` | テキスト                                   |                           |
| ロシア語 (ロシア)                   | `ru-RU` | オーディオ (20200907)<br>Text                 |                はい           |
| スロバキア語 (スロバキア)                  | `sk-SK` | テキスト                                   |                           |
| スロベニア語 (スロベニア)               | `sl-SI` | テキスト                                   |                           |
| スペイン語 (アルゼンチン)                | `es-AR` | テキスト                                   |                           |
| スペイン語 (ボリビア)                  | `es-BO` | テキスト                                   |                           |
| スペイン語 (チリ)                    | `es-CL` | テキスト                                   |                           |
| スペイン語 (コロンビア)                 | `es-CO` | テキスト                                   |                           |
| スペイン語 (コスタリカ)               | `es-CR` | テキスト                                   |                           |
| スペイン語 (キューバ)                     | `es-CU` | テキスト                                   |                           |
| スペイン語 (ドミニカ共和国)       | `es-DO` | テキスト                                   |                           |
| スペイン語 (エクアドル)                  | `es-EC` | テキスト                                   |                           |
| スペイン語 (エルサルバドル)              | `es-SV` | テキスト                                   |                           |
| スペイン語 (赤道ギニア)        | `es-GQ` | テキスト                                   |                           |
| スペイン語 (グアテマラ)                | `es-GT` | テキスト                                   |                           |
| スペイン語 (ホンジュラス)                 | `es-HN` | テキスト                                   |                           |
| スペイン語 (メキシコ)                   | `es-MX` | オーディオ (20200907)<br>Text                 |    はい                       |
| スペイン語 (ニカラグア)                | `es-NI` | テキスト                                   |                           |
| スペイン語 (パナマ)                   | `es-PA` | テキスト                                   |                           |
| スペイン語 (パラグアイ)                 | `es-PY` | テキスト                                   |                           |
| スペイン語 (ペルー)                     | `es-PE` | テキスト                                   |                           |
| スペイン語 (プエルトリコ)              | `es-PR` | テキスト                                   |                           |
| スペイン語 (スペイン)                    | `es-ES` | オーディオ (20201015)<br>Text                 |  はい                         |
| スペイン語 (ウルグアイ)                  | `es-UY` | テキスト                                   |                           |
| スペイン語 (米国)                      | `es-US` | テキスト                                   |                           |
| スペイン語 (ベネズエラ)                | `es-VE` | テキスト                                   |                           |
| スウェーデン語 (スウェーデン)                   | `sv-SE` | Text                                   |   はい                        |
| タミール語 (インド)                      | `ta-IN` | テキスト                                   |                           |
| テルグ語 (インド)                     | `te-IN` | テキスト                                   |                           |
| タイ語 (タイ)                    | `th-TH` | Text                                   |      はい                     |
| トルコ語 (トルコ)                   | `tr-TR` | テキスト                                   |                           |
| ベトナム語 (ベトナム)               | `vi-VN` | テキスト                                   |                           |

## <a name="text-to-speech"></a>テキスト読み上げ

Microsoft Speech SDK と REST API のどちらでもこれらの音声がサポートされ、そのいずれでもロケールで識別される特定の言語と方言がサポートされています。 [音声/リスト API](rest-text-to-speech.md#get-a-list-of-voices) を使用して、特定のリージョン/エンドポイントごとにサポートされている言語と音声の完全な一覧を取得することもできます。 

> [!IMPORTANT]
> 価格は、標準音声、カスタム音声、ニューラル音声ごとに異なります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)のページを参照してください。

### <a name="neural-voices"></a>ニューラル音声

ニューラル テキスト読み上げは、ディープ ニューラル ネットワークを利用した新しい種類の音声合成です。 ニューラル音声を使用した場合、合成音声は人間の録音とほとんど区別がつきません。

ニューラル音声を使用すると、チャットボットや音声アシスタントとの対話をより自然で魅力的なものにできます。また、電子書籍などのデジタル テキストをオーディオブックに変換したり、カーナビゲーション システムを強化したりすることもできます。 ニューラル音声では、人間のような自然な韻律と明瞭な発音により、ユーザーが AI システムと対話する際のリスニング疲労が大幅に軽減されます。

> [!NOTE]
> ニューラル音声は、24 khz サンプル レートを使用するサンプルから作成されます。
> すべての音声は、合成時に他のサンプル レートにアップサンプリングまたはダウンサンプリングできます。


| Language | Locale | 性別 | 音声名 | スタイルのサポート |
|---|---|---|---|---|
| アラビア語 (エジプト) | `ar-EG` | Female | `ar-EG-SalmaNeural` | 全般 |
| アラビア語 (エジプト) | `ar-EG` | Male | `ar-EG-ShakirNeural` <sup>[新規作成]</sup> | 全般 |
| アラビア語 (サウジアラビア) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | 全般 |
| アラビア語 (サウジアラビア) | `ar-SA` | Male | `ar-SA-HamedNeural` <sup>[新規作成]</sup> | 全般 |
| ブルガリア語 (ブルガリア) | `bg-BG` | Female | `bg-BG-KalinaNeural` | 全般 |
| ブルガリア語 (ブルガリア) | `bg-BG` | Male | `bg-BG-BorislavNeural` <sup>[新規作成]</sup> | 全般 |
| カタルニア語 (スペイン) | `ca-ES` | Female | `ca-ES-AlbaNeural` | 全般 |
| カタルニア語 (スペイン) | `ca-ES` | Female | `ca-ES-JoanaNeural` <sup>[新規作成]</sup> | 全般 |
| カタルニア語 (スペイン) | `ca-ES` | Male | `ca-ES-EnricNeural` <sup>[新規作成]</sup> | 全般 |
| 中国語 (繁体字) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | 全般 |
| 中国語 (繁体字) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` <sup>[新規作成]</sup> | 全般 |
| 中国語 (繁体字) | `zh-HK` | Male | `zh-HK-WanLungNeural` <sup>[新規作成]</sup> | 全般 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能  |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | 子供の音声、ストーリーのナレーション向けに最適化 |
| 中国語 (標準、簡体字) | `zh-CN` | Male | `zh-CN-YunyangNeural` | ニュースの読み取り向けに最適化<br /> [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、複数の音声スタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Male | `zh-CN-YunyeNeural` | ストーリーのナレーション向けに最適化  |
| 中国語 (台湾標準中国語) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` <sup>[新規作成]</sup> | 全般 |
| 中国語 (台湾標準中国語) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | 全般 |
| 中国語 (台湾標準中国語) | `zh-TW` | Male | `zh-TW-YunJheNeural` <sup>[新規作成]</sup> | 全般 |
| クロアチア語 (クロアチア) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | 全般 |
| クロアチア語 (クロアチア) | `hr-HR` | Male | `hr-HR-SreckoNeural` <sup>[新規作成]</sup> | 全般 |
| チェコ語 (チェコ) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | 全般 |
| チェコ語 (チェコ) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` <sup>[新規作成]</sup> | 全般 |
| デンマーク語 (デンマーク) | `da-DK` | Female | `da-DK-ChristelNeural` | 全般 |
| デンマーク語 (デンマーク) | `da-DK` | Male | `da-DK-JeppeNeural` <sup>[新規作成]</sup> | 全般 |
| オランダ語 (オランダ) | `nl-NL` | Female | `nl-NL-ColetteNeural` | 全般 |
| オランダ語 (オランダ) | `nl-NL` | Female | `nl-NL-FennaNeural` <sup>[新規作成]</sup> | 全般 |
| オランダ語 (オランダ) | `nl-NL` | Male | `nl-NL-MaartenNeural` <sup>[新規作成]</sup> | 全般 |
| 英語 (オーストラリア) | `en-AU` | Female | `en-AU-NatashaNeural` | 全般 |
| 英語 (オーストラリア) | `en-AU` | Male | `en-AU-WilliamNeural` | 全般 |
| 英語 (カナダ) | `en-CA` | Female | `en-CA-ClaraNeural` | 全般 |
| 英語 (カナダ) | `en-CA` | Male | `en-CA-LiamNeural` <sup>[新規作成]</sup> | 全般 |
| 英語 (インド) | `en-IN` | Female | `en-IN-NeerjaNeural` | 全般 |
| 英語 (インド) | `en-IN` | Male | `en-IN-PrabhatNeural` <sup>[新規作成]</sup> | 全般 |
| 英語 (アイルランド) | `en-IE` | Female | `en-IE-EmilyNeural` | 全般 |
| 英語 (アイルランド) | `en-IE` | Male | `en-IE-ConnorNeural` <sup>[新規作成]</sup> | 全般 |
| 英語 (イギリス) | `en-GB` | Female | `en-GB-LibbyNeural` | 全般 |
| 英語 (イギリス) | `en-GB` | Female | `en-GB-MiaNeural` | 全般 |
| 英語 (イギリス) | `en-GB` | Male | `en-GB-RyanNeural` | 全般 |
| 英語 (米国) | `en-US` | Female | `en-US-AriaNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能  |
| 英語 (米国) | `en-US` | Female | `en-US-JennyNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能  |
| 英語 (米国) | `en-US` | Male | `en-US-GuyNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能  |
| フィンランド語 (フィンランド) | `fi-FI` | Female | `fi-FI-NooraNeural` | 全般 |
| フィンランド語 (フィンランド) | `fi-FI` | Female | `fi-FI-SelmaNeural` <sup>[新規作成]</sup> | 全般 |
| フィンランド語 (フィンランド) | `fi-FI` | Male | `fi-FI-HarriNeural` <sup>[新規作成]</sup> | 全般 |
| フランス語 (カナダ) | `fr-CA` | Female | `fr-CA-SylvieNeural` | 全般 |
| フランス語 (カナダ) | `fr-CA` | Male | `fr-CA-AntoineNeural` <sup>[新規作成]</sup> | 全般 |
| フランス語 (カナダ) | `fr-CA` | Male | `fr-CA-JeanNeural` | 全般 |
| フランス語 (フランス) | `fr-FR` | Female | `fr-FR-DeniseNeural` | 全般 |
| フランス語 (フランス) | `fr-FR` | Male | `fr-FR-HenriNeural` | 全般 |
| フランス語 (スイス) | `fr-CH` | Female | `fr-CH-ArianeNeural` | 全般 |
| フランス語 (スイス) | `fr-CH` | Male | `fr-CH-FabriceNeural` <sup>[新規作成]</sup> | 全般 |
| ドイツ語 (オーストリア) | `de-AT` | Female | `de-AT-IngridNeural` | 全般 |
| ドイツ語 (オーストリア) | `de-AT` | Male | `de-AT-JonasNeural` <sup>[新規作成]</sup> | 全般 |
| ドイツ語 (ドイツ) | `de-DE` | Female | `de-DE-KatjaNeural` | 全般 |
| ドイツ語 (ドイツ) | `de-DE` | Male | `de-DE-ConradNeural` | 全般 |
| ドイツ語 (スイス) | `de-CH` | Female | `de-CH-LeniNeural` | 全般 |
| ドイツ語 (スイス) | `de-CH` | Male | `de-CH-JanNeural` <sup>[新規作成]</sup> | 全般 |
| ギリシャ語 (ギリシャ) | `el-GR` | Female | `el-GR-AthinaNeural` | 全般 |
| ギリシャ語 (ギリシャ) | `el-GR` | Male | `el-GR-NestorasNeural` <sup>[新規作成]</sup> | 全般 |
| ヘブライ語 (イスラエル) | `he-IL` | Female | `he-IL-HilaNeural` | 全般 |
| ヘブライ語 (イスラエル) | `he-IL` | Male | `he-IL-AvriNeural` <sup>[新規作成]</sup> | 全般 |
| ヒンディー語 (インド) | `hi-IN` | Female | `hi-IN-SwaraNeural` | 全般 |
| ヒンディー語 (インド) | `hi-IN` | Male | `hi-IN-MadhurNeural` <sup>[新規作成]</sup> | 全般 |
| ハンガリー語 (ハンガリー) | `hu-HU` | Female | `hu-HU-NoemiNeural` | 全般 |
| ハンガリー語 (ハンガリー) | `hu-HU` | Male | `hu-HU-TamasNeural` <sup>[新規作成]</sup> | 全般 |
| インドネシア語 (インドネシア) | `id-ID` | Female | `id-ID-GadisNeural` <sup>[新規作成]</sup> | 全般 |
| インドネシア語 (インドネシア) | `id-ID` | Male | `id-ID-ArdiNeural` | 全般 |
| イタリア語 (イタリア) | `it-IT` | Female | `it-IT-ElsaNeural` | 全般 |
| イタリア語 (イタリア) | `it-IT` | Female | `it-IT-IsabellaNeural` | 全般 |
| イタリア語 (イタリア) | `it-IT` | Male | `it-IT-DiegoNeural` | 全般 |
| 日本語 (日本) | `ja-JP` | Female | `ja-JP-NanamiNeural` | 全般 |
| 日本語 (日本) | `ja-JP` | Male | `ja-JP-KeitaNeural` | 全般 |
| 韓国語 (韓国) | `ko-KR` | Female | `ko-KR-SunHiNeural` | 全般 |
| 韓国語 (韓国) | `ko-KR` | Male | `ko-KR-InJoonNeural` | 全般 |
| マレー語 (マレーシア) | `ms-MY` | Female | `ms-MY-YasminNeural` | 全般 |
| マレー語 (マレーシア) | `ms-MY` | Male | `ms-MY-OsmanNeural` <sup>[新規作成]</sup> | 全般 |
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Female | `nb-NO-IselinNeural` | 全般 |
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Female | `nb-NO-PernilleNeural` <sup>[新規作成]</sup> | 全般 |
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Male | `nb-NO-FinnNeural` <sup>[新規作成]</sup> | 全般 |
| ポーランド語 (ポーランド) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` <sup>[新規作成]</sup> | 全般 |
| ポーランド語 (ポーランド) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | 全般 |
| ポーランド語 (ポーランド) | `pl-PL` | Male | `pl-PL-MarekNeural` <sup>[新規作成]</sup> | 全般 |
| ポルトガル語 (ブラジル) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数の音声スタイルを使用可能  |
| ポルトガル語 (ブラジル) | `pt-BR` | Male | `pt-BR-AntonioNeural` | 全般 |
| ポルトガル語 (ポルトガル) | `pt-PT` | Female | `pt-PT-FernandaNeural` | 全般 |
| ポルトガル語 (ポルトガル) | `pt-PT` | Female | `pt-PT-RaquelNeural` <sup>[新規作成]</sup> | 全般 |
| ポルトガル語 (ポルトガル) | `pt-PT` | Male | `pt-PT-DuarteNeural` <sup>[新規作成]</sup> | 全般 |
| ルーマニア語 (ルーマニア) | `ro-RO` | Female | `ro-RO-AlinaNeural` | 全般 |
| ルーマニア語 (ルーマニア) | `ro-RO` | Male | `ro-RO-EmilNeural` <sup>[新規作成]</sup> | 全般 |
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-DariyaNeural` | 全般 |
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` <sup>[新規作成]</sup> | 全般 |
| ロシア語 (ロシア) | `ru-RU` | Male | `ru-RU-DmitryNeural` <sup>[新規作成]</sup> | 全般 |
| スロバキア語 (スロバキア) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | 全般 |
| スロバキア語 (スロバキア) | `sk-SK` | Male | `sk-SK-LukasNeural` <sup>[新規作成]</sup> | 全般 |
| スロベニア語 (スロベニア) | `sl-SI` | Female | `sl-SI-PetraNeural` | 全般 |
| スロベニア語 (スロベニア) | `sl-SI` | Male | `sl-SI-RokNeural` <sup>[新規作成]</sup> | 全般 |
| スペイン語 (メキシコ) | `es-MX` | Female | `es-MX-DaliaNeural` | 全般 |
| スペイン語 (メキシコ) | `es-MX` | Male | `es-MX-JorgeNeural` | 全般 |
| スペイン語 (スペイン) | `es-ES` | Female | `es-ES-ElviraNeural` | 全般 |
| スペイン語 (スペイン) | `es-ES` | Male | `es-ES-AlvaroNeural` | 全般 |
| スウェーデン語 (スウェーデン) | `sv-SE` | Female | `sv-SE-HilleviNeural` | 全般 |
| スウェーデン語 (スウェーデン) | `sv-SE` | Female | `sv-SE-SofieNeural` <sup>[新規作成]</sup> | 全般 |
| スウェーデン語 (スウェーデン) | `sv-SE` | Male | `sv-SE-MattiasNeural` <sup>[新規作成]</sup> | 全般 |
| タミール語 (インド) | `ta-IN` | Female | `ta-IN-PallaviNeural` | 全般 |
| タミール語 (インド) | `ta-IN` | Male | `ta-IN-ValluvarNeural` <sup>[新規作成]</sup> | 全般 |
| テルグ語 (インド) | `te-IN` | Female | `te-IN-ShrutiNeural` | 全般 |
| テルグ語 (インド) | `te-IN` | Male | `te-IN-MohanNeural` <sup>[新規作成]</sup> | 全般 |
| タイ語 (タイ) | `th-TH` | Female | `th-TH-AcharaNeural` | 全般 |
| タイ語 (タイ) | `th-TH` | Female | `th-TH-PremwadeeNeural` | 全般 |
| タイ語 (タイ) | `th-TH` | Male | `th-TH-NiwatNeural` <sup>[新規作成]</sup> | 全般 |
| トルコ語 (トルコ) | `tr-TR` | Female | `tr-TR-EmelNeural` | 全般 |
| トルコ語 (トルコ) | `tr-TR` | Male | `tr-TR-AhmetNeural` <sup>[新規作成]</sup> | 全般 |
| ベトナム語 (ベトナム) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | 全般 |
| ベトナム語 (ベトナム) | `vi-VN` | Male | `vi-VN-NamMinhNeural` <sup>[新規作成]</sup> | 全般 |

#### <a name="neural-voices-in-preview"></a>プレビュー段階のニューラル音声

次のニューラル音声は、パブリック プレビュー段階です。 

| Language                         | Locale  | 性別 | 音声名                             | スタイルのサポート |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数のスタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | 全般、複数のロールプレイとスタイルが [SSML の使用](speech-synthesis-markup.md#adjust-speaking-styles)により使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、高齢者の音声、複数のスタイルを使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | 全般、複数のロールプレイとスタイルが [SSML の使用](speech-synthesis-markup.md#adjust-speaking-styles)により使用可能 |
| 中国語 (標準、簡体字) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | [SSML の使用により](speech-synthesis-markup.md#adjust-speaking-styles)、全般、複数のスタイルを使用可能 |
| エストニア語 (エストニア) | `et-EE` | Female | `et-EE-AnuNeural` | 全般 |
| エストニア語 (エストニア) | `et-EE` | Male | `et-EE-KertNeural` <sup>[新規作成]</sup> | 全般 |
| アイルランド語 (アイルランド) | `ga-IE` | Female | `ga-IE-OrlaNeural` | 全般 |
| アイルランド語 (アイルランド) | `ga-IE` | Male | `ga-IE-ColmNeural` <sup>[新規作成]</sup> | 全般 |
| ラトビア語 (ラトビア) | `lv-LV` | Female | `lv-LV-EveritaNeural` | 全般 |
| ラトビア語 (ラトビア) | `lv-LV` | Male | `lv-LV-NilsNeural` <sup>[新規作成]</sup> | 全般 |
| リトアニア語 (リトアニア) | `lt-LT` | Female | `lt-LT-OnaNeural` | 全般 |
| リトアニア語 (リトアニア) | `lt-LT` | Male | `lt-LT-LeonasNeural` <sup>[新規作成]</sup> | 全般 |
| マルタ語 (マルタ) | `mt-MT` | Female | `mt-MT-GraceNeural` | 全般 |
| マルタ語 (マルタ) | `mt-MT` | Male | `mt-MT-JosephNeural` <sup>[新規作成]</sup> | 全般 |

> [!IMPORTANT]
> パブリック プレビュー段階の音声は、3 つのサービス リージョン (米国東部、西ヨーロッパ、東南アジア) でのみ使用できます。

リージョン別の提供状況の詳細については、[リージョン](regions.md#standard-and-neural-voices)に関するページを参照してください。

ニューラル音声 (話し方など) を構成および調整する方法については、「[音声合成マークアップ言語](speech-synthesis-markup.md#adjust-speaking-styles)」を参照してください。

> [!IMPORTANT]
> `en-US-JessaNeural` 音声が `en-US-AriaNeural` に変更されました。 前に "Jessa" を使用していた場合は、"Aria" に変換します。

> [!TIP]
> 音声合成要求では、"Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" のような完全なサービス名マッピングを引き続き使用できます。

### <a name="standard-voices"></a>標準音声

テキストから合成音声への変換用に、45 を超える言語とロケールで 75 を超える標準的な音声が用意されています。 リージョン別の提供状況の詳細については、[リージョン](regions.md#standard-and-neural-voices)に関するページを参照してください。

> [!NOTE]
> 2 つの例外を除き、標準音声は 16 khz サンプル レートを使用するサンプルから作成されます。
> **en-US-AriaRUS** と **en-US-GuyRUS** の音声も、24 khz サンプル レートを使用するサンプルから作成されます。
> すべての音声は、合成時に他のサンプル レートにアップサンプリングまたはダウンサンプリングできます。

| Language | ロケール (BCP-47) | 性別 | 音声名 |
|--|--|--|--|
| アラビア語 (アラブ) | `ar-EG` | Female | `ar-EG-Hoda`|
| アラビア語 (サウジアラビア) | `ar-SA` | Male | `ar-SA-Naayf`|
| ブルガリア語 (ブルガリア) | `bg-BG` | Male | `bg-BG-Ivan`|
| カタルニア語 (スペイン) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| 中国語 (繁体字) | `zh-HK` | Male | `zh-HK-Danny`|
| 中国語 (繁体字) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| 中国語 (標準、簡体字) | `zh-CN` | Male | `zh-CN-Kangkang`|
| 中国語 (標準、簡体字) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| 中国語 (台湾標準中国語) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| 中国語 (台湾標準中国語) |  `zh-TW` | Female | `zh-TW-Yating`|
| 中国語 (台湾標準中国語) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| クロアチア語 (クロアチア) | `hr-HR` | Male | `hr-HR-Matej`|
| チェコ語 (チェコ共和国) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| デンマーク語 (デンマーク) | `da-DK` | Female | `da-DK-HelleRUS`|
| オランダ語 (オランダ) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| 英語 (オーストラリア) | `en-AU` | Female | `en-AU-Catherine`|
| 英語 (オーストラリア) | `en-AU` | Female | `en-AU-HayleyRUS`|
| 英語 (カナダ) | `en-CA` | Female | `en-CA-HeatherRUS`|
| 英語 (カナダ) | `en-CA` | Female | `en-CA-Linda`|
| 英語 (インド) | `en-IN` | Female | `en-IN-Heera`|
| 英語 (インド) | `en-IN` | Female | `en-IN-PriyaRUS`|
| 英語 (インド) | `en-IN` | Male | `en-IN-Ravi`|
| 英語 (アイルランド) | `en-IE` | Male | `en-IE-Sean`|
| 英語 (イギリス) | `en-GB` | Male | `en-GB-George`|
| 英語 (イギリス) | `en-GB` | Female | `en-GB-HazelRUS`|
| 英語 (イギリス) | `en-GB` | Female | `en-GB-Susan`|
| 英語 (米国) | `en-US` | Male | `en-US-BenjaminRUS`|
| 英語 (米国) | `en-US` | Male | `en-US-GuyRUS`|
| 英語 (米国) | `en-US` | Female | `en-US-AriaRUS`|
| 英語 (米国) | `en-US` | Female | `en-US-ZiraRUS`|
| フィンランド語 (フィンランド) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| フランス語 (カナダ) | `fr-CA` | Female | `fr-CA-Caroline`|
| フランス語 (カナダ) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| フランス語 (フランス) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| フランス語 (フランス) | `fr-FR` | Female | `fr-FR-Julie`|
| フランス語 (フランス) | `fr-FR` | Male | `fr-FR-Paul`|
| フランス語 (スイス) | `fr-CH` | Male | `fr-CH-Guillaume`|
| ドイツ語 (オーストリア) | `de-AT` | Male | `de-AT-Michael`|
| ドイツ語 (ドイツ) | `de-DE` | Female | `de-DE-HeddaRUS`|
| ドイツ語 (ドイツ) | `de-DE` | Male | `de-DE-Stefan`|
| ドイツ語 (スイス) | `de-CH` | Male | `de-CH-Karsten`|
| ギリシャ語 (ギリシャ) | `el-GR` | Male | `el-GR-Stefanos`|
| ヘブライ語 (イスラエル) | `he-IL` | Male | `he-IL-Asaf`|
| ヒンディー語 (インド) | `hi-IN` | Male | `hi-IN-Hemant`|
| ヒンディー語 (インド) | `hi-IN` | Female | `hi-IN-Kalpana`|
| ハンガリー語 (ハンガリー) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| インドネシア語 (インドネシア) | `id-ID` | Male | `id-ID-Andika`|
| イタリア語 (イタリア) | `it-IT` | Male | `it-IT-Cosimo`|
| イタリア語 (イタリア) | `it-IT` | Female | `it-IT-LuciaRUS`|
| 日本語 (日本) | `ja-JP` | Female | `ja-JP-Ayumi`|
| 日本語 (日本) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| 日本語 (日本) | `ja-JP` | Male | `ja-JP-Ichiro`|
| 韓国語 (韓国) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| マレー語 (マレーシア) | `ms-MY` | Male | `ms-MY-Rizwan`|
| ノルウェー語 (ブークモール、ノルウェー) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| ポーランド語 (ポーランド) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| ポルトガル語 (ブラジル) | `pt-BR` | Male | `pt-BR-Daniel`|
| ポルトガル語 (ブラジル) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| ポルトガル語 (ポルトガル) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| ルーマニア語 (ルーマニア) | `ro-RO` | Male | `ro-RO-Andrei`|
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| ロシア語 (ロシア) | `ru-RU` | Female | `ru-RU-Irina`|
| ロシア語 (ロシア) | `ru-RU` | Male | `ru-RU-Pavel`|
| スロバキア語 (スロバキア) | `sk-SK` | Male | `sk-SK-Filip`|
| スロベニア語 (スロベニア) | `sl-SI` | Male | `sl-SI-Lado`|
| スペイン語 (メキシコ) | `es-MX` | Female | `es-MX-HildaRUS`|
| スペイン語 (メキシコ) | `es-MX` | Male | `es-MX-Raul`|
| スペイン語 (スペイン) | `es-ES` | Female | `es-ES-HelenaRUS`|
| スペイン語 (スペイン) | `es-ES` | Female | `es-ES-Laura`|
| スペイン語 (スペイン) | `es-ES` | Male | `es-ES-Pablo`|
| スウェーデン語 (スウェーデン) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| タミール語 (インド) | `ta-IN` | Male | `ta-IN-Valluvar`|
| テルグ語 (インド) | `te-IN` | Female | `te-IN-Chitra`|
| タイ語 (タイ) | `th-TH` | Male | `th-TH-Pattara`|
| トルコ語 (トルコ) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| ベトナム語 (ベトナム) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> `en-US-Jessa` 音声が `en-US-Aria` に変更されました。 前に "Jessa" を使用していた場合は、"Aria" に変換します。

> [!TIP]
> 音声合成要求では、"Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" のような完全なサービス名マッピングを引き続き使用できます。

### <a name="customization"></a>カスタマイズ

Custom Voice は、標準およびニューラル レベルで使用できます。 サポートされている言語は、これら 2 つのレベルで異なります。 

| Language | Locale | Standard | ニューラル |
|--|--|--|--|
| 中国語 (標準、簡体字) | `zh-CN` | はい | はい |
| 中国語 (標準、簡体字)、英語バイリンガル | `zh-CN` バイリンガル | はい | はい |
| 英語 (オーストラリア) | `en-AU` | いいえ | はい |
| 英語 (インド) | `en-IN` | はい | はい |
| 英語 (イギリス) | `en-GB` | はい | はい |
| 英語 (米国) | `en-US` | はい | はい |
| フランス語 (カナダ) | `fr-CA` | いいえ | はい |
| フランス語 (フランス) | `fr-FR` | はい | はい |
| ドイツ語 (ドイツ) | `de-DE` | はい | はい |
| イタリア語 (イタリア) | `it-IT` | はい | はい |
| 日本語 (日本) | `ja-JP` | いいえ | はい |
| 韓国語 (韓国) | `ko-KR` | いいえ | はい |
| ポルトガル語 (ブラジル) | `pt-BR` | はい | はい |
| スペイン語 (メキシコ) | `es-MX` | はい | はい |
| スペイン語 (スペイン) | `es-ES` | いいえ | はい |

カスタム音声モデルをトレーニングする必要があるトレーニング データと一致する適切なロケールを選択します。 たとえば、持っている録音データが英国アクセントの英語で話されている場合は、`en-GB` を選択します。

> [!NOTE]
> Custom Voice では、中国語と英語のバイリンガルを除き、バイリンガル モデル トレーニングはサポートされていません。 英語も話すことができる中国人の音声をトレーニングする場合は、[Chinese-English bilingual]\(中国語 - 英語のバイリンガル\) を選択します。 標準方法を使用した中国語 - 英語バイリンガル モデルのトレーニングは、北ヨーロッパと米国中北部のみで使用できます。 カスタム ニューラル音声のトレーニングは、英国南部と米国東部で使用できます。

## <a name="speech-translation"></a>音声翻訳

**Speech Translation** API では、音声を音声に翻訳し、音声をテキストに翻訳するために、さまざまな言語をサポートしています。 ソース言語は、音声テキスト変換言語の表に常に含まれている必要があります。 使用可能なターゲット言語は、何に翻訳するか (音声かテキストか) によって決まります。 受信した音声は [60 言語](https://www.microsoft.com/translator/business/languages/)以上に翻訳できます。 一部の言語は[音声合成](language-support.md#text-languages)に利用できます。

### <a name="text-languages"></a>テキスト言語

| テキスト言語           | 言語コード |
|:------------------------|:-------------:|
| アフリカーンス語               | `af`          |
| アラビア語                  | `ar`          |
| ベンガル語                  | `bn`          |
| ボスニア語 (ラテン)         | `bs`          |
| ブルガリア語               | `bg`          |
| 広東語 (繁体字) | `yue`         |
| カタロニア語                 | `ca`          |
| 簡体中国語      | `zh-Hans`     |
| 中国語 (繁体字)     | `zh-Hant`     |
| クロアチア語                | `hr`          |
| チェコ語                   | `cs`          |
| デンマーク語                  | `da`          |
| オランダ語                   | `nl`          |
| 英語                 | `en`          |
| エストニア語                | `et`          |
| フィジー語                  | `fj`          |
| フィリピン語                | `fil`         |
| フィンランド語                 | `fi`          |
| フランス語                  | `fr`          |
| ドイツ語                  | `de`          |
| ギリシャ語                   | `el`          |
| グジャラート語                | `gu`          |
| ハイチ・クレオール語          | `ht`          |
| ヘブライ語                  | `he`          |
| ヒンディー語                   | `hi`          |
| ミャオ語               | `mww`         |
| ハンガリー語               | `hu`          |
| インドネシア語              | `id`          |
| アイルランド語                   | `ga`          |
| イタリア語                 | `it`          |
| 日本語                | `ja`          |
| カンナダ語                 | `kn`          |
| スワヒリ語               | `sw`          |
| クリンゴン語                 | `tlh-Latn`    |
| クリンゴン語 (plqaD)         | `tlh-Piqd`    |
| 韓国語                  | `ko`          |
| ラトビア語                 | `lv`          |
| リトアニア語              | `lt`          |
| マダガスカル語                | `mg`          |
| マレー語                   | `ms`          |
| マラヤーラム語               | `ml`          |
| マルタ語                 | `mt`          |
| マオリ語                   | `mi`          |
| マラーティー語                 | `mr`          |
| ノルウェー語               | `nb`          |
| ペルシャ語                 | `fa`          |
| ポーランド語                  | `pl`          |
| ポルトガル語 (ブラジル)     | `pt-br`       |
| ポルトガル語 (ポルトガル)   | `pt-pt`       |
| パンジャーブ語                 | `pa`          |
| オトミ語         | `otq`         |
| ルーマニア語                | `ro`          |
| ロシア語                 | `ru`          |
| サモア語                  | `sm`          |
| セルビア語 (キリル)      | `sr-Cyrl`     |
| セルビア語 (ラテン)         | `sr-Latn`     |
| スロバキア語                  | `sk`          |
| スロベニア語               | `sl`          |
| スペイン語                 | `es`          |
| スウェーデン語                 | `sv`          |
| タヒチ語                | `ty`          |
| タミル語                   | `ta`          |
| テルグ語                  | `te`          |
| タイ語                    | `th`          |
| トンガ語                  | `to`          |
| トルコ語                 | `tr`          |
| ウクライナ語               | `uk`          |
| ウルドゥ語                    | `ur`          |
| ベトナム語              | `vi`          |
| ウェールズ語                   | `cy`          |
| ユカテコ語            | `yua`         |

## <a name="speaker-recognition"></a>Speaker Recognition

さまざまな Speaker Recognition API でサポートされる言語については、次の表を参照してください。 Speaker Recognition の詳細については、[概要](speaker-recognition-overview.md)に関するページを参照してください。

| Language | ロケール (BCP-47) | テキスト依存の認証 | テキストに依存しない認証 | テキストに依存しない識別 |
|----|----|----|----|----|
|英語 (米国)  |  ja-JP  |  はい  |  はい  |  はい |
|中国語 (標準、簡体字) | zh-CN     |     該当なし |     はい |     はい|
|英語 (オーストラリア)     | en-AU     | 該当なし     | はい     | はい|
|英語 (カナダ)     | en-CA     | 該当なし |     はい |     はい|
|英語 (英国)     | en-GB     | 該当なし     | はい     | はい|
|フランス語 (カナダ)     | fr-CA     | 該当なし     | はい |     はい|
|フランス語 (フランス)     | fr-FR     | 該当なし     | はい     | はい|
|ドイツ語 (ドイツ)     | de-DE     | 該当なし     | はい     | はい|
|イタリア語 | it-IT     |     該当なし     | はい |     はい|
|日本語     | ja-JP | 該当なし     | はい     | はい|
|ポルトガル語 (ブラジル) | pt-BR |     該当なし |     はい |     はい|
|スペイン語 (メキシコ)     | es-MX     | 該当なし |     はい |     はい|
|スペイン語 (スペイン)     | es-ES | 該当なし     | はい |     はい|

## <a name="next-steps"></a>次のステップ

* [無料の Azure アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください
* [C# で音声を認識する方法を確認する](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
