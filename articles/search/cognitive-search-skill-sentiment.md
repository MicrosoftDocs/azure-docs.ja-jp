---
title: センチメント コグニティブ スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の AI エンリッチメント パイプラインのテキストから正と負のセンチメント スコアを抽出します。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 6519cd952bd1265b4daad3b77b29aabd47ea4cc5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547477"
---
# <a name="sentiment-cognitive-skill"></a>センチメント コグニティブ スキル

**センチメント** スキルは、正と負の連続体に沿って非構造化テキストを評価し、各レコードに対して、0 から 1 の数値スコアを返します。 1 に近いスコアは正の感情、0 に近いスコアは負の感情を示します。 このスキルでは、Cognitive Services の [Text Analytics](../cognitive-services/text-analytics/overview.md) によって提供される機械学習モデルが使用されます。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Cognitive Search のドキュメント解析段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Cognitive Search の価格](https://azure.microsoft.com/pricing/details/search/)に関するページで説明されています。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、[`String.Length`](/dotnet/api/system.string.length) によって測定されるため、5,000 文字にする必要があります。 データをセンチメント アナライザーに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)を使用します。


## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名 | 説明 |
|----------------|----------------------|
| `defaultLanguageCode` | (省略可能) 言語を明示的に指定しないドキュメントに適用する言語コード。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照 |

## <a name="skill-inputs"></a>スキルの入力 

| 入力名 | 説明 |
|--------------------|-------------|
| `text` | 分析されるテキスト。|
| `languageCode`    |  (省略可能) レコードの言語を示す文字列。 このプロパティが指定されていない場合の既定値は "en" です。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照してください。|

## <a name="skill-outputs"></a>スキルの出力

| 出力名 | 説明 |
|--------------------|-------------|
| `score` | 分析されたテキストのセンチメントを表す 0 から 1 の値。 0 に近い値は負のセンチメント、0.5 に近い値は中立のセンチメント、1 に近い値は正のセンチメントを持ちます。|


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

## <a name="warning-cases"></a>警告のケース
テキストが空の場合は、警告が生成され、センチメント スコアは返されません。
言語がサポートされていない場合は、警告が生成され、センチメント スコアは返されません。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)