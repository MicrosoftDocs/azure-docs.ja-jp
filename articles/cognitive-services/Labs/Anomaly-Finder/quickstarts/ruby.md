---
title: How to use the Anomaly Finder API with Ruby - Microsoft Cognitive Services | Microsoft Docs
description: Get information and code samples to help you quickly get started using Ruby and the Anomaly Finder API in Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 020c957baf6673365d64c613bd908a440bc3d05c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735035"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Use the Anomaly Finder API with Ruby

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

This article provides information and code samples to help you quickly get started using the Anomaly Finder API with Ruby to accomplish task of getting anomaly detection result of time series data.

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Getting anomaly points with Anomaly Finder API using Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Example of time series data
The example of the time series data points is as follows,

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Analyze data and get anomaly points Ruby example

The steps of using the example are as follows.

1. Install [rest-client](https://github.com/rest-client/rest-client) by running 'gem install rest-client'.
2. Save below code as a .rb file.
3. Replace the `[YOUR_SUBSCRIPTION_KEY]` value with your valid subscription key.
4. Replace the `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` with the example or your own data points.
5. Execute and check the response.

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>Example response

A successful response is returned in JSON. Sample response is as follows.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
