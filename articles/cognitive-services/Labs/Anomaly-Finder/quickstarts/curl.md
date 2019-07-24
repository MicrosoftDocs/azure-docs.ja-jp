---
title: Anomaly Finder API と cURL の使用方法 - Microsoft Cognitive Services | Microsoft Docs
description: cURL と Cognitive Services の Anomaly Finder API の使用をすぐに開始するために役立つ情報を提供します。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7a4996c83d57b34fcfcff43650b21359acb4e65e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730887"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Anomaly Finder API と cURL の使用

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

この記事は、Anomaly Finder API と cURL の使用をすぐに開始し、時系列データの異常結果を取得するタスクを実行するために役立つ情報とコード サンプルを提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>cURL を使用して Anomaly Finder API で異常ポイントを取得する 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>時系列データの例

時系列データ ポイントの例は、次のとおりです。

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>データを分析して異常ポイントを取得する cURL の例

この例を使用する手順は次のとおりです。

1. `[YOUR_SUBSCRIPTION_KEY]` 値を、有効なサブスクリプション キーに置き換えます。
2. サブスクリプション キーを取得した場所を使用するように、`[YOUR_REGION]` を置き換えます。
3. `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` を例または独自のデータ ポイントに置き換えます。
4. 実行し、応答を確認します。

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>応答の例
成功応答が JSON で返されます。 応答の例は次のとおりです。[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
