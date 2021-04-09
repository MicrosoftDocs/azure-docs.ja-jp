---
title: OData 言語の概要
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search クエリでの filter、select、および order-by 用の OData 言語の概要。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: d04311fce81d147a0830918aee1d4a2a9c0808d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923400"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Azure Cognitive Search での `$filter`、`$orderby`、および `$select` 用の OData 言語の概要

Azure Cognitive Search では、 **$filter** 式、 **$orderby** 式、および **$select** 式で OData 式の構文のサブセットがサポートされています。 filter 式はクエリの構文解析中に評価され、検索が特定のフィールドに制約されるか、インデックス スキャン中に使用される一致条件が追加されます。 order-by 式は、返されたドキュメントを並べ替えるために、結果セットに対する後処理手順として適用されます。 select 式は、ドキュメントのどのフィールドが結果セットに含まれるかを決定します。 これらの式の構文は、**search** パラメーターで使用される [単純な](query-simple-syntax.md)または [完全な](query-lucene-syntax.md)クエリ構文とは異なりますが、参照フィールドの構文には多少の重複があります。

この記事では、filter 式、order-by 式、および select 式で使用される OData 式の言語の概要を示します。 この言語は "ボトムアップ" で提示され、最も基本的な要素から始まり、その上に構築されていきます。 各パラメーターの最上位レベルの構文は、別の記事で説明しています。

- [$filter 構文](search-query-odata-filter.md)
- [$orderby 構文](search-query-odata-orderby.md)
- [$select 構文](search-query-odata-select.md)

OData 式は単純なものから複雑なものまで幅がありますが、すべての式で共通の要素が共有されます。 Azure Cognitive Search での OData 式の最も基本的な部分は次のとおりです。

- **フィールド パス**: インデックスの特定のフィールドを参照します。
- **定数**: 特定のデータ型のリテラル値です。

> [!NOTE]
> Azure Cognitive Search の用語は、いくつかの点で [OData 標準](https://www.odata.org/documentation/)とは異なります。 Azure Cognitive Search で **フィールド** と呼ばれているものは、OData では **プロパティ** と呼ばれています。同様に、**フィールド パス** は **プロパティ パス** に相当します。 Azure Cognitive Search での **ドキュメント** を含む **インデックス** は、OData での **エンティティ** を含む **エンティティ セット** よりも広い意味で使用されています。 このリファレンス全体では、Azure Cognitive Search の用語が使用されます。

## <a name="field-paths"></a>フィールド パス

次の EBNF ([拡張バッカスナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) によって、フィールド パスの文法が定義されます。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Cognitive Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 完全な EBNF については、[Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)に関するページをご覧ください。

フィールド パスは、スラッシュで区切られた 1 つ以上の **識別子** で構成されます。 各識別子は、ASCII 文字またはアンダースコアで始まり、ASCII 文字、数字、またはアンダースコアのみを含む文字のシーケンスです。 文字には、大文字または小文字を指定できます。

識別子は、フィールドの名前を参照するか、フィルター内の [コレクション式](search-query-odata-collection-operators.md) (`any` または `all`) のコンテキストで **範囲変数** を参照できます。 範囲変数は、コレクションの現在の要素を表すループ変数に似ています。 複雑なコレクションでは、その変数はオブジェクトを表します。フィールド パスを使用して変数のサブフィールドを参照できるのはこれが理由です。 これは、多くのプログラミング言語で使用されるドット表記に似ています。

フィールド パスの例を次の表に示します。

| フィールド パス | 説明 |
| --- | --- |
| `HotelName` | インデックスの最上位レベルのフィールドを参照します。 |
| `Address/City` | インデックス内の複雑なフィールドの `City` サブフィールドを参照します。この例では、`Address` は `Edm.ComplexType` 型です。 |
| `Rooms/Type` | インデックス内の複雑なコレクション フィールドの `Type` サブフィールドを参照します。この例では、`Rooms` は `Collection(Edm.ComplexType)` 型です。 |
| `Stores/Address/Country` | インデックス内の複雑なコレクション フィールドの `Address` サブフィールドの `Country` サブフィールドを参照します。この例では、`Stores` は `Collection(Edm.ComplexType)` 型であり、`Address` は `Edm.ComplexType` 型です。 |
| `room/Type` | `room` 範囲変数の `Type` サブフィールドを参照します。例として、フィルター式内の `Rooms/any(room: room/Type eq 'deluxe')` があります。 |
| `store/Address/Country` | `store` 範囲変数の `Address` サブフィールドの `Country` サブフィールドを参照します。例として、フィルター式内の `Stores/any(store: store/Address/Country eq 'Canada')` があります。 |

フィールド パスの意味はコンテキストによって異なります。 フィルターでは、フィールド パスは、現在のドキュメント内の 1 つのフィールドの "*単一のインスタンス*" の値を参照します。 **$orderby**、 **$select**、[完全な Lucene 構文でのフィールド検索](query-lucene-syntax.md#bkmk_fields)などの他のコンテキストでは、フィールド パスはフィールドそのものを参照します。 これらの違いは、フィルターでのフィールド パスの使用方法に多少の影響を与えます。

`Address/City` というフィールド パスを検討します。 フィルターでは、これは現在のドキュメントの単一の都市、たとえば "San Francisco" (サンフランシスコ) を参照します。 一方、`Rooms/Type` は、多数の客室の `Type` サブフィールド (第 1 室は "標準"、第 2 室は "デラックス"、以下同様) を参照します。 `Rooms/Type` では、`Type` サブフィールドの "*単一のインスタンス*" は参照されないため、フィルター内で直接使用することはできません。 代わりに、客室の種類でフィルター処理するには、次のように[ラムダ式](search-query-odata-collection-operators.md)と範囲変数を使用します。

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

この例では、範囲変数 `room` が `room/Type` フィールド パスで使用されます。 これにより、`room/Type` で、現在のドキュメント内の現在の客室の種類が参照されます。 これは `Type` サブ フィールドの単一のインスタンスであるため、フィルター内で直接使用することができます。

### <a name="using-field-paths"></a>フィールド パスの使用

フィールド パスは、[Azure Cognitive Search REST API](/rest/api/searchservice/) の多数のパラメーターの中で使用されます。 次の表に、これらを使用できるすべての場所とその使用に関する制限を示します。

| API | パラメーター名 | 制限 |
| --- | --- | --- |
| インデックスの[作成](/rest/api/searchservice/create-index)または[更新](/rest/api/searchservice/update-index) | `suggesters/sourceFields` | なし |
| インデックスの[作成](/rest/api/searchservice/create-index)または[更新](/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | **検索可能** フィールドのみを参照できます |
| インデックスの[作成](/rest/api/searchservice/create-index)または[更新](/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | **フィルター処理できる** フィールドのみを参照できます |
| [Search](/rest/api/searchservice/search-documents) | `queryType` が `full` の場合の `search` | **検索可能** フィールドのみを参照できます |
| [Search](/rest/api/searchservice/search-documents) | `facet` | **ファセット可能** フィールドのみを参照できます |
| [Search](/rest/api/searchservice/search-documents) | `highlight` | **検索可能** フィールドのみを参照できます |
| [Search](/rest/api/searchservice/search-documents) | `searchFields` | **検索可能** フィールドのみを参照できます |
| [Suggest](/rest/api/searchservice/suggestions) と [Autocomplete](/rest/api/searchservice/autocomplete) | `searchFields` | [suggester](index-add-suggesters.md) の一部であるフィールドのみを参照できます |
| [Search](/rest/api/searchservice/search-documents)、[Suggest](/rest/api/searchservice/suggestions)、および [Autocomplete](/rest/api/searchservice/autocomplete) | `$filter` | **フィルター処理できる** フィールドのみを参照できます |
| [Search](/rest/api/searchservice/search-documents) と [Suggest](/rest/api/searchservice/suggestions) | `$orderby` | **並べ替え可能** フィールドのみを参照できます |
| [Search](/rest/api/searchservice/search-documents)、[Suggest](/rest/api/searchservice/suggestions)、および [Lookup](/rest/api/searchservice/lookup-document) | `$select` | **取得可能** フィールドのみを参照できます |

## <a name="constants"></a>定数

OData では、定数は特定の [Entity Data Model](/dotnet/framework/data/adonet/entity-data-model) (EDM) 型のリテラル値です。 Azure Cognitive Search でサポートされる型の一覧については、「[サポートされるデータ型](/rest/api/searchservice/supported-data-types)」を参照してください。 コレクション型の定数はサポートされていません。

次の表に、Azure Cognitive Search でサポートされている各データ型の定数の例を示します。

| データ型 | 定数の例 |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>文字列定数での特殊文字のエスケープ

OData 内の文字列定数は単一引用符で区切ります。 一重引用符を含む可能性のある文字列定数を使用してクエリを作成する必要がある場合は、埋め込まれた引用符を 2 つ入力することによってエスケープすることができます。

たとえば、"Alice's car" のような書式設定されていないアポストロフィを含む語句は、OData で文字列定数 `'Alice''s car'` として表現されます。

> [!IMPORTANT]
> プログラムによってフィルターを構築する場合は、ユーザー入力に含まれる文字列定数を忘れずにエスケープすることが重要です。 これは、特にフィルターを使用して[セキュリティによるトリミング](search-security-trimming-for-azure-search.md)を実装する場合に、[インジェクション攻撃](https://wikipedia.org/wiki/SQL_injection)の可能性が低減されるようにするためです。

### <a name="constants-syntax"></a>定数の構文

次の EBNF ([拡張バッカスナウア記法フォーム](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) によって、上記の表に示されている定数の大部分に対する文法が定義されます。 地理空間型の文法については、[Azure Cognitive Search での OData 地理空間関数](search-query-odata-geo-spatial-functions.md)に関する記事を参照してください。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
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
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Cognitive Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 完全な EBNF については、[Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)に関するページをご覧ください。

## <a name="building-expressions-from-field-paths-and-constants"></a>フィールド パスと定数からの式の構築

フィールド パスと定数は OData 式の最も基本的なパーツですが、それら自体が既に完全な式です。 実際、Azure Cognitive Search での **$select** パラメーターは単なるフィールド パスのコンマ区切りの一覧であり、 **$orderby** が **$select** よりもはるかに複雑ということはありません。 インデックスに `Edm.Boolean` 型のフィールドが含まれている場合でも、そのフィールドのパスにすぎないフィルターを記述できます。 同様に、`true` 定数と `false` 定数も有効なフィルターです。

ただし、ほとんどの場合、複数のフィールドと定数を参照するより複雑な式が必要になります。 これらの式は、パラメーターに応じてさまざまな方法で構築されます。

次の EBNF ([拡張バッカスナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) によって、 **$filter**、 **$orderby**、および **$select** の各パラメーターの文法が定義されます。 これらは、フィールド パスと定数を参照する単純な式から構築されています。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Cognitive Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 完全な EBNF については、[Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)に関するページをご覧ください。

**$orderby** パラメーターと **$select** パラメーターは、どちらも単純な式のコンマ区切りの一覧です。 **$filter** パラメーターは、単純なサブ式で構成されるブール式です。 これらのサブ式が、[`and`、`or`、`not`](search-query-odata-logical-operators.md) などの論理演算子、[`eq`、`lt`、`gt` など](search-query-odata-comparison-operators.md)の比較演算子、[`any` や `all`](search-query-odata-collection-operators.md) などのコレクション演算子を使用して結合されます。

**$filter**、 **$orderby**、および **$select** の各パラメーターの詳細を、次の記事で確認できます。

- [Azure Cognitive Search での OData $filter 構文](search-query-odata-filter.md)
- [Azure Cognitive Search での OData $orderby 構文](search-query-odata-orderby.md)
- [Azure Cognitive Search での OData $select 構文](search-query-odata-select.md)

## <a name="see-also"></a>関連項目  

- [Azure Cognitive Search のファセット ナビゲーション](search-faceted-navigation.md)
- [Azure Cognitive Search のフィルター](search-filters.md)
- [ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene クエリ構文](query-lucene-syntax.md)
- [Azure Cognitive Search でのシンプルなクエリ構文](query-simple-syntax.md)