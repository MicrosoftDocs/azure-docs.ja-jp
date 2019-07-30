---
title: クイック スタート:Ink Recognizer REST API および Java を使用したデジタル インクの認識
titleSuffix: Azure Cognitive Services
description: Ink Recognizer API を使用して、デジタル インク ストロークの認識を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: b1c739b6355d3b32063e5289720ed1d191611e65
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721233"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>クイック スタート:Ink Recognizer REST API および Java を使用したデジタル インクの認識

このクイックスタートは、デジタル インク ストロークで Ink Recognizer API の使用を始めるときに使用します。 この Java アプリケーションは、JSON 形式のインク ストローク データを含む API 要求を送信し、応答を取得します。

このアプリケーションは Java で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

通常、この API はデジタル インキング アプリから呼び出します。 このクイックスタートでは、JSON ファイルから次の手書きサンプルのインク ストローク データを送信します。

![手書きテキストのイメージ](../media/handwriting-sample.jpg)

このクイック スタートのソース コードは、[GitHub](https://go.microsoft.com/fwlink/?linkid=2089904) にあります。

## <a name="prerequisites"></a>前提条件

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 以降。

- Maven リポジトリから以下のライブラリをインポートします
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json) パッケージ
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) パッケージ

- このクイックスタートのインク ストローク データのサンプルは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json) にあります。

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

1. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成し、以下のライブラリをインポートします。

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. サブスクリプション キーとエンドポイントの変数を作成します。 インク認識に使用できる URI を以下に示します。 これは、API 要求 URL を作成するために、後からサービス エンドポイントに付加されます。

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>要求を送信する関数を作成する

1. 上記で作成した変数を取る `sendRequest()` という新しい関数を作成します。 次に、以下の手順のようにします。

2. API に要求を送信できる `CloseableHttpClient` オブジェクトを作成します。 エンドポイントと Ink Recognizer URL を組み合わせて、`HttpPut` 要求オブジェクトに要求を送信します。

3. 要求の `setHeader()` 関数を使用して、`Content-Type` ヘッダーを `application/json` に設定し、サブスクリプション キーを `Ocp-Apim-Subscription-Key` ヘッダーに追加します。

4. 送信するデータに対して要求の `setEntity()` 関数を使用します。   

5. クライアントの `execute()` 関数を使用して、要求を送信し、それを `CloseableHttpResponse` オブジェクトに格納します。 

6. `HttpEntity` オブジェクトを作成して、応答の内容を格納します。 `getEntity()` で内容を取得します。 応答が空でない場合は、それを返します。
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.setEntity(new StringEntity(requestData));
            try (CloseableHttpResponse response = client.execute(request)) {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    return EntityUtils.toString(respEntity, "utf-8");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            }
        } catch (IOException ex) {
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>インク認識要求を送信する

`recognizeInk()` というメソッドを作成し、インク ストローク データを認識します。 自身のエンドポイント、URL、サブスクリプション キー、および JSON データを使用して、上記で作成した `sendRequest()` メソッドを呼び出します。 結果を取得し、それをコンソールに出力します。

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>デジタル インク データを読み込み、要求を送信する

1. アプリケーションの main メソッドで、要求に追加されるデータを含む JSON ファイルを読み取ります。

2. 上記で作成したインク認識関数を呼び出します。
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>アプリケーションを実行し、応答を表示する

アプリケーションを実行します。 成功応答が JSON 形式で返されます。 JSON 応答は [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json) でも確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2089907)


デジタル インキング アプリで Ink Recognition API がどのように動作するかを確認するには、GitHub 上の次のサンプル アプリケーションを参照してください。
* [C# およびユニバーサル Windows プラットフォーム (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# および Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript Web ブラウザー アプリ](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java および Android モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift および iOS モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089805)
