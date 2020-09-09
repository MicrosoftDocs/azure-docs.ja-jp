---
title: クイック スタート:Go を使用して Web 検索を実行する - Bing Web Search REST API
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、Go を使って Bing Web Search REST API に要求を送信し、JSON 応答を受信します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.reviewer: nhoyadx@gmail.com, v-gedod, erhopf
ms.custom: seodec2018
ms.openlocfilehash: 3ea8375d1ed999d4b617a9d2a78ec53ad22543ac
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85602188"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-go"></a>クイック スタート:Bing Web Search REST API と Go を使用して Web を検索する

このクイックスタートを使用して、Bing Web Search API を初めて呼び出してみましょう。 この Go アプリケーションは、API に検索要求を送信して、JSON 応答を表示します。 このアプリケーションは Go で記述されていますが、この API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

 このクイックスタートのサンプル コードでは、コア ライブラリだけが必要であり、外部との依存関係はありません。  

## <a name="prerequisites"></a>前提条件
このクイック スタートを実行するには、以下のものが必要です。

* [Go バイナリ](https://golang.org/dl/)
* サブスクリプション キー

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]  

## <a name="create-a-project-and-import-core-libraries"></a>プロジェクトの作成とコア ライブラリのインポート

適切な IDE またはエディターで新しい Go プロジェクトを作成します。 次に、要求のための `net/http`、応答を読み取るための `ioutil`、JSON を処理するための `time` と `encoding/json`、および出力を表示するための `fmt` をインポートします。

```go
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)
```

## <a name="create-a-struct-to-format-the-search-results"></a>検索結果を書式設定するための構造体の作成

`BingAnswer` 構造体は、応答で提供されるデータを書式設定します。

```go
// This struct formats the answers provided by the Bing Web Search API.
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}
```

## <a name="declare-the-main-function-and-define-variables"></a>main 関数の宣言と変数の定義  

このコードは main 関数を宣言し、必要な変数を設定します。 

1. `endpoint` 値には、次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、お使いのリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用できます。 

2. エンドポイントが正しいことを確認し、`token` の値を Azure アカウントの有効なサブスクリプション キーに置き換えます。 
 
3. 必要に応じて、`searchTerm` の値を置き換えて、検索クエリをカスタマイズすることができます。

```go
// Declare the main function. This is required for all Go programs.
func main() {
// Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

// The remaining code in this quickstart goes in the main function.

}
```

## <a name="construct-a-request"></a>要求の構築

このコードでは、HTTP 要求を宣言し、ヘッダーとペイロードを挿入して、クライアントをインスタンス化します。

```go
// Declare a new GET request.
req, err := http.NewRequest("GET", endpoint, nil)
if err != nil {
    panic(err)
}

// Add the payload to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the request header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

// Instantiate a client.  
client := new(http.Client)
```

## <a name="make-a-request"></a>要求を行う

このコードを使用して、Bing Web Search API を呼び出し、応答が返された後で接続を閉じます。

```go
// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

// Close the connection.
defer resp.Body.Close()
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}
```

## <a name="handle-the-response"></a>応答の処理

前に作成した構造体を使用して応答を書式設定し、検索結果を出力します。

```go
// Create a new answer.  
ans := new(BingAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
// Iterate over search results and print the result name and URL.
for _, result := range ans.WebPages.Value {
    fmt.Println(result.Name, "||", result.URL)
}
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

最後の手順で、コードを検証して実行します。 作成したコードを完全なプログラムと比較したい場合は、以下を参照してください。

```go
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)

// The is the struct for the data returned by Bing.
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}

// Verify the endpoint URI and replace the token string with a valid subscription key.  
func main() {
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // Add the payload to the request.  
    param := req.URL.Query()
    param.Add("q", searchTerm)
    req.URL.RawQuery = param.Encode()

    // Insert the request header.  
    req.Header.Add("Ocp-Apim-Subscription-Key", token)

    // Create a new client.  
    client := new(http.Client)

    // Send the request to Bing.  
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    // Close the response.
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(body, &ans)
    if err != nil {
         fmt.Println(err)
    }

    // Iterate over search results and print the result name and URL.
    for _, result := range ans.WebPages.Value {
         fmt.Println(result.Name, "||", result.URL)
    }

}
```

## <a name="example-json-response"></a>JSON の応答例

Bing Web Search API からの応答は、JSON として返されます。 このサンプル応答は、`BingAnswer` 構造体を使用して書式設定されており、`result.Name` と `result.URL` が表示されています。

```go
Microsoft Cognitive Services || https://www.microsoft.com/cognitive-services
Cognitive Services | Microsoft Azure || https://azure.microsoft.com/services/cognitive-services/
What is Microsoft Cognitive Services? | Microsoft Docs || https://docs.microsoft.com/azure/cognitive-services/Welcome
Microsoft Cognitive Toolkit || https://www.microsoft.com/en-us/cognitive-toolkit/
Microsoft Customers || https://customers.microsoft.com/en-us/search?sq=%22Microsoft%20Cognitive%20Services%22&ff=&p=0&so=story_publish_date%20desc
Microsoft Enterprise Services - Microsoft Enterprise || https://enterprise.microsoft.com/en-us/services/
Microsoft Cognitive Services || https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236
Cognitive Services - msdn.microsoft.com || https://msdn.microsoft.com/magazine/mt742868.aspx  
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bing Web Search API 単一ページ アプリのチュートリアル](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
