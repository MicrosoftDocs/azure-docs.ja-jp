---
title: Bing Image Search API v5 から v7 へのアップグレード
titleSuffix: Azure Cognitive Services
description: このアップグレード ガイドでは、Bing Image Search API のバージョン 5 とバージョン 7 の間の変更点について説明します。 このガイドは、バージョン 7 を使用するために更新する必要のあるアプリケーションの部分を識別するのに役立ちます。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d10db37934bb0d6571eb0191d5f1be47dae000ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342177"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Bing Image Search API v7 のアップグレード ガイド

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

このアップグレード ガイドでは、Bing Image Search API のバージョン 5 とバージョン 7 の間の変更点を示します。 このガイドは、バージョン 7 を使用するために更新する必要のあるアプリケーションの部分を識別するのに役立ちます。

## <a name="breaking-changes"></a>重大な変更

### <a name="endpoints"></a>エンドポイント

- エンドポイントのバージョン番号は、v5 から v7 に変更されました。 たとえば、https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search のようになっています。

### <a name="error-response-objects-and-error-codes"></a>エラー応答オブジェクトとエラー コード

- すべての失敗した要求には、応答本文内に `ErrorResponse` オブジェクトが含まれるようになりました。

- `Error` オブジェクトに次のフィールドが追加されました。  
  - `subCode`&mdash;エラー コードを別個のバケットに分割します (可能な場合)
  - `moreDetails`&mdash;`message` フィールドで説明されているエラーに関する追加情報


- v5 エラー コードを次の可能な `code` および `subCode` 値に置き換えました。

|コード|サブコード|説明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|サブコード条件のいずれかが発生するたびに、Bing は ServerError を返します。 HTTP 状態コードが 500 の場合、応答にはこれらのエラーが含まれます。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blocked|要求の一部が有効でない場合に Bing は InvalidRequest を返します。 たとえば、必要なパラメーターが不足している場合や、パラメーター値が無効な場合です。<br/><br/>エラーが ParameterMissing または ParameterInvalidValue の場合、HTTP 状態コードは 400 です。<br/><br/>エラーが HttpNotAllowed の場合、HTTP 状態コードは 410 です。
|RateLimitExceeded||1 秒あたりのクエリ数 (QPS) または 1 か月あたりのクエリ数 (QPM) のクォータを超えると、Bing は RateLimitExceeded を返します。<br/><br/>QPS を超えた場合、Bing は HTTP 状態コード 429 を返し、QPM を超過した場合に 403 を返します。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing は、呼び出し元を認証できない場合に InvalidAuthorization を返します。 たとえば、`Ocp-Apim-Subscription-Key` ヘッダーがない場合や、サブスクリプション キーが無効な場合です。<br/><br/>冗長性は、複数の認証方法を指定した場合に発生します。<br/><br/>エラーが InvalidAuthorization の場合、HTTP 状態コードは 401 です。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|呼び出し元がリソースに対するアクセス許可を備えていない場合、Bing は InsufficientAuthorization を返します。 これは、サブスクリプション キーが無効になっているか、期限が切れている場合に発生することがあります。 <br/><br/>エラーが InsufficientAuthorization の場合、HTTP 状態コードは 403 です。

- 以前のエラー コードと新しいコードのマッピングを次に示します。 v5 エラー コードに依存していた場合は、コードを適宜更新してください。

|バージョン 5 コード|バージョン 7 コード.サブコード
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
無効|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blocked|InvalidRequest.Blocked



### <a name="query-parameters"></a>クエリ パラメーター

- `modulesRequested` クエリ パラメーターの名前が [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) に変更されました。  

- Annotations の名前が Tags に変更されました。 Tags に対する [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) クエリ パラメーターについての説明を参照してください。  

- ShoppingSources フィルター値をサポートしている市場の一覧が、en-US のみに変更されました。 [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) に関する説明を参照してください。  


### <a name="image-insights-changes"></a>イメージの分析情報の変更

- [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) の `annotations` フィールドの名前が `imageTags` に変更されました。  

- `AnnotationModule` オブジェクトの名前が [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule) に変更されました。  

- `Annotation` オブジェクトの名前が [Tag](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag) に変更され、`confidence` フィールドが削除されました。  

- [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) オブジェクトの `insightsSourcesSummary` フィールドの名前が `insightsMetadata` に変更されました。  

- `InsightsSourcesSummary` オブジェクトの名前が [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata) に変更されました。  

- `https://api.cognitive.microsoft.com/bing/v7.0/images/details` エンドポイントが追加されました。 イメージの分析情報を要求するときは、/images/search エンドポイントの代わりにこのエンドポイントを使用します。 [Image Insights](./image-insights.md) に関する説明を参照してください。

- 次のクエリ文字列パラメーターは、`/images/details` エンドポイントでのみ有効となりました。  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [ct](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- `ImageInsightsResponse` オブジェクトの名前が [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) に変更されました。  

- [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) オブジェクト内の次のフィールドのデータ型が変更されました。  

    -   `relatedCollections` フィールドの型が `ImageGallery[]` から [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule) に変更されました。  

    -   `pagesIncluding` フィールドの型が `Image[]` から [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule) に変更されました。  

    -   `relatedSearches` フィールドの型が `Query[]` から [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule) に変更されました。  

    -   `recipes` フィールドの型が `Recipe[]` から [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule) に変更されました。  

    -   `visuallySimilarImages` フィールドの型が `Image[]` から [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule) に変更されました。  

    -   `visuallySimilarProducts` フィールドの型が `ProductSummaryImage[]` から [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule) に変更されました。  

    -   `ProductSummaryImage` オブジェクトが削除され、製品関連のフィールドが [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) オブジェクトに移動されました。 `Image` オブジェクトに製品関連のフィールドが含まれるのは、イメージ分析情報の応答で、イメージが、視覚的に類似した製品の一部として含まれている場合のみです。  

    -   `recognizedEntityGroups` フィールドの型が `RecognizedEntityGroup[]` から [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule) に変更されました。  

-   [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) の `categoryClassification` フィールドの名前が `annotations` に変更され、型が `AnnotationsModule` に変更されました。  

### <a name="images-answer"></a>イメージの応答

-   [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) から displayShoppingSourcesBadges フィールドと displayRecipeSourcesBadges フィールドが削除されました。  

-   [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) の `nextOffsetAddCount` フィールドの名前が `nextOffset` に変更されました。 オフセットの使用方法も変更されています。 以前は、[offset](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) クエリ パラメーターを `nextOffsetAddCount` の値、前の offset の値、および結果内のイメージ数の合計に設定していましたが、 `offset` を `nextOffset` の値に設定するようになりました。  


## <a name="non-breaking-changes"></a>非破壊的変更

### <a name="query-parameters"></a>クエリ パラメーター

- [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) フィルターに使用可能な値として、Transparent が追加されました。 Transparent フィルターは、透明な背景を持つイメージのみを返します。

- [license](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) フィルターに使用可能な値として、Any が追加されました。 Any フィルターは、ライセンスを受けているイメージのみを返します。

- [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) および [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) クエリ パラメーターが追加されました。 これらのフィルターを使用すると、ファイル サイズが範囲内であるイメージが返されます。  

- [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight)、[minHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight)、[maxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth)、[minWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) の各クエリ パラメーターが追加されました。 これらのフィルターを使用すると、高さと幅が範囲内であるイメージが返されます。  

### <a name="object-changes"></a>オブジェクトの変更

- [Offer](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) オブジェクトに `description` フィールドと `lastUpdated` フィールドが追加されました。  

- [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) オブジェクトに `name` フィールドが追加されました。  

- [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) オブジェクトに `similarTerms` フィールドが追加されました。 このフィールドには、ユーザーのクエリ文字列と意味的に類似している用語のリストが含まれます。