---
title: Bing News Search API v5 を v7 にアップグレードする
titleSuffix: Azure Cognitive Services
description: バージョン 7 を使用するために更新する必要のあるアプリケーションの部分を識別します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 1263e93b1e316cab4afb51cd828737a5bd087fed
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423851"
---
# <a name="news-search-api-upgrade-guide"></a>News Search API のアップグレード ガイド

このアップグレード ガイドでは、Bing News Search API のバージョン 5 とバージョン 7 の間の変更点を識別します。 このガイドは、バージョン 7 を使用するために更新する必要のあるアプリケーションの部分を識別するのに役立ちます。

## <a name="breaking-changes"></a>重大な変更

### <a name="endpoints"></a>エンドポイント

- エンドポイントのバージョン番号は、v5 から v7 に変更されました。 たとえば、 https://api.cognitive.microsoft.com/bing/**v7.0**/news/search となります。

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
Disabled|InsufficientAuthorization.AuthorizationDisabled
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

### <a name="object-changes"></a>オブジェクトの変更

- `contractualRules` フィールドが [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) オブジェクトに追加されました。 `contractualRules` フィールドには、従う必要があるルールのリストが含まれています (たとえば、記事の属性)。 `provider` を使用するのではなく、`contractualRules` に指定されている属性を適用する必要があります。 この記事には、[Web Search API](../bing-web-search/search-the-web.md) 応答に News の回答が含まれている場合にのみ `contractualRules`が含まれます。

## <a name="non-breaking-changes"></a>非破壊的変更

### <a name="query-parameters"></a>クエリ パラメーター

- [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) クエリ パラメーターをに設定できる可能性のある値として Products が追加されました。 [市場別のカテゴリ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)に関するページを参照してください。

- 最近の日付から並べられたトレンドのトピックを返す [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) クエリ パラメーターが追加されました。

- [Since](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) クエリ パラメーターが追加されました。指定した Unix エポック タイムスタンプ以降に Bing によって検出されたトレンドのトピックを返します。

### <a name="object-changes"></a>オブジェクトの変更

- `mentions` フィールドが [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) オブジェクトに追加されました。 `mentions` フィールドには、記事で見つかったエンティティ (人物または場所) のリストが含まれています。

- `video` フィールドが [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) オブジェクトに追加されました。 `video` フィールドには、ニュース記事に関連する動画が含まれています。 動画は、埋め込むことができる \<iframe\> または動画のサムネイルのいずれかです。

- `sort` フィールドが [News](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) オブジェクトに追加されました。 `sort` フィールドには、記事の並べ替え順序が表示されます。 たとえば、記事は関連性 (既定) または日付順で並べ替えられます。

- 並べ替え順序を定義する [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) オブジェクトが追加されました。 `isSelected` フィールドは、応答が並べ替え順序を使用したかどうかを示します。 **true** の場合、応答は並べ替え順序を使用しました。 `isSelected` が **false** の場合、`url` フィールドの URL を使用して、別の並べ替え順序を要求できます。
