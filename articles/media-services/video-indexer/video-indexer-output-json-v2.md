---
title: v2 API によって生成される Video Indexer の出力を調べる - Azure
titleSuffix: Azure Media Services
description: このトピックでは、v2 API によって生成される Azure Media Services Video Indexer の出力を調べます。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 2ac7c3c2149ce43c860c7726381733ef377de8d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530741"
---
# <a name="examine-the-video-indexer-output"></a>Video Indexer の出力を調べる

ビデオにインデックスを付けると、Video Indexer によって、指定されたビデオの分析情報の詳細を含む JSON コンテンツが生成されます。 分析情報には、トランスクリプト、OCR、顔、トピック、ブロックなどが含まれます。各種の分析情報には、その分析情報がビデオにいつ現れたかを示す時間範囲のインスタンスが含まれます。 

[Video Indexer](https://www.videoindexer.ai/) の Web サイト上にあるビデオの **[再生]** ボタンを押して、ビデオの要約された分析情報を視覚的に確認できます。 

また、**Get Video Index** API を呼び出して API を使用し、応答の状態が OK になると、応答コンテンツとして詳細な JSON 出力が表示されます。

![洞察](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

この記事では、Video Indexer の出力 (JSON コンテンツ) を調べます。 <br/>使用できる機能と分析情報については、[Video Indexer の分析情報](video-indexer-overview.md#video-insights)に関する記事を参照してください。

> [!NOTE]
> Video Indexer のすべてのアクセス トークンの有効期限は 1 時間です。

## <a name="get-the-insights"></a>分析情報を得る

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Web サイトまたはポータルで生成された分析情報または出力

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
1. 調べる出力のビデオを検索します。
1. **[再生]** を押します。
1. **[分析情報]** タブ (分析情報の概要) または **[タイムライン]** タブ (関連する分析情報をフィルター処理できます) を選択します。
1. 成果物とその内容をダウンロードします。

詳しくは、「[View and edit video insights](video-indexer-view-edit.md)」(ビデオの分析情報の表示と編集) をご覧ください。

## <a name="insightsoutput-produced-by-api"></a>API によって生成される分析情報または出力

1. JSON ファイルを取得するには、[Get Video Index API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?) を呼び出します
1. 特定の成果物にも興味がある場合は、[Get Video Artifact Download URL API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?) を呼び出します

    API 呼び出しで、要求される成果物の種類 (OCR、顔、キー フレームなど) を指定します。

## <a name="root-elements-of-the-insights"></a>分析情報のルート要素

|名前|説明|
|---|---|
|accountId|プレイリストの VI アカウント ID|
|id|プレイリストの ID|
|name|プレイリストの名前|
|description|プレイリストの説明|
|userName|プレイリストを作成したユーザーの名前|
|created|プレイリストの作成時刻|
|privacyMode|プレイリストのプライバシー モード (秘密/公開)|
|state|プレイリストの状態 (アップロード完了、処理中、失敗、検疫済み)|
|isOwned|プレイリストが現在のユーザーによって作成されたかどうかを示します。|
|isEditable|現在のユーザーにプレイリストを編集する権限があるかどうかを示します。|
|isBase|プレイリストがベースのプレイリスト (ビデオ) か、他のビデオによって構成されたプレイリスト (派生) かどうかを示します。|
|durationInSeconds|プレイリストの合計期間|
|summarizedInsights|1 つの [summarizedInsights](#summarizedinsights) が含まれます。
|videos|プレイリストを構築する[ビデオ](#videos)の一覧<br/>プレイリストが他のビデオの時間範囲で構成されている場合 (派生)、この一覧のビデオには、含まれる時間範囲からのデータのみが含まれます。|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

このセクションには、分析情報の概要が表示されます。

|属性 | 説明|
|---|---|
|name|ビデオの名前 Azure Monitor など|
|id|ビデオの ID 63c6d532ff など|
|privacyMode|内訳に含めることができるモードは、**秘密**、**公開** のいずれかです。 **公開** - アカウント内のすべてのユーザーと、ビデオへのリンクを持っているユーザーがビデオを見ることができます。 **秘密** - ビデオは、アカウント内のすべてのユーザーに表示されます。|
|duration|分析情報が発生した時刻を示す 1 つの期間が含まれます。 期間は秒単位です。|
|thumbnailVideoId|サムネイルの取得元のビデオの ID。
|thumbnailId|ビデオのサムネイル ID。 実際のサムネイルを取得するには、[Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) を呼び出し、thumbnailVideoId と thumbnailId に渡します。|
|faces/animatedCharacters|0 以上の顔を含めることができます。 詳細については、「[faces/animatedCharacters](#facesanimatedcharacters)」を参照してください。|
|keywords|0 個以上のキーワードを含めることができます。 詳しくは、「[キーワード](#keywords)」をご覧ください。|
|sentiments|0 個以上のセンチメントを含めることができます。 詳しくは、「[センチメント](#sentiments)」をご覧ください。|
|audioEffects| 0 個以上の audioEffects を含めることができます。 詳しくは、「[audioEffects](#audioeffects-public-preview)」をご覧ください。|
|labels| 0 以上のラベルを含めることができます。 詳細については、「[ラベル](#labels)」をご覧ください。|
|brands| 0 以上のブランドを含めることができます。 詳しくは、「[ブランド](#brands)」をご覧ください。|
|statistics | 詳しくは、「[統計](#statistics)」をご覧ください。|
|emotions| 0 個以上の emotions が含まれている場合があります。 詳しくは、「[emotions](#emotions)」をご覧ください。|
|topics|0 個以上の topics が含まれている場合があります。 [topics](#topics) 分析情報。|

## <a name="videos"></a>videos

|名前|説明|
|---|---|
|accountId|ビデオの VI アカウント ID|
|id|ビデオの ID|
|name|ビデオの名前
|state|ビデオの状態 (アップロード完了、処理中、処理済み、失敗、検疫済み)|
|processingProgress|処理中の進行状況 (20% など)|
|failureCode|処理に失敗した場合の失敗コード ('UnsupportedFileType' など)|
|failureMessage|処理に失敗した場合のエラー メッセージ|
|externalId|ビデオの外部 ID (ユーザーが指定した場合)|
|externalUrl|ビデオの外部 URL (ユーザーが指定した場合)|
|metadata|ビデオの外部メタデータ (ユーザーが指定した場合)|
|isAdult|ビデオが手動でレビューされ、成人向けビデオとして識別されたかどうかを示します。|
|insights|分析情報オブジェクト 詳細については、「[分析情報](#insights)」を参照してください。|
|thumbnailId|ビデオのサムネイル ID。 実際のサムネイルを取得するには、[Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) を呼び出し、ビデオ ID と thumbnailId に渡します。|
|publishedUrl|ビデオをストリーミングする URL|
|publishedUrlProxy|ビデオのストリーミング元の URL (Apple デバイス向け)|
|viewToken|ビデオ ストリーミング用の短命表示トークン|
|sourceLanguage|ビデオのソース言語|
|language|ビデオの実際の言語 (翻訳)|
|indexingPreset|ビデオのインデックス付けに使用するプリセット|
|streamingPreset|ビデオの発行に使用するプリセット|
|linguisticModelId|ビデオの書き起こしに使用する CRIS モデル|
|statistics | 詳細については、「[統計](#statistics)」を参照してください。|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

各分析情報 (たとえば、トランスクリプト行、顔、ブランドなど) には、一意の要素 (たとえば、face1、face2、face3 など) の一覧が含まれ、各要素には、独自のメタデータと、そのインスタンス (追加のオプション メタデータがある時間範囲) の一覧が含まれます。

顔は ID、名前、サムネイル、その他のメタデータ、およびそのテンポラル インスタンスの一覧を持ちます (例:00:00:05 - 00:00:10、00:01:00 - 00:02:30、00:41:21 - 00:41:49)。各テンポラル インスタンスは、追加のメタデータを持つことができます。 顔の四角形の座標などです (20,230,60,60)。

|Version|コードのバージョン|
|---|---|
|sourceLanguage|ビデオのソース言語 (マスター言語は 1 つと仮定)。 [BCP 47](https://tools.ietf.org/html/bcp47) 文字列の形式です。|
|language|(ソース言語から翻訳された) 分析情報言語です。 [BCP 47](https://tools.ietf.org/html/bcp47) 文字列の形式です。|
|transcript|[transcript](#transcript) 分析情報。|
|ocr|[OCR](#ocr) 分析情報。|
|keywords|[keywords](#keywords) 分析情報。|
|blocks|1 つ以上の[ブロック](#blocks)を含めることができます。|
|faces/animatedCharacters|[faces/animatedCharacters](#facesanimatedcharacters) 分析情報。|
|labels|[labels](#labels) 分析情報。|
|shots|[shots](#shots) 分析情報。|
|brands|[brands](#brands) 分析情報。|
|audioEffects|[audioEffects](#audioeffects-public-preview) 分析情報。|
|sentiments|[sentiments](#sentiments) 分析情報。|
|visualContentModeration|[visualContentModeration](#visualcontentmoderation) 分析情報。|
|textualContentModeration|[textualContentModeration](#textualcontentmoderation) 分析情報。|
|emotions| [emotions](#emotions) 分析情報。|
|topics|[topics](#topics) 分析情報。|
|speakers|[speakers](#speakers) 分析情報。|

例:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

属性 | 説明
---|---
id|ブロックの ID|
instances|このブロックの時間範囲の一覧|

#### <a name="transcript"></a>transcript

|名前|説明|
|---|---|
|id|行 ID。|
|text|トランスクリプトそのもの。|
|confidence|トランスクリプトの精度の信頼性。|
|speakerId|話者の ID。|
|language|トランスクリプトの言語。 各行の言語が異なる可能性があるトランスクリプトをサポートすることを目的としています。|
|instances|この行が出現する時間範囲の一覧。 インスタンスがトランスクリプトの場合、instance は 1 つだけあります。|

例:

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
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>ocr

|名前|説明|
|---|---|
|id|OCR 行 ID。|
|text|OCR テキスト。|
|confidence|認識の信頼度。|
|language|OCR 言語。|
|instances|この OCR が出現する時間範囲の一覧 (同じ OCR が複数回出現する可能性があります)。|
|height|OCR 四角形の高さ|
|top|px での上部の位置|
|left| px での左側の位置|
|width|OCR 四角形の幅|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>keywords

|名前|説明|
|---|---|
|id|キーワード ID。|
|text|キーワードのテキスト。|
|confidence|キーワード認識の信頼度。|
|language|キーワードの言語 (翻訳時)。|
|instances|このキーワードが出現する時間範囲の一覧 (1 つのキーワードが複数回出現する可能性があります)。|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>faces/animatedCharacters

アニメーション化されたキャラクターのモデルでビデオにインデックスが付けられた場合、`animatedCharacters` 要素が `faces` に置き換わります。 これは Custom Vision のカスタム モデルを使用して行われ、Video Indexer ではキーフレーム上で実行されます。

アニメーション化されたキャラクターではなく顔がある場合、Video Indexer では、ビデオのすべてのフレームで Face API が使用され、顔と有名人が検出されます。

|名前|説明|
|---|---|
|id|顔 ID。|
|name|顔の名前。 「Unknown #0」、識別された著名人、または顧客のトレーニング担当者になることができます。|
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

#### <a name="labels"></a>labels

|名前|説明|
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

#### <a name="scenes"></a>scenes

|名前|説明|
|---|---|
|id|シーン ID。|
|instances|このシーンの時間範囲の一覧 (1 つのシーンに 1 つだけのインスタンスがあります)。|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>shots

|名前|説明|
|---|---|
|id|スナップショット ID。|
|keyFrames|ショット内の keyFrame の一覧 (それぞれに ID とインスタンスの時間範囲の一覧があります)。 各 keyFrame インスタンスには、keyFrame のサムネイル ID を保持する thumbnailId フィールドがあります。|
|instances|このショットの時間範囲の一覧 (1 つのショットに 1 つだけのインスタンスがあります)。|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>brands

音声からテキスト トランスクリプトまたはビデオ OCR への変換で検出されたビジネスおよび製品ブランド名 これには、ブランドまたはロゴ検出の画像認識は含まれません。

|名前|説明|
|---|---|
|id|ブランド ID|
|name|ブランド名|
|referenceId | ブランド Wikipedia の URL のサフィックス たとえば、"Target_Corporation" は [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) のサフィックスです。
|referenceUrl | ブランドの Wikipedia の URL (ある場合) たとえば、[https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) です。
|description|ブランドの説明|
|tags|このブランドに関連付けられていた定義済みタグの一覧|
|confidence|Video Indexer ブランド検出機能の信頼度の値 (0-1)|
|instances|このブランドの時間範囲の一覧。 各インスタンスは、このブランドがトランスクリプトまたは OCR に表示されたかどうかを示す brandType を持ちます。|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistics

|名前|説明|
|---|---|
|CorrespondenceCount|ビデオ内の通知の数|
|SpeakerWordCount|話者あたり単語の数|
|SpeakerNumberOfFragments|ビデオでの話者のフラグメントの数|
|SpeakerLongestMonolog|話者の最も長いモノローグ。 モノローグでの話者の沈黙がある場合、それも含まれます。 モノローグの先頭と末尾の無音は削除されます。| 
|SpeakerTalkToListenRatio|計算は、ビデオの合計時間で割られた話者のモノローグに費やされた時間に基づきます (間の無音は含みません)。 時間は、小数点第 3 位に丸められます。|

#### <a name="audioeffects-public-preview"></a>audioEffects (パブリック プレビュー)

|名前|説明
|---|---|
|id|オーディオ エフェクト ID|
|type|オーディオ エフェクトの種類|
|instances|このオーディオ エフェクトが出現する時間範囲の一覧。 各インスタンスに confidence フィールドがあります。|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Siren",
    "instances": [
    {
       "confidence": 0.87,
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
       "confidence": 0.87,
       "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>sentiments

センチメントは、sentimentType フィールド (肯定/中立/否定) によって集計されます。 例: 0-0.1、0.1-0.2。

|名前|説明|
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

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration ブロックには、Video Indexer で成人向けコンテンツが含まれる可能性があると判断された時間範囲が含まれます。 visualContentModeration が空の場合、特定された成人向けコンテンツはありません。

成人向けまたはわいせつなコンテンツを含むことが検出されたビデオでは、秘密ビューしか利用できない場合があります。 ユーザーは、コンテンツの人間によるレビューの要求を送信できます。この場合、IsAdult 属性に、人間によるレビューの結果が含まれます。

|名前|説明|
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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|名前|説明|
|---|---|
|id|テキスト コンテンツ モデレーションの ID|
|bannedWordsCount |禁止された単語の数|
|bannedWordsRatio |単語の合計数の比率|

#### <a name="emotions"></a>emotions

Video Indexer では、音声とオーディオの手掛かりに基づいて感情を識別します。識別される感情は、喜び、悲しみ、怒り、または恐怖の可能性があります。

|名前|説明|
|---|---|
|id|感情の ID。|
|type|音声とオーディオの手掛かりに基づいて識別された感情の瞬間。この感情は、喜び、悲しみ、怒り、または恐怖の可能性があります。|
|instances|この感情が出現した時間範囲の一覧。|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>topics

Video Indexer では、トランスクリプトから主なトピックを推論します。 可能な場合は、第 2 レベルの [IPTC](https://iptc.org/standards/media-topics/) 分類が含まれています。 

|名前|説明|
|---|---|
|id|トピックの ID。|
|name|トピック名 (例:"Pharmaceuticals")。|
|referenceId|トピックの階層を反映している階層リンク。 次に例を示します。"健康と福祉/医療と健康管理/医薬品"。|
|confidence|範囲が [0,1] の信頼度スコア。 高いほど信頼度が高くなります。|
|language|トピックで使用されている言語。|
|iptcName|IPTC メディア コード名 (検出された場合)。|
|instances |現在、Video Indexer は時間間隔に対してトピックにインデックスを付けないため、ビデオ全体が間隔として使用されます。|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

#### <a name="speakers"></a>speakers

|名前|説明|
|---|---|
|id|話者の ID。|
|name|"Speaker # *<number>* " の形式の話者の名前。例えば、"Speaker #1" です。|
|instances |この話者が出現した時間範囲の一覧。|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>次のステップ

[Video Indexer 開発者ポータル](https://api-portal.videoindexer.ai)

アプリケーションにウィジェットを埋め込む方法については、「[Embed Video Indexer widgets into your applications](video-indexer-embed-widgets.md)」(アプリケーションに Video Indexer ウィジェットを埋め込む) を参照してください。 

