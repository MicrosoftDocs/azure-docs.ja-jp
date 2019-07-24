---
title: センチメント コグニティブ検索スキル - Azure Search
description: Azure Search のエンリッチメント パイプラインのテキストから正と負のセンチメント スコアを抽出します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 03e6cd89280adff508f7c30ba7567664cc8ac9bd
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737367"
---
#   <a name="sentiment-cognitive-skill"></a>センチメント コグニティブ スキル

**センチメント** スキルは、正と負の連続体に沿って非構造化テキストを評価し、各レコードに対して、0 から 1 の数値スコアを返します。 1 に近いスコアは正のセンチメント、0 に近いスコアは負のセンチメントを示します。 このスキルでは、Cognitive Services の [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) によって提供される機械学習モデルが使用されます。

> [!NOTE]
> 2018 年 12 月 21 日より、Azure Search のスキルセットに [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)できるようになっています。 これにより、スキルセットの実行への課金を開始できます。 この日付には、ドキュメント クラッキング ステージの一部として画像抽出への課金も開始します。 ドキュメントからのテキスト抽出は、引き続き追加コストなしで提供されます。
>
> [組み込みコグニティブ スキル](cognitive-search-predefined-skills.md)の実行は、[Cognitive Services の従量制価格](https://azure.microsoft.com/pricing/details/cognitive-services)で課金されます。これは、タスクを直接実行した場合と同じ料金です。 画像の抽出は Azure Search の課金対象であり、現在はプレビュー価格で提供されています。 詳細については、「[Azure Search の価格](https://go.microsoft.com/fwlink/?linkid=2042400)」のページ、または「[請求体系について](search-sku-tier.md#how-billing-works)」を参照してください。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、`String.Length` によって測定されるため、5000 文字にする必要があります。 データをセンチメント アナライザーに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)を使用します。


## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名 |                      |
|----------------|----------------------|
| defaultLanguageCode | (省略可能) 言語を明示的に指定しないドキュメントに適用する言語コード。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/text-analytics/text-analytics-supported-languages.md)を参照 |

## <a name="skill-inputs"></a>スキルの入力 

| 入力名 | 説明 |
|--------------------|-------------|
| text | 分析されるテキスト。|
| languageCode  |  (省略可能) レコードの言語を示す文字列。 このプロパティが指定されていない場合の既定値は "en" です。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/text-analytics/text-analytics-supported-languages.md)を参照してください。|

## <a name="skill-outputs"></a>スキルの出力

| 出力名 | 説明 |
|--------------------|-------------|
| スコア | 分析されたテキストのセンチメントを表す 0 から 1 の値。 0 に近い値は負のセンチメント、0.5 に近い値は中立のセンチメント、1 に近い値は正のセンチメントを持ちます。|


##  <a name="sample-definition"></a>定義例

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>メモ
空の場合は、これらのレコードに対してセンチメント スコアは返されません。

## <a name="error-cases"></a>エラーになる場合
言語がサポートされていない場合、エラーが生成され、センチメント スコアは返されません。

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
