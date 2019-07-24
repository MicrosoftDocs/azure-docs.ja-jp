---
title: Anomaly Finder API と PHP の使用方法 - Microsoft Cognitive Services | Microsoft Docs
description: Cognitive Services の Anomaly Finder と PHP の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: f65f389d93a90df1bc16d2228586beead5f96a73
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728745"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>Anomaly Finder API と PHP の使用

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

この記事は、Anomaly Finder API と PHP の使用をすぐに開始し、時系列データの異常結果を取得するタスクを実行するために役立つ情報とコード サンプルを提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>PHP を使用して Anomaly Finder API で異常ポイントを取得する
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>時系列データの例
時系列データの例は、次のとおりです。
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>データを分析して異常ポイントを取得する PHP の例
1. `[YOUR_SUBSCRIPTION_KEY]` 値を、有効なサブスクリプション キーに置き換えます。
2. `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` を例または独自のデータ ポイントに置き換えます。
3. 実行し、応答を確認します。

```PHP
<?php
# This sample uses the Apache HTTP client from HTTP components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new HTTP_Request2('https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection');
$url = $request->getUrl();

$requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

$headers = array(
    # Request headers
    'Content-Type' => 'application/json',
    # NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '[YOUR_SUBSCRIPTION_KEY]',
);

$request->setHeader($headers);

$request->setMethod(HTTP_Request2::METHOD_POST);

# Request body
$request->setBody($requestData);

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}
?>
```

### <a name="example-response"></a>応答の例

成功応答が JSON で返されます。 応答のサンプルは次のとおりです。
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
