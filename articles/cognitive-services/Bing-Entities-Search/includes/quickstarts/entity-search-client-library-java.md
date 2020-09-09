---
title: Bing Entity Search Java クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: f69b9b989a93949f9a0441676c81af7480fb968f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375305"
---
このクイックスタートは、Bing Entity Search の Java 用クライアント ライブラリからエンティティを検索するのに使用できます。 Bing Entity Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch) にあります。

## <a name="prerequisites"></a>前提条件

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Bing Entity Search の Java 用クライアント ライブラリ

Bing Entity Search クライアント ライブラリの依存関係は、Maven、Gradle、またはその他の依存関係管理システムを使用してをインストールできます。 Maven POM ファイルには、次の宣言が必要です。

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


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

1. `dominantEntityLookup` クライアントを実装します。これには API エンドポイントと `ServiceClientCredentials` クラスのインスタンスが必要となります。 以下のグローバル エンドポイントを使用するか、Azure portal に表示される、リソースの[カスタム サブドメイン](../../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用できます。

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
        List<Thing> entries = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entries) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](../../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../../overview.md)
