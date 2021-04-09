---
title: Bing Visual Search API に検索クエリを送信する
titleSuffix: Azure Cognitive Services
description: この記事では、Bing Visual Search API に送信される要求のパラメーターと属性、および応答オブジェクトについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: aahi
ms.openlocfilehash: 37d9352b6384ee2b5e95903f35d531bd672b25b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96490977"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Bing Visual Search API に検索クエリを送信する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

この記事では、Bing Visual Search API に送信される要求のパラメーターと属性、および応答オブジェクトについて説明します。 

3 つの方法で画像についての分析情報を取得できます。

- [Bing Image Search API](/rest/api/cognitiveservices/bing-images-api-v7-reference) エンドポイントのいずれかに対する前の呼び出しで画像から取得した分析情報トークンを使用する。
- 画像の URL を送信する。
- 画像を (バイナリ形式で) アップロードする。

## <a name="bing-visual-search-requests"></a>Bing Visual Search の要求

画像のトークンまたは URL を Visual Search に送信する場合は、次のスニペットで示す JSON オブジェクトを POST の本文に含める必要があります。

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

`imageInfo` オブジェクトには、`url` または `imageInsightsToken` フィールドのどちらか一方だけが含まれている必要があります。 `url` フィールドには、インターネットでアクセス可能な画像の URL を設定します。 サポートされる画像の最大サイズは 1 MB です。

`imageInsightsToken` には、分析情報トークンを設定する必要があります。 分析情報トークンを取得するには、Bing Image API を呼び出します。 応答には、`Image` オブジェクトのリストが含まれます。 各 `Image` オブジェクトに含まれる `imageInsightsToken` フィールドには、トークンが含まれています。

`cropArea` フィールドは省略可能です。 トリミング領域では、関心領域の左上隅と右下隅を指定します。 0\.0 から 1.0 の範囲の値を指定します。 値は、全体の幅または高さに対する割合です。 たとえば、上の例では、画像の右半分を関心領域として指定しています。 このフィールドは、分析情報の要求を関心領域に制限する場合に含めます。

`filters` オブジェクトに含まれるサイト フィルター (`site` フィールドを参照) を使うと、類似画像および類似製品の結果を特定のドメインに制限できます。 たとえば、画像が Surface Book のものである場合は、`site` を `www.microsoft.com` に設定できます。

画像のローカル コピーに関する分析情報を取得する場合は、バイナリ データとして画像をアップロードします。

POST の本文でこれらのオプションを指定する方法について詳しくは、「[コンテンツ フォームの種類](#content-form-types)」をご覧ください。

### <a name="search-endpoint"></a>Search エンドポイント

Visual Search のエンドポイントは、https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch です。

要求は、HTTP POST 要求としてのみ送信する必要があります。

### <a name="query-parameters"></a>クエリ パラメーター

要求で指定する必要があるクエリ パラメーターを次に示します。 少なくとも、`mkt` クエリ パラメーターを含める必要があります。

| 名前 | 値 | Type | 必須 |
| --- | --- | --- | --- |
| <a name="cc"></a>cc  | 結果の取得元を表す 2 文字の国番号です。<br /><br /> このパラメーターを設定する場合は、[Accept-Language](#acceptlanguage) ヘッダーも指定する必要があります。 Bing は、言語の一覧で見つかった最初のサポートされている言語を使用し、その言語と指定された国番号を組み合わせて、結果を返す市場を決定します。 言語一覧にサポートされている言語が含まれない場合、Bing は要求をサポートする最も近い言語と市場を検索します。 または、指定された市場ではなく、集計された市場または既定の市場を使って結果を取得する場合もあります。<br /><br /> このクエリ パラメーターと `Accept-Language` クエリ パラメーターは、複数の言語を指定する場合にのみ使う必要があります。それ以外の場合は、`mkt` および `setLang` クエリ パラメーターを使う必要があります。<br /><br /> このパラメーターと [mkt](#mkt) クエリ パラメーターは相互に排他的なので、両方指定することはできません。 | String | いいえ       |
| <a name="mkt"></a>mkt   | 結果の取得元の市場。 <br /><br /> **注:** 常に市場を指定することをお勧めします (わかっている場合)。 市場を指定すると、Bing が要求をルーティングして最適な応答を返すのに役立ちます。<br /><br /> このパラメーターと [cc](#cc) クエリ パラメーターは相互に排他的なので、両方指定することはできません。 | String | はい      |
| <a name="safesearch"></a>safeSearch | 成人向けコンテンツのフィルターです。 次のフィルター値を指定できます。大文字と小文字は区別されません。<br /><ul><li>Off &mdash; 成人向けのテキストや画像を含む Web ページを返します。<br /><br/></li><li>Moderate &mdash; 成人向けのテキストを含む Web ページは返しますが、成人向けの画像は返しません。<br /><br/></li><li>Strict &mdash; 成人向けのテキストや画像を含む Web ページを返しません。</li></ul><br /> 既定値は Moderate です。<br /><br /> **注:** 要求元の市場の Bing 成人向けコンテンツ ポリシーで `safeSearch` を Strict に設定することが要求されている場合、Bing では `safeSearch` の値が無視されて、Strict が使用されます。<br/><br/>**注:** `site:` クエリ演算子を使用している場合、`safeSearch` クエリ パラメーターの設定にかかわらず、成人向けのコンテンツが応答に含まれることがあります。 `site:` は、そのサイト上のコンテンツがわかっていて、成人向けコンテンツが含まれていても問題のないシナリオの場合にのみ使用してください。  | String | いいえ       |
| <a name="setlang"></a>setLang  | ユーザー インターフェイス文字列に使用する言語。 ISO 639-1 の 2 文字の言語コードを使って言語を指定します。 たとえば、英語の言語コードは EN です。 既定値は EN (英語) です。<br /><br /> 省略可能ですが、常に言語を指定することをお勧めします。 ユーザー インターフェイス文字列が別の言語で表示されることをユーザーが望まない限り、通常、`setLang` は `mkt` で指定されるのと同じ言語に設定します。<br /><br /> このパラメーターと [Accept-Language](#acceptlanguage) ヘッダーは相互に排他的なので、両方は指定しないでください。<br /><br /> ユーザー インターフェイス文字列は、ユーザー インターフェイスでラベルとして使われる文字列です。 JSON 応答オブジェクトには、いくつかのユーザー インターフェイス文字列があります。 また、応答オブジェクト内の Bing.com プロパティへのリンクには、指定された言語が適用されます。 | String | いいえ   |

## <a name="headers"></a>ヘッダー

要求で指定する必要があるヘッダーを次に示します。 必須のヘッダーは `Content-Type` と `Ocp-Apim-Subscription-Key` ヘッダーのみですが、`User-Agent`、`X-MSEdge-ClientID`、`X-MSEdge-ClientIP`、および `X-Search-Location` も指定することをお勧めします。

| ヘッダー | 説明 |
| --- | --- |
| <a name="acceptlanguage"></a>Accept-Language  | 省略可能な要求ヘッダー。<br /><br /> ユーザー インターフェイス文字列に使用する言語のコンマ区切りリストです。 リストでは優先度の高いものから順に指定します。 有効な形式など、詳細については [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) を参照してください。<br /><br /> このヘッダーと [setLang](#setlang) クエリ パラメーターは相互に排他的なので、両方は指定しないでください。<br /><br /> このヘッダーを設定する場合は、[cc](#cc) クエリ パラメーターも指定する必要があります。 結果が返される市場を特定するために、Bing によってリストから検出された最初のサポート対象言語が使用され、それが `cc` パラメーター値と組み合わされます。 サポート対象言語がリストに含まれていない場合、要求がサポートされる最も近い言語と市場が Bing によって検出されるか、集計された市場または既定の市場が結果に使用されます。 Bing によって使用された市場を確認するには、`BingAPIs-Market` ヘッダーを調べます。<br /><br /> このヘッダーと `cc` クエリ パラメーターは、複数の言語を指定する場合にのみ使用します。 それ以外の場合は、[mkt](#mkt) クエリ パラメーターおよび [setLang](#setlang) クエリ パラメーターを使用します。<br /><br /> ユーザー インターフェイス文字列は、ユーザー インターフェイスでラベルとして使われる文字列です。 JSON 応答オブジェクトには、いくつかのユーザー インターフェイス文字列があります。 応答オブジェクト内の Bing.com プロパティへのリンクには、指定された言語が適用されます。  |
| <a name="contenttype"></a>Content-Type  |     |
| <a name="market"></a>BingAPIs-Market    | 応答ヘッダー。<br /><br /> 要求で使用された市場。 形式は \<languageCode\>-\<countryCode\> です。 たとえば、en-US などです。  |
| <a name="traceid"></a>BingAPIs-TraceId  | 応答ヘッダー。<br /><br /> 要求の詳細が含まれたログ エントリの ID。 エラーが発生した場合、この ID をキャプチャします。 問題を特定して解決できない場合は、その他の情報と共にこの ID をサポート チームに提供します。 |
| <a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key | 必須の要求ヘッダー。<br /><br /> [Cognitive Services](https://www.microsoft.com/cognitive-services/) でこのサービスにサインアップしたときに受け取ったサブスクリプション キーです。 |
| <a name="pragma"></a>Pragma |   |
| <a name="useragent"></a>User-Agent  | 省略可能な要求ヘッダー。<br /><br /> 要求送信元のユーザー エージェント。 Bing では、モバイル ユーザーに最適なエクスペリエンスを提供するためにユーザー エージェントが使用されます。 省略可能ですが、このヘッダーは常に指定することをお勧めします。<br /><br /> ユーザーエージェントは、よく使用されるブラウザーによって送信されるのと同じ文字列にする必要があります。 ユーザー エージェントについては、[RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) を参照してください。<br /><br /> ユーザーエージェント文字列の例を次に示します。<br /><ul><li>Windows Phone &mdash; Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android &mdash; Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone &mdash; Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC &mdash; Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad &mdash; Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid"></a>X-MSEdge-ClientID  | 省略可能な要求および応答ヘッダー。<br /><br /> このヘッダーは、Bing API の呼び出し間で一貫性のある動作をユーザーに提供するために Bing によって使用されます。 Bing によって、新しい機能と改善点が頻繁にフライト化されます。そして、トラフィックを異なるフライトに割り当てるためのキーとして、クライアント ID が使用されます。 複数の要求に対してユーザーの同じクライアント ID を使用しないと、ユーザーが複数の競合するフライトに割り当てられる可能性があります。 複数の競合するフライトに割り当てられると、ユーザー エクスペリエンスの一貫性がなくなる場合があります。 たとえば、2 番目の要求に 1 番目とは異なるフライトが割り当てられていると、エクスペリエンスが予期しないものになる可能性があります。 また、クライアント ID は、そのクライアント ID の検索履歴に合わせて Web の結果を調整し、ユーザーにリッチなエクスペリエンスを提供するために Bing によって使用される場合があります。<br /><br /> このヘッダーは、クライアント ID で生成されたアクティビティを分析して結果の順位付けを向上させるために Bing によって使用されることもあります。 関連性の向上は、Bing API によって提供される結果の品質向上に役立ち、API コンシューマーのクリックスルー率の向上を実現します。<br /><br /> **重要:** このヘッダーは省略可能ですが、必須であると考える必要があります。 同じエンド ユーザーとデバイスの組み合わせによる複数の要求に対してクライアント ID を保持することで、1) API コンシューマーが一貫性のあるユーザー エクスペリエンスを受け取ることができ、2) Bing API からの結果の品質向上を通じてクリックスルー率の向上が実現します。<br /><br /> このヘッダーに適用される基本的な使用規則を次に示します。<br /><ul><li>デバイスでアプリケーションを使用する各ユーザーは、Bing によって生成された一意のクライアント ID を持っている必要があります。<br /><br/>このヘッダーを要求に含めない場合、Bing によって ID が生成され、それが X-MSEdge-ClientID 応答ヘッダーで返されます。 このヘッダーを要求に含めるべきでない唯一の場合は、ユーザーがそのデバイスでアプリを初めて使用するときです。<br /><br/></li><li>**注意：** このクライアント ID が認証済みのユーザー アカウント情報にリンク可能ではないことを確認する必要があります。</li><li>そのユーザーのためにアプリによってデバイスで実行される各 Bing API 要求で、クライアント ID を使用します。<br /><br/></li><li>クライアント ID を保持します。 ブラウザー アプリで ID を永続化するには、永続的な HTTP Cookie を使用して ID がすべてのセッションで確実に使用されるようにします。 セッション Cookie は使用しないようにしてください。 モバイル アプリなど、他のアプリの場合は、デバイスの永続的ストレージを使用して ID を保持します。<br /><br/>次にそのデバイスでユーザーがアプリを使用するときに、保持したクライアント ID を取得します。</li></ul><br /> **注:** Bing の応答には、このヘッダーが含まれる場合と含まれない場合があります。 このヘッダーが応答に含まれる場合、クライアント ID をキャプチャして、ユーザーのためにそのデバイスで実行される後続のすべての Bing 要求でそれを使用します。<br /><br /> **注:** X-MSEdge-ClientID を含める場合、要求には Cookie を含めないようにしてください。 |
| <a name="clientip"></a>X-MSEdge-ClientIP   | 省略可能な要求ヘッダー。<br /><br /> クライアント デバイスの IPv4 アドレスまたは IPv6 アドレス。 IP アドレスは、ユーザーの位置情報の検出に使用されます。 位置情報は、安全な検索動作を決定するために Bing によって使用されます。<br /><br /> **注:** 省略可能ですが、このヘッダーと X-Search-Location ヘッダーは常に指定することをお勧めします。<br /><br /> (最後のオクテットを 0 に変更するなど) アドレスを難読化しないようにしてください。 アドレスを難読化すると、デバイスの実際の場所から離れた場所が検出され、Bing から誤った結果が提供される可能性があります。 |
| <a name="location"></a>X-Search-Location   | 省略可能な要求ヘッダー。<br /><br /> クライアントの地理的な場所を示す、キーと値のペアのセミコロン区切りリストです。 位置情報は、安全な検索動作を決定して関連するローカル コンテンツを返すために、Bing によって使用されます。 キーと値のペアは、\<key\>:\<value\> の形式で指定します。 ユーザーの場所の指定に使用するキーは次のとおりです。<br /><br /><ul><li>lat &mdash; 必須。 クライアントの場所の緯度です (度単位)。 緯度は、-90.0 以上、+90.0 以下である必要があります。 負の値は南半球の緯度を示し、正の値は北半球の緯度を示します。<br /><br /></li><li>long &mdash; 必須。 クライアントの場所の経度です (度単位)。 経度は、-180.0 以上、+180.0 以下である必要があります。 負の値は西半球の経度を示し、正の値は東半球の経度を示します。<br /><br /></li><li>re &mdash; 必須。 座標の水平方向の精度を指定する半径 (m) です。 デバイスの位置情報サービスによって返される値を渡します。 一般的な値は、GPS/Wi-Fi の 22 m、携帯電話基地局の三角測量の 380 m、IP 逆引き参照の 18,000 m などです。<br /><br /></li><li>ts &mdash; 省略可能。 クライアントがその場所にいたときの UTC UNIX タイムスタンプです。 (UNIX タイムスタンプは、1970 年 1 月 1 日からの経過秒数です)。<br /><br /></li><li>head &mdash; 省略可能。 クライアントの相対的な先頭方向または移動方向。 移動方向は、真北を基準として時計回りに 0 から 360 度で指定します。 このキーは、`sp` キーが 0 以外の場合にのみ指定します。<br /><br /></li><li>sp &mdash; 省略可能。 クライアント デバイスが移動している水平方向の速度 (m/秒) です。<br /><br /></li><li>alt &mdash; 省略可能。 クライアント デバイスの高度 (m) です。<br /><br /></li><li>are &mdash; 省略可能。 座標の垂直方向の精度を指定する半径 (m)。 このキーは、`alt` キーを指定する場合にのみ指定します。<br /><br /></li></ul> **注:** 多くのキーは省略可能ですが、提供する情報が多いほど、場所の結果の正確さが増します。<br /><br /> **注:** 省略可能ですが、ユーザーの地理的な場所は常に指定することをお勧めします。 位置情報を提供することは、クライアントの IP アドレスがユーザーの物理的な場所を正確に反映していない場合 (たとえば、クライアントによって VPN が使用されている場合) に特に重要です。 最適な結果を得るには、このヘッダーと `X-MSEdge-ClientIP` ヘッダーを含める必要がありますが、最低限、このヘッダーを含めることをお勧めします。       |

> [!NOTE]
> [Bing Search API の利用と表示の要件](../../bing-web-search/use-display-requirements.md)ですべての該当法規 (これらのヘッダーの使用に関するものなど) への準拠が要求されていることに注意してください。 たとえば、ヨーロッパなどの特定の地域では、特定の追跡デバイスをユーザー デバイスに組み込む前に、ユーザーの同意を得る必要があります。

<a name="content-form-types"></a>

### <a name="content-form-types"></a>コンテンツ フォームの種類

各要求には `Content-Type` ヘッダーを含める必要があります。 ヘッダーは、`multipart/form-data; boundary=\<boundary string\>` のように設定する必要があります。\<boundary string\> は、フォーム データの境界を示す一意の非透過的な文字列です。 たとえば、「 `boundary=boundary_1234-abcd` 」のように入力します。

画像のトークンまたは URL を Visual Search に送信する場合は、次のスニペットで示すフォーム データを POST の本文に含める必要があります。 フォーム データには `Content-Disposition` ヘッダーが含まれ、その `name` パラメーターが "knowledgeRequest" に設定されている必要があります。 `imageInfo` オブジェクトの詳細については、「要求」を参照してください。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

必要に応じてヘッダーの `enableEntityData` 属性を `true` に設定できます。アップロードする画像内のメイン エンティティに関する詳しい情報 (Web へのリンクや属性情報を含む) を得ることができます。 既定では、このフィールドが `false` になっています。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
  "imageInfo" : {
      "url" : "https://contoso.com/2018/05/fashion/red.jpg"
  },
  "knowledgeRequest" : {
    "invokedSkillsRequestData" : {
        "enableEntityData" : "true"
    }
  }
}

--boundary_1234-abcd--
```

ローカル イメージをアップロードする場合は、次のスニペットで示すフォーム データを POST の本文に含める必要があります。 フォーム データには `Content-Disposition` ヘッダーを含める必要があります。 その `name` パラメーターには "image" を設定する必要があり、`filename` パラメーターには任意の文字列を設定できます。 `Content-Type` ヘッダーは、一般的に使用されるイメージの任意の MIME の種類に設定できます。 フォームの内容は、イメージのバイナリ データです。 アップロードできるイメージの最大サイズは、1 MB です。 幅または高さの最大値は、1,500 ピクセル以下にする必要があります。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

次のスニペットは、アップロードされたイメージの関心領域を指定する方法を示しています。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

### <a name="example-request"></a>要求の例

次のスニペットは、画像トークンと関心領域を渡す完全な画像分析情報要求を示しています。 分析情報トークンは、先に /images/search を呼び出して取得します。

```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```

## <a name="bing-visual-search-responses"></a>Bing Visual Search の応答


[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

画像に使用できる分析情報がある場合、応答には分析情報を含む 1 つ以上の `tags` が含まれています。 `image` フィールドには、入力画像に対する分析情報トークンが含まれます。

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

`tags` フィールドには、表示名とアクション (分析情報) のリストが含まれます。 タグの 1 つの `displayName` フィールドには、空の文字列が設定されています。 このタグには、画像を含む Web ページ、視覚的に似た画像、画像で見つかったアイテムのショッピング ソースなど、既定の分析情報が含まれています。 画像全体に関心があるので、既定の分析情報タグには関心のある領域の境界ボックスは含まれません。

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

既定の分析情報の一覧については、「[既定の分析情報タグ](../default-insights-tag.md)」をご覧ください。

残りのタグには、ユーザーが関心を持つ可能性のある他の分析情報が含まれています。 たとえば、画像にテキストが含まれる場合、タグの 1 つには TextResults 分析情報が含まれ、認識されたテキストが格納されている可能性があります。 または、Bing が画像でエンティティ (つまり、文化的によく知られた有名な人、場所、または物) を認識した場合、タグの 1 つでそのエンティティが示されている可能性があります。 Visual Search は、入力画像から得られるさまざまな用語セット (タグ) も返します。 これらのタグを使用して、ユーザーは画像で見つかる概念を調べることができます。 たとえば、入力画像が有名なアスリートの場合、タグの 1 つは Sports で、スポーツの画像へのリンクが含まれる可能性があります。

各タグには、分析情報の分類に使用できる表示名、分析情報が適用される関心領域を示す境界ボックス、分析情報自体、画像のサムネイルが含まれています。 たとえば、スポーツのジャージを着ている人の画像の場合、タグの 1 つにはジャージを囲む境界ボックスが含まれる可能性があり、VisualSearch 分析情報と ProductVisualSearch 分析情報が含まれます。 そして、別のタグには ImageResults 分析情報が含まれ、局所的に関連のある画像を取得するための /images/search API 要求の URL、またはユーザーを Bing.com 画像検索結果に移動させる Bing.com 検索 URL が含まれている可能性があります。

既定の分析情報タグ以外のすべてのタグには、画像内の関心対象領域を示す境界ボックスが含まれます。 たとえば、画像で複数の人が認識された場合、タグには各人の境界ボックスが含まれることがあります。または、画像で衣類アイテムが認識された場合は、認識された各衣類アイテムの境界ボックスがタグに含まれる可能性があります。 境界ボックスを使用して画像にホット スポットを作成し、そこがクリックされると、画像のその領域の内容に関する詳細を提供することができます。 画像全体を示す境界ボックスに対するホット スポットは作成しないでください。

### <a name="text-recognition"></a>テキストの認識

画像に含まれるテキストをサービスが認識した場合、タグの 1 つには TextResults 分析情報 (アクション) が含まれます。 その分析情報の `displayName` には、認識されたテキストが格納されています。

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

タグの `displayName` フィールドには ##TextRecognition が含まれているため、それを UX 内のカテゴリ タイトルとして使用しないでください。 ## で始まるすべての表示名に適用されます。 代わりに、アクションの表示名を使います。

テキストの認識では、電話番号やメール アドレスなど、名刺の連絡先情報も認識できます。 境界ボックスは、カード上での連絡先情報の場所を示します。

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

文化的によく知られた有名な人、場所、物などの認識されたエンティティが画像に含まれる場合、タグの 1 つには Entity 分析情報が含まれる可能性があります。 `mainEntity` フィールドと `data` フィールドは、`Content-Type` ヘッダーの `enableEntityData` 属性が `true` に設定されている場合にのみ利用できます。

```json
{
  "image" : {
    "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
  },
  "displayName" : "Statue of Liberty",
  "boundingBox" : {
    "queryRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    },
    "displayRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    }
  },
  "actions" : [
    {
      "_type" : "ImageEntityAction",
      "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
      "displayName" : "Statue of Liberty",
      "actionType" : "Entity",
      "mainEntity" : {
        "name" = "Statue of liberty",
        "bingId" : "..."
      },
      "data" : {
        "id" : "https://api.cognitive.microsoft.com/api/v7/entities/...",
        "readLink": "https://www.bingapis.com/api/v7/search?q=...",
        "readLinkPingSuffix": "...",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
                "name": "CC-BY-SA",
                "url": "http://creativecommons.org/licenses/by-sa/3.0/",
                "urlPingSuffix": "..."
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/...",
            "urlPingSuffix": "..."
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q=...",
        "webSearchUrlPingSuffix": "...",
        "name": "Statue of Liberty",
        "image": {
          "thumbnailUrl": "https://tse1.mm.bing.net/th?id=...",
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/...",
          "hostPageUrlPingSuffix": "...",
          "width": 50,
          "height": 50,
          "sourceWidth": 474,
          "sourceHeight": 598
        },
        "description" : "...",
        "bingId": "..."
        }
      }
  ]
}
```

## <a name="see-also"></a>関連項目

- [Bing Visual Search API とは](../overview.md)
- [チュートリアル:Visual Search のシングルページ Web アプリを作成する](../tutorial-bing-visual-search-single-page-app.md)