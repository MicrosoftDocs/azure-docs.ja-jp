---
title: 単純なクエリ構文
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のフルテキスト検索クエリに使用する単純なクエリ構文のリファレンスです。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516587"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search での単純なクエリ構文

Azure Cognitive Search は、2 つの Lucene ベースのクエリ言語を実装します。[Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) と [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) です。 単純なパーサーは、より柔軟であり、完全に構成されていない場合でも要求の解釈が試みられます。 この柔軟性により、Azure Cognitive Search のクエリの既定値になっています。

単純な構文は、[ドキュメントの検索 (REST API)](/rest/api/searchservice/search-documents) 要求の **`search`** パラメーターで渡されるクエリ式に使用されます。同じ要求の [ **`$filter`**](search-filters.md) および [ **`$orderby`**](search-query-odata-orderby.md) に使用される [OData 構文](query-odata-filter-orderby-syntax.md)と混同しないでください。 OData パラメーターにはさまざまな構文があり、クエリの作成や文字列のエスケープなどを行うための規則があります。

単純なパーサーは [Apache Lucene Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) クラスに基づいていますが、Cognitive Search における実装では、あいまい検索が除外されています。 [あいまい検索](search-query-fuzzy.md)を使用する必要がある場合は、代わりに代替の[完全な Lucene クエリ構文](query-lucene-syntax.md)をお勧めします。

## <a name="example-simple-syntax"></a>例 (単純な構文)

**`queryType`** は下に設定されていますが、これは既定値であり、代替の型から元に戻す場合を除き、省略できます。 次の例は、一致するすべてのドキュメントに "pool" が含まれていることを要件とした、独立した用語の検索です。

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

**`searchMode`** パラメーターは、この例で関連しています。 クエリにブール演算子があるときは、通常 `searchMode=all` を設定して、*すべて* の条件が確実に一致するようにします。 それ以外の場合は、精度を超える再現率を優先する既定の `searchMode=any` を使用できます。

その他の例については、[単純なクエリ構文の例](search-query-simple-examples.md)に関するページをご覧ください。 クエリ要求とパラメーターの詳細については、「[ドキュメントの検索 (REST API)](/rest/api/searchservice/Search-Documents)」をご覧ください。

## <a name="keyword-search-on-terms-and-phrases"></a>用語と語句のキーワード検索

**`search`** パラメーターに渡される文字列には、サポートされている任意の言語の用語や語句、ブール演算子、優先順位演算子、"次で始まる" クエリに対するワイルドカードまたはプレフィックス文字、エスケープ文字、URL エンコード文字を含めることができます。 **`search`** パラメーターは省略可能です。 指定されていない場合、search (`search=*` または `search=" "`) は、上位 50 のドキュメントを任意の (ランク付けされていない) 順序で返します。

+ *用語検索* は、1 つ以上の用語のクエリで、いずれかの用語が一致と見なされます。

+ *語句検索* は引用符 `" "` で囲まれた完全に一致する語句です。 たとえば、```Roach Motel``` (引用符なし) では、```Roach``` と ```Motel``` (またはそのいずれか) を含むドキュメントが検索されます。その場合、語句が指定されている場所と順序は関係ありません。一方、```"Roach Motel"``` (引用符あり) では、その語句全体を一緒に、その順序で含むドキュメントだけに一致します (字句解析は適用されます)。 

  検索クライアントによっては、語句検索で引用符をエスケープすることが必要になる場合があります。 たとえば、POST 要求の Postman では、要求本文内の `"Roach Motel"` での語句検索が `"\"Roach Motel\""`として指定されます。

既定では、 **`search`** パラメーターで渡されるすべての用語または語句は字句解析を受けます。 必ず、使用しているアナライザーのトークン化動作を理解してください。 多くの場合、クエリ結果が予期しないものであるときには、クエリ時に用語をトークン化した方法にまで、その理由をトレースできます。

1 つ以上の用語を含むテキストはすべて、クエリ実行の有効な開始ポイントと見なされます。 Azure Cognitive Search は、テキストの分析中に検出されたバリエーションを含め、それらの用語の一部またはすべてを含むドキュメントをマッチングします。

単純に思われるかもしれませんが、Azure Cognitive Search でのクエリ実行には、入力文字列に追加される用語および演算子が増えるにつれて、検索結果が減るのではなく増えるという、予期しない結果を生み出す *可能性がある* 1 つの側面があります。 この拡張が実際に行われるかどうかは、NOT 演算子の組み込みと、AND または OR の動作の観点から NOT の解釈方法を決定する **`searchMode`** パラメーター設定の組み合わせで決まります。 詳細については、「[ブール演算子](#boolean-operators)」で NOT 演算子をご覧ください。

## <a name="boolean-operators"></a>ブール演算子

照合の精度を向上させるために、クエリ文字列にブール演算子を埋め込むことができます。 単純な構文では、ブール演算子は文字ベースです。 テキスト演算子 (AND など) はサポートされていません。

| 文字 | 例 | 使用法 |
|----------- |--------|-------|
| `+` | `pool + ocean` | AND 演算子。 たとえば、`pool + ocean` は、ドキュメントに両方の用語が含まれている必要があることを規定します。|
| `|` | `pool | ocean` | OR 演算子は、いずれかの用語が見つかった場合に一致を検索します。 この例では、クエリ エンジンは、`pool` または `ocean` のいずれか、あるいはその両方を含むドキュメントに対して一致を返します。 OR は、既定の結合演算子のため、除外することもできます。たとえば、`pool ocean` は `pool | ocean` と同等です。|
| `-` | `pool – ocean` | NOT 演算子は、用語を除外するドキュメントに対して一致を返します。 <br/><br/>NOT 式で予期される動作を得るには、要求に対して **`searchMode=all`** を設定することを検討してください。 それ以外の場合、既定の **`searchMode=any`** では、`pool` での一致に加え、`ocean` が含まれていないすべてのドキュメント (大量のドキュメントになることがあります) 上の一致が得られます。 クエリ要求の **`searchMode`** パラメーターでは、NOT 演算子がついた用語を、クエリ内の他の用語と AND 演算するか OR 演算するかどうかが制御されます (他の用語に `+` または `|` 演算子がないと仮定します)。 **`searchMode=all`** を使用すると、より少ない結果を含めることによりクエリの精度が上がり、既定で - は "AND NOT" と解釈されます。 <br/><br/>**`searchMode`** 設定を決定するときは、さまざまなアプリケーションでのクエリのユーザー操作パターンを検討してください。 情報を検索するユーザーは、より多くの組み込みのナビゲーション構造を持つ eコマース サイトとは対照的に、クエリに演算子を含める可能性が高くなります。 |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>プレフィックス クエリ

"次の文字で始まる" クエリの場合は、語句の残りの部分のプレースホルダーとしてサフィックス演算子 (`*`) を追加します。 プレフィックス クエリは、サフィックス演算子を追加する前に、少なくとも 1 つの英数字で始まる必要があります。

| 文字 | 例 | 使用法 |
|----------- |--------|-------|
| `*` | `lingui*` は、"linguistic" または "linguini" で照合します | アスタリスク (`*`) は、大文字と小文字を区別しない、任意の長さの 1 つまたは複数の文字を表します。  |

フィルターと同様に、プレフィックス クエリは完全一致を検索します。 そのため、関連性スコアリングは存在しません (すべての結果が 1.0 の検索スコアを受け取ります)。 プレフィックス クエリは、遅い場合があることに注意してください。インデックスが大きく、プレフィックスが少ない文字数で構成されている場合は特にそうです。 エッジ n-gram トークン化などの別の方法の方が、実行速度が速いことがあります。

単純な構文は、プレフィックスの照合のみをサポートします。 語句の末尾または中間に対するサフィックスや挿入辞の照合の場合は、[ワイルドカード検索に対して完全な Lucene 構文](query-lucene-syntax.md#bkmk_wildcard)を使用します。

## <a name="escaping-search-operators"></a>検索演算子のエスケープ  

単純な構文では、検索演算子には次の文字が含まれます: `+ | " ( ) ' \`  

これらの文字のいずれかがインデックスのトークンの一部として含まれている場合、クエリ内では、その文字の前に 1 つの円記号 (`\`) を付けてエスケープします。 たとえば、用語の完全なトークン化のためにカスタム アナライザーを使用し、インデックスに文字列 "Luxury+Hotel" が含まれているとします。 このトークンと完全に一致するものを取得するには、`search=luxury\+hotel` のようにエスケープ文字を挿入します。

より標準的なケースで操作を簡単にするために、この規則には、エスケープが不要な次の 2 つの例外があります。  

+ NOT 演算子 `-` のエスケープが必要なのは、スペースの後の最初の文字である場合のみです。 `-` が途中に現れる場合 (たとえば、`3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) は、エスケープをスキップできます。

+ サフィックス演算子 `*` は、スペースの前の最後の文字である場合にのみ、エスケープする必要があります。 `*` が途中に現れる場合 (たとえば、`4*4=16`)、エスケープは必要ありません。

> [!NOTE]  
> 標準のアナライザーでは、既定により、[字句解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)の際にハイフン、スペース、アンパサンド、およびその他の文字で単語が削除されたり、分割されたりします。 クエリ文字列内に特殊文字を残す必要がある場合は、それらをインデックス内に保持するアナライザーが必要になることがあります。 選択肢には、ハイフンでつながれた単語を保持する Microsoft 自然[言語アナライザー](index-add-language-analyzers.md)や、より複雑なパターン用のカスタム アナライザーなどが含まれます。 詳細については、[部分的な語句、パターン、特殊文字](search-query-partial-matching.md)に関する記事を参照してください。

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL での安全でない文字および予約文字のエンコード

安全でない文字および予約文字はすべて、URL でエンコードするようにしてください。 たとえば、'#' は、URL 内のフラグメント ID またはアンカー ID のため、安全でない文字です。 この文字を URL で使用する場合は、`%23` にエンコードする必要があります。 '&' と '=' は、予約文字の例です。これらの文字は、Azure Cognitive Search でパラメーターを区切り、値を指定します。 詳細については、「[RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt)」をご覧ください。

安全でない文字は ``" ` < > # % { } | \ ^ ~ [ ]`` です。 予約文字は `; / ? : @ = + &` です。

## <a name="special-characters"></a>特殊文字

場合によっては、"❤" のような絵文字や "€" のような記号などの特殊文字を検索することが必要です。 そのような場合は、使用しているアナライザーによってそれらの文字が除外されないことを確認します。標準アナライザーでは多くの特殊文字が無視され、インデックスから除外されます。

特殊文字をトークン化するアナライザーには、"whitespace" アナライザーが含まれ、ここでは、空白で区切られた任意の文字シーケンスがトークンとして考慮されます (したがって "❤" という文字列はトークンと見なされます)。 また、Microsoft English アナライザー ("en.microsoft") のような言語アナライザーでは、"€" という文字列がトークンとして見なされます。 [アナライザーをテスト](/rest/api/searchservice/test-analyzer)して、特定のクエリに対して生成されるトークンを確認できます。

Unicode 文字を使用する場合は、クエリ URL でシンボルが適切にエスケープされていることを確認します (たとえば、"❤" の場合は、エスケープ シーケンス `%E2%9D%A4+` を使用します)。 Postman では、この変換が自動的に行われます。  

## <a name="precedence-grouping"></a>優先順位 (グループ化)

かっこを使用して、かっこで囲まれたステートメント内に演算子を含むサブクエリを作成できます。 たとえば、`motel+(wifi|luxury)` を指定すると、"motel" という用語と "wifi" または "luxury" (あるいはその両方) を含むドキュメントが検索されます。

## <a name="query-size-limits"></a> クエリ サイズの上限

アプリケーションがプログラムで検索クエリを生成する場合は、無制限のサイズのクエリが生成されないように設計することをお勧めします。 

+ GET の場合、URL の長さは 8 KB を超えることはできません。

+ POST (およびその他の要求) については、要求の本文に `search` とその他のパラメーター (`filter` や `orderby` など) が含まれている場合、最大サイズは 16 MB です。この場合、`search` (AND、OR などで区切られた式) 内の句の最大数は 1024 です。 クエリ内の個々の用語のサイズにも約 32 KB の制限があります。 詳細については、「[API 要求の制限](search-limits-quotas-capacity.md#api-request-limits)」をご覧ください。

## <a name="next-steps"></a>次の手順

プログラムを使用してクエリを作成する場合は、「[Azure Cognitive Search でのフルテキスト検索](search-lucene-query-architecture.md)」を参照して、クエリ処理の段階とテキスト分析の影響について理解してください。

また、クエリ構築の詳細について、次の記事をご覧ください。

+ [単純な検索のクエリ例](search-query-simple-examples.md)
+ [完全な Lucene 検索のクエリ例](search-query-lucene-examples.md)
+ [Search Documents REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene クエリ構文](query-lucene-syntax.md)
+ [(OData) 式構文のフィルター処理と選択](query-odata-filter-orderby-syntax.md)