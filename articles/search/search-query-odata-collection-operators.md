---
title: OData コレクション演算子のリファレンス - Azure Search
description: Azure Search クエリでの OData コレクション演算子 (any および all) とラムダ式。
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081924"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Azure Search の OData コレクション演算子 - `any` と `all`

Azure Search で使用する [OData フィルター式](query-odata-filter-orderby-syntax.md)を作成する場合、コレクション フィールドにフィルターを適用すると便利なことがよくあります。 これを行うには、`any` 演算子と `all`演算子を使用します。

## <a name="syntax"></a>構文

次の EBNF ([拡張バッカス・ナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) では、`any` または `all` を使用する OData 式の文法が定義されています。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 完全な EBNF については、「[Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)」をご覧ください。

コレクションをフィルター処理する式には 3 つの形式があります。

- 最初の 2 つでは、コレクション フィールドが反復処理されて、ラムダ式の形式で指定された述語がコレクションの各要素に適用されます。
  - `all` を使用する式からは、コレクションのすべての要素に対して述語が true の場合、`true` が返されます。
  - `any` を使用する式からは、コレクションの少なくとも 1 つの要素に対して述語が true の場合、`true` が返されます。
- コレクション フィルターの 3 番目の形式では、ラムダ式なしで `any` を使用して、コレクション フィールドが空かどうかがテストされます。 コレクションに要素が含まれている場合は、`true` が返されます。 コレクションが空の場合は、`false` が返されます。

コレクション フィルター内の**ラムダ式**は、プログラミング言語のループ本体のようなものです。 これにより、反復中にコレクションの現在の要素を保持する、**範囲変数**と呼ばれる変数が定義されます。 また、コレクションの各要素の範囲変数に適用されるフィルター条件である別のブール式も定義されます。

## <a name="examples"></a>例

`tags` フィールドに文字列 "wifi" と一致するものが含まれているドキュメントを照合します。

    tags/any(t: t eq 'wifi')

`ratings` フィールドのすべての要素が 3 から 5 の範囲 (両数値を含む) に収まるドキュメントを照合します。

    ratings/all(r: r ge 3 and r le 5)

`locations` フィールドの地理座標のいずれかが、指定された多角形の範囲内にあるドキュメントを照合します。

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

`rooms` フィールドが空であるドキュメントを照合します。

    not rooms/any()

すべての部屋において、`rooms/amenities` フィールドに "tv" が含まれ、`rooms/baseRate` が 100 未満であるドキュメントを照合します。

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>制限事項

フィルター式のすべての機能がラムダ式の本体内で使用できるわけではありません。 制限事項は、フィルター処理するコレクション フィールドのデータ型によって異なります。 以下の表に制限事項をまとめています。

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

これらの制限と例の詳細については、[Azure Search でのコレクション フィルターのトラブルシューティング](search-query-troubleshoot-collection-filters.md)に関する記事をご覧ください。 これらの制限が存在する理由の詳細については、[Azure Search でのコレクション フィルター](search-query-understand-collection-filters.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順  

- [Azure Search のフィルター](search-filters.md)
- [Azure Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
