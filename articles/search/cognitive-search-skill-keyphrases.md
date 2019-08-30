---
title: キー フレーズ抽出コグニティブ検索スキル - Azure Search
description: 非構造化テキストを評価し、各レコードに対し、Azure Search のエンリッチメント パイプラインのキー フレーズのリストを返します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 1c6b6455176001c752eb99d04deb3fe63a9d4006
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639095"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>キー フレーズ抽出コグニティブ スキル

**キー フレーズ抽出**スキルは、非構造化テキストを評価し、各レコードに対してキー フレーズのリストを返します。 このスキルでは、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) Cognitive Services によって提供される機械学習モデルが使用されます。

この機能は、レコード内の要点をすばやく特定する必要がある場合に便利です。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、 "食べ物" と "すばらしいスタッフ" がサービスによって返されます。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Search のドキュメントクラッキング段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Search の価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) によって測定されるため、50,000 文字にする必要があります。 データをキー フレーズ エクストラクターに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| 入力                | 説明 |
|---------------------|-------------|
| defaultLanguageCode | (省略可能) 言語を明示的に指定しないドキュメントに適用する言語コード。  既定の言語コードが指定されていない場合、既定の言語コードとして英語 (en) が使用されます。 <br/> [サポートされる言語の完全な一覧](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)を参照してください。 |
| maxKeyPhraseCount   | (省略可能) 生成するキー フレーズの最大数。 |

## <a name="skill-inputs"></a>スキルの入力

| 入力     | 説明 |
|--------------------|-------------|
| text | 分析されるテキスト。|
| languageCode  |  レコードの言語を示す文字列。 このパラメーターが指定されていない場合、既定の言語コードがレコードを分析するために使用されます。 <br/>[サポートされる言語の完全な一覧](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)を参照|

##  <a name="sample-definition"></a>定義例

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>サンプル出力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>エラーと警告
サポートされていない言語コードを指定すると、エラーが生成され、キー フレーズは抽出されません。
テキストが空の場合、警告が生成されます。
テキストが 50,000 文字を超えると、最初の 50,000 文字のみが分析され、警告が発行されます。

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
