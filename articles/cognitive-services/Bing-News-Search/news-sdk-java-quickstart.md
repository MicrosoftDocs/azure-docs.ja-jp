---
title: クイック スタート:ニュース検索を実行する - Bing News Search SDK for Java
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、Bing News Search SDK for Java を使ってニュースを検索し、応答を処理します。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 648e1701154c725a0645bd520045238030a71ea9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108697"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>クイック スタート:Bing News Search SDK for Java でニュースを検索する

このクイック スタートを使用して、Bing News Search SDK for Java を使ったニュース検索を始めましょう。 Bing News Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) にあります。

## <a name="prerequisites"></a>前提条件

Maven、Gradle、または別の依存関係管理システムを使用して Bing News Search SDK の依存関係をインストールします。 Maven POM ファイルには、次の宣言が必要です。

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

普段使用している IDE またはエディターで新しい Java プロジェクトを作成し、以下のライブラリをインポートします。

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>検索クライアントの作成と資格情報の格納

1. 新しい `NewsSearchAPIImpl` 検索クライアントを返す `getClient()` というメソッドを作成します。 新しい `NewsSearchAPIImpl` オブジェクトの第 1 パラメーターとしてエンドポイントを追加し、さらに資格情報を格納するための新しい `ServiceClientCredentials` オブジェクトを追加します。

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. `ServiceClientCredentials` オブジェクトを作成するには、`applyCredentialsFilter()` 関数をオーバーライドします。 このメソッドに `OkHttpClient.Builder` を渡し、ビルダーの `addNetworkInterceptor()` メソッドを使用して、SDK 呼び出しのための資格情報を作成します。

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>検索要求の送信と受信

1. `getClient()` を呼び出すメソッドを作成し、Bing News Search サービスに検索要求を送信します。 *market* パラメーターと *count* パラメーターで検索にフィルターを適用し、最初のニュース結果に関する情報 (名前、URL、公開日、説明、提供元の名前、検索の推定一致数の合計) を出力します。

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. 検索メソッドを `main()` メソッドに追加してコードを実行します。

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](tutorial-bing-news-search-single-page-app.md)
