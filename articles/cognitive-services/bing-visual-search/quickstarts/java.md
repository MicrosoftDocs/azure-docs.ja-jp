---
title: クイック スタート:Bing Visual Search REST API と Java を使用して画像に関する分析情報を取得する
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API に画像をアップロードし、画像に関する分析情報を取得する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 2fe4e9dad0b198fe54e06ce07100d231f1f7d157
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046446"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>クイック スタート:Bing Visual Search REST API と Java を使用して画像に関する分析情報を取得する

このクイック スタートを使用すると、Bing Visual Search API への最初の呼び出しを行い、結果を表示することができます。 この Java アプリケーションは、API に画像をアップロードし、返された情報を表示するものです。 このアプリケーションは Java で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

ローカルの画像をアップロードする際には、フォーム データに `Content-Disposition` ヘッダーが含まれている必要があります。 その `name` パラメーターは "image" に設定する必要があります。`filename` パラメーターは任意の文字列に設定できます。 フォームの内容には、画像のバイナリ データが含まれます。 アップロードできる画像の最大サイズは、1 MB です。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>前提条件

* [Java Development Kit(JDK) 7 または 8](https://aka.ms/azure-jdks)
* [Gson Java ライブラリ](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成し、以下のライブラリをインポートします。

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. API エンドポイント、サブスクリプション キー、および画像へのパスを格納する変数を作成します。

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>JSON パーサーを作成する

API からの JSON 応答を `JsonParser` を使って読みやすくするためのメソッドを作成します。

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>検索要求とクエリを構築する

1. アプリケーションのメイン メソッドでは、`HttpClientBuilder.create().build();` を使用して HTTP クライアントを作成します。

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. 画像を API にアップロードするための `HttpEntity` オブジェクトを作成します。

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. エンドポイントを使って `httpPost` オブジェクトを作成し、サブスクリプション キーを使用するようにヘッダーを設定します。

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>JSON 応答の受信と処理

1. `HttpClient.execute()` メソッドを使用して API に要求を送信し、応答を `InputStream` オブジェクトに格納します。
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. JSON 文字列を格納し、応答を出力します。

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Visual Search のシングルページ Web アプリを作成する](../tutorial-bing-visual-search-single-page-app.md)
