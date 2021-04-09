---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386666"
---
この Postman ベースのクイック スタートでは、ナレッジ ベースから回答を取得する手順を紹介しています。

## <a name="prerequisites"></a>前提条件

* 最新の [**Postman**](https://www.getpostman.com/)。
* 以下が必要です。
    * [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)
    * クイックスタートから作成される、トレーニングおよび発行済みの[質問と回答を含むナレッジ ベース](../Quickstarts/add-question-metadata-portal.md)がメタデータとおしゃべりで構成されている。

> [!NOTE]
> ナレッジ ベースからの質問への回答を生成する準備ができたら、ナレッジ ベースを[トレーニング](../Quickstarts/create-publish-knowledge-base.md#save-and-train)して[発行](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)する必要があります。 ナレッジ ベースが発行されると、 **[発行]** ページに、回答を生成するための HTTP 要求の設定が表示されます。 **[Postman]** タブには、回答の生成に必要な設定が表示されます。

## <a name="set-up-postman-for-requests"></a>要求に対して Postman を設定する

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

このクイックスタートでは、Postman の **POST** 要求と同じ設定を使用し、照会する内容に基づいて、サービスに送信される本文の JSON を POST するように構成します。

この手順を使用して Postman を構成し、次に、後続の各セクションを読んで POST 本文の JSON を構成します。

1. ナレッジ ベースの **[Settings]\(設定\)** ページで **[Postman]** タブを選択し、ナレッジ ベースからの回答を生成するために使用される構成を表示します。 Postman で使用する次の情報をコピーします。

    |名前|設定|目的と値|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|これは、URL の HTTP メソッドとルートです。|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|これは URL のホストです。 Host と Post の値を連結して、完全な generateAnswer URL を取得します。|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Azure に対する要求を承認するためのヘッダー値。 |
    |`Content-type`|`application/json`|コンテンツのヘッダー値。|
    ||`{"question":"<Your question>"}`|JSON オブジェクトとしての POST 要求の本文。 この値は、クエリ実行の目的に応じて、次の各セクションで異なります。|

1. Postman を開き、発行済みのナレッジ ベース設定を使用して、新しい基本的な **POST** 要求を作成します。 以降のセクションでは、POST 本文の JSON を変更して、ナレッジ ベースに対するクエリを変更します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

このクイックスタートでは、Postman の **POST** 要求と同じ設定を使用し、照会する内容に基づいて、サービスに送信される本文の JSON を POST するように構成します。

この手順を使用して Postman を構成し、次に、後続の各セクションを読んで POST 本文の JSON を構成します。

1. ナレッジ ベースの **[Settings]\(設定\)** ページで **[Postman]** タブを選択し、ナレッジ ベースからの回答を生成するために使用される構成を表示します。 Postman で使用する次の情報をコピーします。

    |名前|設定|目的と値|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|これは、URL の HTTP メソッドとルートです。|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|これは URL のホストです。 Host と Post の値を連結して、完全な generateAnswer URL を取得します。|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|要求を承認するためのヘッダー値。 |
    |`Content-type`|`application/json`|コンテンツのヘッダー値。|
    ||`{"question":"<Your question>"}`|JSON オブジェクトとしての POST 要求の本文。 この値は、クエリ実行の目的に応じて、次の各セクションで異なります。|

1. Postman を開き、発行済みのナレッジ ベース設定を使用して、新しい基本的な **POST** 要求を作成します。 以降のセクションでは、POST 本文の JSON を変更して、ナレッジ ベースに対するクエリを変更します。
---

## <a name="use-metadata-to-filter-answer"></a>メタデータを使用して回答をフィルター処理する

前のクイックスタートでは、2 つの異なる質問を区別するために、メタデータが 2 つの QnA ペアに追加されました。 このメタデータをクエリに追加して、関連する QnA ペアのみにフィルターを制限します。

1. Postman で、`service:qna_maker` の名前と値のペアを使用して `strictFilters` プロパティを追加することによって、クエリの JSON のみを変更します。 本文の JSON は次のようになります。

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    質問はたったの 1 単語 (`size`) です。これにより、2 つの質問と回答のペアのいずれかを返すことができます。 `strictFilters` 配列の指定により、`qna_maker` の回答のみに応答が限定されます。

1. この応答には、フィルター条件を満たした回答のみが含まれます。

    次の応答は、読みやすくするために書式設定されています。

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    検索語句は満たしていないもののフィルターの条件は満たしているような質問と回答のペアがあったとしても、それは返されません。 代わりに一般的な回答 `No good match found in KB.` が返されます。

## <a name="use-debug-query-property"></a>デバッグ クエリ プロパティを使用する

> [!NOTE]
>すべての依存関係に Debug プロパティの使用をお勧めするわけではありません。 このプロパティは、トラブルシューティングで製品チームを支援するために追加されました。

デバッグ情報により、返された回答がどのように決定されたかを理解できます。 これは役に立ちますが、必須ではありません。 デバッグ情報を含む回答を生成するには、`debug` プロパティを追加します。

1. Postman で、`debug` プロパティを追加することによって、本文の JSON のみを変更します。 JSON は次のようになります。

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. 応答には、回答に関する関連情報が含まれます。 次の JSON 出力では、一部のデバッグの詳細が省略記号に置き換えられています。

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>テスト ナレッジ ベースを使用する

テスト ナレッジ ベースから回答を取得するには、本文のプロパティ `isTest` を使用します。

Postman で、`isTest` プロパティを追加することによって、本文の JSON のみを変更します。 JSON は次のようになります。

```json
{
    'question':'size',
    'isTest': true
}
```

JSON 応答では、発行されたナレッジ ベース クエリと同じスキーマが使用されます。

> [!NOTE]
> テスト ナレッジ ベースと発行されたナレッジ ベースがまったく同じ場合でも、テスト インデックスはリソース内のすべてのナレッジ ベース間で共有されるため、少し異なるバリエーションが存在する可能性があります。

## <a name="query-for-a-chit-chat-answer"></a>おしゃべりの回答を照会する

1. Postman で、ユーザーからの会話を終了する文言に対する本文の JSON のみを変更します。 JSON は次のようになります。

    ```json
    {
        'question':'thank you'
    }
    ```

1. 応答には、スコアと回答が含まれます。

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    `Thank you` という質問はおしゃべりの質問に完全に一致したため、QnA Maker は 100 というスコアにより完全に信頼できます。 また、QnA Maker からは、関連するすべての質問のほか、おしゃべりのメタデータ タグ情報を含むメタデータ プロパティも返されました。

## <a name="use-threshold-and-default-answer"></a>しきい値と既定の回答を使用する

回答の最小しきい値を要求できます。 しきい値が満たされない場合は、既定の回答が返されます。

1. Postman で、ユーザーからの会話を終了する文言に対する本文の JSON のみを変更します。 JSON は次のようになります。

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    ナレッジ ベースでは、質問のスコアが 71% であるためその回答が見つからず、ナレッジ ベース作成時に指定した既定の回答が代わりに返されます。

    スコアと回答を含む、返される JSON 応答は次のとおりです。

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker からは、信頼できないことを意味するスコア `0` が返されました。 既定の回答も返されました。

1. しきい値を60% に変更し、クエリを再度要求します。

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    返された JSON で回答が見つかりました。

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```