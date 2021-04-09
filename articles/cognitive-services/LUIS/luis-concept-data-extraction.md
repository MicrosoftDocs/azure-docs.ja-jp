---
title: データの抽出 - LUIS
description: 意図とエンティティが含まれる発話テキストからデータを抽出します。 Language Understanding (LUIS) から抽出できるデータの種類について説明します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: e6f01354bb5aa2b78d3c9962bac49be39dd2c81f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025995"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>意図とエンティティが含まれる発話テキストからデータを抽出する
LUIS を使用すると、ユーザーの自然言語での発話から情報を取得できます。 この情報は、アクションを実行するために、プログラム、アプリケーション、またはチャットボットで使用できるような方法で抽出されます。 以降のセクションで、JSON の例を使用して、意図とエンティティから返されるデータについて説明します。

抽出するのが最も困難なデータは機械学習データです。その理由は、テキストが完全一致ではないためです。 機械学習[エンティティ](luis-concept-entity-types.md)のデータ抽出は、期待どおりのデータを受け取っていると確信できるまでは、[作成サイクル](luis-concept-app-iteration.md)の一部としておく必要があります。

## <a name="data-location-and-key-usage"></a>データの場所とキー使用法
LUIS により、公開されている[エンドポイント](luis-glossary.md#endpoint)でのユーザーの発話からデータが抽出されます。 **HTTPS 要求** (POST または GET) には、発話と、ステージング環境や運用環境など、オプションの構成がいくつか含まれます。

**V2 予測エンドポイントの要求**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3 予測エンドポイントの要求**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

`appID` は、LUIS アプリの **[設定]** ページで確認できます。また、LUIS アプリの編集時に URL の一部 (`/apps/` の後) として確認できます。 `subscription-key` は、アプリの照会に使用するエンドポイント キーです。 LUIS を学習している間は無料のオーサリング/スターター キーを使用できますが、エンドポイント キーを、[想定される LUIS の使用法](luis-limits.md#key-limits)をサポートするキーに変更することが重要です。 `timezoneOffset` の単位は分です。

**HTTPS 応答** には、ステージングまたは運用エンドポイントの現在公開されているモデルに基づいて LUIS によって判別できるすべての意図およびエンティティの情報が含まれています。 エンドポイント URL は [LUIS](luis-reference-regions.md) Web サイトの **[Manage]\(管理\)** セクションの **[Keys and endpoints]\(キーとエンドポイント\)** ページにあります。

## <a name="data-from-intents"></a>意図からのデータ
プライマリ データは、最上位スコアの **意図の名前** です。 エンドポイントの応答は次のとおりです。

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

* * *

|データ オブジェクト|データ型|データの場所|値|
|--|--|--|--|
|Intent|String|topScoringIntent.intent|"GetStoreInfo"|

チャットボットまたは LUIS 呼び出し元アプリが複数の意図のスコアに基づいて決定を行う場合、すべての意図のスコアを返します。


#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

querystring パラメーター `verbose=true` を設定します。 エンドポイントの応答は次のとおりです。

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)

querystring パラメーター `show-all-intents=true` を設定します。 エンドポイントの応答は次のとおりです。

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

* * *

意図は、スコアが最も高いものから最も低いものへと並べ替えられます。

|データ オブジェクト|データ型|データの場所|値|Score|
|--|--|--|--|:--|
|Intent|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intent|String|intents[1].intent|"None"|0.0168218873|

事前構築済みのドメインを追加する場合、意図の名前は、`Utilties` や`Communication` などのドメインと、意図を表します。

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

* * *

|Domain|データ オブジェクト|データ型|データの場所|値|
|--|--|--|--|--|
|Utilities|Intent|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Communication|Intent|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intent|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>エンティティからのデータ
ほとんどのチャット ボットおよびアプリケーションでは、意図名以外の情報も必要です。 この省略可能な追加データは、発話で検出されたエンティティから取得されます。 エンティティの種類ごとに、一致に関するさまざまな情報が返されます。

発話内の 1 つの単語またはフレーズは、複数のエンティティと一致する可能性があります。 その場合、一致するエンティティごとにそのスコアが返されます。

すべてのエンティティが、エンドポイントから応答の **エンティティ** 配列で返されます

## <a name="tokenized-entity-returned"></a>返されるトークン化されたエンティティ

LUIS での [トークンのサポート](luis-language-support.md#tokenization)を確認します。


## <a name="prebuilt-entity-data"></a>事前構築済みのエンティティ データ
[事前構築済み](luis-concept-entity-types.md)のエンティティは、オープン ソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトを使用して、正規表現の一致に基づき検出されます。 事前構築済みのエンティティは、エンティティ配列で返され、`builtin::` というプレフィックスが付加された種類の名前が使用されます。

## <a name="list-entity-data"></a>リスト エンティティ データ

[リスト エンティティ](reference-entity-list.md)は、固定かつ限定された関連単語セットとそのシノニムを表します。 LUIS では、リスト エンティティの追加の値は検出されません。 現在のリストに基づいて新しい単語の候補を表示するには、 **[Recommend] (推奨)** 機能を使用します。 同じ値を持つリスト エンティティが複数存在する場合は、エンドポイント クエリに各エンティティが返されます。

## <a name="regular-expression-entity-data"></a>正規表現エンティティ データ

[正規表現エンティティ](reference-entity-regular-expression.md)は、指定された正規表現に基づいてエンティティを抽出します。

## <a name="extracting-names"></a>名前の抽出
名前は、文字と単語のほぼすべての組み合わせが考えられるため、発話から名前を取得することは困難です。 抽出する名前の種類に応じて、複数のオプションがあります。 次の推奨事項はルールではなく、詳細なガイドラインです。

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>事前構築済みの PersonName エンティティと GeographyV2 エンティティを追加する

[PersonName](luis-reference-prebuilt-person.md) エンティティと [GeographyV2](luis-reference-prebuilt-geographyV2.md) エンティティは、いくつかの[言語カルチャ](luis-reference-prebuilt-entities.md)で利用できます。

### <a name="names-of-people"></a>人の名前

人の名前は、言語およびカルチャに応じて、幾分ある種の形式を持つことがあります。 事前構築済みの **[personName](luis-reference-prebuilt-person.md)** エンティティ、または氏名のロールが含まれる **[簡易エンティティ](luis-concept-entity-types.md)** のどちらかを使用します。

簡易エンティティを使用する場合は、必ず発話のさまざまな部分に姓と名を使用している例を提供してください。また、None 意図を含むあらゆる意図にわたるさまざまな長さの発話で、姓と名を使用している例を提供してください。 エンドポイントの発話を定期的に[確認](./luis-how-to-review-endpoint-utterances.md)して、適切に予測されていないすべての名前にラベルを付けます。

### <a name="names-of-places"></a>場所の名前

場所の名前には、市区町村、郡、州、都道府県、国/地域などの既知の名前が設定されています。 位置情報を抽出するには、事前構築済みエンティティ **[geographyV2](luis-reference-prebuilt-geographyv2.md)** を使用します。

### <a name="new-and-emerging-names"></a>新しい名前

一部のアプリでは、製品や企業などの新しい名前を検索できる必要があります。 これらの種類の名前は、最も困難な種類のデータ抽出です。 **[簡易エンティティ](luis-concept-entity-types.md#simple-entity)** から始めて、[フレーズ リスト](luis-concept-feature.md)を追加します。 エンドポイントの発話を定期的に[確認](./luis-how-to-review-endpoint-utterances.md)して、適切に予測されていないすべての名前にラベルを付けます。

## <a name="patternany-entity-data"></a>Pattern.any エンティティ データ

[Pattern.any](reference-entity-pattern-any.md) は、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される、可変長プレースホルダーです。 パターンが適用されるためには、パターンで使用されるエンティティが必要です。

## <a name="sentiment-analysis"></a>センチメント分析
[公開](luis-how-to-publish-app.md#sentiment-analysis)中にセンチメント分析が構成されている場合、LUIS の json 応答にはセンチメント分析が含まれます。 センチメント分析の詳細については、[Text Analytics](../text-analytics/index.yml) のドキュメントを参照してください。

## <a name="key-phrase-extraction-entity-data"></a>キー フレーズ抽出エンティティ データ
[キー フレーズ抽出エンティティ](luis-reference-prebuilt-keyphrase.md)では、[Text Analytics](../text-analytics/index.yml) によって提供される、発話内のキー フレーズを返します。

## <a name="data-matching-multiple-entities"></a>複数のエンティティに一致するデータ

LUIS では、発話内で検出されたすべてのエンティティを返します。 その結果、チャット ボットでは、結果に基づいて決定を行う必要があります。

## <a name="data-matching-multiple-list-entities"></a>複数のリスト エンティティに一致するデータ

単語またはフレーズが複数のリスト エンティティに一致すると、エンドポイント クエリからそれぞれのリスト エンティティが返されます。

クエリ `when is the best time to go to red rock?` の場合、アプリの複数のリストに単語 `red` があると、LUIS ではすべてのエンティティを認識し、JSON エンドポイントの応答の一部としてエンティティの配列を返します。

## <a name="next-steps"></a>次のステップ

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。
