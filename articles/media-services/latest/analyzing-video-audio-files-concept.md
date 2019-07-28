---
title: Azure Media Services を使用したビデオおよびオーディオ ファイルの分析 | Microsoft Docs
description: Azure Media Services を使用すると、AudioAnalyzerPreset と VideoAnalyzerPreset を使用して、音声と画像のコンテンツを分析できます。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 9154e5d58a36bde1827d63d11d57a77b4289a781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64689365"
---
# <a name="analyzing-video-and-audio-files"></a>ビデオおよびオーディオ ファイルの分析

Azure Media Services v3 では、Video Indexer と AMS v3 アナライザー プリセットを使用して、オーディオおよびビデオ ファイルから分析情報を抽出することもできます (この記事で説明します)。 より詳細な分析情報が必要な場合は、Video Indexer を直接使用します。 どのような場合に Video Indexer やMedia Services アナライザー プリセットを使用するかについて詳しくは、[比較のドキュメント](../video-indexer/compare-video-indexer-with-media-services-presets.md)をご覧ください。

Media Services v3 プリセットを使用してコンテンツを分析するには、**Transform** を作成し、次のいずれかのプリセットを使用する **Job** を送信します ([VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) または **AudioAnalyzerPreset**)。 **VideoAnalyzerPreset** を使用する方法については、[Azure Media Services を使用してビデオを分析する方法に関するチュートリアル](analyze-videos-tutorial-with-api.md)を参照してください。

> [!NOTE]
> ビデオ アナライザーまたはオーディオ アナライザーのプリセットを使用する場合は、Azure portal を使用して、10 個の S3 メディア占有ユニットを備えるようアカウントを設定します。 詳細については、[メディア処理のスケーリング](media-reserved-units-cli-how-to.md)に関するページを参照してください。

## <a name="built-in-presets"></a>組み込みのプリセット

現在、Media Services は次の組み込みのアナライザー プリセットをサポートしています。  

|**プリセット名**|**シナリオ**|**詳細**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|オーディオの分析|このプリセットは、音声の文字起こしなど、事前定義された一連の AI ベースの分析操作を適用します。 現在、プリセットは、1 つの言語での音声を含む単一オーディオ トラックを使用したコンテンツ処理をサポートしています。 BCP-47 形式の "language tag-region" を使用して、入力のオーディオ ペイロードの言語を指定できます。 サポートされる言語は、英語 ("en-US" および "en-GB")、スペイン語 ("es-ES" および "es-MX")、フランス語 ("fr-FR")、イタリア語 ("it-IT")、日本語 ("ja-JP")、ポルトガル語 ("pt-BR")、中国語 ("zh-CN")、ドイツ語 ("de-DE")、アラビア語 ("ar-EG")、ロシア語 ("ru-RU")、ヒンディー語 ("hi-IN")、韓国語 ("ko-KR") です。<br/><br/> 言語が指定されていないか、null 値に設定されている場合、自動言語検出は、最初に検出された言語を選択し、ファイルの実行中、選択された言語で処理を行います。 自動言語検出機能では、現在、英語、中国語、フランス語、ドイツ語、イタリア語、日本語、スペイン語、ロシア語、およびポルトガル語がサポートされています。 現在、最初の言語が検出された後に複数の言語を動的に切り替えることはサポートされていません。 自動言語検出機能は、はっきりと音声が認識できる録音において最も効果的に機能します。 自動言語検出で言語を認識できなかった場合、文字起こしは英語にフォールバックされます。|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|オーディオとビデオの分析|オーディオとビデオの両方から分析情報 (リッチ メタデータ) を抽出し、JSON 形式のファイルを出力します。 ビデオ ファイルを処理するときにオーディオの分析情報のみを抽出するかどうかを指定できます。 詳細については、[ビデオの分析](analyze-videos-tutorial-with-api.md)に関するページを参照してください。|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|ビデオに存在するすべての顔を検出|存在するすべての顔を検出するために、ビデオを分析する際に使用する設定について説明します。|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

このプリセットを使用すると、音声または画像ファイルから複数の音声分析情報を抽出できます。 出力には、JSON ファイル (すべての分析情報が含まれます) と、音声トランスクリプト用の VTT ファイルが含まれます。 このプリセットには、入力ファイルの言語を [BCP47](https://tools.ietf.org/html/bcp47) 文字列形式で指定するプロパティを指定できます。 音声分析情報には、以下が含まれます。

* 音声の文字起こし – タイムスタンプ付きの発話のトランスクリプト。 複数の言語がサポートされます。
* 話者インデックス – 話者と対応する音声のマッピング。
* 音声のセンチメント分析 – 音声の文字起こしに対して実行されたセンチメント分析の出力。
* キーワード – 音声の文字起こしから抽出されたキーワード。

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

このプリセットを使用して、ビデオ ファイルから複数の音声と画像の分析情報を抽出できます。 出力には、JSON ファイル (すべての分析情報が含まれます)、画像トランスクリプト用の VTT ファイル、およびサムネールのコレクションが含まれます。 このプリセットには、プロパティとして [BCP47](https://tools.ietf.org/html/bcp47) 文字列 (画像の言語を表します) を指定することもできます。 画像分析情報には、前述のすべての音声分析情報と、次の項目が含まれます。

* 顔追跡 – ビデオに顔が登場している時間。 それぞれの顔には、顔 ID と、対応するサムネイルのコレクションがあります。
* 視覚テキスト – 光学式文字認識を使用して検出されたテキスト。 テキストにはタイムスタンプが付けられ、(音声トランスクリプションに加え) キーワードの抽出でも使用されます。
* キーフレーム – 画像から抽出されたキーフレームのコレクション。
* 視覚コンテンツ モデレーション – 成人向けまたは性的描写としてフラグ付けされている画像の一部。
* 注釈 – 定義済みのオブジェクト モデルに基づいてビデオに注釈を付けた結果。

##  <a name="insightsjson-elements"></a>insights.json 要素

出力には、画像または音声内で検出されたすべての分析情報を持つ JSON ファイル (insights.json) が含まれます。 json には、次の要素が含まれる可能性があります。

### <a name="transcript"></a>transcript

|Name|説明|
|---|---|
|id|行 ID。|
|text|トランスクリプトそのもの。|
|language|トランスクリプトの言語。 各行の言語が異なる可能性があるトランスクリプトをサポートすることを目的としています。|
|instances|この行が出現する時間範囲の一覧。 インスタンスがトランスクリプトの場合、instance は 1 つだけあります。|

例:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|Name|説明|
|---|---|
|id|OCR 行 ID。|
|text|OCR テキスト。|
|confidence|認識の信頼度。|
|language|OCR 言語。|
|instances|この OCR が出現する時間範囲の一覧 (同じ OCR が複数回出現する可能性があります)。|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>faces

|Name|説明|
|---|---|
|id|顔 ID。|
|name|顔の名前。 "Unknown #0"、識別された著名人、または顧客のトレーニング担当者になることができます。|
|confidence|顔認識の信頼度。|
|description|著名人の説明 |
|thumbnailId|その顔のサムネイルの ID|
|knownPersonId|既知の人物の場合は、その内部 ID|
|referenceId|Bing に登録されている著名人の場合は、その Bing ID|
|referenceType|現時点では Bing のみ。|
|title|著名人の場合は、その肩書 (例: "Microsoft の CEO")|
|imageUrl|著名人の場合は、その 画像 の URL|
|instances|特定の時間範囲の中で顔が出現したインスタンス。 各インスタンスにも、thumbnailsId があります。 |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>shots

|Name|説明|
|---|---|
|id|スナップショット ID。|
|keyFrames|ショット内のキー フレームの一覧 (各キー フレームに ID とインスタンスの時間範囲の一覧があります)。 キー フレームのインスタンスには、keyFrame のサムネイル ID 付きサムネイル フィードがあります。|
|instances|このショットの時間範囲の一覧 (ショットのインスタンスは 1 つだけあります)。|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistics

|Name|説明|
|---|---|
|CorrespondenceCount|ビデオ内の通知の数|
|WordCount|話者あたり単語の数|
|SpeakerNumberOfFragments|ビデオでの話者のフラグメントの数|
|SpeakerLongestMonolog|話者の最も長いモノローグ。 モノローグでの話者の沈黙がある場合、それも含まれます。 モノローグの先頭と末尾の無音は削除されます。| 
|SpeakerTalkToListenRatio|計算は、ビデオの合計時間で割られた話者のモノローグに費やされた時間に基づきます (間の無音は含みません)。 時間は、小数点第 3 位に丸められます。|


### <a name="sentiments"></a>sentiments

センチメントは、sentimentType フィールド (肯定/中立/否定) によって集計されます。 例: 0-0.1、0.1-0.2。

|Name|説明|
|---|---|
|id|センチメント ID。|
|averageScore |センチメントの種類 (肯定/中立/否定) が同じすべてのインスタンスのすべてのスコアの平均値。|
|instances|このセンチメントが出現する時間範囲の一覧。|
|sentimentType |種類として、'Positive'、'Neutral'、'Negative' があります。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>labels

|Name|説明|
|---|---|
|id|ラベル ID。|
|name|ラベル名 (例: "Computer"、"TV")。|
|language|ラベル名の言語 (翻訳時)。 BCP-47|
|instances|このラベルが出現する時間範囲の一覧 (1 つのラベルが複数回出現する可能性があります)。 各インスタンスに confidence フィールドがあります。 |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>keywords

|Name|説明|
|---|---|
|id|キーワード ID。|
|text|キーワードのテキスト。|
|confidence|キーワード認識の信頼度。|
|language|キーワードの言語 (翻訳時)。|
|instances|このキーワードが出現する時間範囲の一覧 (1 つのキーワードが複数回出現する可能性があります)。|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration ブロックには、Video Indexer で成人向けコンテンツが含まれる可能性があると判断された時間範囲が含まれます。 visualContentModeration が空の場合、特定された成人向けコンテンツはありません。

成人向けまたはわいせつなコンテンツを含むことが検出されたビデオでは、秘密ビューしか利用できない場合があります。 ユーザーは、コンテンツの人間によるレビューの要求を送信できます。この場合、IsAdult 属性に、人間によるレビューの結果が含まれます。

|Name|説明|
|---|---|
|id|ビジュアル コンテンツ モデレーションの ID|
|adultScore|(コンテンツ モデレーターからの) 成人スコア|
|racyScore|(コンテンツ モデレーションからの) わいせつスコア|
|instances|このビジュアル コンテンツ モデレーションが発生した時間範囲の一覧|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>次の手順

[チュートリアル: Azure Media Services でビデオを分析する](analyze-videos-tutorial-with-api.md)
