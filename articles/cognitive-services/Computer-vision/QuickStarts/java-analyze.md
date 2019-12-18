---
title: クイック スタート:REST API と Java でリモート画像を分析する
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Java で Computer Vision API を使用して画像を分析します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 526cc8ca002b3b6c2821eb842b0c6a7794f2cd33
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968988"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-java"></a>クイック スタート:Computer Vision の REST API と Java を使用してリモート画像を分析する

このクイックスタートでは、Java と Computer Vision の REST API を使用して、リモートに格納されている画像を分析し、視覚的特徴を抽出します。 [画像分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)メソッドを使うと、画像の内容に基づいて視覚的特徴を抽出できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) を作成してください。

## <a name="prerequisites"></a>前提条件

- [Java&trade; プラットフォーム、Standard Edition Development Kit 7 または 8](https://aka.ms/azure-jdks) (JDK 7 または 8) をインストールしている必要があります。
- Computer Vision のサブスクリプション キーが必要です。 無料試用版のキーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。 次に、キーとサービス エンドポイント文字列用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

## <a name="create-and-run-the-sample-application"></a>サンプル アプリケーションを作成して実行する

このサンプルを作成して実行するには、次の手順を実行します。

1. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成します。 オプションを使用できる場合は、コマンド ライン アプリケーション テンプレートから Java プロジェクトを作成します。
1. 次のライブラリを Java プロジェクトにインポートします。 Maven を使用している場合、Maven 座標はライブラリごとに提供されます。
   - [Apache HTTP client](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache HTTP core](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON library](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. プロジェクトの `Main` パブリック クラスを含むファイルに、次の `import` ステートメントを追加します。  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. `Main` パブリック クラスを次のコードに置き換えます。
1. 必要に応じて、`imageToAnalyze` 値を、分析したい別の画像の URL に置き換えます。

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    String endpoint = ("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + 
            "vision/v2.1/analyze";
    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="compile-and-run-the-program"></a>プログラムをコンパイルして実行する

1. 保存し、Java プロジェクトをビルドします。
1. IDE を使用している場合は、`Main` を実行します。

または、コマンド ライン ウィンドウからプログラムを実行する場合は、次のコマンドを実行します。 これらのコマンドでは、`Main.java` と同じフォルダー内の `libs` という名前のフォルダーにライブラリが含まれていると想定しています。そうでない場合は、`libs` をご使用のライブラリへのパスに置き換える必要があります。

1. `Main.java` ファイルをコンパイルします。

    ```bash
    javac -cp ".;libs/*" Main.java
    ```

1. プログラムを実行します。 QnA Maker API に要求が送信され、KB が作成された後、30 秒おきに結果がポーリングされます。 それぞれの応答は、コマンド ライン ウィンドウに出力されます。

    ```bash
    java -cp ".;libs/*" Main
    ```

## <a name="examine-the-response"></a>結果の確認

成功応答が JSON で返されます。 サンプル アプリケーションによって成功応答が解析され、次の例のようにコンソール ウィンドウに表示されます。

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="next-steps"></a>次の手順

Computer Vision を使用して、光学文字認識 (OCR) を実行し、スマートにクロップされたサムネイルを作成するほか、イメージ内の視覚的な特徴 (顔など) を検出、カテゴライズ、タグ付け、および記述する Java Swing アプリケーションについて説明します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API Java チュートリアル](../Tutorials/java-tutorial.md)
