---
title: v5 から v7 へのアップグレード - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search v7 API を使用するために更新が必要なアプリケーションの部分を特定します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d930543671a5328d76a38aa7e1b421c111e89e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349522"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Bing Web Search API v5 から v7 へのアップグレード

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

このアップグレード ガイドでは、Bing Web Search API のバージョン 5 とバージョン 7 の間の変更点を識別します。 このガイドは、バージョン 7 を使用するために更新する必要のあるアプリケーションの部分を識別するのに役立ちます。

## <a name="breaking-changes"></a>重大な変更

### <a name="endpoints"></a>エンドポイント

- エンドポイントのバージョン番号は、v5 から v7 に変更されました。 例: https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search。

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
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|呼び出し元がリソースに対するアクセス許可を備えていない場合、Bing は InsufficientAuthorization を返します。 このエラーは、サブスクリプション キーが無効になっているか、期限が切れている場合に発生することがあります。 <br/><br/>エラーが InsufficientAuthorization の場合、HTTP 状態コードは 403 です。

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


## <a name="non-breaking-changes"></a>非破壊的変更  

### <a name="headers"></a>ヘッダー

- オプションの [Pragma](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) 要求ヘッダーが追加されました。 既定では、Bing はキャッシュされたコンテンツがある場合にそれを返します。 キャッシュされたコンテンツを Bing が返さないようにするには、Pragma ヘッダーを no-cache に設定します (例: Pragma: no-cache)。

### <a name="query-parameters"></a>クエリ パラメーター

- [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) クエリ パラメーターが追加されました。 応答に含める回答の数を指定するには、このパラメーターを使用します。 回答は、ランキングに基づいて選択されます。 たとえば、このパラメーターを 3 に設定すると、上位の 3 つの回答が応答に含まれます。  

- [promote](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) クエリ パラメーターが追加されました。 ランキングに関係なく 1 つ以上の回答タイプを明示的に含めるには、このパラメーターを `answerCount` と共に使用します。 たとえば、ビデオと画像を応答に昇格させるには、promote を *videos, images* に設定します。 昇格させる回答の一覧は、`answerCount` の制限にはカウントされません。 たとえば、`answerCount` が 2、`promote` が *videos, images* に設定されている場合、応答には Web ページ、ニュース、ビデオ、および画像が含まれる可能性があります。

### <a name="object-changes"></a>オブジェクトの変更

- `someResultsRemoved` フィールドが [WebAnswer](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) オブジェクトに追加されました。 このフィールドには、応答において Web 回答のいくつかの結果が除外されているかどうかを示すブール値が含まれています。