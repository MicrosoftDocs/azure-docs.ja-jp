---
title: チュートリアル:REST API と C# を使用して画像の詳細を抽出する - Bing Image Search
titleSuffix: Azure Cognitive Services
description: この記事では、Bing Image Search API を使用して画像の詳細を抽出する C# アプリケーションを作成します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.openlocfilehash: 9f707dd6b93080e550b4f75e7c9c23139b8adf1d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930684"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>チュートリアル:Bing Image Search API と C# を使用して画像の詳細情報を抽出する

Bing Image Search API で使用できる[エンドポイント](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint)は複数があります。 `/details` エンドポイントは画像を含む POST 要求を受け取ります。また、画像に関するさまざまな詳細を返すことができます。 この C# アプリケーションは、この API を使用して画像を送信し、Bing から返された詳細を表示します。これらは次のような JSON オブジェクトです。

![[JSON の結果]](media/cognitive-services-bing-images-api/jsonResult.jpg)

このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * `POST` 要求で Image Search `/details` エンドポイントを使用する
> * 要求のヘッダーを指定する
> * URL パラメーターを使用して結果を指定する
> * 画像データをアップロードし、`POST` 要求を送信する
> * コンソールに JSON の結果を出力する

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017 またはそれ以降](https://visualstudio.microsoft.com/downloads/)の任意のエディション。

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>画像の詳細の検索要求を構築する

以下は、要求の本文に画像データがある POST 要求を受け取る `/details` エンドポイントです。 以下のグローバル エンドポイントを使用するか、Azure portal に表示される、リソースの[カスタム サブドメイン](../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用できます。
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

検索要求の URL を作成する場合、`modules` パラメーターを上記のエンドポイントの次に指定し、結果に含める詳細の種類を指定します。

* `modules=All`
* `modules=RecognizedEntities` (画像に表示されている人または場所)

POST 要求で `modules=All` を指定して、以下を含む JSON テキストを取得します。

* `bestRepresentativeQuery` - アップロードされた画像に似ている画像を返す Bing クエリ
* `detectedObjects` - 画像に含まれるオブジェクト
* `image` - 画像のメタデータ
* `imageInsightsToken` - 後で画像から `RecognizedEntities` (画像に表示される人または場所) を取得する GET 要求のトークン。
* `imageTags` - 画像のタグ
* `pagesIncluding` - 画像を含む Web ページ
* `relatedSearches` - 画像内の詳細に基づいて検索します。
* `visuallySimilarImages` - Web 上の類似画像。

POST 要求で `modules=RecognizedEntities` を指定して `imageInsightsToken` のみを取得します。これは後続の GET 要求で画像内の人や場所を識別するために使用できます。

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>WebClient オブジェクトを作成し、API 要求のヘッダーを設定する

`WebClient` オブジェクトを作成し、ヘッダーを設定します。 Bing Search API に対するすべての要求に `Ocp-Apim-Subscription-Key` が必要です。 画像をアップロードする `POST` 要求では、`ContentType: multipart/form-data` を指定する必要もあります。

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>画像をアップロードして結果を表示する

`WebClient` クラスの `UpLoadFile()` メソッドは、`RequestStream` の書式設定、`HttpWebRequest` の呼び出しなど、`POST` 要求に合わせてデータの書式を設定します。

`/details` エンドポイントおよびアップロードする画像ファイルを指定して `WebClient.UpLoadFile()` を呼び出します。 JSON 応答を使用して `SearchResult` 構造体のインスタンスを初期化し、応答を格納します。

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
この JSON 応答は、コンソールに出力することができます。

## <a name="use-an-image-insights-token-in-a-request"></a>要求に画像の分析情報トークンを使用する

`POST` の結果で返された `ImageInsightsToken` を使用するには、`GET` 要求に追加することができます。 例:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

画像内に識別可能な人物または場所が含まれる場合、この要求はそれらに関する情報を返します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一ページの Web アプリで画像と検索オプションを表示する](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目

* [Bing Image Search API リファレンス](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
