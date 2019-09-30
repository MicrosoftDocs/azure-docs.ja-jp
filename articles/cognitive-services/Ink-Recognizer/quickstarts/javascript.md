---
title: クイック スタート:Ink Recognizer REST API および Node.js を使用したデジタル インクの認識
titleSuffix: Azure Cognitive Services
description: Ink Recognizer API を使用して、デジタル インク ストロークの認識を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 5e3b97faaed84f2c07ea70ddb73bd8e8c9efa71d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212645"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>クイック スタート:Ink Recognizer REST API および JavaScript を使用したデジタル インクの認識

このクイックスタートは、デジタル インク ストロークで Ink Recognizer API の使用を始めるときに使用します。 この JavaScript アプリケーションは、JSON 形式のインク ストローク データを含む API 要求を送信し、応答を表示します。

このアプリケーションは JavaScript で記述され、Web ブラウザーで実行されますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

通常、この API はデジタル インキング アプリから呼び出します。 このクイックスタートでは、JSON ファイルから次の手書きサンプルのインク ストローク データを送信します。

![手書きテキストのイメージ](../media/handwriting-sample.jpg)

このクイック スタートのソース コードは、[GitHub](https://go.microsoft.com/fwlink/?linkid=2089905) にあります。

## <a name="prerequisites"></a>前提条件

- Web ブラウザー
- このクイックスタートのインク ストローク データのサンプルは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json) にあります。


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

1. 好みの IDE またはエディターで、新しい `.html` ファイルを作成します。 次に、後で追加するコードのための基本的な HTML を追加します。
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. `<body>` タグ内に、次の html を追加します。
    1. JSON の要求と応答を表示するための 2 つのテキスト領域。
    2. 後で作成する `recognizeInk()` 関数を呼び出すためのボタン。
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>JSON データの例を読み込む

1. `<script>` タグ内に、sampleJson 用の変数を作成します。 次に、エクスプローラーを開いて JSON ファイルを選択できるようにする、`openFile()` という名前の JavaScript 関数を作成します。 `Recognize ink` ボタンをクリックすると、この関数が呼び出されて、ファイルの読み取りが開始されます。
2. `FileReader` オブジェクトの `onload()` 関数を使用して、ファイルを非同期的に処理します。 
    1. ファイル内の `\n` または `\r` 文字は、空の文字列に置き換えます。 
    2. `JSON.parse()` を使用して、テキストを有効な JSON に変換します
    3. アプリケーションで `request` テキスト ボックスを更新します。 `JSON.stringify()` を使用して、JSON 文字列の書式を設定します。 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Ink Recognizer API に要求を送信する

1. `<script>` タグ内に、`recognizeInk()` という関数を作成します。 この関数は後で API を呼び出し、応答でページを更新します。 この関数内に、以下の手順のコードを追加します。 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. エンドポイント URL、サブスクリプション キー、サンプル JSON 用の変数を作成します。 次に、API 要求を送信するための `XMLHttpRequest` オブジェクトを作成します。 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. `XMLHttpRequest` オブジェクトの戻り関数を作成します。 この関数では、成功した要求からの API 応答を解析し、アプリケーションでそれを表示します。 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. 要求オブジェクトのエラー関数を作成します。 この関数では、コンソールにエラーを出力します。 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. 要求オブジェクトの `onreadystatechange` プロパティ用の関数を作成します。 要求オブジェクトの準備状態が変化すると、上記の戻り関数とエラー関数が適用されます。
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. API 要求を送信すします。 サブスクリプション キーを `Ocp-Apim-Subscription-Key` ヘッダーに追加し、`content-type` を `application/json` に設定します
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>アプリケーションを実行し、応答を表示する

このアプリケーションは Web ブラウザー内で実行できます。 成功応答が JSON 形式で返されます。 JSON 応答は [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json) でも確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2089907)

デジタル インキング アプリで Ink Recognition API がどのように動作するかを確認するには、GitHub 上の次のサンプル アプリケーションを参照してください。
* [C# およびユニバーサル Windows プラットフォーム (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# および Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript Web ブラウザー アプリ](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java および Android モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift および iOS モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089805)
