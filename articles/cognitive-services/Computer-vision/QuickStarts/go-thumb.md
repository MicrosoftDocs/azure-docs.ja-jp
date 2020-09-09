---
title: クイック スタート:サムネイルの生成 - REST、Go
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision API と Go を使って、画像からサムネイルを生成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e55968f94df820f4d1e2708c0b52283aed3cc903
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835189"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-with-go"></a>クイック スタート:Computer Vision の REST API と Go を使用してサムネイルを生成する

このクイックスタートでは、Computer Vision の REST API を使用して、画像からサムネイルを生成します。 その際に指定する高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision では、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域に基づいてトリミングの座標を生成します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Go](https://golang.org/dl/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* キーとエンドポイント URL 用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。


## <a name="create-and-run-the-sample"></a>サンプルの作成と実行

このサンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコードをコピーします。
1. 必要に応じて、サムネイルを生成する別のイメージの URL で `imageUrl` 値を置き換えます。
1. `.go` 拡張子のファイルとして、コードを保存します。 たとえば、「 `get-thumbnail.go` 」のように入力します。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`go build` コマンドを実行して、ファイルからパッケージをコンパイルします。 たとえば、「 `go build get-thumbnail.go` 」のように入力します。
1. プロンプトで、コンパイル済みのパッケージを実行します。 たとえば、「 `get-thumbnail` 」のように入力します。

```go
package main

import (
    "bytes"
    "fmt"
    "io/ioutil"
    "io"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")

    uriBase := endpoint + "vision/v3.0/generateThumbnail"
    const imageUrl = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    uri := uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }
    
    // Convert byte[] to io.Reader type
    readerThumb := bytes.NewReader(data)

    // Write the image binary to file
    file, err := os.Create("thumb_local.png")
    if err != nil { log.Fatal(err) }
    defer file.Close()
    _, err = io.Copy(file, readerThumb)
    if err != nil { log.Fatal(err) }

    fmt.Println("The thunbnail from local has been saved to file.")
    fmt.Println()
}
```

## <a name="examine-the-response"></a>結果の確認

成功応答には、サムネイル画像のバイナリ データが格納されます。 要求が失敗した場合は、原因の特定につながるエラー コードとメッセージが応答に格納されます。

## <a name="next-steps"></a>次のステップ

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出を実行する Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
