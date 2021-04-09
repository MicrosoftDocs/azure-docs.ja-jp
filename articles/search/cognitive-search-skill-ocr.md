---
title: OCR 認知スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のエンリッチメント パイプラインで光学式文字認識 (OCR) を使用して、イメージ ファイルからテキストを抽出します。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 8b6a7c3e05b26cbda80ebf1a3fc0d4fed8255e6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950807"
---
# <a name="ocr-cognitive-skill"></a>OCR 認知スキル

**光学式文字認識 (OCR)** スキルでは、画像ファイルに印字された手書きテキストが認識されます。 このスキルでは、Cognitive Services の [Computer Vision](../cognitive-services/computer-vision/overview.md) API [v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) によって提供される機械学習モデルが使用されます。 **OCR** スキルは、次の機能にマップします。

+ 英語、スペイン語、ドイツ語、フランス語、イタリア語、ポルトガル語、およびオランダ語については、新しい["Read"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) API が使用されます。
+ その他のすべての言語については、["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-api) API が使用されます。

**OCR** スキルはイメージファイルからテキストを抽出します｡ サポートされているファイル形式は以下の通りです｡

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Cognitive Search のドキュメント解析段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Cognitive Search の価格](https://azure.microsoft.com/pricing/details/search/)に関するページで説明されています。


## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `detectOrientation`   | イメージの向きの自動検出を有効にします。 <br/> 有効な値: true / false｡|
| `defaultLanguageCode` | <p>   入力テキストの言語コード。 以下の言語がサポートされています。 <br/> zh-Hans (簡体中国語) <br/> zh-Hant (繁体字中国語) <br/>cs (チェコ語) <br/>da (デンマーク語) <br/>nl (オランダ語) <br/>en (英語) <br/>fi (フィンランド語)  <br/>fr (フランス語) <br/>  de (ドイツ語) <br/>el (ギリシア語) <br/> hu (ハンガリー語) <br/> it (イタリア語) <br/>  ja (日本語) <br/> ko (韓国語) <br/> nb (ノルウェー語) <br/>   pl (ポーランド語) <br/> pt (ポルトガル語) <br/>  ru (ロシア語) <br/>  es (スペイン語) <br/>  sv (スウェーデン語) <br/>  tr (トルコ語) <br/> ar (アラビア語) <br/> ro (ルーマニア語) <br/> sr-Cyrl (セルビア語キリル文字) <br/> sr-Latn (セルビア語ローマ字) <br/>  sk (スロバキア語) <br/>  unk (不明) <br/><br/> 言語コードが指定されないか null の場合､言語は英語に設定されます。 言語が明示的に "unk" に設定されている場合、言語は自動検出されます。 </p> |
| `lineEnding` | 検出された各行の間で使用される値。 指定できる値"Space"、"CarriageReturn"、"LineFeed"。  既定は "Space" です。 |

以前は、スキルで "印刷" テキストまたは "手書き" テキストを抽出するかどうかを指定する "textExtractionAlgorithm" というパラメーターがありました。  最新の Read API アルゴリズムはどちらのタイプのテキストも一度に抽出できるようになっているため、このパラメーターは非推奨となり、不要になりました。  スキル定義に既にこのパラメーターが含まれている場合、削除する必要はありませんが、今後は不使用となります。今後は、その設定に関係なく、両方のタイプのテキストが抽出されます。

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                                          |
|---------------|------------------------------------------------------|
| `image`         | 複合型｡ 現在は "/document/normalized_images" フィールドでのみ機能し､ ```imageAction``` が ```none``` 以外の値に設定されている場合に､Azure BLOB インデクサーによって生成されます。 詳しくは､[サンプル](#sample-output) をご覧ください｡|


## <a name="skill-outputs"></a>スキルの出力
| 出力名     | 説明                   |
|---------------|-------------------------------|
| `text`            | イメージから抽出されたプレーン テキスト｡   |
| `layoutText`    | 抽出されたテキストと､そのテキストが検出された場所を記述した複合型。|


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
          "name":"text",
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
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
+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [TextMerger スキル](cognitive-search-skill-textmerger.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [インデクサーの作成 (REST)](/rest/api/searchservice/create-indexer)