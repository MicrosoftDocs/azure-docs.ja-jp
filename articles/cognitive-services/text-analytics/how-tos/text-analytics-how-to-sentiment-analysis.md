---
title: Text Analytics REST API を使用した感情分析の実行
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Text Analytics REST API でテキスト内のセンチメントを検出する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 08/25/2020
ms.author: aahi
ms.openlocfilehash: a0557c3ccf6510ab3ee2ae29cbef1fc754473345
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933020"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>方法:Text Analytics API を使用してセンチメントを検出する

Text Analytics API の感情分析機能では、テキストを評価し、各文のセンチメント スコアとセンチメント ラベルを返します。 これは、ソーシャル メディア、顧客レビュー、ディスカッション フォーラムなどで肯定的および否定的センチメントを検出する際に役立ちます。 API で使用される AI モデルはサービスによって提供されるため、分析対象のコンテンツを送信するだけで済みます。

感情分析要求の送信後、文レベルやドキュメントレベルのセンチメント ラベル ("negative"、"neutral"、"positive" など) と信頼度スコアが API から返されます。

感情分析はさまざまな言語をサポートしており、さらにプレビューで追加されます。 詳細については、[サポートされている言語](../text-analytics-supported-languages.md)に関するページを参照してください。

## <a name="sentiment-analysis-versions-and-features"></a>感情分析のバージョンと機能

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| 機能                                   | 感情分析 v3 | Sentiment Analysis v3.1 (プレビュー) |
|-------------------------------------------|-----------------------|-----------------------------------|
| 単一要求およびバッチ要求のメソッド    | X                     | X                                 |
| センチメント スコアとラベル付け             | X                     | X                                 |
| Linux ベースの [Docker コンテナー](text-analytics-how-to-install-containers.md) | X  |  |
| 意見マイニング                            |                       | X                                 |

### <a name="sentiment-scoring-and-labeling"></a>センチメント スコアとラベル付け

Sentiment Analysis v3 では、文レベルとドキュメント レベルで返されるセンチメント ラベルとそれぞれの信頼度スコアがテキストに適用されます。 

ラベルは、*positive*、*negative*、および *neutral* です。 ドキュメント レベルでは、*mixed* センチメント ラベルが返されることもあります。 ドキュメントのセンチメントは、次のように決定されます。

| 文章のセンチメント                                                                            | 返されるドキュメントのラベル |
|-----------------------------------------------------------------------------------------------|-------------------------|
| ドキュメントに `positive` の文が少なくとも 1 つ含まれている。 残りの文は `neutral` である。 | `positive`              |
| ドキュメントに `negative` の文が少なくとも 1 つ含まれている。 残りの文は `neutral` である。 | `negative`              |
| ドキュメントに、`negative` の文が少なくとも 1 つ、`positive` の文が少なくとも 1 つ含まれている。    | `mixed`                 |
| ドキュメント内のすべての文が `neutral` である。                                                  | `neutral`               |

信頼度スコアは、1 から 0 の範囲で割り当てられます。 スコアが 1 に近いほど、ラベルの分類の信頼度が高いことを示し、スコアが低いほど信頼度が低いことを示します。 各ドキュメントまたは各文で、ラベル (positive、negative、neutral) に関連付けられた予測スコアは合計で 1 になります。

### <a name="opinion-mining"></a>意見マイニング

意見マイニングは、バージョン 3.1-preview.1 以降の感情分析の機能です。 この機能は、自然言語処理 (NLP) ではアスペクトベースの感情分析とも呼ばれます。テキストに含まれるアスペクト (製品やサービスの属性など) に関連した意見について、より粒度の細かい情報が得られます。

たとえば、あるホテルについて "the room was great, but the staff was unfriendly (部屋はすばらしいのですが、スタッフが無愛想でした)" のようなフィードバックを利用者が残した場合、そのテキストに含まれるアスペクトと、それに関連する意見および感情が意見マイニングによって特定されます。

| 特徴 | 意見    | センチメント |
|--------|------------|-----------|
| room   | great      | ポジティブ  |
| staff  | unfriendly | ネガティブ  |

意見マイニングを結果として取得するには、感情分析の要求に `opinionMining=true` フラグを追加する必要があります。 感情分析の応答に意見マイニングの結果が含められます。

## <a name="sending-a-rest-api-request"></a>REST API 要求の送信 

### <a name="preparation"></a>準備

感情分析では、作業するテキストの分量が小さいほど、高い品質の結果が得られます。 これは、テキスト ブロックが大きい方がパフォーマンスが向上するキー フレーズ抽出とは反対です。 両方の操作から最善の結果を得るには、両方の操作に応じて入力を再構成することを検討してください。

JSON ドキュメントは、次の形式である必要があります: ID、テキスト、および言語。

ドキュメントのサイズは、ドキュメントごとに 5120 文字未満でなければなりません。 コレクションごとに最大 1000 個の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。

## <a name="structure-the-request"></a>要求を構造化する

POST 要求を作成します。 次のリファレンス リンクにある [Postman](text-analytics-how-to-call-api.md) または **API テスト コンソール**を使用して、簡単に要求を構造化し、送信することができます。 

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

[感情分析 v3 のリファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

#### <a name="version-31-preview1"></a>[Version 3.1-preview.1](#tab/version-3-1)

[感情分析 v3.1 のリファレンス](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

---

### <a name="request-endpoints"></a>要求エンドポイント

Azure 上の Text Analytics リソースまたはインスタンス化された [Text Analytics コンテナー](text-analytics-how-to-install-containers.md)を使用して、感情分析用の HTTPS エンドポイントを設定します。 使用するバージョンの正しい URL を指定する必要があります。 次に例を示します。

> [!NOTE]
> Azure portal で Text Analytics リソースのキーとエンドポイントを確認できます。 それらは、リソースの**クイック スタート** ページの**リソース管理**の下にあります。 

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

#### <a name="version-31-preview1"></a>[Version 3.1-preview.1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

意見マイニングの結果を得るには、`opinionMining=true` パラメーターを追加する必要があります。 次に例を示します。

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

既定では、このパラメーターが `false` に設定されています。 

---

要求ヘッダーに Text Analytics API キーが含まれるように設定します。 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

### <a name="example-sentiment-analysis-request"></a>感情分析要求の例 

感情分析のために送信するコンテンツの例を次に示します。 要求の形式は、どちらのバージョンでも同じです。
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>要求を投稿する

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[data limits (データ制限)](../overview.md#data-limits)」セクションを参照してください。

Text Analytics API はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。


### <a name="view-the-results"></a>結果の確認

感情分析は、ドキュメント全体およびそこに含まれるそれぞれの文について、センチメント ラベルと信頼度スコアを返します。 スコアが 1 に近いほど、ラベルの分類の信頼度が高いことを示し、スコアが低いほど信頼度が低いことを示します。 ドキュメントには複数の文が含まれる場合があり、それぞれのドキュメントまたは文の範囲内での信頼度スコアの合計は 1 になります。

出力はすぐに返されます。 JSON を受け入れるアプリケーションに結果をストリーミングすることも、出力をローカル システム上のファイルに保存することもできます。 次に、データの並べ替え、検索、および操作に使用できるアプリケーション内に出力をインポートします。 多言語と絵文字のサポートにより、応答にはテキスト オフセットが含まれる場合があります。 詳細については[オフセットの処理方法](../concepts/text-offsets.md)に関するページを参照してください。

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>感情分析 v3.0 の応答の例

感情分析 v3 からの応答には、分析された各文およびドキュメントのセンチメント ラベルとセンチメント スコアが記載されています。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-31-preview1"></a>[Version 3.1-preview.1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>感情分析 v3.1 の応答の例

Sentiment Analysis v3.1 では、「**Version 3.0**」タブに示した応答オブジェクトに加え意見マイニングが返されます。以下の応答では、"*The restaurant had great food and our waiter was friendly (レストランの食事はすばらしく、ウエーターの態度も良かった)* " という文に、"*food (食事)* " と "*waiter (ウエーター)* " の 2 つのアスペクトが存在します。 それぞれのアスペクトの `relations` プロパティには `ref` 値があり、関連する `documents`、`sentences`、`opinions` オブジェクトへの URI 参照が設定されています。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>まとめ

この記事では、Text Analytics API を使用した感情分析の概念とワークフローについて説明しました。 要約すると:

+ 感情分析が利用できる言語は限られています。
+ 要求本文内の JSON ドキュメントには、ID、テキスト、および言語のコードが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を使用して `/sentiment` エンドポイントに対して行われます。
+ ドキュメント ID ごとのセンチメント スコアで構成される応答出力は、JSON を受け入れるすべてのアプリにストリーミングすることができます。 たとえば、Excel や Power BI です。

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [Text Analytics クライアント ライブラリの使用](../quickstarts/text-analytics-sdk.md)
* [新機能](../whats-new.md)
