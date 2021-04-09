---
title: Azure CDN ルール エンジンの HTTP 変数 | Microsoft Docs
description: 一部のルール エンジン機能に関して、HTTP 要求および応答メタデータを取得できる HTTP 変数について説明します。 要求と応答を変更するにはメタデータを使用します。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: allensu
ms.openlocfilehash: a2d9fc98ba6f514afbd88e543a859a69e0fc6c6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192672"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN ルール エンジンの HTTP 変数
HTTP 変数は、HTTP 要求と応答のメタデータを取得するための手段を提供します。 このメタデータを使用して、要求または応答を動的に変更できます。 HTTP 変数の使用は、次のルール エンジンの機能に限定されています。

- [Cache-Key Rewrite (キャッシュキー書き換え)](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm)
- [Modify Client Request Header (クライアント要求ヘッダーの修正)](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm)
- [Modify Client Response Header (クライアント応答ヘッダーの修正)](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm)
- [URL Redirect (URL リダイレクト)](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm)
- [URL Rewrite (URL 書き換え)](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm)

## <a name="definitions"></a>定義
次の表で、サポートされている HTTP 変数について説明します。 GEO メタデータ (たとえば、郵便番号) が特定の要求に利用できない場合は、空白の値が返されます。


| 名前 | 変数 | 説明 | 値の例 |
| ---- | -------- | ----------- | ------------ |
| ASN (要求者) | %{geo_asnum} | 要求者の AS 番号を示します。 <br /><br />**非推奨:** %{virt_dst_asnum}。 <br />%{geo_asnum} を優先して、この変数は非推奨となりました。 この非推奨の変数を使用するルールは今後も正常に機能しますが、更新して新しい変数を使用する必要があります。 | AS15133 |
| 市区町村 (要求者) | %{geo_city} | 要求者の市区町村を示します。 | Los Angeles |
| 大陸 (要求者) | %{geo_continent} | 要求者の大陸を省略形で示します。 <br />有効な値は次のとおりです。 <br />AF:アフリカ<br />AS:アジア<br />EU:ヨーロッパ<br />NA:北米<br />OC:オセアニア<br />SA:南アメリカ<br /><br />**非推奨:** %{virt_dst_continent}。 <br />%{geo_continent} を優先して、この変数は非推奨となりました。 <br />この非推奨の変数を使用するルールは今後も正常に機能しますが、更新して新しい変数を使用する必要があります。| 該当なし |
| Cookie 値 | %{cookie_Cookie} | Cookie という語で識別される Cookie キーに対応する値を返します。 | 使用例: <br />%{cookie__utma}<br /><br />値の例:<br />111662281.2.10.1222100123 |
| 国および地域 (要求者) | %{geo_country} | 国および地域コードによって配信元の要求者の国および地域を示します。 <br />**非推奨:** %{virt_dst_country}。 <br /><br />%{geo_country} を優先して、この変数は非推奨となりました。 この非推奨の変数を使用するルールは今後も正常に機能しますが、更新して新しい変数を使用する必要があります。 | US |
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

| 構文 | アクション |
| ------ | ------ |
| %{Variable#Pattern} | 指定したパターンが変数の値の先頭で見つかった場合は、テキストを削除します。 |
| %{Variable%Pattern} | 指定したパターンが変数の値の末尾で見つかった場合は、テキストを削除します。 |

#### <a name="example"></a>例:

このサンプル シナリオでは、*request_uri* 変数は次のように設定されています。

`/800001/myorigin/marketing/product.html?language=en-US`

次の表で、この構文の動作方法を示します。

| 構文例 | 結果 | 説明 |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | 変数がパターンで始まっているため、置き換えられました。 |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | 変数がパターンで終わっていないため、変更はありませんでした。|

### <a name="find-and-replace"></a>検索と置換
次の表で、検索と置換の構文について説明します。

| 構文 | アクション |
| ------ | ------ |
| %{Variable/Find/Replace} | 指定したパターンの最初の出現箇所の検索と置換を行います。 |
| %{Variable//Find/Replace} | 指定したパターンのすべての出現箇所の検索と置換を行います。 |
| %{Variable^} |値全体を大文字に変換します。 |
| %{Variable^Find} | 指定したパターンの最初の出現箇所を大文字に変換します。 |
| %{Variable,} | 値全体を小文字に変換します。 |
| %{Variable,Find} | 指定したパターンの最初の出現箇所を小文字に変換します。 |

### <a name="find-and-rewrite"></a>検索と書き換え
検索と置換のバリエーションでは、書き換えるときに指定したパターンに一致するテキストを使用します。 次の表で、検索と書き換えの構文について説明します。

| 構文 | アクション |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | 指定したパターンのすべてのインスタンスの検索、コピー、および書き換えを行います。 |
| %{Variable/^Find/Rewrite} | 指定したパターンが変数の先頭にあるときに、その検索、コピー、および書き換えを行います。 |
| %{Variable/$Find/Rewrite} | 指定したパターンが変数の末尾にあるときに、その検索、コピー、および書き換えを行います。 |
| %{Variable/Find} | 指定したパターンのすべての出現箇所を検索し、削除します。 |

重要な情報:

- ドル記号の後に整数を指定して (たとえば、$1)、指定したパターンに一致するテキストを展開します。

- 複数のパターンを指定することができます。 パターンを指定する順序が、それに割り当てられる整数を決定します。 次の例では、最初のパターンは「 www.,」に一致し、2 つ目のパターンは第 2 レベル ドメインに一致し、3 つ目のパターンはトップレベル ドメインに一致します。

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


