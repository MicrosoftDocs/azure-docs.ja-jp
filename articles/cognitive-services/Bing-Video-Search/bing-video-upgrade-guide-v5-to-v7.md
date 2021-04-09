---
title: Bing Video Search API v5 を v7 にアップグレードする
titleSuffix: Azure Cognitive Services
description: バージョン 7 を使用するために更新が必要な Bing Video Search アプリケーションの部分を識別します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: e47691c82d43a35a7235eb5c3fd3cf7bca081aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351914"
---
# <a name="video-search-api-upgrade-guide"></a>Video Search API のアップグレード ガイド

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

このアップグレード ガイドでは、Bing Video Search API のバージョン 5 とバージョン 7 の間の変更を識別します。 このガイドは、バージョン 7 を使用するために更新する必要のあるアプリケーションの部分を識別するのに役立ちます。

## <a name="breaking-changes"></a>重大な変更

### <a name="endpoints"></a>エンドポイント

- エンドポイントのバージョン番号は、v5 から v7 に変更されました。 たとえば、「 `https://api.cognitive.microsoft.com/bing/v7.0/videos/search` 」のように入力します。

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

- `modulesRequested` クエリ パラメーターの名前が [modules](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested) に変更されました。  

### <a name="object-changes"></a>オブジェクトの変更

- [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) の `nextOffsetAddCount` フィールドの名前が `nextOffset` に変更されました。 オフセットの使用方法も変更されています。 以前は、[オフセット](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) クエリ パラメーターを値 `nextOffset` と、前のオフセット値と、結果のビデオの数に設定していました。 現在は、`offset` クエリ パラメーターを値 `nextOffset` に設定するだけです。  
  
- `relatedVideos` フィールドのデータ型を `Video[]` から [VideosModule](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) に変更しました ([VideoDetails](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails) を参照)。