---
title: Bing Image Search の API を使用した GIF 画像の検索
titleSuffix: Azure Cognitive Services
description: Bing Image Search API では、最も関連性の高い .gif 画像を探し、Web 全体を検索することもできます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d1fcac891db240def2e7bbdcb45b45caf7f49a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341905"
---
# <a name="search-for-gif-images"></a>GIF 画像を検索する 

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Image Search API では、最も関連性の高い .gif 画像を探し、Web 全体を検索することもできます。  開発者は、さまざまな会話シナリオで、人を引きつける gif を統合できます。 

次の URL は、アニメーション .gif 画像のクエリです。
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
[q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) パラメーターによって検索語句が指定されます。  上記のクエリでは、[imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) フィルター パラメーターによって `animatedGif` も指定されます。

結果の例を見るには、次の URL で bing.com を検索してください。
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>クエリ パラメーター

クエリのパラメーターとオプションに関する詳細については、[Image Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters) ページをご覧ください。 サンプルは、「[Java を利用したアニメーション gif のサンプル検索](#gifExample)」という見出しの下にあります。

## <a name="tips-and-suggestions"></a>ヒントと検索候補

- [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) パラメーターと [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) パラメーターを指定できます。 Microsoft では、インデックスの gif の多くが 2 MB 以下です。maxFileSize を 2000000 に設定することをお勧めします。  また、モバイル通信シナリオなど、帯域幅が問題となる場合でデータ サイズの制御に役立ちます。
- ソース URL を読み込む前にサムネイルを読み込むと、パフォーマンスの改善が体感されます。  
- 最初の実行や待ち受けページでユーザー クエリがまだない場合、Microsoft の注目の gif 検索をお試しください。[注目の画像 API](trending-images.md) がお役に立ちます。
- [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) パラメーターには 3 つの設定があります。  `strict` オプションで成人向けコンテンツがブロックされます。
- サポートされている言語と場所の完全一覧が必要な場合、[mkt](./language-support.md) をご覧ください。
- *AnimatedGifHttps* は、https アドレスを出典とするアニメーション gif 画像のみを返します。 安全のため、アプリケーションの多くは、外部 Web リンクに接続するとき、https 経由を要求します。 たとえば、Apple App Store は Web サービスに接続するとき、転送中のユーザー データを暗号化する HTTPS を要求します。

<a name="gifExample"></a>

## <a name="example-search-for-animated-gif-using-java"></a>Java を利用したアニメーション gif のサンプル検索

次の URL では、アニメーション .gif 画像 `q=interesting` が検索されます。
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
次の例に示すように、この URL クエリは [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers) ヘッダーを要求します。

次の Java サンプルは要求をビルドし、送信します。

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>結果
このコードは、次の結果を JSON オブジェクトとして取得します。

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>次のステップ
- [C# のクイック スタート](quickstarts/csharp.md)
- [Image Search の単一ページ アプリケーションのチュートリアル](tutorial-bing-image-search-single-page-app.md)