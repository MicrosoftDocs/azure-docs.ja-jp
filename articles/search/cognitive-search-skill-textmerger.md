---
title: テキスト マージ コグニティブ スキル
titleSuffix: Azure Cognitive Search
description: フィールドのコレクションからテキストを 1 つの統合されたフィールドにマージします。 Azure Cognitive Search の AI エンリッチメント パイプラインでこのコグニティブ スキルを使用します。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: b364655f26c6ac29c14d387d69d7b4277d6aeb86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88924641"
---
#   <a name="text-merge-cognitive-skill"></a>テキスト マージ コグニティブ スキル

**テキスト マージ** スキルは、フィールドのコレクションからのテキストを 1 つのフィールドに統合します。 

> [!NOTE]
> このスキルは Cognitive Services API にバインドされていないため、この使用に対しては課金されません。 ただし、1 日あたりの毎日のエンリッチメントの数を少数に制限する **無料** リソースのオプションをオーバーライドするには、引き続き [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)する必要があります。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `insertPreTag`    | 各挿入前に含まれる文字列。 既定値は `" "` です。 スペースを省略するには、値を `""` に設定します。  |
| `insertPostTag`   | 各挿入後に含まれる文字列。 既定値は `" "` です。 スペースを省略するには、値を `""` に設定します。  |


##  <a name="sample-input"></a>サンプル入力
このスキルに使用可能な入力を提供する JSON ドキュメントは、次のようになります。

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>サンプル出力
この例は、*insertPreTag* が `" "` に設定され、*insertPostTag* が `""` に設定されているという想定で、以前の入力の出力を示しています。 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>拡張サンプル スキルの定義

テキスト マージ使用の一般的なシナリオは、イメージのテキスト表現 (OCR スキルからのテキストかイメージのキャプション) をドキュメントの content フィールドにマージすることです。 

次のサンプル スキルセットでは、OCR スキルを使用して、ドキュメントに埋め込まれた画像からテキストを抽出します。 次に、*merged_text* フィールドを作成して、元のテキストと各イメージから OCR されたテキストの両方を格納します。 OCR スキルについて詳しくは、[こちら](./cognitive-search-skill-ocr.md)をご覧ください。

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
上記のサンプル スキルセットは、normalized-images フィールドが存在していることを前提としています。 normalized-images フィールドを取得するには、以下に示すように、インデクサー定義内の *imageAction* 構成を *generateNormalizedImages* に設定します。

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [インデクサーの作成 (REST)](/rest/api/searchservice/create-indexer)