---
title: クイック スタート:Bing Entity Search SDK for Java を使用してエンティティを検索する
titleSuffix: Azure Cognitive Services
description: Bing Entity Search SDK for Java を使用してエンティティを検索する場合は、このクイック スタートを使用します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 63312dcbad515f364bd4b66c5541f1db90af85b6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479041"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>クイック スタート:Bing Entity Search SDK for Java を使用して検索要求を送信する

Bing Entity Search SDK for Java を使用してエンティティの検索を始める場合は、このクイック スタートを使用します。 Bing Entity Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch) にあります。

## <a name="prerequisites"></a>前提条件

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Bing Entity Search SDK for Java

Maven、Gradle、または別の依存関係管理システムを使用して Bing Entity Search SDK の依存関係をインストールします。 Maven POM ファイルには、次の宣言が必要です。

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成し、以下のライブラリをインポートします。

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. サブスクリプション キーの変数を作成します

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>検索クライアントを作成する

1. `dominantEntityLookup` クライアントを実装します。これには API エンドポイントと `ServiceClientCredentials` クラスのインスタンスが必要となります。

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
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
      ## <a name="send-a-request-and-receive-a-response"></a>要求を送信して応答を受信する

1. サブスクリプション キーを使用して検索クライアントの新しいインスタンスを作成します。 `client.entities().search()` を使用して検索クエリ `satya nadella` に対する検索要求を送信し、応答を受け取ります。 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. エンティティが返された場合は、リストに変換します。 それを反復処理し、主要なエンティティを表示します。

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../overview.md )
