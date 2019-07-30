---
title: OData コレクション フィルターのトラブルシューティング - Azure Search
description: Azure Search クエリでの OData コレクション フィルター エラーのトラブルシューティング。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081828"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Azure Search での OData コレクション フィルターのトラブルシューティング

Azure Search でコレクションのフィールドの[フィルター処理を行う](query-odata-filter-orderby-syntax.md)には、[`any` および `all` 演算子](search-query-odata-collection-operators.md)を**ラムダ式**と共に使用できます。 ラムダ式は、コレクションの各要素に適用されるサブフィルターです。

フィルター式のすべての機能をラムダ式内で使用できるわけではありません。 使用できる機能は、フィルター処理するコレクション フィールドのデータ型によって異なります。 そのコンテキストでサポートされていないラムダ式の機能を使おうとした場合、エラーが発生する可能性があります。 コレクションのフィールドに対する複雑なフィルターを作成しようとしてそのようなエラーが発生した場合、この記事は問題のトラブルシューティングに役立ちます。

## <a name="common-collection-filter-errors"></a>一般的なコレクション フィルター エラー

次の表では、コレクションのフィルターを実行しようとしたときに発生する可能性があるエラーの一覧を示します。 これらのエラーは、ラムダ式の内部でサポートされていないフィルター式の機能を使うと発生します。 各エラーでは、エラーを回避するためにフィルターを書き直す方法についてのガイダンスを示します。 表には、そのエラーを回避する方法についての詳細を提供するこの記事の関連セクションへのリンクも含まれています。

| エラー メッセージ | 状況 | 詳細については、次をご覧ください。 |
| --- | --- | --- |
| The function 'ismatch' has no parameters bound to the range variable 's'. Only bound field references are supported inside lambda expressions ('any' or 'all'). Please change your filter so that the 'ismatch' function is outside the lambda expression and try again. (関数 'ismatch' に範囲変数 's' にバインドされたパラメーターがありません。ラムダ式内ではバインドされたフィールド参照のみがサポートされます ('any' または 'all')。'ismatch' 関数がラムダ式の外部になるようにフィルターを変更して、もう一度お試しください。) | ラムダ式の内部での `search.ismatch` または `search.ismatchscoring` の使用 | [複雑なコレクションのフィルター処理に関する規則](#bkmk_complex) |
| Invalid lambda expression. Found a test for equality or inequality where the opposite was expected in a lambda expression that iterates over a field of type Collection(Edm.String). For 'any', please use expressions of the form 'x eq y' or 'search.in(...)'. For 'all', please use expressions of the form 'x ne y', 'not (x eq y)', or 'not search.in(...)'. (無効なラムダ式です。Collection(Edm.String) 型のフィールドに対して反復処理を行うラムダ式において、逆のテストが必要な場所で等値または非等値のテストが見つかりました。'any' の場合は、'x eq y' または 'search.in(...)' の形式の式を使用してください。'all' の場合は、'x ne y'、'not (x eq y)'、または 'not search.in(...)' の形式の式を使用してください。) | `Collection(Edm.String)` 型のフィールドでのフィルター処理 | [文字列コレクションのフィルター処理に関する規則](#bkmk_strings) |
| Invalid lambda expression. Found an unsupported form of complex Boolean expression. For 'any', please use expressions that are 'ORs of ANDs', also known as Disjunctive Normal Form. For example: '(a and b) or (c and d)' where a, b, c, and d are comparison or equality sub-expressions. For 'all', please use expressions that are 'ANDs of ORs', also known as Conjunctive Normal Form. For example: '(a or b) and (c or d)' where a, b, c, and d are comparison or inequality sub-expressions. Examples of comparison expressions: 'x gt 5', 'x le 2'. Example of an equality expression: 'x eq 5'. Example of an inequality expression: 'x ne 5'. (無効なラムダ式です。サポートされない形式の複合ブール式が見つかりました。'any' の場合は、'ORs of ANDs' (選言標準形) の式を使用してください。例: '(a and b) or (c and d)'、ここで a、b、c、d は比較または等値サブ式。'all' の場合は、'ANDs of ORs' (連言標準形) の式を使用してください。例: '(a or b) and (c or d)'、ここで a、b、c、d は比較または非等値サブ式。比較式の例: 'x gt 5'、'x le 2'。等値式の例: 'x eq 5'。非等値式の例: 'x ne 5'。) | `Collection(Edm.DateTimeOffset)`、`Collection(Edm.Double)`、`Collection(Edm.Int32)`、または `Collection(Edm.Int64)` 型のフィールドでのフィルター処理 | [比較可能なコレクションのフィルター処理に関する規則](#bkmk_comparables) |
| Invalid lambda expression. Found an unsupported use of geo.distance() or geo.intersects() in a lambda expression that iterates over a field of type Collection(Edm.GeographyPoint). For 'any', make sure you compare geo.distance() using the 'lt' or 'le' operators and make sure that any usage of geo.intersects() is not negated. For 'all', make sure you compare geo.distance() using the 'gt' or 'ge' operators and make sure that any usage of geo.intersects() is negated. (無効なラムダ式です。Collection(Edm.GeographyPoint) 型のフィールドを反復処理するラムダ式で、geo.distance() または geo.intersects() のサポートされない使用が見つかりました。'any' の場合は、'lt' または 'le' 演算子を使用して geo.distance() を比較していること、および geo.intersects() のどの使用も否定されていないことを確認してください。'all' の場合は、'gt' または 'ge' 演算子を使用して geo.distance() を比較していること、および geo.intersects() のどの使用も否定されていることを確認してください。) | `Collection(Edm.GeographyPoint)` 型のフィールドでのフィルター処理 | [GeographyPoint コレクションのフィルター処理に関する規則](#bkmk_geopoints) |
| Invalid lambda expression. Complex Boolean expressions are not supported in lambda expressions that iterate over fields of type Collection(Edm.GeographyPoint). For 'any', please join sub-expressions with 'or'; 'and' is not supported. For 'all', please join sub-expressions with 'and'; 'or' is not supported. (無効なラムダ式です。Collection(Edm.GeographyPoint) 型のフィールドを反復処理するラムダ式では、複合フール式はサポートされていません。'any' の場合は、'or' でサブ式を結合してください。'and' はサポートされていません。'all' の場合は、'and' でサブ式を結合してください。'or' はサポートされていません。) | `Collection(Edm.String)` または `Collection(Edm.GeographyPoint)` 型のフィールドでのフィルター処理 | [文字列コレクションのフィルター処理に関する規則](#bkmk_strings) <br/><br/> [GeographyPoint コレクションのフィルター処理に関する規則](#bkmk_geopoints) |
| Invalid lambda expression. Found a comparison operator (one of 'lt', 'le', 'gt', or 'ge'). Only equality operators are allowed in lambda expressions that iterate over fields of type Collection(Edm.String). For 'any', please use expressions of the form 'x eq y'. For 'all', please use expressions of the form 'x ne y' or 'not (x eq y)'. (無効なラムダ式です。Collection(Edm.String) 型のフィールドを反復処理するラムダ式では、等値演算子のみが許可されます。'any' の場合は、'x eq y' の形式の式を使用してください。'all' の場合は、'x ne y' または 'not (x eq y)' の形式の式を使用してください。) | `Collection(Edm.String)` 型のフィールドでのフィルター処理 | [文字列コレクションのフィルター処理に関する規則](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>有効なコレクション フィルターを記述する方法

有効なコレクション フィルターを記述するための規則は、データ型により異なります。 以下のセクションでは、サポートされているフィルター機能とサポートされていないフィルター機能の例を示すことにより、規則について説明します。

- [文字列コレクションのフィルター処理に関する規則](#bkmk_strings)
- [ブール型コレクションのフィルター処理に関する規則](#bkmk_bools)
- [GeographyPoint コレクションのフィルター処理に関する規則](#bkmk_geopoints)
- [比較可能なコレクションのフィルター処理に関する規則](#bkmk_comparables)
- [複雑なコレクションのフィルター処理に関する規則](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>文字列コレクションのフィルター処理に関する規則

文字列コレクションに対するラムダ式の内部では、使用できる比較演算子は `eq` と `ne` だけです。

> [!NOTE]
> Azure Search では、ラムダ式の内部でも外部でも、文字列に対する `lt`/`le`/`gt`/`ge` 演算子はサポートされていません。

`any` の本体では等値のテストだけを行うことができ、`all` の本体では非等値のテストだけを行うことができます。

また、`any` の本体では `or` を使って、`all` の本体では `and` を使って、複数の式を組み合わせることもできます。 `search.in` 関数は `or` による等値チェックの結合と同じなので、それも `any` の本体で使用できます。 逆に、`not search.in` は `all` の本体で使用できます。

たとえば、次の式は使用できます。

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

一方、次の式は使用できません。

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>ブール型コレクションのフィルター処理に関する規則

`Edm.Boolean` 型では、`eq` および `ne` 演算子のみがサポートされます。 そのため、`and`/`or` で同じ範囲変数をチェックする句を組み合わせても、常に同語反復または矛盾になるため、意味がありません。

許可されるブール型コレクションのフィルター処理の例を次に示します。

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

文字列コレクションとは異なり、ブール型コレクションには、演算子を使用できるラムダ式の型についての制限はありません。 `eq` と `ne` はどちらも、`any` または `all` の本体で使用できます。

次のような式は、ブール型コレクションでは使用できません。

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>GeographyPoint コレクションのフィルター処理に関する規則

コレクション内の `Edm.GeographyPoint` 型の値を相互に直接比較することはできません。 代わりに、`geo.distance` および `geo.intersects` 関数に対するパラメーターとして使う必要があります。 一方、`geo.distance` 関数は、比較演算子 `lt`、`le`、`gt`、または `ge` のいずれかを使って距離の値と比較する必要があります。 これらの規則は、コレクションではない Edm.GeographyPoint のフィールドにも適用されます。

文字列コレクションと同様に、`Edm.GeographyPoint` コレクションには、異なる型のラムダ式で地理空間関数を使用および結合できる方法についていくつかの規則があります。

- `geo.distance` 関数で使用できる比較演算子は、ラムダ式の型によって異なります。 `any` の場合は、`lt` または `le` のみを使用できます。 `all` の場合は、`gt` または `ge` のみを使用できます。 `geo.distance` が含まれる式を否定できますが、比較演算子を変更する必要があります (`geo.distance(...) lt x` は `not (geo.distance(...) ge x)` になり、`geo.distance(...) le x` は `not (geo.distance(...) gt x)` になります)。
- `all` の本体では、`geo.intersects` 関数を否定する必要があります。 逆に、`any` の本体では、`geo.intersects` 関数を否定しないようにする必要があります。
- `any` の本体では、`or` を使って地理空間式を組み合わせることができます。 `all` の本体では、`and` を使ってそのような式を組み合わせることができます。

上記の制限は、文字列コレクションでの等値/非等値の制限と同様の理由で存在します。 これらの理由について詳しくは、「[Azure Search での OData コレクション フィルターの概要](search-query-understand-collection-filters.md)」をご覧ください。

許可される `Edm.GeographyPoint` コレクションのフィルター処理の例を次に示します。

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

次のような式は、`Edm.GeographyPoint` コレクションでは使用できません。

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>比較可能なコレクションのフィルター処理に関する規則

このセクションは、次のすべてのデータ型に適用されます。

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

`Edm.Int32` や `Edm.DateTimeOffset` などの型では、6 つの比較演算子 `eq`、`ne`、`lt`、`le`、`gt`、`ge` がすべてサポートされます。 これらの型のコレクションに対するラムダ式には、これらの演算子のいずれかを使用する単純な式を含めることができます。 これは、`any` と `all` の両方に適用されます。 たとえば、次のフィルターは使用できます。

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

ただし、ラムダ式の内部でそのような比較式を組み合わせてより複雑な式にする方法に関しては制限があります。

- `any` に関する規則:
  - 単純な非等値式を他の任意の式と組み合わせることはできません。 たとえば、次の式は使用できます。
    - `ratings/any(r: r ne 5)`

    しかし、次の式は使用できません。
    - `ratings/any(r: r ne 5 and r gt 2)`

    また、次の式は使用できますが、条件が重複しているため役には立ちません。
    - `ratings/any(r: r ne 5 or r gt 7)`
  - `eq`、`lt`、`le`、`gt`、または `ge` を含む単純な比較式は、`and`/`or` と組み合わせることができます。 例:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - `and` (積) で組み合わされた比較式を、`or` を使ってさらに組み合わせることができます。 この形式は、ブール ロジックでは "[選言標準形](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF) と呼ばれます。 例:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- `all` に関する規則:
  - 単純な等値式を他の任意の式と組み合わせることはできません。 たとえば、次の式は使用できます。
    - `ratings/all(r: r eq 5)`

    しかし、次の式は使用できません。
    - `ratings/all(r: r eq 5 or r le 2)`

    また、次の式は使用できますが、条件が重複しているため役には立ちません。
    - `ratings/all(r: r eq 5 and r le 7)`
  - `ne`、`lt`、`le`、`gt`、または `ge` を含む単純な比較式は、`and`/`or` と組み合わせることができます。 例:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - `or` (和) で組み合わされた比較式を、`and` を使ってさらに組み合わせることができます。 この形式は、ブール ロジックでは "[連言標準形](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF) と呼ばれます。 例:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>複雑なコレクションのフィルター処理に関する規則

複雑なコレクションに対するラムダ式では、プリミティブ型のコレクションに対するラムダ式より、はるかに柔軟な構文がサポートされます。 そのようなラムダ式の内部では、ラムダ式の外部で使用できるフィルター構造のうち、2 つの例外を除くすべてを使用できます。

第 1 に、関数 `search.ismatch` と `search.ismatchscoring` は、ラムダ式の内部ではサポートされていません。 詳しくは、「[Azure Search での OData コレクション フィルターの概要](search-query-understand-collection-filters.md)」をご覧ください。

第 2 に、範囲変数に "*バインド*" されていない参照フィールド (いわゆる "*自由変数*") は、使用できません。 たとえば、次のような 2 つの同等の OData フィルター式について考えます。

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

1 つ目の式は使用できますが、2 つ目の形式は `details/margin` が範囲変数 `s` にバインドされていないため使用できません。

この規則は、外側のスコープにバインドされた変数がある式にも拡張されます。 そのような変数は、それらが出現するスコープに関して自由です。 たとえば、1 つ目の式は使用できますが、2 つ目の同等の式は `s/name` が範囲変数 `a` のスコープに関して自由であるため使用できません。

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

ラムダ式にバインドされた変数のみが含まれるようにフィルターを作成することが常に可能であるため、この制限は実際には問題にならないはずです。

## <a name="cheat-sheet-for-collection-filter-rules"></a>コレクションのフィルター規則に関するチート シート

次の表は、各コレクションのデータ型に対して有効なフィルターを作成するための規則をまとめたものです。

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

各ケースについて有効なフィルターを作成する方法の例については、「[有効なコレクション フィルターを記述する方法](#bkmk_examples)」をご覧ください。

フィルターを頻繁に作成し、単に憶えるより最初の原則から規則を理解する方が役に立つ場合は、「[Azure Search での OData コレクション フィルターの概要](search-query-understand-collection-filters.md)」をご覧ください。

## <a name="next-steps"></a>次の手順  

- [Azure Search での OData コレクション フィルターの概要](search-query-understand-collection-filters.md)
- [Azure Search のフィルター](search-filters.md)
- [Azure Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
