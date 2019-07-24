---
title: Anomaly Finder API と Java の使用方法 - Microsoft Cognitive Services | Microsoft Docs
description: Java と Cognitive Services の異常検出の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
ms.openlocfilehash: 0f96ee13e6020bc69508f8ced45741fe7dec396a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732881"
---
# <a name="use-the-anomaly-finder-api-with-java"></a>Anomaly Finder API と Java の使用

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

この記事は、Anomaly Detection API と Java の使用をすぐに開始し、時系列データの異常検出結果を取得するタスクを実行するために役立つ情報とコード サンプルを提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-detection-api-using-java"></a>Java を使用して Anomaly Detection API で異常ポイントを取得する

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>時系列データの例

時系列データ ポイントの例は、次のとおりです。

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-java-example"></a>データを分析して異常ポイントを取得する Java の例

このサンプルを実行するには、次の手順を実行します。
1. 新しいコマンド ライン アプリを作成します。
2. Main クラスを次のコードに置き換えます (`package` 文はすべて保持します)。
3. `[YOUR_SUBSCRIPTION_KEY]` 値を、有効なサブスクリプション キーに置き換えます。
4. `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` を例または独自のデータ ポイントに置き換えます。
5. Maven リポジトリから、次のグローバル ライブラリをプロジェクトの `lib` ディレクトリにダウンロードします。
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. 'Main' を実行します。

```java

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    public static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

    public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

    public static void main(String[] args) {
        final String content = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";

        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost request = new HttpPost(uriBase);

        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        try {
            StringEntity params = new StringEntity(content);
            request.setEntity(params);

            CloseableHttpResponse response = client.execute(request);
            try {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    System.out.println("----------");
                    System.out.println(response.getStatusLine());
                    System.out.println("Response content is :\n");
                    System.out.println(EntityUtils.toString(respEntity, "utf-8"));
                    System.out.println("----------");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            } finally {
                response.close();
            }

        } catch (Exception ex) {
            System.err.println("Exception on Anomaly Detection: " + ex.getMessage());
            ex.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                System.err.println("Exception on closing HttpClient: " + e.getMessage());
                e.printStackTrace();
            }
        }
    }
}

```

### <a name="example-response"></a>応答の例

成功応答が JSON で返されます。 応答の例は次のとおりです。
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Java アプリ](../tutorials/java-tutorial.md)
