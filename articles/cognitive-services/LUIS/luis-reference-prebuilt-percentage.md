---
title: percentage 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での percentage 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: b55aca79047a224a9e1a474afdabf43e2701872d
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341744"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの percentage 作成済みエンティティ
パーセンテージの値は、分数 `3 1/2` またはパーセンテージ `2%` として出現する可能性があります。 このエンティティは既にトレーニングされているので、パーセンテージを含む発話の例をアプリケーション意図に追加する必要はありません。 percentage エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。 

## <a name="types-of-percentage"></a>percentage のタイプ
percentage は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-percentage-entity"></a>作成済み percentage エンティティの解決
次の例では、**builtin.percentage** エンティティの解決を示します。

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。 
