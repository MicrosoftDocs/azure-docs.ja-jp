---
title: OData 論理演算子のリファレンス - Azure Search
description: Azure Search クエリでの OData 論理演算子 (and、or、not)。
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081872"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Azure Search クエリでの OData 論理演算子 - `and`、`or`、`not`

Azure Search での [OData フィルター式](query-odata-filter-orderby-syntax.md)は、`true` または `false` に評価されるブール式です。 一連の[よりシンプルなフィルター](search-query-odata-comparison-operators.md)を記述し、それらを[ブール代数](https://en.wikipedia.org/wiki/Boolean_algebra)の論理演算子を使って作成することで、複雑なフィルターを記述することができます。

- `and`:左と右のサブ式が両方とも `true` に評価された場合に `true` に評価される 2 項演算子です。
- `or`:左または右のサブ式のいずれかが `true` に評価された場合に `true` に評価される 2 項演算子です。
- `not`:サブ式が `false` に評価された場合に `true` に評価される、またはその逆が当てはまる単項演算子です。

これらを、[コレクション演算子 `any` および `all`](search-query-odata-collection-operators.md) と組み合わせることで、非常に複雑な検索条件を表現するフィルターを作成することができます。

## <a name="syntax"></a>構文

次の EBNF ([拡張バッカス・ナウア記法形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) では、論理演算子を使用する OData 式の文法が定義されます。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 完全な EBNF については、「[Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)」をご覧ください。

論理式には次の 2 つの形式があります。2 つのサブ式がある 2 項 (`and`/`or`) 形式と、サブ式が 1 つしかない単項 (`not`) 形式です。 サブ式は、次のような任意の種類のブール式とすることができます。

- `Edm.Boolean` 型のフィールドまたは範囲変数
- `geo.intersects` や `search.ismatch` など `Edm.Boolean` 型の値を返す関数
- `rating gt 4` などの[比較式](search-query-odata-comparison-operators.md)
- `Rooms/any(room: room/Type eq 'Deluxe Room')` などの[コレクション式](search-query-odata-collection-operators.md)
- ブール型リテラル: `true` または `false`。
- `and`、`or`、および `not` を使用して構築されるその他の論理式。

> [!IMPORTANT]
> 状況によっては (特にラムダ式内では)、すべての種類のサブ式を `and`/`or` と一緒に使用できるとは限りません。 詳細については、[Azure Search での OData コレクション演算子](search-query-odata-collection-operators.md#limitations)に関するページを参照してください。

### <a name="logical-operators-and-null"></a>論理演算子と `null`

関数や比較などのほとんどのブール式では `null` 値を生成できません。また論理演算子を `null` リテラルに直接適用することはできません (たとえば、`x and null` は許可されていません)。 ただし、ブール値フィールドは `null` にすることができます。そのため、null が存在する場合に `and`、`or`、および `not` 演算子がどのように動作するかを認識しておく必要があります。 次の表に、これについてまとめてあります。ここで、`b` は `Edm.Boolean` 型のフィールドです。

| 式 | `b` が `null` のときの結果 |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

ブール値フィールド `b` がフィルター式内に単独で現れると、それは `b eq true` と記述されているかのように動作します。したがって、`b` が`null` の場合、式は `false` と評価されます。 同様に、`not b` は `not (b eq true)` のように動作するので、`true` と評価されます。 このように、`null` フィールドは `false` と同様に動作します。 上の表に示すように、これは、`and` および `or` を使用して他の式と組み合わせたときの動作と一致しています。 それでも、`false` との直接比較 (`b eq false`) はやはり `false` に評価されます。 つまり、ブール式内で両者が同様に動作しても、`null` は `false` と等しくありません。

## <a name="examples"></a>例

`rating` フィールドが 3 から 5 (両端を含む) の範囲のドキュメントを照合します。

    rating ge 3 and rating le 5

`ratings` フィールドのすべての要素が 3 未満または 5 を超えるドキュメントを照合します。

    ratings/all(r: r lt 3 or r gt 5)

`location` フィールドが指定された多角形内にあり、"public" という用語が含まれていないドキュメントを照合します。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

カナダのバンクーバーにある基本料金が 160 未満のデラックス ルームを備えたホテルに関するドキュメントを照合します。

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>次の手順  

- [Azure Search のフィルター](search-filters.md)
- [Azure Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
