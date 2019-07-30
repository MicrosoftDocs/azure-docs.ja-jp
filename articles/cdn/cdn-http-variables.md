---
title: Azure CDN ルール エンジンの HTTP 変数 | Microsoft Docs
description: HTTP 変数を使用すると、HTTP 要求と応答のメタデータを取得できます。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593816"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN ルール エンジンの HTTP 変数
HTTP 変数は、HTTP 要求と応答のメタデータを取得するための手段を提供します。 このメタデータを使用して、要求または応答を動的に変更できます。 HTTP 変数の使用は、次のルール エンジンの機能に限定されています。

- [Cache-Key Rewrite (キャッシュキー書き換え)](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header (クライアント要求ヘッダーの修正)](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header (クライアント応答ヘッダーの修正)](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect (URL リダイレクト)](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite (URL 書き換え)](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>定義
次の表で、サポートされている HTTP 変数について説明します。 GEO メタデータ (たとえば、郵便番号) が特定の要求に利用できない場合は、空白の値が返されます。


| Name | 変数 | 説明 | 値の例 |
| ---- | -------- | ----------- | ------------ |
| ASN (要求者) | %{geo_asnum} | 要求者の AS 番号を示します。 <br /><br />**非推奨:** %{virt_dst_asnum}。 <br />%{geo_asnum} を優先して、この変数は非推奨となりました。 この非推奨の変数を使用するルールは今後も正常に機能しますが、更新して新しい変数を使用する必要があります。 | AS15133 |
| 市区町村 (要求者) | %{geo_city} | 要求者の市区町村を示します。 | ロサンゼルス |
| 大陸 (要求者) | %{geo_continent} | 要求者の大陸を省略形で示します。 <br />有効な値は次のとおりです。 <br />AF:アフリカ<br />AS:アジア<br />EU:ヨーロッパ<br />NA:北米<br />OC:オセアニア<br />SA:南アメリカ<br /><br />**非推奨:** %{virt_dst_continent}。 <br />%{geo_continent} を優先して、この変数は非推奨となりました。 <br />この非推奨の変数を使用するルールは今後も正常に機能しますが、更新して新しい変数を使用する必要があります。| 該当なし |
| Cookie 値 | %{cookie_Cookie} | Cookie という語で識別される Cookie キーに対応する値を返します。 | 使用例: <br />%{cookie__utma}<br /><br />値の例:<br />111662281.2.10.1222100123 |
| 国 (要求者) | %{geo_country} | 国コードによって配信元の要求者の国を示します。 <br />**非推奨:** %{virt_dst_country}。 <br /><br />%{geo_country} を優先して、この変数は非推奨となりました。 この非推奨の変数を使用するルールは今後も正常に機能しますが、更新して新しい変数を使用する必要があります。 | US |
| 指定の市場地域 (要求者) | %{geo_dma_code} |要求者のメディア市場をその地域コードによって示します。 <br /><br />このフィールドは、米国から送信される要求のみに適用されます。| 745 |
| HTTP 要求メソッド | %{request_method} | HTTP 要求メソッドを示します。 | GET |
| HTTP 状態コード | %{status} | 応答の HTTP 状態コードを示します。 | 200 |
| IP アドレス (要求者) | %{virt_dst_addr} | 要求者の IP アドレスを示します。 | 192.168.1.1 |
| 緯度 (要求者) | %{geo_latitude} | 要求者の緯度を示します。 | 34.0995 |
| 経度 (要求者) | %{geo_longitude} | 要求者の経度を示します。 | -118.4143 |
| 大都市統計圏 (要求者) | %{geo_metro_code} | 要求者の大都市圏を示します。 <br /><br />このフィールドは、米国から送信される要求のみに適用されます。<br />| 745 |
| ポート (要求者) | %{virt_dst_port} | 要求者の一時的なポートを示します。 | 55885 |
| 郵便番号 (要求者) | %{geo_postal_code} | 要求者の郵便番号を示します。 | 90210 |
| クエリ文字列あり | %{is_args} | この変数の値は、要求にクエリ文字列が含まれているかどうかによって異なります。<br /><br />- クエリ文字列あり: ?<br />- クエリ文字列なし:NULL | ? |
| クエリ文字列パラメーター値あり | %{is_amp} | この変数の値は、要求に少なくとも 1 つのクエリ文字列パラメーターが含まれているかどうかによって異なります。<br /><br />- パラメーターあり: &<br />- パラメーターなし:NULL | & |
| クエリ文字列パラメーター値 | %{arg_&lt;parameter&gt;} | &lt;parameter&gt; という語で識別されるクエリ文字列パラメーターに対応する値を返します。 | 使用例: <br />%{arg_language}<br /><br />クエリ文字列パラメーターの例: <br />?language=en<br /><br />値の例: en |
| クエリ文字列値 | %{query_string} | 要求 URL に定義されているクエリ文字列値全体を示します。 |key1=val1&key2=val2&key3=val3 |
| 参照元のドメイン | %{referring_domain} | Referrer 要求ヘッダーに定義されているドメインを示します。 | <www.google.com> |
| 地域 (要求者) | %{geo_region} | 要求者の地域 (たとえば、都道府県) を、その英数字の省略形で示します。 | CA |
| 要求ヘッダーの値 | %{http_RequestHeader} | RequestHeader という語で識別される要求ヘッダーに対応する値を返します。 <br /><br />要求ヘッダーの名前にダッシュが含まれている (たとえば、User-Agent) 場合は、アンダースコアで置き換えます (たとえば、User_Agent)。| 使用例: %{http_Connection}<br /><br />値の例:Keep-Alive | 
| 要求ホスト | %{host} | 要求 URL に定義されているホストを示します。 | <www.mydomain.com> |
| 要求プロトコル | %{request_protocol} | 要求プロトコルを示します。 | HTTP/1.1 |
| Request Scheme (要求スキーム) | %{scheme} | 要求スキームを示します。 |http |
| 要求 URI (相対) | %{request_uri} | 要求 URI に定義されている相対パス (クエリ文字列を含む) を示します。 | /marketing/foo.js?loggedin=true |
| 要求 URI (クエリ文字列なしの相対) | %{uri} | 要求されたコンテンツへの相対パスを示します。 <br /><br/>重要な情報:<br />- この相対パスはクエリ文字列を除外します。<br />- この相対パスは URL 書き換えを反映します。 URL は、次の条件下で書き換えられます。<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL 書き換え機能:この機能は、要求 URI に定義されている相対パスを書き換えます。<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- エッジ CNAME URL:この種類の要求は、対応する CDN URL に書き換えられます。 |/800001/corigin/rewrittendir/foo.js |
| 要求 URI | %{request} | 要求を説明します。 <br />構文:&lt;HTTP メソッド&gt; &lt;相対パス&gt; &lt;HTTP プロトコル&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| 応答ヘッダー値 | %{resp_&lt;ResponseHeader&gt;} | &lt;ResponseHeader&gt; という語で識別される応答ヘッダーに対応する値を返します。 <br /><br />応答ヘッダーの名前にダッシュが含まれている (たとえば、User-Agent) 場合は、アンダースコアで置き換えます (たとえば、User_Agent)。 | 使用例: %{resp_Content_Length}<br /><br />値の例:100 |

## <a name="usage"></a>使用法
次の表で、HTTP 変数を指定するための適切な構文について説明します。


| 構文 | 例 | 説明 |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | この構文を使用して、指定された &lt;HTTPVariable&gt; に対応する値全体を取得します。 |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | この構文を使用して、指定された &lt;HTTPVariableDelimiter&gt; に対応する値全体の大文字と小文字の区別を設定します。 |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | HTTP 変数の値を置換、削除、または操作するには、&lt;HTTPVariableDelimiterExpression&gt; で正規表現を使用します。 |

HTTP 変数名は、アルファベット文字およびアンダースコアのみをサポートします。 サポートされていない文字はアンダースコアに変換してください。

## <a name="delimiter-reference"></a>区切り記号のリファレンス
HTTP 変数の後に区切り記号を指定することで、次のいずれかの効果を得ることができます。

- 変数に関連付けられている値を変換します。

     例:値全体を小文字に変換します。

- 変数に関連付けられている値を削除します。

- 変数に関連付けられている値を操作します。

     例:正規表現を使用して、HTTP 変数に関連付けられている値を変更します。

区切り記号については次の表で説明します。

| 区切り記号 | 説明 |
| --------- | ----------- |
| := | 次のいずれかであるときに、変数に既定値が割り当てられることを示します。 <br />- 見つからない <br />- NULL に設定されている。 |
| :+ | 値が既に割り当てられているときに、変数に既定値が割り当てられることを示します。 |
| : | 変数に割り当てられている値の部分文字列が展開されることを示します。 |
| # | この区切り記号の後に指定したパターンが、変数に関連付けられている値の先頭にある場合は、削除する必要があることを示します。 |
| % | この区切り記号の後に指定したパターンが、変数に関連付けられている値の末尾にある場合は、削除する必要があることを示します。 <br />この定義は、% 記号を区切り記号として使用する場合にのみ適用されます。 |
| / | HTTP 変数またはパターンを区切ります。 |
| // | 指定したパターンのすべてのインスタンスの検索と置換を行います。 |
| /= | 指定したパターンのすべてのインスタンスの検索、コピー、および書き換えを行います。 |
| , | HTTP 変数に関連付けられている値を小文字に変換します。 |
| ^ | HTTP 変数に関連付けられている値を大文字に変換します。 |
| ,, | HTTP 変数に関連付けられている値に指定した文字のすべてのインスタンスを、小文字に変換します。 |
| ^^ | HTTP 変数に関連付けられている値に指定した文字のすべてのインスタンスを、大文字に変換します。 |

## <a name="exceptions"></a>例外
次の表で、指定したテキストが HTTP 変数として扱われない状況について説明します。

| 条件 | 説明 | 例 |
| --------- | ----------- | --------|
| % 記号のエスケープ | パーセント記号は、バックスラッシュを使用してエスケープすることができます。 <br />右側にある値の例は、HTTP 変数としてではなく、リテラル値として扱われます。| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| 不明な変数 | 不明な変数には常に空の文字列が返されます。 | %{unknown_variable} |
| 無効な文字または構文 | 無効な文字または構文が含まれている変数は、リテラル値として扱われます。 <br /><br />例 1:指定した値に、無効な文字 (たとえば、-) が含まれている。 <br /><br />例 2:指定した値に、2 つの中かっこのセットが含まれている。 <br /><br />例 3:指定した値に右中かっこがない。<br /> | 例 1: %{resp_user-agent} <br /><br />例 2: %{{host}} <br /><br />例 3: %{host |
| 変数名が見つからない | 変数が指定されていない場合は、常に NULL 値が返されます。 | %{} |
| 末尾の文字 | 変数の末尾の文字は、リテラル値として扱われます。 <br />右側にある値の例には、リテラル値として扱われる末尾の中かっこが含まれています。 | %{host}} |

## <a name="setting-default-header-values"></a>既定のヘッダー値の設定
次のいずれかの条件を満たしているときは、ヘッダーに既定値を割り当てることができます。
- 見つからない/設定解除されている
- NULL に設定されている。

次の表で、既定値を定義する方法について説明します。

| 条件 | 構文 | 例 | 説明 |
| --------- | ------ | --------| ----------- |
| 次のいずれかの条件を満たしている場合は、ヘッダーを既定値に設定します。 <br /><br />- ヘッダーがない <br /><br />- ヘッダー値が NULL に設定されている。| %{Variable:=Value} | %{http_referrer:=unspecified} | Referrer ヘッダーは、見つからないか NULL に設定されている場合は *unspecified* のみに設定されます。 設定されている場合、アクションは行われません。 |
| 見つからない場合は、ヘッダーを既定値に設定します。 | %{Variable=Value} | %{http_referrer=unspecified} | Referrer ヘッダーは、見つからない場合は *unspecified* のみに設定されます。 設定されている場合、アクションは行われません。 |
| 次のどの条件も満たしていない場合は、ヘッダーを既定値に設定します。 <br /><br />- 見つからない<br /><br /> - NULL に設定されている。 | %{Variable:+Value} | %{http_referrer:+unspecified} | Referrer ヘッダーは、値が割り当てられている場合は *unspecified* のみに設定されます。 見つからないか NULL に設定されている場合、アクションは行われません。 |

## <a name="manipulating-variables"></a>変数の操作
変数は、次の方法で操作できます。

- 部分文字列の展開
- パターンの削除

### <a name="substring-expansion"></a>部分文字列の拡張
既定では、変数は完全な値に展開されます。 単に変数の値の部分文字列を展開するには、次の構文を使用します。

`%<Variable>:<Offset>:<Length>}`

重要な情報:

- Offset の語に割り当てられる値は、部分文字列の最初の文字を決定します。

     - 正の値:部分文字列の最初の文字は、文字列の最初の文字から計算されます。
     - ゼロ:部分文字列の最初の文字は、文字列の最初の文字です。
     - 負の値:部分文字列の最初の文字は、文字列の最後の文字から計算されます。

- 部分文字列の長さは、*Length* で決定されます。

     - 省略:Length という用語を省略すると、文字列の開始文字と末尾文字の間のすべての文字を部分文字列に含めることできます。
     - 正の値:開始文字から右への部分文字列の長さを決定します。
     - 負の値:開始文字から左への部分文字列の長さを決定します。

#### <a name="example"></a>例:

次の例は、次の要求 URL の例に依存します。

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

次の文字列は、変数を操作するためのさまざまな方法を示しています。

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

要求 URL の例に基づいて、上記の変数の操作で次の値が生成されます。

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>パターンの削除
特定のパターンに一致するテキストを、変数の値の先頭または末尾のいずれかから削除できます。

| 構文 | Action |
| ------ | ------ |
| %{Variable#Pattern} | 指定したパターンが変数の値の先頭で見つかった場合は、テキストを削除します。 |
| %{Variable%Pattern} | 指定したパターンが変数の値の末尾で見つかった場合は、テキストを削除します。 |

#### <a name="example"></a>例:

このサンプル シナリオでは、*request_uri* 変数は次のように設定されています。

`/800001/myorigin/marketing/product.html?language=en-US`

次の表で、この構文の動作方法を示します。

| 構文例 | 結果 | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | 変数がパターンで始まっているため、置き換えられました。 |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | 変数がパターンで終わっていないため、変更はありませんでした。|

### <a name="find-and-replace"></a>検索と置換
次の表で、検索と置換の構文について説明します。

| 構文 | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | 指定したパターンの最初の出現箇所の検索と置換を行います。 |
| %{Variable//Find/Replace} | 指定したパターンのすべての出現箇所の検索と置換を行います。 |
| %{Variable^} |値全体を大文字に変換します。 |
| %{Variable^Find} | 指定したパターンの最初の出現箇所を大文字に変換します。 |
| %{Variable,} | 値全体を小文字に変換します。 |
| %{Variable,Find} | 指定したパターンの最初の出現箇所を小文字に変換します。 |

### <a name="find-and-rewrite"></a>検索と書き換え
検索と置換のバリエーションでは、書き換えるときに指定したパターンに一致するテキストを使用します。 次の表で、検索と書き換えの構文について説明します。

| 構文 | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | 指定したパターンのすべてのインスタンスの検索、コピー、および書き換えを行います。 |
| %{Variable/^Find/Rewrite} | 指定したパターンが変数の先頭にあるときに、その検索、コピー、および書き換えを行います。 |
| %{Variable/$Find/Rewrite} | 指定したパターンが変数の末尾にあるときに、その検索、コピー、および書き換えを行います。 |
| %{Variable/Find} | 指定したパターンのすべての出現箇所を検索し、削除します。 |

重要な情報:

- ドル記号の後に整数を指定して (たとえば、$1)、指定したパターンに一致するテキストを展開します。

- 複数のパターンを指定することができます。 パターンを指定する順序が、それに割り当てられる整数を決定します。 次の例では、最初のパターンは「www.,」に一致し、2 つ目のパターンは第 2 レベル ドメインに一致し、3 つ目のパターンはトップレベル ドメインに一致します。

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- 書き換えられた値は、テキストとこれらのプレースホルダーの任意の組み合わせで構成できます。

    前の例では、ホスト名は `cdn.$2.$3:80` に書き換えられます (たとえば、cdn.mydomain.com:80)。

- パターンのプレースホルダー (たとえば、$1) の大文字と小文字の区別の場合は、次のフラグを使用して変更できます。
     - U:展開値を大文字にします。

         構文例:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L:展開値を小文字にします。

         構文例:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- 演算子は、パターンの前に指定する必要があります。 指定された演算子は、パターンのキャプチャ動作を決定します。

     - `=`:指定したパターンのすべての出現箇所をキャプチャして書き換える必要があることを示します。
     - `^`:指定したパターンで始まるテキストのみがキャプチャされることを示します。
     - `$`:指定したパターンで終わるテキストのみがキャプチャされることを示します。
 
- */Rewrite* 値を省略した場合は、パターンに一致するテキストが削除されます。


