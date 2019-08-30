---
title: デプロイされた Web サービスを使用するクライアントを作成する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning モデルでモデルがデプロイされたときに生成された Web サービスを使用する方法について説明します。 REST API を公開する Web サービス。 任意のプログラミング言語を使用して、この API 用のクライアントを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 03579d75ad9cbaceca1a5d42913ff546088f6cfe
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982793"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する

Web サービスとして Azure Machine Learning モデルをデプロイすると、REST API が作成されます。 この API にデータを送信し、モデルによって返される予測を受信できます。 このドキュメントでは、C#、Go、Java、Python を使用して Web サービス用のクライアントを作成する方法について説明します。

Web サービスは、Azure Container Instances、Azure Kubernetes Service、FPGA (field-programmable gate array) にイメージをデプロイするときに作成されます。 イメージを登録済みのモデルとスコアリング ファイルから作成します。 [Azure Machine Learning SDK](https://aka.ms/aml-sdk) を使用して、Web サービスにアクセスするために使用される URI を取得します。 認証が有効になっている場合は、SDK を使用して認証キーまたはトークンを取得することもできます。

機械学習 Web サービスを使用するクライアントを作成するための一般的なワークフローは、次のとおりです。

1. SDK を使用して接続情報を取得します。
1. モデルで使用される要求データの種類を決定します。
1. Web サービスを呼び出すアプリケーションを作成します。

> [!TIP]
> このドキュメントの例は、OpenAPI (Swagger) 仕様を使用せずに手動で作成されています。 デプロイに対して OpenAPI 仕様を有効にした場合は、[swagger-codegen](https://github.com/swagger-api/swagger-codegen) などのツールを使用して、サービスのクライアント ライブラリを作成できます。

## <a name="connection-information"></a>接続情報

> [!NOTE]
> Azure Machine Learning SDK を使用して、Web サービス情報を取得します。 これは Python SDK です。 任意の言語を使用して、サービスに対してクライアントを作成できます。

[azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) クラスでは、クライアントを作成するために必要な情報が提供されます。 クライアント アプリケーションを作成するときに役立つ `Webservice` プロパティを以下に示します。

* `auth_enabled` - キー認証が有効になっている場合は `True`、それ以外の場合は `False` です。
* `token_auth_enabled` - トークン認証が有効になっている場合は `True`、それ以外の場合は `False` です。
* `scoring_uri` - REST API のアドレス。
* `swagger_uri` - OpenAPI 仕様のアドレス。 この URI は、スキーマの自動生成を有効にした場合に使用できます。 詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md#schema)」を参照してください。

デプロイされた Web サービスについてこの情報を取得する場合、次の 3 つの方法があります。

* モデルをデプロイするときに、サービスに関する情報と共に `Webservice` オブジェクトが返されます。

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* `Webservice.list` を使用して、ワークスペース内のモデル用にデプロイされた Web サービスのリストを取得することができます。 フィルターを追加して、返される情報のリストを絞り込むことができます。 フィルター処理できる内容の詳細については、[Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) のリファレンス ドキュメントを参照してください。

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* デプロイされたサービスの名前がわかっている場合は、`Webservice` の新しいインスタンスを作成し、ワークスペースとサービスの名前をパラメーターとして指定できます。 新しいオブジェクトには、デプロイされたサービスに関する情報が含まれます。

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="authentication-for-services"></a>サービスの認証

Azure Machine Learning には、Web サービスへのアクセスを制御する 2 つの方法が用意されています。

|認証方法|ACI|AKS|
|---|---|---|
|Key|既定で無効| 既定で有効|
|トークン| 利用不可| 既定で無効 |

キーまたはトークンで保護されているサービスに要求を送信する場合は、__Authorization__ ヘッダーを使用してキーまたはトークンを渡します。 キーまたはトークンは `Bearer <key-or-token>` の形式にする必要があります。ここで、`<key-or-token>` はキーまたはトークンの値です。

#### <a name="authentication-with-keys"></a>キーによる認証

デプロイに対して認証を有効にした場合、認証キーが自動的に作成されます。

* Azure Kubernetes Service にデプロイする場合、認証は既定で有効になります。
* Azure Container Instances にデプロイする場合、認証は既定で無効になります。

認証を制御するには、デプロイの作成や更新時に `auth_enabled` パラメーターを使用します。

認証が有効になっている場合は、`get_keys` メソッドを使用して、プライマリおよびセカンダリ認証キーを取得できます。

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> キーを再生成する必要がある場合は、[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) を使用します。

#### <a name="authentication-with-tokens"></a>トークンによる認証

Web サービスのトークン認証を有効にする場合、ユーザーは、Web サービスにアクセスするために Azure Machine Learning JWT トークンを提供する必要があります。 

* Azure Kubernetes Service にデプロイする場合、トークン認証は既定で無効になります。
* Azure Container Instances にデプロイする場合、トークン認証はサポートされません。

トークン認証を制御するには、デプロイの作成や更新時に `token_auth_enabled` パラメーターを使用します。

トークン認証が有効になっている場合は、`get_token` メソッドを使用して、ベアラー トークンとそのトークンの有効期限を取得できます。

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> トークンの `refresh_by` 時刻の後に新しいトークンを要求する必要があります。 

## <a name="request-data"></a>要求データ

REST API では、要求の本文が次の構造の JSON ドキュメントであることが予期されます。

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> データの構造は、サービス内のスコアリング スクリプトとモデルで予期される内容と一致する必要があります。 スコアリング スクリプトでは、データがモデルに渡される前に変更される可能性があります。

たとえば、[ノートブック内のトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)例のモデルでは、10 個の数値の配列が予期されます。 この例のスコアリング スクリプトでは、要求から Numpy 配列が作成され、モデルに渡されます。 次の例には、このサービスで予期されるデータが示されています。

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
```

Web サービスでは、1 つの要求で複数のデータ セットを受け入れることができます。 応答の配列を含む JSON ドキュメントが返されます。

### <a name="binary-data"></a>バイナリ データ

サービスでバイナリ データのサポートを有効にする方法については、「[バイナリ データ](how-to-deploy-and-where.md#binary)」を参照してください。

### <a name="cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS)

サービスで CORS のサポートを有効にする方法については、「[クロスオリジン リソース共有](how-to-deploy-and-where.md#cors)」を参照してください。

## <a name="call-the-service-c"></a>サービスを呼び出す (C#)

この例では、C# を使用して、[ノートブック内のトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)例から作成された Web サービスを呼び出す方法を示します。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

返される結果は、次の JSON ドキュメントのようになります。

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>サービスを呼び出す (Go)

この例では、Go を使用して、[ノートブック内のトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)例から作成された Web サービスを呼び出す方法を示します。

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

返される結果は、次の JSON ドキュメントのようになります。

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>サービスを呼び出す (Java)

この例では、Java を使用して、[ノートブック内のトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)例から作成された Web サービスを呼び出す方法を示します。

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

返される結果は、次の JSON ドキュメントのようになります。

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>サービスを呼び出す (Python)

この例では、Python を使用して、[ノートブック内のトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)例から作成された Web サービスを呼び出す方法を示します。

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

返される結果は、次の JSON ドキュメントのようになります。

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Power BI からサービスを使用する

Power BI では、予測によって Power BI のデータを拡充できるように、Azure Machine Learning Web サービスの使用がサポートされています。 

Power BI での使用がサポートされている Web サービスを生成するには、Power BI で必要とされる形式をスキーマがサポートしている必要があります。 [Power BI でサポートされているスキーマの作成方法をご確認ください](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#example-entry-script)。

デプロイした Web サービスは、Power BI データフローから使用できます。 [Power BI から Azure Machine Learning Web サービスを使用する方法をご確認ください](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。

## <a name="next-steps"></a>次の手順

Python とディープ ラーニングのモデルのリアルタイム スコアリングに関する参照アーキテクチャを確認するには、「[Azure アーキテクチャ センター](/azure/architecture/reference-architectures/ai/realtime-scoring-python)」を参照してください。
