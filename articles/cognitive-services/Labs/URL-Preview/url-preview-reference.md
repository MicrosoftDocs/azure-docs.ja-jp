---
title: Project URL Preview リファレンス
titlesuffix: Azure Cognitive Services
description: Project URL Preview エンドポイントのリファレンス。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 69db722295c9c81d45913bd078fe9cc5ab74c512
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104711"
---
# <a name="project-url-preview-v7-reference"></a>Project URL Preview v7 のリファレンス

URL Preview では、ブログ記事、フォーラム ディスカッション、プレビュー ページなどに関する Web リソースの簡単な説明がサポートされます。URL は、任意の種類のインターネット リソース (Web ページ、ニュース、画像、ビデオ) である可能性があります。 クエリは、HTTP または HTTPS スキームを備えた絶対 URL である必要があります。相対 URL や他のスキーム (ftp:// など) はサポートされていません。

URL Preview を使用するアプリケーションでは、プレビューする URL をクエリ パラメーターに含めてエンドポイントに Web 要求を送信します。 要求には、*Ocp-Apim-Subscription-Key* ヘッダーを含める必要があります。

JSON 応答は、名前、リソースの説明、*familyFriendly*、代表的な画像と完全なリソースにオンラインでアクセスするためのリンクといったプレビュー情報について解析できます。

ソーシャル メディアやチャット ボットなどのサービスでエンド ユーザーが URL を共有した場合に、出典サイトへのハイパーリンクを設定したプレビュー スニペットとサムネイル画像を表示するときは、URL Preview から取得したデータのみを使用する必要があります。 Project URL Preview から取得したデータはコピー、保存、キャッシュしないでください。 Web サイトやコンテンツの所有者からプレビューを無効にするよう要請があった場合は、その意向を尊重してください。

## <a name="endpoint"></a>エンドポイント
URL Preview の結果を要求するには、次のエンドポイントに要求を送信します。 ヘッダーと URL パラメーターを使用して、さらに詳細を定義します。

エンドポイントに対する GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

要求には HTTPS プロトコルが使用される必要があるほか、次のクエリ パラメーターが含まれている必要があります。

q - プレビューされる URL を特定するクエリ

以下のセクションでは、検索結果に影響するクエリ パラメーターおよびヘッダーと、応答オブジェクトに関する技術的な詳細について説明します。

要求に含める必要があるヘッダーについては、「[ヘッダー](#headers)」を参照してください。

要求に含める必要があるクエリ パラメーターについては、「[クエリ パラメーター](#query-parameters)」を参照してください。

応答に含まれる JSON オブジェクトについては、「[応答オブジェクト](#response-objects)」を参照してください。

クエリ URL の最大長は 2,048 文字です。 URL の長さが上限を超えないよう、クエリ パラメーターの最大長は 1,500 文字未満にする必要があります。 URL が 2,048 文字を超えた場合、サーバーが 404 Not found を返します。

結果の使用と表示に関する許可については、[使用と表示の要件](use-display-requirements.md)に関するページを参照してください。

> [!NOTE]
> 他の検索 API で有効な一部の要求ヘッダーは、URL Preview に影響しません
> - Pragma – 呼び出し元は URL Preview でキャッシュが使用されるかどうかを制御できません
> - User-Agent – 現在のところ、URL Preview API は、PC、ノート PC、モバイルから送信された呼び出しに対して異なる応答を返しません。
> 
> さらに、一部のパラメーターは現在 URL Preview API で有効ではありません。ただし、これらのパラメーターは、使用範囲が拡大されて使用できるようになる可能性があります。

## <a name="headers"></a>headers
要求と応答に含まれる可能性があるヘッダーを次に示します。

|ヘッダー|説明|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|応答ヘッダー。<br /><br /> 要求で使用された市場。 形式は \<言語コード\>-\<国番号\> です。 たとえば、en-US などです。|
|<a name="traceid" />BingAPIs-TraceId|応答ヘッダー。<br /><br /> 要求の詳細が含まれたログ エントリの ID。 エラーが発生した場合、この ID をキャプチャします。 問題を特定して解決できない場合は、その他の情報と共にこの ID をサポート チームに提供します。|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|必須の要求ヘッダー。<br /><br /> [Cognitive Services](https://www.microsoft.com/cognitive-services/) でこのサービスにサインアップしたときに受け取ったサブスクリプション キー。|
|<a name="clientid" />X-MSEdge-ClientID|省略可能な要求および応答ヘッダー。<br /><br /> このヘッダーは、Bing API の呼び出し間で一貫性のある動作をユーザーに提供するために Bing によって使用されます。 Bing によって、新しい機能と改善点が頻繁にフライト化されます。そして、トラフィックを異なるフライトに割り当てるためのキーとして、クライアント ID が使用されます。 複数の要求に対してユーザーの同じクライアント ID を使用しないと、ユーザーが複数の競合するフライトに割り当てられる可能性があります。 複数の競合するフライトに割り当てられると、ユーザー エクスペリエンスの一貫性がなくなる場合があります。 たとえば、2 番目の要求に 1 番目とは異なるフライトが割り当てられていると、エクスペリエンスが予期しないものになる可能性があります。 また、クライアント ID は、そのクライアント ID の検索履歴に合わせて Web の結果を調整し、ユーザーにリッチなエクスペリエンスを提供するために Bing によって使用される場合があります。<br /><br /> このヘッダーは、クライアント ID で生成されたアクティビティを分析して結果の順位付けを向上させるために Bing によって使用されることもあります。 関連性の向上は、Bing API によって提供される結果の品質向上に役立ち、API コンシューマーのクリックスルー率の向上を実現します。<br /><br />このヘッダーに適用される基本的な使用規則を次に示します。<br /><ul><li>デバイスでアプリケーションを使用する各ユーザーは、Bing によって生成された一意のクライアント ID を持っている必要があります。<br /><br/>このヘッダーを要求に含めない場合、Bing によって ID が生成され、それが X-MSEdge-ClientID 応答ヘッダーで返されます。 このヘッダーを要求に含めるべきでない唯一の場合は、ユーザーがそのデバイスでアプリを初めて使用するときです。<br /><br/></li><li>そのユーザーのためにアプリによってデバイスで実行される各 Bing API 要求で、クライアント ID を使用します。<br /><br/></li><li>**注意：** このクライアント ID は認証可能なユーザー アカウント情報にリンクできないようにする必要があります。</li><br/><li>クライアント ID を保持します。 ブラウザー アプリで ID を永続化するには、永続的な HTTP Cookie を使用して ID がすべてのセッションで確実に使用されるようにします。 セッション Cookie は使用しないようにしてください。 モバイル アプリなど、他のアプリの場合は、デバイスの永続的ストレージを使用して ID を保持します。<br /><br/>次にそのデバイスでユーザーがアプリを使用するときに、保持したクライアント ID を取得します。</li></ul><br /> **注:** Bing の応答には、このヘッダーが含まれる場合と含まれない場合があります。 このヘッダーが応答に含まれる場合、クライアント ID をキャプチャして、ユーザーのためにそのデバイスで実行される後続のすべての Bing 要求でそれを使用します。<br /><br /> **注:** X-MSEdge-ClientID を含める場合、要求には Cookie を含めないようにしてください。|
|<a name="clientip" />X-MSEdge-ClientIP|省略可能な要求ヘッダー。<br /><br /> クライアント デバイスの IPv4 アドレスまたは IPv6 アドレス。 IP アドレスは、ユーザーの位置情報の検出に使用されます。 位置情報は、安全な検索動作を決定するために Bing によって使用されます。<br /><br /> (最後のオクテットを 0 に変更するなど) アドレスを難読化しないようにしてください。 アドレスを難読化すると、デバイスの実際の場所から離れた場所が検出され、Bing から誤った結果が提供される可能性があります。|

## <a name="query-parameters"></a>クエリ パラメーター
要求には次のクエリ パラメーターを含めることができます。 必須のパラメーターについては、必須の列を参照してください。 クエリ パラメーターは URL エンコードする必要があります。 クエリは、HTTP または HTTPS スキームを備えた絶対 URL である必要があります。Microsoft は相対 URL や他のスキーム (ftp:// など) をサポートしません。

|Name|値|type|必須|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|結果の取得元の市場。 <br /><br />可能な市場の値の一覧については、市場コードに関するページを参照してください。<br /><br /> **注:** 現在 URL Preview API でサポートされるのは米国地域および英語のみです。<br /><br />|String|はい|
|<a name="query" />q|プレビューする URL|String|はい|
|<a name="responseformat" />responseFormat|応答に使用するメディアの種類。 次の値を指定できます。大文字と小文字は区別されません。<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> 既定値は JSON です。 応答に含まれる JSON オブジェクトについては、「[応答オブジェクト](#response-objects)」を参照してください。<br /><br />JsonLd を指定すると、検索結果が含まれている JSON-LD オブジェクトが応答本文に含まれます。 JSON-LD については、[JSON-LD](https://json-ld.org/) に関するページを参照してください。|String|いいえ |
|<a name="safesearch"/>safeSearch|違法な成人向けコンテンツや海賊版コンテンツはエラー コード 400 でブロックされ、*isFamilyFriendly* フラグは返されません。 <p>合法的な成人向けコンテンツの場合は、次のような動作になります。 状態コード 200 が返され、*isFamilyFriendly* フラグが false に設定されます。<ul><li>safeSearch=strict:タイトル、説明、URL、画像は返されません。</li><li>safeSearch=moderate: タイトル、URL、説明は取得しますが、説明的な画像は取得しません。</li><li>safeSearch=off: すべての応答オブジェクト/要素 (タイトル、URL、説明、画像) を取得します。</li></ul> |String|不要。 </br> 既定値は safeSearch=strict です。|

## <a name="response-objects"></a>応答オブジェクト
Web Search API では、応答スキーマは [WebPage] と ErrorResponse のいずれかです。 要求が失敗した場合、最上位レベルのオブジェクトは [ErrorResponse](#errorresponse) オブジェクトになります。

|Object|説明|
|------------|-----------------|
|[WebPage](#webpage)|プレビューの属性が含まれている最上位の JSON オブジェクト。|

### <a name="error"></a>Error
発生したエラーを定義します。

|要素|説明|type|
|-------------|-----------------|----------|
|<a name="error-code" />code|エラーのカテゴリを特定するエラー コード。 考えられるコードの一覧については、「[エラー コード](#error-codes)」を参照してください。|String|
|<a name="error-message" />message|エラーの説明。|String|
|<a name="error-moredetails" />moreDetails|エラーに関する追加情報を提供する説明。|String|
|<a name="error-parameter" />parameter|エラーを引き起こした要求内のクエリ パラメーター。|String|
|<a name="error-subcode" />subCode|エラーを特定するエラー コード。 たとえば、`code` が InvalidRequest の場合、`subCode` は ParameterInvalid か ParameterInvalidValue の場合があります。 |String|
|<a name="error-value" />value|有効でなかったクエリ パラメーター値。|String|

### <a name="errorresponse"></a>ErrorResponse
要求が失敗したときの応答に含まれている最上位レベルのオブジェクト。

|Name|値|type|
|----------|-----------|----------|
|_type|種類のヒント。|String|
|<a name="errors" />errors|要求が失敗した理由を示すエラーの一覧。|[エラー](#error)[]|

### <a name="webpage"></a>WebPage
プレビューの Web ページに関する情報を定義します。

|Name|値|type|
|----------|-----------|----------|
|name|ページ タイトル (必ずしも HTML タイトルではありません)|String|
|url|実際にクロールされた URL (要求はリダイレクトされた可能性があります)|String|
|description|ページとコンテンツの簡単な説明|String|
|isFamilyFriendly|Web インデックスの項目で最も正確です。この検出は、ページのコンテンツではなく URL のみに基づいて、リアルタイム フェッチによって実行されます|ブール値|
|primaryImageOfPage/contentUrl|プレビューに含まれる代表的な画像への URL|String|

### <a name="identifiable"></a>Identifiable
|Name|値|type|
|-------------|-----------------|----------|
|id|リソース識別子|String|

## <a name="error-codes"></a>エラー コード

要求によって返される可能性のある HTTP 状態コードを次に示します。

|状態コード|説明|
|-----------------|-----------------|
|200|成功。|
|400|クエリ パラメーターの 1 つが欠落しているか無効です。|
|400|ServerError で subCode が ResourceError:要求された URL に到達できませんでした|
|400|ServerError で subCode が ResourceError:要求された URL が成功コードを返しませんでした (HTTP 404 が返された場合も含む)|
|400|InvalidRequest で subCode が Blocked:要求された URL に成人向けコンテンツが含まれている可能性があり、この URL はブロックされました|
|401|サブスクリプション キーが見つからないか、無効です。|
|403|(たとえば、有効なサブスクリプション キーを使用して) ユーザーは認証されたものの、要求されたリソースへのアクセス許可がありません。<br /><br /> また、呼び出し元が 1 か月あたりのクエリ数のクォータを超えた場合にも、Bing はこの状態を返します。|
|410|HTTPS プロトコルではなく HTTP プロトコルが使用された要求。 サポートされるプロトコルは HTTPS のみです。|
|429|呼び出し元が 1 秒あたりのクエリ数のクォータを超えました。|
|500|予期しないサーバー エラー。|

要求が失敗すると、応答に [ErrorResponse](#errorresponse) オブジェクトが含まれます。このオブジェクトには、エラーの原因を示す [Error](#error) オブジェクトの一覧が含まれています。 エラーがパラメーターに関連している場合、`parameter` フィールドで、問題であるパラメーターが特定されます。 エラーがパラメーター値に関連している場合、`value` フィールドで、無効な値が特定されます。

```json
{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidRequest",
      "subCode": "ParameterMissing",
      "message": "Required parameter is missing.",
      "parameter": "q"
    }
  ]
}

{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidAuthorization",
      "subCode": "AuthorizationMissing",
      "message": "Authorization is required.",
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

考えられるエラー コードとサブエラー コードの値を次に示します。

|コード|サブコード|説明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP 状態コードは 500 です。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blocked|要求の一部が有効でない場合に Bing は InvalidRequest を返します。 たとえば、必要なパラメーターが不足している場合や、パラメーター値が無効な場合です。<br/><br/>エラーが ParameterMissing または ParameterInvalidValue の場合、HTTP 状態コードは 400 です。<br/><br/>HTTPS プロトコルではなく HTTP プロトコルを使用すると、Bing は HttpNotAllowed を返し、HTTP 状態コードは 410 になります。
|RateLimitExceeded|No sub-codes|1 秒あたりのクエリ数 (QPS) または 1 か月あたりのクエリ数 (QPM) のクォータを超えると、Bing は RateLimitExceeded を返します。<br/><br/>QPS を超えた場合、Bing は HTTP 状態コード 429 を返します。また、QPM を超えた場合、Bing は 403 を返します。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing は、呼び出し元を認証できない場合に InvalidAuthorization を返します。 たとえば、`Ocp-Apim-Subscription-Key` ヘッダーがない場合や、サブスクリプション キーが無効な場合です。<br/><br/>冗長性は、複数の認証方法を指定した場合に発生します。<br/><br/>エラーが InvalidAuthorization の場合、HTTP 状態コードは 401 です。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|呼び出し元がリソースに対するアクセス許可を備えていない場合、Bing は InsufficientAuthorization を返します。 これは、サブスクリプション キーが無効になっているか、期限が切れている場合に発生することがあります。 <br/><br/>エラーが InsufficientAuthorization の場合、HTTP 状態コードは 403 です。

## <a name="next-steps"></a>次の手順
- [C# のクイック スタート](csharp.md)
- [Java のクイック スタート](java-quickstart.md)
- [JavaScript のクイック スタート](javascript.md)
- [Node のクイック スタート](node-quickstart.md)
- [Python のクイック スタート](python-quickstart.md)
