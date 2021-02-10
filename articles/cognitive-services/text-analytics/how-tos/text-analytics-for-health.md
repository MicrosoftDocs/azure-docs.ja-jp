---
title: Text Analytics for Health を使用する方法
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health を使用して、構造化されていない臨床テキストから医療情報を抽出してラベル付けする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: d7f71afd1cf47ab1a94d984090e8fb52ee69c6b4
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99088940"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>方法:Text Analytics for Health (プレビュー) を使用する

> [!IMPORTANT] 
> Text Analytics for Health は、"現状のまま"、"保証なしで" 提供されるプレビュー機能です。 そのため、**Text Analytics for Health (プレビュー) は、運用環境に実装またはデプロイして使用しないでください。** Text Analytics for Health は、医療デバイス、臨床サポート、診断ツール、または他のテクノロジ (病気や他の状況の診断、治療、軽減、取り扱い、防止での使用が意図されているもの) として使用することを意図されたり、使用できるようにされているものではなく、そのような目的でのこの機能の使用に対して、マイクロソフトからはライセンスや権利は付与されません。 この機能は、専門的な医療のアドバイスや医学的意見、診断、治療、または医療専門家による医学的判断に代わるものとして実装またはデプロイするために設計されたり、それを意図されたりしたものではなく、そのようには使用しないでください。 お客様は、Text Analytics for Health の使用に関するすべての責任を持ちます。 Microsoft では、Text Analytics for Health またはその機能に関連して提供されるすべての資料が、すべての医療目的に対して十分であること、またはすべての人の健康的または医療的な要件を満たすことについて、いっさい保証しません。 


Text Analytics for Health は、医師のメモ、退院要約、臨床ドキュメント、電子健康記録などの非構造化テキストからの、関連する医療情報の抽出とラベル付けが行われる、Text Analytics API サービスの機能です。  このサービスを利用するには、次の 2 つの方法があります。 

* [Web ベースの API (非同期)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Docker コンテナー (同期)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>特徴

Text Analytics for Health により、英語のテキストに対して固有表現認識 (NER)、関係抽出、エンティティ否定、エンティティ リンク設定が実行されて、臨床および生物医学に関する構造化されていないテキストに含まれる分析情報が明らかにされます。

### <a name="named-entity-recognition"></a>[名前付きエンティティの認識](#tab/ner)

固有表現認識では、非構造化テキストに記載されている語句が検出され、それを診断、薬剤名、症状や兆候、年齢など、1 つまたは複数のセマンティックの種類に関連付けることができます。

> [!div class="mx-imgBorder"]
> ![Health NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[関係抽出](#tab/relation-extraction)

関係抽出により、テキスト内で言及されている概念間の意味のある関係が識別されます。 たとえば、"条件の時間" の関係は、条件名と時間を関連付けることによって検出されます。 

> [!div class="mx-imgBorder"]
> ![Health RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

エンティティ リンク設定では、テキストで言及されている説明されている固有表現を概念の定義済みデータベースで見つかった概念に関連付けることによって、個別のエンティティが明確にされます。 たとえば、統一医療言語システム (UMLS) などです。

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

Text Analytics for Health では、統一医療言語システム ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) メタシソーラス知識ソースにある保健衛生と生物医学のボキャブラリへのリンクがサポートされています。

### <a name="negation-detection"></a>[否定検出](#tab/negation-detection) 

医療コンテンツの意味は、否定などの修飾子によって大きな影響を受け、誤って診断されると重大な影響を及ぼす可能性があります。 Text Analytics for Health では、テキストに記載されているさまざまなエンティティに対する否定の検出がサポートされています。 

> [!div class="mx-imgBorder"]
> ![Health NEG](../media/ta-for-health/health-negation.png)

---

サポートされているすべてのエンティティの一覧については、Text Analytics for Health によって返される[エンティティのカテゴリ](../named-entity-types.md?tabs=health)を参照してください。 信頼度スコアの詳細については、[Text Analytics の透明性に関するメモ](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context)をご覧ください。 

### <a name="supported-languages-and-regions"></a>サポートされている言語とリージョン

Text Analytics for Health では、英語のドキュメントのみがサポートされます。 

現在、Text Analytics for Health のホストされた Web API は、次のリージョンでのみ利用できます: 米国西部 2、米国東部 2、米国中部、北ヨーロッパ、西ヨーロッパ。

## <a name="request-access-to-the-public-preview"></a>パブリック プレビューへのアクセスを要求する

Text Analytics for Health のパブリック プレビューへのアクセスを要求するには、[Cognitive Services 要求フォーム](https://aka.ms/csgate)に記入して送信します。 Text Analytics for Health の使用には課金されません。 

このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、そのフォームは Azure Cognitive Services チームによって確認されます。その後、チームから決定事項がメールで届きます。

> [!IMPORTANT]
> * このフォームでは、Azure サブスクリプション ID に関連付けられているメール アドレスを使用する必要があります。
> * 使用する Azure リソースは、承認された Azure サブスクリプション ID で作成されている必要があります。 
> * Microsoft からのアプリケーションの状態に関する更新については、電子メール (受信トレイと迷惑フォルダーの両方) を確認してください。

## <a name="using-the-docker-container"></a>Docker コンテナーの使用 

独自の環境で Text Analytics for Health コンテナーを実行するには、こちらの[コンテナーのダウンロードとインストールに関する手順](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)に従ってください。

## <a name="using-the-client-library"></a>クライアント ライブラリの使用

Text Analytics クライアント ライブラリの最新のプレリリース版を使用すると、クライアント オブジェクトを使用して Text Analytics for Health を呼び出すことができます。 リファレンス ドキュメントと、GitHub の例を参照してください。
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>REST API 要求の送信 

### <a name="preparation"></a>準備

Text Analytics for Health を使用すると、処理するテキストの量が少ない方が、高い品質の結果を得られます。 これは、大きなテキスト ブロックの方がよい結果が得られる、キー フレーズ抽出のような他の Text Analytics の機能とは対照的です。 これらの操作から最善の結果を得るには、必要に応じて入力を再構成することを検討します。

JSON ドキュメントは、次の形式である必要があります: ID、テキスト、および言語。 

ドキュメントのサイズは、ドキュメントごとに 5120 文字未満でなければなりません。 1 つのコレクションで許可されるドキュメントの最大数については、概念の下にある[データ制限](../concepts/data-limits.md?tabs=version-3)に関する記事を参照してください。 コレクションは、要求の本文で送信されます。

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>ホストされた非同期 Web API に対する API 要求を作成する

コンテナーとホストされた Web API の両方について、POST 要求を作成する必要があります。 [Postman](text-analytics-how-to-call-api.md)、cURL コマンド、または [Text Analytics for Health のホストされた API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health)の **API テスト コンソール** を使用して、POST 要求をすばやく作成し、目的のリージョンのホストされた Web API に送信することができます。 

次に示すのは、Text Analytics for Health API 要求の POST 本文に添付される JSON ファイルの例です。

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>ホストされた非同期 Web API の応答 

この POST 要求は非同期操作にジョブを送信するために使用されるので、応答オブジェクトにテキストはありません。  ただし、ジョブと出力の状態を確認するための GET 要求を行うには、応答ヘッダーの operation-location KEY 値が必要です。  POST 要求の応答ヘッダーの operation-location KEY 値の例を次に示します。

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

ジョブの状態を確認するには、POST 応答の operation-location KEY ヘッダー値の URL に対して GET 要求を行います。  ジョブの状態を反映するには、次の状態が使用されます: `NotStarted`、`running`、`succeeded`、`failed`、`rejected`、`cancelling`、`cancelled`。  

`NotStarted` または `running` 状態のジョブは、GET 要求と同じ URL に対する DELETE HTTP 呼び出しを使用して取り消すことができます。  DELETE 呼び出しの詳細については、[Text Analytics for Health のホストされた API のリファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob)に関するページを参照してください。

GET 要求の応答の例を次に示します。  出力を取得に使用できるのは、`expirationDateTime` (ジョブが作成されてから 24 時間) が経過するまでであることに注意してください。それを過ぎると、出力は消去されます。

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>コンテナーへの API 要求を作成する

[Postman を使用](text-analytics-how-to-call-api.md)するか、次の cURL 要求の例を使用して、デプロイしたコンテナーにクエリを送信します。`serverURL` 変数は適切な値に置き換えます。  コンテナーへの URL での API のバージョンは、ホストされた API とは異なることに注意してください。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

次の JSON は、Text Analytics for Health API 要求の POST 本文に添付されている JSON ファイルの例です。

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>コンテナーの応答本文

次の JSON は、コンテナー化された同期呼び出しからの Text Analytics for Health API 応答本文の例です。

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>否定検出の出力

否定検出を使用する場合、1 つの否定語句によって一度に複数の用語が処理される場合があります。 認識されたエンティティの否定は、JSON 出力では `isNegated` フラグのブール値によって表されます。次に例を示します。

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>関係抽出の出力

関係抽出の出力には、関係の "*ソース*" とその "*ターゲット*" への URI 参照が含まれています。 関係ロールが `ENTITY` のエンティティは、`target` フィールドに割り当てられます。 関係ロールが `ATTRIBUTE` のエンティティは、`source` フィールドに割り当てられます。 省略形の関係には、双方向の `source` と `target` フィールドが含まれ、`bidirectional` は `true` に設定されます。 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [名前付きエンティティのカテゴリ](../named-entity-types.md)
* [新機能](../whats-new.md)
