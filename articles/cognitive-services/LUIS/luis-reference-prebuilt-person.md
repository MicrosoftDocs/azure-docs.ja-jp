---
title: PersonName 作成済みエンティティ
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) での personName 作成済みエンティティについて説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 7b748c507d5c848cc83a8a0c55cb7b05903bc542
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958534"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの PersonName 作成済みエンティティ
作成済みの personName エンティティは、人の名前を検出します。 このエンティティは既にトレーニングされているので、personName を含む発話の例をアプリケーション意図に追加する必要はありません。 personName エンティティは、英語および中国語の[カルチャ](luis-reference-prebuilt-entities.md)でサポートされます。

## <a name="resolution-for-personname-entity"></a>personName エンティティの解決
次の例では、**builtin.personName** エンティティの解決を示します。

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

[email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md) エンティティについて学習します。 
