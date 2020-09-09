---
title: Bing Video Search Java クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 2b3d4993406f150b2983d4d820f7d070b5de1e96
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375589"
---
このクイックスタートでは、Bing Video Search の Java 用のクライアント ライブラリを使用してニュースを検索する方法を説明します。 Bing Video Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch) にあり、追加の注釈と機能があります。

## <a name="prerequisites"></a>前提条件

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Gson ライブラリ](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

Bing Video Search クライアント ライブラリの依存関係は、Maven、Gradle、またはその他の依存関係管理システムを使用してをインストールできます。 Maven POM ファイルには、次の宣言が必要です。

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化


普段使用している IDE またはエディターで新しい Java プロジェクトを作成し、以下のライブラリをインポートします。

```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
```

## <a name="create-a-search-client"></a>検索クライアントを作成する

1. `VideoSearchAPIImpl` クライアントを実装します。これには API エンドポイントと `ServiceClientCredentials` クラスのインスタンスが必要となります。

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    `ServiceClientCredentials` を実装するには、次の手順に従います。

    1. `OkHttpClient.Builder` オブジェクトをパラメーターとして `applyCredentialsFilter()` 関数をオーバーライドします。 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. `applyCredentialsFilter()` 内で `builder.addNetworkInterceptor()` を呼び出します。 新しい `Interceptor` オブジェクトを作成し、その `intercept()` メソッドをオーバーライドして、`Chain` インターセプター オブジェクトを受け取るようにします。

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. `intercept` 関数内に、要求のための変数を作成します。 `Request.Builder()` を使用して要求を作成します。 `Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加し、要求オブジェクトで `chain.proceed()` を返します。
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```

## <a name="send-a-search-request-and-receive-the-response"></a>検索要求を送信して応答を受信する 

1. 文字列としてサブスクリプション キーを受け取る `VideoSearch()` という関数を作成します。 先ほど作成した検索クライアントをインスタンス化します。
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. `VideoSearch()` 内で、このクライアントを使用して動画検索要求を送信します。その際、検索語句として `SwiftKey` を指定します。 Video Search API から結果が返された場合は、最初の結果を取得してその ID、名前、URL を、返された動画の総数と共に出力します。 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. この検索メソッドを main メソッドから呼び出します。

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングル ページ Web アプリを作成する](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>関連項目 

* [Bing Video Search API とは](../../overview.md)
* [Cognitive Services の .NET 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
