---
title: Anomaly Finder API と C# の使用方法 - Microsoft Cognitive Services | Microsoft Docs
description: C# と Cognitive Services の Anomaly Finder API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 8288d5cf3ffbc6d1721a9c28c48ff89d93bf2c1b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736636"
---
# <a name="use-the-anomaly-finder-api-with-c"></a>Anomaly Finder API と C# の使用

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

この記事は、Anomaly Finder API と C# の使用をすぐに開始し、時系列データの異常結果を取得するタスクを実行するために役立つ情報とコード サンプルを提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-c"></a>C# を使用して Anomaly Finder API で異常ポイントを取得する

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data-points"></a>時系列データ ポイントの例

時系列データ ポイントの例は、次のとおりです。
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-c-example"></a>データを分析して異常ポイントを取得する C# の例

この例を使用する手順は次のとおりです。

1. Visual Studio で、新しいコンソール ソリューションを作成します。
2. Program.cs を以下のコードに置き換え、System.Net.Http への参照を追加します。
3. `[YOUR_SUBSCRIPTION_KEY]` 値を、有効なサブスクリプション キーに置き換えます。
4. `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` を、データ ポイントに置き換えます。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading.Tasks;

namespace Console
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

        const string endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

        // Replace the request data with your real data.
        const string requestData = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";
        static void Main(string[] args)
        {
            var match = Regex.Match(endpoint, "(?<BaseAddress>https://[^/]+)(?<Url>/*.+)");
            if (match.Success)
            {
                var res = Request(
                    match.Groups["BaseAddress"].Value,
                    match.Groups["Url"].Value,
                    subscriptionKey,
                    requestData).Result;
                System.Console.Write(res);
            }
            else
            {
                System.Console.Write("Incorrect endpoint.");
            }
        }

        /// <summary>
        /// Call API to detect the anomaly points
        /// </summary>
        /// <param name="baseAddress">Base address of the API endpoint.</param>
        /// <param name="endpoint">The endpoint of the API</param>
        /// <param name="subscriptionKey">The subscription key applied  </param>
        /// <param name="requestData">The JSON string for requet data points</param>
        /// <returns>The JSON string for anomaly points and expected values.</returns>
        static async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
        {
            using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
            {
                client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

                var content = new StringContent(requestData, Encoding.UTF8, "application/json");
                var res = await client.PostAsync(endpoint, content);
                if (res.IsSuccessStatusCode)
                {
                    return await res.Content.ReadAsStringAsync();
                }
                else
                {
                    return $"ErrorCode: {res.StatusCode}";
                }
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
> [C# アプリ](../tutorials/csharp-tutorial.md)
