---
title: クイック スタート:Java 用の Bing Web Search SDK を使用する
titleSuffix: Azure Cognitive Services
description: Bing Web Search SDK を使用すると、Java アプリケーションに Bing Web Search を簡単に統合することができます。 このクイック スタートでは、要求を送信し、JSON 応答を受け取って、結果をフィルター処理および解析する方法を学習します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: aahi
ms.openlocfilehash: 0d2c1ca439dbbbd79ddabd2ea7a4eb5298d1071a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500965"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-java"></a>クイック スタート:Java 用の Bing Web Search SDK を使用する

Bing Web Search SDK を使用すると、Java アプリケーションに Bing Web Search を簡単に統合することができます。 このクイック スタートでは、要求を送信し、JSON 応答を受け取って、結果をフィルター処理および解析する方法を学習します。

今すぐコードを確認したい場合は、 GitHub で、[Java 用の Bing Web Search SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/)を入手できます。

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下のものが必要です。

* [JDK 7 または 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) または普段使用しているビルド オートメーション ツール
* サブスクリプション キー

## <a name="create-a-project-and-set-up-your-pom-file"></a>プロジェクトを作成して POM ファイルを設定する

Maven または普段使用しているビルド オートメーション ツールを使用して、新しい Java プロジェクトを作成します。 Maven を使用している場合は、以下の行を[プロジェクト オブジェクト モデル (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) ファイルに追加します。 すべての `mainClass` を実際のアプリケーションに置き換えます。

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
          <!--Your comment
            Replace the mainClass with the path to your java application.
            It should begin with com and doesn't require the .java extension.
            For example: com.bingwebsearch.app.BingWebSearchSample. This maps to
            The following directory structure:
            src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
          -->
          <mainClass>com.path.to.your.app.APP_NAME</mainClass>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.0</version>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>attached</goal>
            </goals>
            <configuration>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
              <archive>
                <manifest>
                  <!--Your comment
                    Replace the mainClass with the path to your java application.
                    For example: com.bingwebsearch.app.BingWebSearchSample.java.
                    This maps to the following directory structure:
                    src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
                  -->
                  <mainClass>com.path.to.your.app.APP_NAME.java</mainClass>
                </manifest>
              </archive>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.9.0</version>
    </dependency>
    <dependency>
      <groupId>commons-net</groupId>
      <artifactId>commons-net</artifactId>
      <version>3.3</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-websearch</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies>
```

## <a name="declare-dependencies"></a>依存関係の宣言

普段使用している IDE またはエディターでプロジェクトを開き、以下の依存関係をインポートします。

```java
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchAPI;
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchManager;
import com.microsoft.azure.cognitiveservices.search.websearch.models.ImageObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.NewsArticle;
import com.microsoft.azure.cognitiveservices.search.websearch.models.SearchResponse;
import com.microsoft.azure.cognitiveservices.search.websearch.models.VideoObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.WebPage;
```

プロジェクトを Maven で作成した場合、パッケージは既に宣言されています。 それ以外の場合は、ここでパッケージを宣言します。 例:

```java
package com.bingwebsearch.app
```

## <a name="declare-the-bingwebsearchsample-class"></a>BingWebSearchSample クラスの宣言

`BingWebSearchSample` クラスを宣言します。 これには、`main` メソッドなど、ほとんどのコードが含まれます。  

```java
public class BingWebSearchSample {

// The code in the following sections goes here.

}
```

## <a name="construct-a-request"></a>要求の構築

`BingWebSearchSample` クラスに存在する `runSample` メソッドは、要求を構築します。 次のコードをアプリケーションにコピーします。

```java
public static boolean runSample(BingWebSearchAPI client) {
    /*
     * This function performs the search.
     *
     * @param client instance of the Bing Web Search API client
     * @return true if sample runs successfully
     */
    try {
        /*
         * Performs a search based on the .withQuery and prints the name and
         * url for the first web pages, image, news, and video result
         * included in the response.
         */
        System.out.println("Searched Web for \"Xbox\"");
        // Construct the request.
        SearchResponse webData = client.bingWebs().search()
            .withQuery("Xbox")
            .withMarket("en-us")
            .withCount(10)
            .execute();

// Code continues in the next section...
```

## <a name="handle-the-response"></a>応答の処理

次に、応答を解析し、結果を出力するコードを追加してみましょう。 最初の Web ページ、画像、ニュース記事、および動画の `name` と `url` が出力されます (応答オブジェクトに含まれている場合)。

```java
/*
* WebPages
* If the search response has web pages, the first result's name
* and url are printed.
*/
if (webData != null && webData.webPages() != null && webData.webPages().value() != null &&
        webData.webPages().value().size() > 0) {
    // find the first web page
    WebPage firstWebPagesResult = webData.webPages().value().get(0);

    if (firstWebPagesResult != null) {
        System.out.println(String.format("Webpage Results#%d", webData.webPages().value().size()));
        System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
        System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
    } else {
        System.out.println("Couldn't find the first web result!");
    }
} else {
    System.out.println("Didn't find any web pages...");
}
/*
 * Images
 * If the search response has images, the first result's name
 * and url are printed.
 */
if (webData != null && webData.images() != null && webData.images().value() != null &&
        webData.images().value().size() > 0) {
    // find the first image result
    ImageObject firstImageResult = webData.images().value().get(0);

    if (firstImageResult != null) {
        System.out.println(String.format("Image Results#%d", webData.images().value().size()));
        System.out.println(String.format("First Image result name: %s ", firstImageResult.name()));
        System.out.println(String.format("First Image result URL: %s ", firstImageResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first image result!");
    }
} else {
    System.out.println("Didn't find any images...");
}
/*
 * News
 * If the search response has news articles, the first result's name
 * and url are printed.
 */
if (webData != null && webData.news() != null && webData.news().value() != null &&
        webData.news().value().size() > 0) {
    // find the first news result
    NewsArticle firstNewsResult = webData.news().value().get(0);
    if (firstNewsResult != null) {
        System.out.println(String.format("News Results#%d", webData.news().value().size()));
        System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
        System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
    } else {
        System.out.println("Couldn't find the first news result!");
    }
} else {
    System.out.println("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response has videos, the first result's name
 * and url are printed.
 */
if (webData != null && webData.videos() != null && webData.videos().value() != null &&
        webData.videos().value().size() > 0) {
    // find the first video result
    VideoObject firstVideoResult = webData.videos().value().get(0);

    if (firstVideoResult != null) {
        System.out.println(String.format("Video Results#%s", webData.videos().value().size()));
        System.out.println(String.format("First Video result name: %s ", firstVideoResult.name()));
        System.out.println(String.format("First Video result URL: %s ", firstVideoResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first video result!");
    }
} else {
    System.out.println("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>main メソッドの宣言

このアプリケーションの main メソッドには、クライアントをインスタンス化し、`subscriptionKey` を検証して、`runSample` を呼び出すコードが含まれています。 続行する前に、ご自分の Azure アカウントの有効なサブスクリプション キーを入力してください。

```java
public static void main(String[] args) {
    try {
        // Enter a valid subscription key for your account.
        final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
        // Instantiate the client.
        BingWebSearchAPI client = BingWebSearchManager.authenticate(subscriptionKey);
        // Make a call to the Bing Web Search API.
        runSample(client);
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
}
```

## <a name="run-the-program"></a>プログラムの実行

最後の手順で、プログラムを実行します。

```console
mvn compile exec:java
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このプロジェクトの使用を終了するときに、プログラムのコードからサブスクリプション キーを必ず削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Cognitive Services の Java 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)

## <a name="see-also"></a>関連項目

* [Azure Java SDK リファレンス](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingwebsearchapi?view=azure-java-stable)