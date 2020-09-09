---
title: 感情分析 - LUIS
titleSuffix: Azure Cognitive Services
description: センチメント分析が構成されている場合、LUIS の json 応答には、センチメント分析が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.author: diberry
ms.openlocfilehash: 2d15170e3785d8978b9cb21eae3b94b002f9172e
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857174"
---
# <a name="sentiment-analysis"></a>センチメント分析
センチメント分析が構成されている場合、LUIS の json 応答には、センチメント分析が含まれます。 センチメント分析の詳細については、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) のドキュメントを参照してください。

LUIS では Text Analytics V2 を使用します。 

## <a name="resolution-for-sentiment"></a>センチメントの解決

センチメント データは 1 と 0 の間のスコアで、1 に近いほどポジティブなセンチメントを示し、0 に近いほどネガティブな感情を示します。

#### <a name="english-language"></a>[英語](#tab/english)

カルチャが `en-us` の場合、応答は次のとおりです。

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[その他の言語](#tab/other-languages)

その他のすべてのカルチャについては、応答は次のとおりです。

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

