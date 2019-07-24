---
title: エンティティ認識の認知検索スキル - Azure Search
description: Azure Search 認知検索パイプラインのテキストからさまざまな種類のエンティティを抽出します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 2a245a6e3d76a7df41b5ef28f9bac8a2c2122402
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985429"
---
#    <a name="entity-recognition-cognitive-skill"></a>エンティティ認識の認知スキル

**エンティティ認識**スキルによってテキストからさまざまな種類のエンティティが抽出されます。 このスキルでは、Cognitive Services の [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) によって提供される機械学習モデルが使用されます。

> [!NOTE]
> 2018 年 12 月 21 日より、Azure Search のスキルセットに [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)できるようになっています。 これにより、スキルセットの実行への課金を開始できます。 この日付には、ドキュメント クラッキング ステージの一部として画像抽出への課金も開始します。 ドキュメントからのテキスト抽出は、引き続き追加コストなしで提供されます。
>
> [組み込みコグニティブ スキル](cognitive-search-predefined-skills.md)の実行は、[Cognitive Services の従量制価格](https://azure.microsoft.com/pricing/details/cognitive-services)で課金されます。これは、タスクを直接実行した場合と同じ料金です。 画像の抽出は Azure Search の課金対象であり、現在はプレビュー価格で提供されています。 詳細については、「[Azure Search の価格](https://go.microsoft.com/fwlink/?linkid=2042400)」のページ、または「[請求体系について](search-sku-tier.md#how-billing-works)」を参照してください。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、`String.Length` によって測定されるため、50,000 文字にする必要があります。 データをキー フレーズ エクストラクターに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターでは大文字と小文字が区別されます。パラメーターはすべて任意です。

| パラメーター名     | 説明 |
|--------------------|-------------|
| categories    | 抽出する必要があるカテゴリの配列。  可能なカテゴリの型は、`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"Datetime"`、`"URL"`、`"Email"` です。 カテゴリが指定されていない場合、すべての型が返されます。|
|defaultLanguageCode |  入力テキストの言語コード。 次の言語がサポートされます。`de, en, es, fr, it`|
|minimumPrecision | 未使用。 将来利用するために予約されています。 |
|includeTypelessEntities | true に設定されている場合、テキストに既知のエンティティが含まれるが、サポートされているカテゴリのいずれかに分類できないとき、`"entities"` 複合出力フィールドの一部として返されます。 
これらは、よく知られているものの、現在サポートされている "カテゴリ" の一部として分類されていないエンティティです。 たとえば、"Windows 10" はよく知られているエンティティ (製品) ですが、"製品" は現在サポートされているカテゴリではありません。 既定値は `false` です。 |


## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                   |
|---------------|-------------------------------|
| languageCode  | 省略可能。 既定値は `"en"` です。  |
| text          | 分析するテキスト。          |

## <a name="skill-outputs"></a>スキルの出力

**注**: 言語によっては、一部のエンティティ カテゴリがサポートされていません。
_en_ と _es_ でのみ、`"Quantity"`、`"Datetime"`、`"URL"`、`"Email"` を抽出できます。

| 出力名     | 説明                   |
|---------------|-------------------------------|
| persons      | 各文字列が人物の名前を表す文字列の配列。 |
| locations  | 各文字列が場所を表す文字列の配列。 |
| organizations  | 各文字列が組織を表す文字列の配列。 |
| quantities  | 各文字列が数量を表す文字列の配列。 |
| dateTimes  | 各文字列が DateTime (テキストに表示される) 値を表す文字列の配列。 |
| urls | 各文字列が URL を表す文字列の配列。 |
| emails | 各文字列が電子メールを表す文字列の配列。 |
| namedEntities | 次のフィールドが含まれる複合型の配列。 <ul><li>category</li> <li>value (実際のエンティティ名)</li><li>offset (テキスト内で見つかった場所)</li><li>confidence (ここでは使用しません。 値 -1 に設定されます。)</li></ul> |
| entities | 複合型の配列。テキストから抽出されたエンティティに関する豊富な情報と次のフィールドが含まれます。 <ul><li> name (実際のエンティティ名。 これは "正規化" フォームです)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (エンティティの Wikipedia ページのリンク)</li><li>bingId</li><li>type (認識されたエンティティのカテゴリ)</li><li>subType (特定のカテゴリのみで利用可能。エンティティ型がより詳しく表示されます)</li><li> matches (次を含む複合コレクション)<ul><li>text (エンティティの未加工テキスト)</li><li>offset (それが見つかった場所)</li><li>length (未加工エンティティ テキストの長さ)</li></ul></li></ul> |

##  <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>エラーになる場合
ドキュメントの言語コードがサポートされていない場合、エラーが返され、エンティティは抽出されません。

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
