---
title: クイック スタート:Go を使用して Text Analytics API を呼び出す
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: e6d641109bafdc3dba05a30fd627a3246c7edef5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002779"
---
# <a name="quickstart-using-go-to-call-the-text-analytics-cognitive-service"></a>クイック スタート:Go を使用して Text Analytics Cognitive Service を呼び出す 
<a name="HOLTop"></a>

この記事では、 [Text Analytics API シリーズ](//go.microsoft.com/fwlink/?LinkID=759711) を Go で使用して、[言語の検出](#Detect)、[センチメントの分析](#SentimentAnalysis)、[キー フレーズの抽出](#KeyPhraseExtraction)、および[リンクされているエンティティの識別](#Entities)を行う方法について説明します。

API の技術ドキュメントについては、[API の定義](//go.microsoft.com/fwlink/?LinkID=759346)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

また、サインアップ中に生成された[エンドポイントとアクセス キー](../How-tos/text-analytics-how-to-access-key.md)も必要です。

<a name="Detect"></a>

## <a name="detect-language"></a>言語を検出する

言語検出 API では、[言語検出メソッド](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)を使用してテキスト ドキュメントの言語を検出します。

1. 任意のコード エディターで新しい Go プロジェクトを作成します。
1. 次に示すコードを追加します。
1. `subscriptionKey` 値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
1. `uriBase` の場所 (現在は `westcentralus`) を、サインアップしたリージョンで置き換えます。
1. "go" という拡張子でファイルを保存します。
1. Go がインストールされているコンピューターのコマンド プロンプトをルート フォルダーから開きます。
1. ファイルをビルドします (例: `go build detect.go`)。
1. ファイルを実行します (例: `go run detect.go`)。

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.1/languages"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="detect-language-response"></a>言語検出応答

成功した応答は、次の例に示すように JSON で返されます。

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>センチメントを分析する

Sentiment Analysis API では、[Sentiment メソッド](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)を使用して、一連のテキスト レコードのセンチメントを検出します。 次の例では、英語とスペイン語の 2 つのドキュメントをスコア付けしています。

1. 任意のコード エディターで新しい Go プロジェクトを作成します。
1. 次に示すコードを追加します。
1. `subscriptionKey` 値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
1. `uriBase` の場所 (現在は `westcentralus`) を、サインアップしたリージョンで置き換えます。
1. "go" という拡張子でファイルを保存します。
1. Go がインストールされているコンピューターのコマンド プロンプトをルート フォルダーから開きます。
1. ファイルをビルドします (例: `go build sentiment.go`)。
1. ファイルを実行します (例: `go run sentiment.go`)。

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.1/sentiment"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="analyze-sentiment-response"></a>センチメント分析応答

結果は、スコアが 1.0 に近いほど肯定的と評価され、0.0 に近いほど否定的と評価されます。
成功した応答は、次の例に示すように JSON で返されます。

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>キー フレーズを抽出する

Key Phrase Extraction API では、[Key Phrases メソッド](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)を使用して、テキスト ドキュメントからキー フレーズを抽出します。 次の例では、英語とスペイン語、両方のドキュメントのキー フレーズを抽出しています。

1. 任意のコード エディターで新しい Go プロジェクトを作成します。
1. 次に示すコードを追加します。
1. `subscriptionKey` 値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
1. `uriBase` の場所 (現在は `westcentralus`) を、サインアップしたリージョンで置き換えます。
1. "go" という拡張子でファイルを保存します。
1. Go がインストールされているコンピューターのコマンド プロンプトを開きます。
1. ファイルをビルドします (例: `go build key-phrases.go`)。
1. ファイルを実行します (例: `go run key-phrases.go`)。

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.1/keyPhrases"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="extract-key-phrases-response"></a>キー フレーズ抽出応答

成功した応答は、次の例に示すように JSON で返されます。

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>エンティティの識別

Entities API は、[Entities メソッド](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634)を使用して、テキスト ドキュメント内のよく知られたエンティティを識別します。 [Entities](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) は、テキストから "United States" などの単語を抽出して、その種類や Wikipedia リンクを返します。 "United States" の種類は `location` で、Wikipedia のリンクは `https://en.wikipedia.org/wiki/United_States` です。  次の例では、英語のドキュメントのエンティティを識別しています。

1. 任意のコード エディターで新しい Go プロジェクトを作成します。
1. 次に示すコードを追加します。
1. `subscriptionKey` 値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
1. `uriBase` の場所 (現在は `westcentralus`) を、サインアップしたリージョンで置き換えます。
1. "go" という拡張子でファイルを保存します。
1. Go がインストールされているコンピューターのコマンド プロンプトを開きます。
1. ファイルをビルドします (例: `go build entities.go`)。
1. ファイルを実行します (例: `go run entities.go`)。

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westus" in the URI below with "westcentralus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.1/entities"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "Microsoft is an It company."}
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="entity-extraction-response"></a>エンティティ抽出の応答

成功した応答は、次の例に示すように JSON で返されます。

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>関連項目

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)
