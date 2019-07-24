---
title: クイック スタート:Bing News Search REST API と Go を使用してニュースを取得する
titleSuffix: Azure Cognitive Services
description: Bing News Search API からニュースの結果を取得する方法について説明します。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 2/21/2019
ms.author: rosh
ms.openlocfilehash: 295c32c1e14dc6a69a37040f92d27a6862359228
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545052"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>クイック スタート:Bing News Search REST API と Go を使用してニュースの結果を取得する

このクイック スタートでは、Go 言語を使用して Bing News Search API を呼び出します。 結果には、クエリ文字列によって識別されるニュース ソースの名前と URL が含まれます。

## <a name="prerequisites"></a>前提条件
* [Go バイナリ](https://golang.org/dl/)をインストールする
* 結果を表示するためのプリティ プリンター用の go-spew ライブラリをインストールする
    * ライブラリ `$ go get -u https://github.com/davecgh/go-spew` をインストールする

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-libraries"></a>プロジェクトの作成とライブラリのインポート

お使いの IDE またはエディターで新しい Go プロジェクトを作成します。 次に、要求のための `net/http`、応答を読み取るための `ioutil`、結果の JSON テキストを処理するための `encoding/json` をインポートします。 go-spew ライブラリは JSON を解析するために必要です。 

```
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>ニュースの検索結果を書式設定する構造体を作成する

`NewsAnswer` 構造体は、応答で提供されるデータを書式設定します。 応答の JSON はマルチレベルであり、非常に複雑です。  次の実装は、基本事項を示しています。

```
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
        } `json: "image"` 
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>main 関数の宣言と変数の定義  

次のコードは main 関数を宣言し、必要な変数を割り当てます。 エンドポイントが正しいことを確認し、`token` の値を Azure アカウントの有効なサブスクリプション キーに置き換えます。

```
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>クエリとヘッダー

クエリ文字列とアクセス キー ヘッダーを追加する

```
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>Get 要求

クライアントを作成して Get 要求を送信する 

```
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>要求を送信する

`ioutil` を使用して要求を送信して応答を読み取ります。

```
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
resbody, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>応答の処理

`Unmarshall` 関数は、News Search API から返された JSON テキストから情報を抽出します。  `go-spew` プリティ プリンターを使用して結果のノードを表示できます。

```
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>結果

結果には、各結果の名前と URL が含まれます。

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing News Search とは](search-the-web.md)
