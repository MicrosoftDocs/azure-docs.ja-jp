---
title: ブラウザーを使用して予測を照会する方法 - LUIS
description: この記事では、利用可能なパブリック LUIS アプリを使用して、ブラウザーで会話形式のテキストからユーザーの意図を判断します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/30/2020
ms.openlocfilehash: a3bad4ab69f6950f83db9cf1f49cfa4cb7c7b5f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040126"
---
# <a name="how-to-query-the-prediction-runtime-with-user-text"></a>ユーザー テキストを使用して予測ランタイムを照会する方法

LUIS の予測エンドポイントから返される内容を理解するために、予測結果を Web ブラウザーで表示します。

## <a name="prerequisites"></a>前提条件

パブリック アプリのクエリには、以下が必要です。

* お使いの Language Understanding (LUIS) リソース情報:
    * **予測キー** - [LUIS ポータル](https://www.luis.ai/)から取得できます。 まだキーを作成するサブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/cognitive-services)に登録できます。
    * **予測エンドポイント サブドメイン** - サブドメインは、お使いの LUIS リソースの **名前** でもあります。
* LUIS アプリ ID - パブリック IoT アプリ ID `df67dcdb-c37d-46af-88e1-8b97951ca1c2` を使用します。 クイックスタートのコードで使用されるユーザー クエリは、そのアプリに固有のものです。

## <a name="use-the-browser-to-see-predictions"></a>ブラウザーを使用して予測を表示する

1. Web ブラウザーを開きます。
1. 以下の完全な URL を使用して、`YOUR-KEY` を独自の LUIS 予測キーに置き換えます。 要求は GET 要求であり、LUIS 予測キーと共に、クエリ文字列パラメーターとして承認が含まれます。

    #### <a name="v3-prediction-request"></a>[V3 予測要求](#tab/V3-1-1)


    **GET** エンドポイント (スロットによる) 要求に使う V3 URL の形式は、次のとおりです。

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 予測要求](#tab/V2-1-2)

    **GET** エンドポイント要求に使う V2 URL の形式は、次のとおりです。

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. この URL をブラウザー ウィンドウに貼り付け、Enter キーを押します。 `HomeAutomation.TurnOn` の意図が最上位の意図であることと、値が `on` の `HomeAutomation.Operation` エンティティが LUIS で検出されたことを示す JSON 結果がブラウザーに表示されます。

    #### <a name="v3-prediction-response"></a>[V3 予測応答](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2 予測応答](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. すべての意図を表示するには、適切なクエリ文字列パラメーターを追加します。

    #### <a name="v3-prediction-endpoint"></a>[V3 予測エンドポイント](#tab/V3-3-1)

    **すべての意図を表示する** には、クエリ文字列の末尾に `show-all-intents=true` を追加します。

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[V2 予測エンドポイント](#tab/V2)

    **すべての意図を表示する** には、クエリ文字列の末尾に `verbose=true` を追加します。

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>次のステップ

各項目の詳細情報
* [V3 予測エンドポイント](luis-migration-api-v3.md)
* [カスタム サブドメイン](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [LUIS ポータル内でアプリを作成する](get-started-portal-build-app.md)
