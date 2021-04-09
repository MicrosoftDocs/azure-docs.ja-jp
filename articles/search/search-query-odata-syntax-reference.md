---
title: OData 式の構文リファレンス
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のクエリにおける OData 式の正式な文法と構文仕様。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 83e9ae4aa68bc0c819d02a0cc6c39758549811cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88928855"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Azure Cognitive Search の OData 式構文リファレンス

Azure Cognitive Search では、API 全体でパラメーターとして [OData 式](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html)を使用します。 `$orderby` および `$filter` パラメーターに OData 式を使用するのが最も一般的です。 これらの式は、複数の句、関数、および演算子を含む複合式になることがあります。 ただし、Azure Cognitive Search REST API の多くの部分では、プロパティ パスのようなシンプルな OData 式も使用されます。 たとえば、[suggester](index-add-suggesters.md) 内のサブフィールドの一覧表示、[スコアリング関数](index-add-scoring-profiles.md)、`$select` パラメーター、さらには [Lucene クエリのフィールド化検索](query-lucene-syntax.md)など、API のいたるところで、パス式を使用して複合フィールドのサブフィールドを参照します。

この記事では、これらすべての形式の OData 式について、正式な文法を使用して説明します。 文法を視覚的に調べるために役立つ[対話形式のダイアグラム](#syntax-diagram)もあります。

## <a name="formal-grammar"></a>正式な文法

Azure Cognitive Search でサポートされている OData 言語のサブセットは、EBNF ([拡張バッカス・ナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 文法を使用して記述できます。 規則は "トップダウン" で列挙しています。最も複雑な式から始めて、よりプリミティブな式に分解していきます。 上部には、Azure Cognitive Search REST API の特定のパラメーターに対応する文法規則があります。

- [`$filter`](search-query-odata-filter.md)、`filter_expression` 規則によって定義。
- [`$orderby`](search-query-odata-orderby.md)、`order_by_expression` 規則によって定義。
- [`$select`](search-query-odata-select.md)、`select_expression` 規則によって定義。
- フィールド パス、`field_path` 規則によって定義。 フィールド パスは API 全体で使用されます。 インデックスの最上位フィールド、または、1 つ以上の[複合フィールド](search-howto-complex-data-types.md)を先祖に持つサブフィールドのいずれかを参照できます。

EBNF に続くのは、文法や、文法規則間の関係を対話的に調べられるブラウズ可能な[構文ダイアグラム](https://en.wikipedia.org/wiki/Syntax_diagram)です。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>構文ダイアグラム

Azure Cognitive Search でサポートされている OData 言語の文法を視覚的に調べるには、対話形式の構文ダイアグラムを試してみてください。

> [!div class="nextstepaction"]
> [Azure Cognitive Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>関連項目  

- [Azure Cognitive Search のフィルター](search-filters.md)
- [ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene クエリ構文](query-lucene-syntax.md)
- [Azure Cognitive Search でのシンプルなクエリ構文](query-simple-syntax.md)