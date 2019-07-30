---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 67c95ffcdbdbcfbb9a86e15c91d984953d7bbffc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181488"
---
LUIS の予測エンドポイントから返される内容を理解するために、予測結果を Web ブラウザーで表示します。 パブリック アプリのクエリには、キーとアプリ ID が必要です。 パブリック IoT アプリ ID である `df67dcdb-c37d-46af-88e1-8b97951ca1c2` は、手順 1 の URL の一部として提供されます。

**GET** エンドポイント要求に使う URL の形式を示します。

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. パブリック IoT アプリのエンドポイントは、次の形式です。`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    URL をコピーして、`<YOUR_KEY>` の値を実際のキーに置き換えます。

2. この URL をブラウザー ウィンドウに貼り付け、Enter キーを押します。 `HomeAutomation.TurnOn` の意図が最上位の意図であることと、値が `bedroom` の `HomeAutomation.Room` エンティティが LUIS で検出されたことを示す JSON 結果がブラウザーに表示されます。

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. URL の `q=` パラメーターの値を `turn off the living room light` に変更し、Enter キーを押します。 今度は、`HomeAutomation.TurnOff` の意図が最上位の意図であることと、値が `living room` の `HomeAutomation.Room` エンティティが LUIS で検出されたことを示す結果が表示されます。 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
