---
title: 単純なクエリ構文
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のフルテキスト検索クエリに使用する単純なクエリ構文のリファレンスです。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923383"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search での単純なクエリ構文

Azure Cognitive Search は、2 つの Lucene ベースのクエリ言語を実装します。[Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) と [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) です。

単純なパーサーは、より柔軟であり、完全に構成されていない場合でも要求の解釈が試みられます。 この柔軟性により、Azure Cognitive Search のクエリの既定値になっています。 

単純な構文は、[ドキュメントの検索要求](/rest/api/searchservice/search-documents)の `search` パラメーターで渡されるクエリ式に使用されます。同じドキュメントの検索 API の [$filter 式](search-filters.md)パラメーターで使用される [OData 構文](query-odata-filter-orderby-syntax.md)と混同しないでください。 `search` および `$filter` パラメーターにはさまざまな構文があり、クエリの作成や文字列のエスケープなどを行うための独自の規則があります。

単純なパーサーは [Apache Lucene Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) クラスに基づいていますが、Azure Cognitive Search における実装では、あいまい検索が除外されています。 [あいまい検索](search-query-fuzzy.md)またはその他の高度なクエリ フォームを使用する必要がある場合は、代わりに代替の[完全な Lucene クエリ構文](query-lucene-syntax.md)をお勧めします。

## <a name="invoke-simple-parsing"></a>単純な解析を呼び出す

簡単な構文は既定値です。 呼び出しが必要なのは、フルから単純に構文をリセットする場合のみです。 構文を明示的に設定するには、`queryType` 検索パラメーターを使用します。 有効な値には、`queryType=simple` または `queryType=full` が含まれます。`simple` は既定値で、`full` は詳細なクエリのために[完全な Lucene クエリ パーサー](query-lucene-syntax.md)を呼び出します。 

## <a name="syntax-fundamentals"></a>構文の基礎

1 つ以上の用語を含むテキストはすべて、クエリ実行の有効な開始ポイントと見なされます。 Azure Cognitive Search は、テキストの分析中に検出されたバリエーションを含め、それらの用語の一部またはすべてを含むドキュメントをマッチングします。

単純に思われるかもしれませんが、Azure Cognitive Search でのクエリ実行には、入力文字列に追加される用語および演算子が増えるにつれて、検索結果が減るのではなく増えるという、予期しない結果を生み出す*可能性がある* 1 つの側面があります。 この拡張が実際に行われるかどうかは、NOT 演算子の組み込みと、AND または OR の動作の観点から NOT の解釈方法を決定する **searchMode** パラメーター設定の組み合わせで決まります。 詳細については、「[NOT 演算子](#not-operator)」を参照してください。

### <a name="precedence-operators-grouping"></a>優先順位の演算子 (グループ化)

かっこを使用して、かっこで囲まれたステートメント内に演算子を含むサブクエリを作成できます。 たとえば、`motel+(wifi|luxury)` を指定すると、"motel" という用語と "wifi" または "luxury" (あるいはその両方) を含むドキュメントが検索されます。

フィールドのグループ化は似ていますが、グループ化のスコープを 1 つのフィールドに設定します。 たとえば、`hotelAmenities:(gym+(wifi|pool))` を指定すると、"hotelAmenities" のフィールドで "gym" と "wifi"、または "gym" と "pool" が検索されます。  

### <a name="escaping-search-operators"></a>検索演算子のエスケープ  

単純な構文では、検索演算子には次の文字が含まれます: `+ | " ( ) ' \`  

これらの文字のいずれかがインデックスのトークンの一部として含まれている場合、クエリ内では、その文字の前に 1 つの円記号 (`\`) を付けてエスケープします。 たとえば、用語の完全なトークン化のためにカスタム アナライザーを使用し、インデックスに文字列 "Luxury+Hotel" が含まれているとします。 このトークンと完全に一致するものを取得するには、 `search=luxury\+hotel` のようにエスケープ文字を挿入します。 

より標準的なケースで操作を簡単にするために、この規則には、エスケープが不要な次の 2 つの例外があります。  

+ NOT 演算子 `-` のエスケープが必要なのは、スペースの後の最初の文字である場合のみです。 `-` が途中に現れる場合 (たとえば、`3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) は、エスケープをスキップできます。

+ サフィックス演算子 `*` は、スペースの前の最後の文字である場合にのみ、エスケープする必要があります。 `*` が途中に現れる場合 (たとえば、`4*4=16`)、エスケープは必要ありません。

> [!NOTE]  
> 標準のアナライザーでは、既定により、[字句解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)の際にハイフン、スペース、アンパサンド、およびその他の文字で単語が削除されたり、分割されたりします。 クエリ文字列内に特殊文字を残す必要がある場合は、それらをインデックス内に保持するアナライザーが必要になることがあります。 選択肢には、ハイフンでつながれた単語を保持する Microsoft 自然[言語アナライザー](index-add-language-analyzers.md)や、より複雑なパターン用のカスタム アナライザーなどが含まれます。 詳細については、[部分的な語句、パターン、特殊文字](search-query-partial-matching.md)に関する記事を参照してください。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL での安全でない文字および予約文字のエンコード

安全でない文字および予約文字はすべて、URL でエンコードするようにしてください。 たとえば、'#' は、URL 内のフラグメント ID またはアンカー ID のため、安全でない文字です。 この文字を URL で使用する場合は、`%23` にエンコードする必要があります。 '&' と '=' は、予約文字の例です。これらの文字は、Azure Cognitive Search でパラメーターを区切り、値を指定します。 詳細については、「[RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt)」を参照してください。

安全でない文字は ``" ` < > # % { } | \ ^ ~ [ ]`` です。 予約文字は `; / ? : @ = + &` です。

### <a name="querying-for-special-characters"></a>特殊文字のクエリ

場合によっては、"❤" のような絵文字や "€" のような記号などの特殊文字を検索することが必要です。 そのような場合は、使用しているアナライザーによってそれらの文字が除外されないことを確認します。標準アナライザーでは、インデックス内のトークンにならないように、特殊文字の多くが無視されます。

したがって、最初のステップとして、それらの要素トークンが考慮されるアナライザーを使用していることを確認します。 たとえば、"whitespace" アナライザーでは、空白で区切られた任意の文字シーケンスがトークンとして考慮されるため、"❤" という文字列はトークンと見なされます。 また、Microsoft English アナライザー ("en.microsoft") のようなアナライザーでは、"€" という文字列がトークンとして考慮されます。 [アナライザーをテスト](/rest/api/searchservice/test-analyzer)して、特定のクエリに対して生成されるトークンを確認できます。

Unicode 文字を使用する場合は、クエリ URL でシンボルが適切にエスケープされていることを確認します (たとえば、"❤" の場合は、エスケープ シーケンス `%E2%9D%A4+` を使用します)。 Postman では、この変換が自動的に行われます。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> クエリ サイズの上限

 Azure Cognitive Search に送信できるクエリのサイズには制限があります。 具体的には、最大で 1024 句 (AND、OR、その他で区切られた式) を持つことができます。 クエリ内の個々の用語のサイズにも約 32 KB の制限があります。 アプリケーションがプログラムで検索クエリを生成する場合は、無制限のサイズのクエリが生成されないように設計することをお勧めします。  

## <a name="boolean-search"></a>ブール検索

クエリ文字列にブール演算子 (AND、OR、NOT) を埋め込んで、一致するドキュメントの検出に使用する豊富な条件セットを作成することができます。 

### <a name="and-operator-"></a>AND 演算子 `+`

AND 演算子はプラス記号です。 たとえば、`wifi + luxury` を指定すると、`wifi` と `luxury` の両方を含むドキュメントが検索されます。

### <a name="or-operator-"></a>OR 演算子 `|`

OR 演算子は、縦棒、つまりパイプ文字です。 たとえば、`wifi | luxury` を指定すると、 `wifi` または `luxury` (あるいはその両方) を含むドキュメントが検索されます。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 演算子 `-`

NOT 演算子はマイナス記号です。 たとえば、`wifi –luxury` を指定すると、`wifi` という用語を含む、または `luxury` を含まないドキュメントが検索されます。

クエリ要求の **searchMode** パラメーターでは、NOT 演算子がついた用語を、クエリ内の他の用語と AND 演算するか OR 演算するかどうかが制御されます (他の用語に `+` または `|` 演算子がないと仮定します)。 有効な値は、`any` または `all` です。

`searchMode=any` にすると、より多くの結果を含めることによりクエリの再現率が増加し、既定で `-` は "OR NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含むドキュメント、または `luxury` という用語を含まないドキュメントのどちらにも一致します。

`searchMode=all` にすると、より少ない結果を含めることによりクエリの精度が上がり、既定で - は "AND NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含み、かつ "luxury" という用語を含まないドキュメントに一致します。 これはほぼ間違いなく、`-` 演算子にとってより直感的な動作です。 したがって、再現率ではなく精度で検索を最適化し、"*かつ*" ユーザーが検索で `-` 演算子を頻繁に使用する場合は、`searchMode=any` よりも `searchMode=all` を選択することを検討してください。

**searchMode** 設定を決定するときは、さまざまなアプリケーションでのクエリのユーザー操作パターンを検討してください。 情報を検索するユーザーは、より多くの組み込みのナビゲーション構造を持つ eコマース サイトとは対照的に、クエリに演算子を含める可能性が高くなります。

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>ワイルドカード プレフィックスの一致 (*、?)

"次の文字で始まる" クエリの場合は、語句の残りの部分のプレースホルダーとしてサフィックス演算子を追加します。 複数文字の場合はアスタリスク `*`、1 文字の場合は `?` を使用します。 たとえば、`lingui*` は "linguistic" や "linguini" と一致し、大文字と小文字の違いは無視されます。 

フィルターと同様に、プレフィックス クエリは完全一致を検索します。 そのため、関連性スコアリングは存在しません (すべての結果が 1.0 の検索スコアを受け取ります)。 プレフィックス クエリは、遅い場合があることに注意してください。インデックスが大きく、プレフィックスが少ない文字数で構成されている場合は特にそうです。 エッジ n-gram トークン化などの別の方法の方が、実行速度が速いことがあります。

語句の末尾または中間に対するサフィックスや挿入辞の照合など、他のワイルドカード クエリ バリエーションの場合は、[ワイルドカード検索に対して完全な Lucene 構文](query-lucene-syntax.md#bkmk_wildcard)を使用します。

## <a name="phrase-search-"></a>語句検索 `"`

用語検索は、1 つ以上の用語に対するクエリで、いずれかの用語が一致と見なされます。 語句検索は引用符 `" "` で囲まれた完全に一致する語句です。 たとえば、`Roach Motel` (引用符なし) では、`Roach` と `Motel` (またはそのいずれか) を含むドキュメントが検索されます。その場合、語句が指定されている場所と順序は関係ありません。一方、`"Roach Motel"` (引用符あり) では、その語句全体を一緒に、その順序で含むドキュメントだけに一致します (字句解析は適用されます)。

## <a name="see-also"></a>関連項目  

+ [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [単純な検索のクエリ例](search-query-simple-examples.md)
+ [完全な Lucene 検索のクエリ例](search-query-lucene-examples.md)
+ [Search Documents REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene クエリ構文](query-lucene-syntax.md)
+ [OData 式の構文](query-odata-filter-orderby-syntax.md)