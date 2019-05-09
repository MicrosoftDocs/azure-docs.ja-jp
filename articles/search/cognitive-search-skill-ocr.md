---
title: OCR コグニティブ検索スキル - Azure Search
description: Azure Search のエンリッチメント パイプラインで光学式文字認識 (OCR) を使用して、イメージ ファイルからテキストを抽出します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fb7fe0689ce54031880565c0c6409afeab2ff523
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777893"
---
# <a name="ocr-cognitive-skill"></a>OCR 認知スキル

光学式文字認識 (OCR) スキルは、画像ファイルに印字された手書きテキストを認識します。 このスキルでは、[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) Cognitive Services によって提供される機械学習モデルが使用されます。 **OCR** スキルは、次の機能にマップします。

+ textExtractionAlgorithm が "handwritten" に設定されている場合は、["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) 機能が使用されます。
+ textExtractionAlgorithm が "printed" に設定されている場合、英語以外の言語には ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) 機能が使用されます。 英語については、印刷されたテキスト用の新しい ["Recognize Text"](../cognitive-services/computer-vision/concept-recognizing-text.md) 機能が使用されます。

**OCR** スキルはイメージファイルからテキストを抽出します｡ サポートされているファイル形式は以下の通りです｡

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> 2018 年 12 月 21 日より、Azure Search のスキルセットに [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)できるようになっています。 これにより、スキルセットの実行への課金を開始できます。 この日付には、ドキュメント クラッキング ステージの一部として画像抽出への課金も開始します。 ドキュメントからのテキスト抽出は、引き続き追加コストなしで提供されます。
>
> [組み込みコグニティブ スキル](cognitive-search-predefined-skills.md)の実行は、[Cognitive Services の従量制価格](https://azure.microsoft.com/pricing/details/cognitive-services)で課金されます。これは、タスクを直接実行した場合と同じ料金です。 画像の抽出は Azure Search の課金対象であり、現在はプレビュー価格で提供されています。 詳細については、「[Azure Search の価格](https://go.microsoft.com/fwlink/?linkid=2042400)」のページ、または「[請求体系について](search-sku-tier.md#how-billing-works)」を参照してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| detectOrientation | イメージの向きの自動検出を有効にします。 <br/> 有効な値: true / false｡|
|defaultLanguageCode | <p>  入力テキストの言語コード。 以下の言語がサポートされています。 <br/> zh-Hans (簡体字中国語) <br/> zh-Hant (繁体字中国語) <br/>cs (チェコ語) <br/>da (デンマーク語) <br/>nl (オランダ語) <br/>en (英語) <br/>fi (フィンランド語)  <br/>fr (フランス語) <br/>  de (ドイツ語) <br/>el (ギリシア語) <br/> hu (ハンガリー語) <br/> it (イタリア語) <br/>  ja (日本語) <br/> ko (韓国語) <br/> nb (ノルウェー語) <br/>   pl (ポーランド語) <br/> pt (ポルトガル語) <br/>  ru (ロシア語) <br/>  es (スペイン語) <br/>  sv (スウェーデン語) <br/>  tr (トルコ語) <br/> ar (アラビア語) <br/> ro (ルーマニア語) <br/> sr-Cyrl (セルビア語キリル文字) <br/> sr-Latn (セルビア語ローマ字) <br/>  sk (スロバキア語) <br/>  unk (不明) <br/><br/> 言語コードが指定されないか null の場合､言語は英語に設定されます。 言語が明示的に "unk" に設定されている場合、言語は自動検出されます。 </p> |
| textExtractionAlgorithm | "printed" または "handwritten"｡ "handwritten" テキスト認識の OCR アルゴリズムは現在プレビュー段階であり､英語でのみサポートされます｡ |

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                                          |
|---------------|------------------------------------------------------|
| image         | 複合型｡ 現在は "/document/normalized_images" フィールドでのみ機能し､ ```imageAction``` が ```none``` 以外の値に設定されている場合に､Azure BLOB インデクサーによって生成されます。 詳しくは､[サンプル](#sample-output) をご覧ください｡|


## <a name="skill-outputs"></a>スキルの出力
| 出力名     | 説明                   |
|---------------|-------------------------------|
| text          | イメージから抽出されたプレーン テキスト｡   |
| layoutText    | 抽出されたテキストと､そのテキストが検出された場所を記述した複合型。|


## <a name="sample-definition"></a>定義例

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>テキストと layoutText 出力例

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>サンプル:埋め込まれたイメージから抽出されたテキストとドキュメントの内容をマージ

Text Merger の一般的なユース ケースとしては､ドキュメントの content  フィールドへのイメージのテキスト表現 (OCR スキルからのテキストかイメージのキャプション) のマージがあります｡

次に示すのは、*merged_text* フィールドを作成するスキルセットの例です。 このフィールドには、ドキュメントのテキスト コンテンツが格納されるほか、そのドキュメントに埋め込まれている各画像から OCR で読み込まれたテキストが格納されます。

#### <a name="request-body-syntax"></a>要求本文の構文
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
上記のスキルセット例は､normalized-images フィールドが存在していることを前提としています｡ このフィールドを生成するには､以下に示すように､インデクサー定義内の *imageAction* 構成を *generateNormalizedImages* に設定します｡

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>関連項目
+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [TextMerger スキル](cognitive-search-skill-textmerger.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [インデクサーの作成 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
