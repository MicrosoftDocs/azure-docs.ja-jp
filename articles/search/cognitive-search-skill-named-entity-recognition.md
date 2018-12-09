---
title: 名前付きエンティティの認識コグニティブ検索スキル (Azure Search) | Microsoft Docs
description: Azure Search コグニティブ検索パイプライン内のテキストから、人物、場所、および組織の名前付きエンティティを抽出します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.openlocfilehash: f9ff3f66f3a73fbaf1a4c2ca280c85f4bde65444
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442031"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>名前付きエンティティの認識コグニティブ スキル

**名前付きエンティティ認識** スキルは、テキストから名前付きエンティティを抽出します。 使用可能なエンティティ タイプには、`person`、`location`、`organization` が含まれます。

> [!NOTE]
> <ul>
> <li>コグニティブ検索はパブリック プレビュー段階です。 スキルセットの実行および画像の抽出と正規化は、現在無料で提供されています。 これらの機能の価格は、後日、発表される予定です。 </li>
> <li> 名前付きエンティティ認識スキルは "非推奨" と見なされており、2019 年 2 月 15 日以降は正式にサポートされなくなります。 <a href="cognitive-search-skill-deprecated.md">非推奨のコグニティブ検索スキル</a>に関するページに記載されている推奨事項に従い、サポートされているスキルに移行してください。</li>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、`String.Length` によって測定されるため、50,000 文字にする必要があります。 データをキー フレーズ エクストラクターに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| categories    | 抽出する必要があるカテゴリの配列。  可能なカテゴリの型は、`"Person"`、`"Location"`、`"Organization"` です。 カテゴリが指定されていない場合、すべての型が返されます。|
|defaultLanguageCode |  入力テキストの言語コード。 次の言語がサポートされます。`de, en, es, fr, it`|
| minimumPrecision  | 0 から 1 の数値。 精度がこの値よりも小さい場合は、エンティティは返されません。 既定値は 0 です。|

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                   |
|---------------|-------------------------------|
| languageCode  | 省略可能。 既定値は `"en"` です。  |
| text          | 分析するテキスト。          |

## <a name="skill-outputs"></a>スキルの出力

| 出力名     | 説明                   |
|---------------|-------------------------------|
| persons      | 各文字列が人物の名前を表す文字列の配列。 |
| locations  | 各文字列が場所を表す文字列の配列。 |
| organizations  | 各文字列が組織を表す文字列の配列。 |
| entities | 複合型の配列。 各複合型には、次のフィールドが含まれます。 <ul><li>category (`"person"`、`"organization"`、または `"location"`)</li> <li>value (実際のエンティティ名)</li><li>offset (テキスト内で見つかった場所)</li><li>confidence (値が実際のエンティティの信頼度を表す 0 から 1 の値)</li></ul> |

##  <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
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
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
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
+ [エンティティ認識スキル](cognitive-search-skill-entity-recognition.md)
