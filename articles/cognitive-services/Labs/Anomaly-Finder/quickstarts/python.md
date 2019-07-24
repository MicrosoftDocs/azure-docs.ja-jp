---
title: Anomaly Finder API と Python の使用方法 - Microsoft Cognitive Services | Microsoft Docs
description: Cognitive Services の Anomaly Finder と Python の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: df894fee3328b2199b8f7436adabfd3db62f4004
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737129"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Anomaly Finder API と Python の使用

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

この記事は、Anomaly Finder API と Python の使用をすぐに開始し、時系列データの異常結果を取得するタスクを実行するために役立つ情報とコード サンプルを提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Python を使用して Anomaly Finder API で異常ポイントを取得する 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>時系列データの例

時系列データ ポイントの例は、次のとおりです。

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>データを分析して異常ポイントを取得する Python の例

必ず python3 をインストールし、detect.py という名前の python 実行可能ファイルを作成します。 detect.py には、次のコードを含める必要があります。 コードを実行する前に、忘れずに `[YOUR_SUBSCRIPTION_KEY]` 値を有効なサブスクリプション キーに置き換えてください。
`[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` を、データ ポイントに置き換えます。

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>応答の例

成功応答が JSON で返されます。 応答のサンプルは次のとおりです。
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Python アプリ](../tutorials/python-tutorial.md)
