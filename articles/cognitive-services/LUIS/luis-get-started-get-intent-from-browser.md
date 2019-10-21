---
title: クイック スタート:ブラウザーで意図を取得する - LUIS
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、利用可能なパブリック LUIS アプリを使用して、ブラウザーで会話形式のテキストからユーザーの意図を判断します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703603"
---
# <a name="quickstart-get-intent-with-a-browser"></a>クイック スタート:ブラウザーで意図を取得する

LUIS の予測エンドポイントから返される内容を理解するために、予測結果を Web ブラウザーで表示します。 

## <a name="prerequisites"></a>前提条件

パブリック アプリのクエリには、以下が必要です。

* Language Understanding (LUIS) キーを持っていること。 まだキーを作成するサブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)に登録できます。
* パブリック アプリの ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`。 

## <a name="use-the-browser-to-see-predictions"></a>ブラウザーを使用して予測を表示する

1. Web ブラウザーを開きます。 
1. 以下の完全な URL を使用して、`{your-key}` を独自の LUIS キーに置き換えます。 要求は GET 要求であり、クエリ文字列パラメーターとして LUIS キーを使用した承認が含まれます。

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 予測エンドポイントの要求](#tab/V2)
    
    **GET** エンドポイント要求に使う V2 URL の形式は、次のとおりです。
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 予測エンドポイントの要求](#tab/V3)
    
    
    **GET** エンドポイント (スロットによる) 要求に使う V3 URL の形式は、次のとおりです。
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. この URL をブラウザー ウィンドウに貼り付け、Enter キーを押します。 `HomeAutomation.TurnOn` の意図が最上位の意図であることと、値が `on` の `HomeAutomation.Operation` エンティティが LUIS で検出されたことを示す JSON 結果がブラウザーに表示されます。

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

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

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
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

    * * *

1. すべての意図を表示するには、適切なクエリ文字列パラメーターを追加します。 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 予測エンドポイント](#tab/V2)

    **すべての意図を表示する**には、クエリ文字列の末尾に `verbose=true` を追加します。

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
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

    #### <a name="v3-prediction-endpointtabv3"></a>[V3 予測エンドポイント](#tab/V3)

    **すべての意図を表示する**には、クエリ文字列の末尾に `show-all-intents=true` を追加します。

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
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

    * * * 

## <a name="next-steps"></a>次の手順

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

> [!div class="nextstepaction"]
> [LUIS ポータル内でアプリを作成する](get-started-portal-build-app.md)